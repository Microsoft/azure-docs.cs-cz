---
title: Infrastruktura Azure pro SAP ASCS/SCS se službou WSFC&sdíleným diskem | Microsoft Docs
description: Naučte se připravit infrastrukturu Azure pro SAP HA pomocí clusteru s podporou převzetí služeb při selhání Windows a sdíleného disku pro instanci SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4218b4c00b79d78965eaf6e73028e63f52b1ff17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673620"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Příprava infrastruktury Azure pro SAP HA pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdíleného disku pro SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfigurace s vysokou dostupností pro SAP multi-SID)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Operační systém Windows][Logo_Windows] Windows


Tento článek popisuje kroky, které můžete provést při přípravě infrastruktury Azure pro instalaci a konfiguraci instance SAP ASCS/SCS s vysokou dostupností v clusteru Windows s podporou převzetí služeb při selhání pomocí *sdíleného disku clusteru* jako možnosti CLUSTERINGU instance SAP ASCS.
V dokumentaci se zobrazí dvě alternativy pro *sdílený disk clusteru* :

- [Sdílené disky Azure](../../disks-shared.md)
- Vytvoření zrcadleného úložiště s využitím s využitím s využitím s [DataKeeper Edition](https://us.sios.com/products/datakeeper-cluster/) pro simulaci sdíleného disku v clusteru 

Uvedená konfigurace se spoléhá na [skupiny umístění v blízkosti Azure (PPG)](./sap-proximity-placement-scenarios.md) , aby dosáhly optimální latence sítě pro úlohy SAP. Dokumentace nepokrývá databázovou vrstvu.  

> [!NOTE]
> Skupiny umístění v blízkosti Azure jsou předpokladem pro použití sdíleného disku Azure.
 

## <a name="prerequisites"></a>Předpoklady

Než začnete s instalací, přečtěte si tento článek:

* [Průvodce architekturou: cluster a instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdíleného disku clusteru][sap-high-availability-guide-wsfc-shared-disk]

## <a name="create-the-ascs-vms"></a>Vytvoření virtuálních počítačů s ASCS

V případě clusteru SAP ASCS/SCS nasaďte dva virtuální počítače ve skupině dostupnosti Azure. Nasaďte virtuální počítače do stejné skupiny umístění pro Proximity. Po nasazení virtuálních počítačů:  
- Vytvoření interního Load Balancer Azure pro instanci SAP ASCS/SCS 
- Přidání virtuálních počítačů s Windows do domény AD

Názvy hostitelů a IP adresy pro uvedený scénář jsou:

| Role názvu hostitele | Název hostitele | Statická IP adresa | Skupina dostupnosti | Skupina umístění blízkosti |
| --- | --- | --- |---| ---|
| cluster ASCS/SCS prvního uzlu clusteru |PR1-ASCS-10 |10.0.0.4 |PR1-ASCS-avset |PR1PPG |
| druhý cluster uzlu clusteru ASCS/SCS |PR1-ASCS-11 |10.0.0.5 |PR1-ASCS-avset |PR1PPG |
| Název sítě s clustery | pr1clust |10.0.0.42 (**pouze** pro cluster se Win 2016) | Není k dispozici | Není k dispozici |
| Název sítě clusteru ASCS | pr1-ascscl |10.0.0.43 | Není k dispozici | Není k dispozici |
| Název sítě clusteru OLAJÍCÍCH (**jenom** pro ERS2) | pr1-erscl |10.0.0.44 | Není k dispozici | Není k dispozici |


## <a name="create-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Vytvoření interního nástroje pro vyrovnávání zatížení Azure

SAP ASCS, SAP SCS a nový ERS2 SAP použijte virtuální název hostitele a virtuální IP adresy. V Azure musí [Nástroj pro vyrovnávání zatížení](../../../load-balancer/load-balancer-overview.md) používat virtuální IP adresu. Důrazně doporučujeme použít službu [Load Balancer úrovně Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). 

> [!IMPORTANT]
> Plovoucí IP adresa není ve scénářích Vyrovnávání zatížení podporována u sekundární konfigurace IP adresy NIC. Podrobnosti najdete v tématu [omezení nástroje pro vyrovnávání zatížení Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Pokud pro virtuální počítač potřebujete další IP adresu, nasaďte druhou síťovou kartu.    


Následující seznam uvádí konfiguraci nástroje pro vyrovnávání zatížení (A) SCS/OLAJÍCÍCH. Konfigurace pro SAP ASCS i ERS2 se provádí ve stejném nástroji pro vyrovnávání zatížení Azure.  

**Určitého SCS**
- Konfigurace front-endu
    - Statická ASCS/SCS IP adresa **10.0.0.43**
- Konfigurace back-endu  
    Přidejte všechny virtuální počítače, které by měly být součástí clusteru (A) SCS/OLAJÍCÍCH. V tomto příkladu jsou virtuální počítače **PR1-ASCS-10** a **PR1-ASCS-11**.
- Port testu paměti
    - Port 620 **Nr** opouští výchozí možnost protokolu (TCP), intervalu (5), prahové hodnoty v pořádku (2)
- Pravidla vyrovnávání zatížení
    - Pokud používáte Standard Load Balancer, vyberte porty HA.
    - Pokud používáte základní Load Balancer, vytvořte pravidla vyrovnávání zatížení pro následující porty.
        - 32 **Nr** TCP
        - 36 **Nr** TCP
        - 39 **Nr** TCP
        - 81 **Nr** TCP
        - 5 **Nr** 13 TCP
        - 5 **Nr** 14 TCP
        - 5 **.** 16 TCP

    - Ujistěte se, že časový limit nečinnosti (v minutách) je nastavený na maximální hodnotu 30 a že je povolená plovoucí IP adresa (přímá odpověď serveru).

**ERS2**

Jako cluster replikace ERS2 (Replication Server 2) je také nutné nakonfigurovat virtuální IP adresu ERS2 na Azure interního nástroje kromě výše uvedeného protokolu SAP ASCS/SCS. Tato část platí jenom v případě, že se používá architektura replikačního serveru fronty 2.  
- Druhá front-endové konfigurace
    - Statické IP adresy SAP ERS2 **10.0.0.44**

- Konfigurace back-endu  
  Virtuální počítače se už přidaly do back-endu interního nástroje fondu.  

- druhý port testu paměti
    - Port 621 **Nr**  
    Ponechte výchozí možnost protokolu (TCP), interval (5), mezní hodnota není v pořádku (2).

- Druhá pravidla vyrovnávání zatížení
    - Pokud používáte Standard Load Balancer, vyberte porty HA.
    - Pokud používáte základní Load Balancer, vytvořte pravidla vyrovnávání zatížení pro následující porty.
        - 32 **Nr** TCP
        - 33 **Nr** TCP
        - 5 **Nr** 13 TCP
        - 5 **Nr** 14 TCP
        - 5 **.** 16 TCP

    - Ujistěte se, že časový limit nečinnosti (v minutách) je nastavený na maximální hodnotu 30 a že je povolená plovoucí IP adresa (přímá odpověď serveru).


> [!TIP]
> Pomocí [šablony Azure Resource Manager pro službu WSFC pro instanci SAP ASCS/SCS se sdíleným diskem Azure](https://github.com/robotechredmond/301-shared-disk-sap)můžete automatizovat přípravu infrastruktury pomocí sdíleného disku Azure pro jedno rozhraní SAP SID s ERS1.  
> Šablona Azure ARM vytvoří dva virtuální počítače s Windows 2019 nebo 2016, vytvoří sdílený disk Azure a připojí se k virtuálním počítačům. Vytvoří a nakonfiguruje se i interní Load Balancer Azure. Podrobnosti – viz Šablona ARM. 

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Přidejte položky registru na oba uzly clusteru instance ASCS/SCS.

Azure Load Balancer mohou ukončit připojení, pokud jsou připojení nečinná po určitou dobu a překračují časový limit nečinnosti. Pracovní procesy SAP otevírají připojení k procesu zařazování do fronty SAP ihned po odeslání první žádosti o zařazení do fronty nebo vyřazení z fronty. Aby nedošlo k přerušení těchto připojení, změňte hodnoty protokolu TCP/IP KeepAliveTime a hodnoty KeepAliveInterval na obou uzlech clusteru. Pokud používáte ERS1, je také nutné přidat parametry profilu SAP, jak je popsáno dále v tomto článku.
Na obou uzlech clusteru musí být změněny následující položky registru:

- KeepAliveTime
- KeepAliveInterval

| Cesta| Název proměnné | Typ proměnné  | Hodnota | Dokumentace |
| --- | --- | --- |---| ---|
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveTime |REG_DWORD (desetinné číslo) |120000 |[KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveInterval |REG_DWORD (desetinné číslo) |120000 |[KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |


Chcete-li změny použít, restartujte oba uzly clusteru.
  
## <a name="add-the-windows-vms-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Přidání virtuálních počítačů s Windows do domény
Po přiřazení statických IP adres k virtuálním počítačům přidejte virtuální počítače do domény. 

## <a name="install-and-configure--windows-failover-cluster"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Instalace a konfigurace clusteru s podporou převzetí služeb při selhání Windows 

### <a name="install-the-windows-failover-cluster-feature"></a>Instalace funkce clusteru s podporou převzetí služeb při selhání systému Windows

Spusťte tento příkaz na jednom z uzlů clusteru:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # Install Windows features.
    # After the feature installs, manually reboot both nodes
    Invoke-Command $ClusterNodes {Install-WindowsFeature Failover-Clustering, FS-FileServer -IncludeAllSubFeature -IncludeManagementTools }
   ```

Po dokončení instalace funkce Restartujte oba uzly clusteru.  

### <a name="test-and-configure-windows-failover-cluster"></a>Testování a konfigurace clusteru s podporou převzetí služeb při selhání Windows 

V systému Windows 2019 cluster automaticky rozpozná, že je spuštěný v Azure, a jako výchozí možnost pro IP adresu správy clusteru bude používat název distribuované sítě. Proto bude používat všechny uzly clusteru s místními IP adresami. V důsledku toho není potřeba vyhrazený (virtuální) síťový název clusteru a tuto IP adresu není potřeba konfigurovat na interním Load Balancer Azure.

Další informace najdete v tématu [Clustering s podporou převzetí služeb při selhání ve Windows serveru 2019 – nové funkce](https://techcommunity.microsoft.com/t5/failover-clustering/windows-server-2019-failover-clustering-new-features/ba-p/544029) spouštějí tento příkaz na jednom z uzlů clusteru:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # IP adress for cluster network name is needed ONLY on Windows Server 2016 cluster
    $ClusterStaticIPAddress = "10.0.0.42"
        
    # Test cluster
    Test-Cluster –Node $ClusterNodes -Verbose
    
    $ComputerInfo = Get-ComputerInfo
    
    $WindowsVersion = $ComputerInfo.WindowsProductName
    
    if($WindowsVersion -eq "Windows Server 2019 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2019 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes -Verbose
    }elseif($WindowsVersion -eq "Windows Server 2016 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2016 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes –StaticAddress $ClusterStaticIPAddress -Verbose 
    }else{
        Write-Error "Not supported Windows version!"
    }
   ```

### <a name="configure-cluster-cloud-quorum"></a>Konfigurovat kvorum cloudu clusteru
Když používáte Windows Server 2016 nebo 2019, doporučujeme nakonfigurovat [Azure Cloud určující](/windows-server/failover-clustering/deploy-cloud-witness)jako kvorum clusteru.

Spusťte tento příkaz na jednom z uzlů clusteru:

   ```powershell
    $AzureStorageAccountName = "cloudquorumwitness"
    Set-ClusterQuorum –CloudWitness –AccountName $AzureStorageAccountName -AccessKey <YourAzureStorageAccessKey> -Verbose
   ```

### <a name="tuning-the-windows-failover-cluster-thresholds"></a>Ladění prahových hodnot clusteru Windows Failover
 
Po úspěšné instalaci clusteru s podporou převzetí služeb při selhání pro Windows je potřeba upravit některé prahové hodnoty, aby byly vhodné pro clustery nasazené v Azure. Parametry, které se mají změnit, jsou popsané v části [ladění prahových hodnot sítě clusteru s podporou převzetí služeb](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) Za předpokladu, že vaše dva virtuální počítače, které tvoří konfiguraci clusteru Windows pro ASCS/SCS, jsou ve stejné podsíti, změňte následující parametry na tyto hodnoty:
- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Tato nastavení byla testována se zákazníky a nabízejí dobré zabezpečení. Jsou dostatečně odolné, ale také poskytují převzetí služeb při selhání, které je dostatečně rychlé pro reálné chybové stavy v úlohách SAP nebo při selhání virtuálního počítače.  

## <a name="configure-azure-shared-disk"></a>Konfigurace sdíleného disku Azure
Tato část je platná jenom v případě, že používáte sdílený disk Azure. 

### <a name="create-and-attach-azure-shared-disk-with-powershell"></a>Vytvoření a připojení sdíleného disku Azure pomocí PowerShellu
Spusťte tento příkaz na jednom z uzlů clusteru. Budete muset upravit hodnoty pro skupinu prostředků, oblast Azure, SAPSID a tak dále.  

   ```powershell
    #############################
    # Create Azure Shared Disk
    #############################
    
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyAzureRegion"
    $SAPSID = "PR1"
    
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    
    # With parameter '-MaxSharesCount', we define the maximum number of cluster nodes to attach the shared disk
    $NumberOfWindowsClusterNodes = 2
            
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "$SAPSID-ascs-10"
    
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "$SAPSID-ascs-11"
    
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```

### <a name="format-the-shared-disk-with-powershell"></a>Formátování sdíleného disku pomocí prostředí PowerShell
1. Získejte číslo disku. Spusťte tyto příkazy PowerShellu na jednom z uzlů clusteru:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 2      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Naformátujte disk. V tomto příkladu je to disk číslo 2. 

   ```powershell
    # Format SAP ASCS Disk number '2', with drive letter 'S'
    $SAPSID = "PR1"
    $DiskNumber = 2
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR1SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Ověřte, zda je disk nyní viditelný jako disk clusteru.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : 88ff1d94-0cf1-4c70-89ae-cbbb2826a484
    # Name       : Cluster Disk 1
    # Number     : 2
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk2\Partition2\}
   ```
4. Zaregistrujte disk v clusteru.  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 1 Online Available Storage Physical Disk
   ```

## <a name="sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> S clusterem DataKeeper pro disk sdílené složky clusteru SAP ASCS/SCS
Tato část je platná pouze v případě, že používáte software třetí strany s clusterem DataKeeper Edition k vytvoření zrcadleného úložiště, které simuluje sdílený disk clusteru.  

Teď máte v Azure fungující konfiguraci clusteringu s podporou převzetí služeb při selhání ve Windows serveru. K instalaci instance SAP ASCS/SCS potřebujete prostředek sdíleného disku. Jednou z možností je použití s tím, že Clusterová edice s datakeeps je řešení třetí strany, které můžete použít k vytvoření prostředků sdíleného disku.  

Instalace s tím, že Clusterová edice DataKeeper pro sdílený disk clusteru SAP ASCS/SCS zahrnuje tyto úlohy:
- V případě potřeby přidejte Microsoft .NET Framework. Viz [s dokumentace] (( https://us.sios.com/products/datakeeper-cluster/) pro nejaktuálnější požadavky rozhraní .NET Framework 
- Instalace s DataKeeper
- Konfigurace s datakeeping

### <a name="install-sios-datakeeper"></a>Instalace s DataKeeper
Nainstalujte cluster s DataKeeper Edition do každého uzlu v clusteru. Pokud chcete vytvořit virtuální sdílené úložiště s využitím DataKeeper, vytvořte synchronizovaný zrcadlový svazek a pak Simulujte sdílené úložiště clusteru.

Než nainstalujete software s, vytvořte uživatele domény DataKeeperSvc.

> [!NOTE]
> Přidejte uživatele domény DataKeeperSvc do místní skupiny správců na obou uzlech clusteru.
>

1. Nainstalujte software v obou uzlech clusteru.

   ![S instalačním programem][sap-ha-guide-figure-3030]

   ![Obrázek 31: první stránka Instalace s datakeeping][sap-ha-guide-figure-3031]

   _První stránka Instalace s datakeeping_

2. V dialogovém okně vyberte **Ano**.

   ![Obrázek 32: DataKeeper vás informuje, že služba bude zakázaná.][sap-ha-guide-figure-3032]

   _DataKeeper vás informuje, že služba bude zakázaná._

3. V dialogovém okně doporučujeme vybrat možnost **účet domény nebo serveru**.

   ![Obrázek 33: výběr uživatele pro s datakeeping][sap-ha-guide-figure-3033]

   _Výběr uživatele pro s datakeeping_

4. Zadejte uživatelské jméno a heslo účtu domény, které jste vytvořili pro datakeeping.

   ![Obrázek 34: zadejte uživatelské jméno a heslo domény pro instalaci s názvem s-Keep.][sap-ha-guide-figure-3034]

   _Zadejte uživatelské jméno a heslo domény pro instalaci s názvem s instalací._

5. Nainstalujte licenční klíč pro vaši instanci s tím, jak je znázorněno na obrázku 35.

   ![Obrázek 35: Zadejte svůj licenční klíč s klíčem DataKeeper.][sap-ha-guide-figure-3035]

   _Zadejte svůj licenční klíč s klíčem datakeeping._

6. Po zobrazení výzvy restartujte virtuální počítač.

### <a name="configure-sios-datakeeper"></a>Konfigurace s datakeeping
Po instalaci s DataKeeper na oba uzly spusťte konfiguraci. Cílem konfigurace je synchronní replikace dat mezi dalšími disky, které jsou připojené ke každému virtuálnímu počítači.

1. Spusťte nástroj pro správu a konfiguraci datakeep a pak vyberte **připojit server**.

   ![Obrázek 36: s nástroje pro správu a konfiguraci datakeeping][sap-ha-guide-figure-3036]

   _Správa s nástrojem datakeeping a Configuration Tool_

2. Zadejte název nebo TCP/IP adresu prvního uzlu, ke kterému se má nástroj pro správu a konfiguraci připojit, a v druhém kroku druhý uzel.

   ![Obrázek 37: Vložte název nebo TCP/IP adresu prvního uzlu, ke kterému se má nástroj pro správu a konfiguraci připojit, a v druhém kroku druhý uzel][sap-ha-guide-figure-3037]

   _Vložte název nebo TCP/IP adresu prvního uzlu, ke kterému se má nástroj pro správu a konfiguraci připojit, a v druhém kroku druhý uzel._

3. Vytvořte úlohu replikace mezi dvěma uzly.

   ![Obrázek 38: vytvoření úlohy replikace][sap-ha-guide-figure-3038]

   _Vytvoření úlohy replikace_

   Průvodce vás provede procesem vytvoření úlohy replikace.

4. Zadejte název úlohy replikace.

   ![Obrázek 39: definování názvu úlohy replikace][sap-ha-guide-figure-3039]

   _Zadejte název úlohy replikace._

   ![Obrázek 40: definujte základní data pro uzel, který by měl být aktuálním zdrojovým uzlem.][sap-ha-guide-figure-3040]

   _Definujte základní data pro uzel, který by měl být aktuálním zdrojovým uzlem._

5. Zadejte název, TCP/IP adresu a diskový svazek cílového uzlu.

   ![Obrázek 41: zadejte název, TCP/IP adresu a diskový svazek aktuálního cílového uzlu.][sap-ha-guide-figure-3041]

   _Zadejte název, TCP/IP adresu a diskový svazek aktuálního cílového uzlu._

6. Definujte algoritmy komprese. V našem příkladu doporučujeme komprimovat datový proud replikace. Obzvláště v situacích opakované synchronizace se komprese streamu replikace výrazně zkracuje i doba opakované synchronizace. Komprese používá prostředky procesoru a paměti RAM virtuálního počítače. Jak se zvyšuje rychlost komprese, takže se použije svazek prostředků procesoru. Toto nastavení můžete upravit později.

7. Další nastavení, které je třeba ověřit, je, zda replikace probíhá asynchronně nebo synchronně. Při ochraně konfigurací SAP ASCS/SCS je nutné použít synchronní replikaci.  

   ![Obrázek 42: definování podrobností replikace][sap-ha-guide-figure-3042]

   _Definování podrobností replikace_

8. Určete, zda má být svazek replikovaný úlohou replikace reprezentován konfigurací clusteru s podporou převzetí služeb při selhání systému Windows Server jako sdílený disk. Pro konfiguraci SAP ASCS/SCS vyberte **Ano** , aby se cluster Windows mohl replikovat replikovaný svazek jako sdílený disk, který může použít jako svazek clusteru.

   ![Obrázek 43: vyberte Ano a nastavte replikovaný svazek jako svazek clusteru.][sap-ha-guide-figure-3043]

   _Vyberte **Ano** a nastavte replikovaný svazek jako svazek clusteru._

   Po vytvoření svazku se v nástroji pro správu a konfiguraci datakeepu zobrazí, že je úloha replikace aktivní.

   ![Obrázek 44: aktivní Synchronní zrcadlení datakeeping pro disk Share SAP ASCS/SCS je aktivní][sap-ha-guide-figure-3044]

   _Synchronní zrcadlení datakeeping pro disk sdílené složky SAP ASCS/SCS je aktivní._

   Správce clusteru s podporou převzetí služeb při selhání nyní zobrazuje disk jako datakeeping disk, jak je znázorněno na obrázku 45:

   ![Obrázek 45: Správce clusteru s podporou převzetí služeb při selhání zobrazuje disk, na který se replikuje datakeeping][sap-ha-guide-figure-3045]

   _Správce clusteru s podporou převzetí služeb při selhání zobrazuje disk, který replikuje datakeeping._


## <a name="next-steps"></a>Další kroky

* [Instalace SAP NetWeaver HA pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdíleného disku pro instanci SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]