---
title: Infrastruktura Azure pro SAP ASCS/SCS s wsfc&sdílený disk | Dokumenty společnosti Microsoft
description: Zjistěte, jak připravit infrastrukturu Azure pro SAP HA pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdíleného disku pro instanci SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8a49bc979923bf52d099e30615910c5bdb0601b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279855"
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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfigurace sap multi-SID s vysokou dostupností)

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


> ![Windows][Logo_Windows] Windows
>

Tento článek popisuje kroky, které podniknete s přípravou infrastruktury Azure pro instalaci a konfiguraci vysoce dostupnosti systému SAP v clusteru s podporou převzetí služeb při selhání systému Windows pomocí *sdíleného disku clusteru* jako možnosti pro clusterování instance SAP ASCS.

## <a name="prerequisites"></a>Požadavky

Než začnete s instalací, přečtěte si tento článek:

* [Průvodce architekturou: Cluster instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdíleného disku clusteru][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Příprava infrastruktury pro architektonickou šablonu 1
Šablony Azure Resource Managerpro SAP pomáhají zjednodušit nasazení požadovaných prostředků.

Třívrstvé šablony ve Správci prostředků Azure také podporují scénáře s vysokou dostupností. Například architektonická šablona 1 má dva clustery. Každý cluster je sap jediný bod selhání pro SAP ASCS/SCS a DBMS.

Tady můžete získat šablony Azure Resource Manageru pro ukázkový scénář, který popisujeme v tomto článku:

* [Image Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Image Azure Marketplace pomocí spravovaných disků Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Vlastní obrázek](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Vlastní obrázek pomocí spravovaných disků](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Příprava infrastruktury pro architektonickou šablonu 1:

- Na webu Azure Portal vyberte v podokně **Parametry** v poli **SYSTEMAVAILABILITY** **možnost HA**.

  ![Obrázek 1: Nastavení sap u azure resource manager parametrů][sap-ha-guide-figure-3000]

_**Obrázek 1:** Nastavení parametrů Azure Resource Manageru s vysokou dostupností SAP_


  Šablony vytvářejí:

  * **Virtuální počítače**:
    * Virtuální počítače aplikačního \<serveru SAP: SAPSystemSID\>-di-\<Číslo\>
    * Virtuální počítače clusteru \<ASCS/SCS:\>SAPSystemSID -ascs-\<Číslo\>
    * Cluster DBMS: \<SAPSystemSID\>\<-db- Číslo\>

  * **Síťové karty pro všechny virtuální počítače s přidruženými IP adresami**:
    * \<SAPSystemSID\>-nic-di-\<Číslo\>
    * \<SAPSystemSID\>-nic-ascs-\<Číslo\>
    * \<SAPSystemSID\>-nic-db-\<Číslo\>

  * **Účty úložiště Azure (pouze nespravované disky):**

  * **Skupiny dostupnosti** pro:
    * Virtuální počítače aplikačního \<serveru SAP: SAPSystemSID\>-avset-di
    * Virtuální počítače clusteru SAP ASCS/SCS: \<SAPSystemSID\>-avset-ascs
    * Virtuální počítače clusteru \<DBMS:\>SAPSystemSID -avset-db

  * **Interní systém pro vyrovnávání zatížení Azure**:
    * Se všemi porty pro instanci \<ASCS/SCS a IP adresu SAPSystemSID\>-lb-ascs
    * Se všemi porty pro SQL Server \<DBMS\>a IP adresu SAPSystemSID -lb-db

  * **Skupina zabezpečení** \<sítě :\>SAPSystemSID -nsg-ascs-0  
    * S otevřeným externím portem RDP (Remote Desktop Protocol) k virtuálnímu počítači \<SAPSystemSID\>-ascs-0

> [!NOTE]
> Všechny IP adresy síťových karet a interních vykladačů zatížení Azure jsou ve výchozím nastavení dynamické. Změňte je na statické ADRESY IP. Popisujeme, jak to udělat později v článku.
>
>

## <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Nasazení virtuálních počítačů s připojením k podnikové síti (mezi místními) pro použití v produkčním prostředí
Pro produkční systémy SAP nasazujte virtuální počítače Azure s připojením k podnikové síti pomocí Azure VPN Gateway nebo Azure ExpressRoute.

> [!NOTE]
> Můžete použít instanci virtuální sítě Azure. Virtuální síť a podsíť již byly vytvořeny a připraveny.
>
>

1. Na webu Azure Portal **vyberte** v podokně Parametry v poli **NEWOREXISTINGSUBNET** **existující**.
2. Do pole **SUBNETID** přidejte úplný řetězec připraveného ID sítě Azure, kde plánujete nasazení virtuálních počítačů Azure.
3. Pokud chcete získat seznam všech síťových podsítí Azure, spusťte tento příkaz PowerShellu:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   V poli **ID** se zobrazí hodnota ID podsítě.
4. Chcete-li získat seznam všech hodnot ID podsítě, spusťte tento příkaz Prostředí PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   ID podsítě vypadá takto:

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Nasazení instancí SAP pouze pro cloud pro testování a ukázku
Systém SAP s vysokou dostupností můžete nasadit v modelu nasazení pouze v cloudu. Tento druh nasazení je primárně užitečné pro ukázkové a testovací případy použití. Není vhodný pro případy použití výroby.

- Na webu Azure Portal vyberte v podokně **Parametry** v poli **NEWOREXISTINGSUBNET** **položku new**. Ponechte pole **SUBNETID** prázdné.

  Šablona SAP Azure Resource Manager automaticky vytvoří virtuální síť Azure a podsíť.

> [!NOTE]
> Musíte také nasadit alespoň jeden vyhrazený virtuální počítač pro službu Active Directory a službu DNS ve stejné instanci virtuální sítě Azure. Šablona nevytváří tyto virtuální počítače.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Příprava infrastruktury pro architektonickou šablonu 2

Pomocí této šablony Azure Resource Manager pro SAP můžete zjednodušit nasazení požadovaných prostředků infrastruktury pro architektonickou šablonu SAP 2.

Tady můžete získat šablony Azure Resource Manageru pro tento scénář nasazení:

* [Image Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Image Azure Marketplace pomocí spravovaných disků](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Vlastní obrázek](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Vlastní obrázek pomocí spravovaných disků](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Příprava infrastruktury pro architektonickou šablonu 3

Můžete připravit infrastrukturu a nakonfigurovat SAP pro multi-SID. Můžete například přidat další instanci SAP ASCS/SCS do *existující* konfigurace clusteru. Další informace najdete [v tématu Konfigurace další instance SAP ASCS/SCS pro existující konfiguraci clusteru k vytvoření konfigurace SAP s více SID ve Správci prostředků Azure][sap-ha-multi-sid-guide].

Pokud chcete vytvořit nový cluster s více SID, můžete použít šablony rychlého spuštění více SID [na GitHubu](https://github.com/Azure/azure-quickstart-templates).

Chcete-li vytvořit nový cluster s více sid, musíte nasadit následující tři šablony:

* [Šablona ASCS/SCS](#ASCS-SCS-template)
* [Šablona databáze](#database-template)
* [Šablona aplikačních serverů](#application-servers-template)

V následujících částech jsou uvedeny další podrobnosti o šablonách a parametrech, které je třeba zadat v šablonách.

### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a>Šablona ASCS/SCS

Šablona ASCS/SCS nasazuje dva virtuální počítače, které můžete použít k vytvoření clusteru s podporou převzetí služeb při selhání systému Windows Server, který hostuje více instancí ASCS/SCS.

Chcete-li nastavit šablonu ASCS/SCS multi-SID, zadejte do [šablony ASCS/SCS multi-SID][sap-templates-3-tier-multisid-xscs-marketplace-image] nebo [ascs/scs multi-SID pomocí spravovaných disků][sap-templates-3-tier-multisid-xscs-marketplace-image-md]hodnoty pro následující parametry:

- **Předpona prostředků**: Nastavte předponu prostředku, která slouží k předponě všech prostředků vytvořených během nasazení. Vzhledem k tomu, že prostředky nepatří pouze do jednoho systému SAP, předpona prostředku není SID jednoho systému SAP.  Předpona musí být mezi třemi a šesti znaky.
- **Typ zásobníku**: Vyberte typ zásobníku systému SAP. V závislosti na typu zásobníku má Nástroj pro vyrovnávání zatížení Azure jednu (jenom ABAP nebo Java) nebo dvě privátní IP adresy (ABAP+Java) na jeden systém SAP.
- **Typ operačního systému**: Vyberte operační systém virtuálních počítačů.
- **Počet systémů SAP**: Vyberte počet systémů SAP, které chcete nainstalovat v tomto clusteru.
- **Dostupnost systému**: Vyberte **možnost HA**.
- **Admin Uživatelské jméno a heslo správce:** Vytvořte nového uživatele, který lze použít k přihlášení k počítači.
- **Nová nebo existující podsíť**: Nastavte, zda chcete vytvořit novou virtuální síť a podsíť nebo použít existující podsíť. Pokud už máte virtuální síť, která je připojená k místní síti, vyberte **existující**.
- **ID podsítě**: Pokud chcete nasadit virtuální ho do existující virtuální sítě, kde máte podsíť definovanou, k které by měl být virtuální hotel přiřazen, pojmenujte ID této konkrétní podsítě. ID obvykle vypadá takto:

  /subscriptions/\<id\>\<předplatného /resourceGroups/\>název skupiny prostředků /providers/Microsoft.Network/virtualNetworks/\<název\>virtuální sítě /podsítě/\<název podsítě\>

Šablona nasazuje jednu instanci Azure Load Balancer, která podporuje více systémů SAP:

- Instance ASCS jsou konfigurovány pro instanci číslo 00, 10, 20...
- Instance SCS jsou konfigurovány pro instanci číslo 01, 11, 21...
- Instance serveru ascs enqueue replication server (ERS) (pouze Linux) jsou konfigurovány pro instanci číslo 02, 12, 22...
- Instance SCS ERS (pouze Linux) jsou konfigurovány pro například číslo 03, 13, 23...

Nástroj pro vyrovnávání zatížení obsahuje 1 VIP (2 pro Linux), 1x VIP pro ASCS/SCS a 1x VIP pro ERS (pouze Linux).

#### <a name="sap-ascsscs-ports"></a><a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a>Porty SAP ASCS/SCS
Následující seznam obsahuje všechna pravidla vyrovnávání zatížení (kde x je číslo systému SAP, například 1, 2, 3...):
- Porty specifické pro Windows pro každý systém SAP: 445, 5985
- Porty ASCS (číslo instance x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Porty SCS (číslo instance x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Porty ASCS ERS na Linuxu (číslo instance x2): 33x2, 5x213, 5x214, 5x216
- Porty SCS ERS na Linuxu (číslo instance x3): 33x3, 5x313, 5x314, 5x316

Nástroj pro vyrovnávání zatížení je nakonfigurován tak, aby používal následující porty sondy (kde x je číslo systému SAP, například 1, 2, 3...):
- Port sondy interního systému pro vyrovnávání zatížení ASCS/SCS: 620x0
- PORT interní sondy ERS (pouze Linux): 621x2

### <a name="database-template"></a><a name="database-template"></a>Šablona databáze

Šablona databáze nasazuje jeden nebo dva virtuální počítače, které můžete použít k instalaci systému správy relačních databází (RDBMS) pro jeden systém SAP. Pokud například nasadíte šablonu ASCS/SCS pro pět systémů SAP, budete muset tuto šablonu nasadit pětkrát.

Chcete-li nastavit databázovou šablonu multi-SID, zadejte do [šablony více SID v databázi][sap-templates-3-tier-multisid-db-marketplace-image] nebo [v šabloně více SID pomocí spravovaných disků][sap-templates-3-tier-multisid-db-marketplace-image-md]hodnoty pro následující parametry:

- **Id systému SAP**: Zadejte ID systému SAP systému SAP, který chcete nainstalovat. ID se používá jako předpona pro prostředky, které jsou nasazeny.
- **Typ operačního systému**: Vyberte operační systém virtuálních počítačů.
- **Dbtype**: Vyberte typ databáze, kterou chcete nainstalovat do clusteru. Chcete-li nainstalovat server Microsoft SQL Server, vyberte **sql.** Pokud plánujete instalaci SAP HANA do virtuálních počítačů, vyberte **hana.** Ujistěte se, že jste vybrali správný typ operačního systému. Vyberte **Windows** pro SQL a vyberte linuxovou distribuci pro HANA. Nástroj Pro vyrovnávání zatížení Azure, který je připojený k virtuálním počítačům, je nakonfigurovaný tak, aby podporoval vybraný typ databáze:
  * **SQL**: Port pro vyrovnávání zatížení nástroje pro vyrovnávání zatížení 1433. Ujistěte se, že tento port používáte pro nastavení sql serveru AlwaysOn.
  * **HANA**: Porty pro vyrovnávání zatížení vyvažovače zatížení 35015 a 35017. Ujistěte se, že jste nainstalovali SAP HANA s instancí číslo **50**.
  Vykladač zatížení používá port sondy 62550.
- **Velikost systému SAP**: Nastavte počet SAPS, které nový systém poskytuje. Pokud si nejste jisti, kolik SAPS systém vyžaduje, zeptejte se svého technologického partnera SAP nebo systémového integrátora.
- **Dostupnost systému**: Vyberte **možnost HA**.
- **Admin Uživatelské jméno a heslo správce:** Vytvořte nového uživatele, který lze použít k přihlášení k počítači.
- **Id podsítě**: Zadejte ID podsítě, kterou jste použili při nasazení šablony ASCS/SCS, nebo ID podsítě, která byla vytvořena jako součást nasazení šablony ASCS/SCS.

### <a name="application-servers-template"></a><a name="application-servers-template"></a>Šablona aplikačních serverů

Šablona aplikačních serverů nasazuje dva nebo více virtuálních počítačů, které lze použít jako instance aplikačního serveru SAP pro jeden systém SAP. Pokud například nasadíte šablonu ASCS/SCS pro pět systémů SAP, budete muset tuto šablonu nasadit pětkrát.

Chcete-li nastavit šablonu aplikačních serverů s více SID, zadejte do [šablony více SID aplikačních serverů][sap-templates-3-tier-multisid-apps-marketplace-image] nebo [aplikačních serverů s více SID pomocí spravovaných disků][sap-templates-3-tier-multisid-apps-marketplace-image-md]hodnoty pro následující parametry:

  -  **Id systému SAP**: Zadejte ID systému SAP systému SAP, který chcete nainstalovat. ID se používá jako předpona pro prostředky, které jsou nasazeny.
  -  **Typ operačního systému**: Vyberte operační systém virtuálních počítačů.
  -  **Velikost systému SAP**: Počet SAPS, který nový systém poskytuje. Pokud si nejste jisti, kolik SAPS systém vyžaduje, zeptejte se svého technologického partnera SAP nebo systémového integrátora.
  -  **Dostupnost systému**: Vyberte **možnost HA**.
  -  **Admin Uživatelské jméno a heslo správce:** Vytvořte nového uživatele, který lze použít k přihlášení k počítači.
  -  **Id podsítě**: Zadejte ID podsítě, kterou jste použili při nasazení šablony ASCS/SCS, nebo ID podsítě, která byla vytvořena jako součást nasazení šablony ASCS/SCS.


## <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Virtuální síť Azure
V našem příkladu je adresní prostor instance virtuální sítě Azure 10.0.0.0/16. Existuje jedna podsíť s názvem Podsíť s rozsahem adres 10.0.0.0/24. Všechny virtuální počítače a interní nástroje pro vyrovnávání zatížení se nasazují v této virtuální síti.

> [!IMPORTANT]
> Neprovávejte žádné změny v nastavení sítě v hostovaném operačním systému. To zahrnuje adresy IP, servery DNS a podsíť. Nakonfigurujte všechna nastavení sítě v Azure. Služba DHCP (Dynamic Host Configuration Protocol) rozšíří vaše nastavení.
>
>

## <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>IP adresy DNS

Chcete-li nastavit požadované adresy IP DNS, proveďte následující kroky:

1. Na webu Azure Portal v podokně **serverů DNS** zkontrolujte, jestli je možnost **dns** virtuální sítě **nastavená**na vlastní DNS .
2. Vyberte nastavení na základě typu sítě, kterou máte. Další informace najdete v následujících materiálech:
   * Přidejte adresy IP místních serverů DNS.  
   Místní servery DNS můžete rozšířit na virtuální počítače, které běží v Azure. V takovém scénáři můžete přidat IP adresy virtuálních počítačů Azure, na kterých spustíte službu DNS.
   * Pro nasazení virtuálních počítačů, které jsou izolované v Azure: Nasazení dalšího virtuálního počítače ve stejné instanci virtuální sítě, která slouží jako server DNS. Přidejte IP adresy virtuálních počítačů Azure, které jste nastavili pro spuštění služby DNS.

   ![Obrázek 2: Konfigurace serverů DNS pro virtuální síť Azure][sap-ha-guide-figure-3001]

   _**Obrázek 2:** Konfigurace serverů DNS pro virtuální síť Azure_

   > [!NOTE]
   > Pokud změníte IP adresy serverů DNS, budete muset restartovat virtuální počítače Azure použít změnu a šířit nové servery DNS.
   >
   >

V našem příkladu je služba DNS nainstalována a nakonfigurována na těchto virtuálních počítačích s Windows:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Název síťové karty | Statická IP adresa |
| --- | --- | --- | --- |
| První DNS server |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Druhý server DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Názvy hostitelů a statické adresy IP pro clusterověvěvěvě vsap ASCS/SCS a clusterovnou instanci DBMS

Pro místní nasazení potřebujete tyto vyhrazené názvy hostitelů a IP adresy:

| Role názvu virtuálního hostitele | Název virtuálního hostitele | Virtuální statická IP adresa |
| --- | --- | --- |
| SAP ASCS/SCS první název virtuálního hostitele clusteru (pro správu clusteru) |pr1-ascs-vir |10.0.0.42 |
| Název virtuálního hostitele instance SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Název virtuálního hostitele SAP DBMS druhý cluster (správa clusteru) |pr1-dbms-vir |10.0.0.32 |

Při vytváření clusteru vytvořte názvy virtuálních hostitelů pr1-ascs-vir a pr1-dbms-vir a přidružené IP adresy, které spravují samotný cluster. Informace o tom, jak to provést, naleznete [v tématu Shromažďování uzlů clusteru v konfiguraci clusteru][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Můžete ručně vytvořit další dva názvy virtuálních hostitelů, pr1-ascs-sap a pr1-dbms-sap a přidružené IP adresy na serveru DNS. Clusterovaná instance SAP ASCS/SCS a clusterovaná instance DBMS používají tyto prostředky. Informace o tom, jak to provést, naleznete [v tématu Vytvoření názvu virtuálního hostitele pro clusterově ovou instanci SAP ASCS/SCS][sap-ha-guide-9.1.1].

## <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Nastavení statických IP adres pro virtuální počítače SAP
Po nasazení virtuálních počítačů pro použití v clusteru je potřeba nastavit statické IP adresy pro všechny virtuální počítače. Udělejte to v konfiguraci virtuální sítě Azure a ne v hostovaném operačním systému.

1. Na portálu Azure vyberte**IP adresa****nastavení** > **síťové karty** >  **skupiny** > prostředků .
2. V podokně **Adresy IP** vyberte v části **Přiřazení** **položku Statický**. Do pole **IP adresa** zadejte adresu IP, kterou chcete použít.

   > [!NOTE]
   > Pokud změníte IP adresu síťové karty, budete muset restartovat virtuální počítače Azure použít změnu.  
   >
   >

   ![Obrázek 3: Nastavení statických IP adres pro síťovou kartu každého virtuálního počítače][sap-ha-guide-figure-3002]

   _**Obrázek 3:** Nastavení statických IP adres pro síťovou kartu každého virtuálního počítače_

   Tento krok opakujte pro všechna síťová rozhraní, tj.

V našem příkladu máme tyto virtuální počítače a statické IP adresy:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Název síťové karty | Statická IP adresa |
| --- | --- | --- | --- |
| První instance aplikačního serveru SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Druhá instance aplikačního serveru SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Poslední instance aplikačního serveru SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| První uzel clusteru pro instanci ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Druhý uzel clusteru pro instanci ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| První uzel clusteru pro instanci DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Druhý uzel clusteru pro instanci DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Nastavení statické IP adresy pro interní systém vyrovnávání zatížení Azure

Šablona SAP Azure Resource Manager vytvoří interní nástroje pro vyrovnávání zatížení Azure, který se používá pro cluster instancí SAP ASCS/SCS a cluster DBMS.

> [!IMPORTANT]
> IP adresa názvu virtuálního hostitele SAP ASCS/SCS je stejná jako IP adresa interního nástroje pro vyrovnávání zatížení SAP ASCS/SCS: pr1-lb-ascs.
> IP adresa virtuálního názvu DBMS je stejná jako IP adresa interního nástroje pro vyrovnávání zatížení DBMS: pr1-lb-dbms.
>
>

Nastavení statické IP adresy pro interní systém vyrovnávání zatížení Azure:

1. Počáteční nasazení nastaví adresu IP interního vykladače zatížení na **dynamickou**. Na webu Azure Portal vyberte v podokně **IP adresy** v části **Přiřazení** **možnost Statická**.
2. Nastavte IP adresu interního nástroje pro vyrovnávání zatížení **pr1-lb-ascs** na IP adresu názvu virtuálního hostitele instance SAP ASCS/SCS.
3. Nastavte IP adresu interního nástroje pro vyrovnávání zatížení **pr1-lb-dbms** na IP adresu názvu virtuálního hostitele instance DBMS.

   ![Obrázek 4: Nastavení statických IP adres pro interní vyrovnávání zatížení pro instanci SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Obrázek 4:** Nastavení statických IP adres pro interní systém vyrovnávání zatížení pro instanci SAP ASCS/SCS_

V našem příkladu máme dva interní vykladače zatížení Azure, které mají tyto statické IP adresy:

| Role interního vyrovnávání zatížení Azure | Název interního systému vyrovnávání zatížení Azure | Statická IP adresa |
| --- | --- | --- |
| Interní systém vyrovnávání zatížení instance SAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Interní systém vyrovnávání zatížení SAP DBMS |pr1-lb-dbms |10.0.0.33 |


## <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Výchozí pravidla vyrovnávání zatížení ASCS/SCS pro interní vyrovnávání zatížení Azure

Šablona SAP Azure Resource Manager vytvoří porty, které potřebujete:
* Instance ASCS ABAP s výchozím číslem instance 00
* Instance Java SCS s výchozím číslem instance 01

Při instalaci instance SAP ASCS/SCS je nutné použít výchozí číslo instance 00 pro instanci ABAP ASCS a výchozí číslo instance 01 pro instanci Java SCS.

Dále vytvořte požadované koncové body internal vyrovnávání zatížení pro porty SAP NetWeaver.

Chcete-li vytvořit požadované koncové body internal vyrovnávání zatížení, vytvořte nejprve tyto koncové body vyrovnávání zatížení pro porty ASCS SAP NetWeaver:

| Název pravidla vyrovnávání služby/zatížení | Výchozí čísla portů | Konkrétní porty pro (instance ASCS s číslem instance 00) (ERS s 10) |
| --- | --- | --- |
| Enqueue server / *lbrule3200* |32\<Číslo instance\> |3200 |
| Server zpráv ABAP / *lbrule3600* |36\<Číslo instance\> |3600 |
| Interní zpráva ABAP / *lbrule3900* |39\<Číslo instance\> |3900 |
| Server zpráv HTTP / *Lbrule8100* |81\<Číslo instance\> |8100 |
| Sap start service ASCS HTTP / *Lbrule50013* |5\<Číslo\>instance 13 |50013 |
| SAP start service ASCS HTTPS / *Lbrule50014* |5\<Číslo\>instance 14 |50014 |
| Replikace fronty / *Lbrule50016* |5\<Číslo\>instance 16 |50016 |
| SAP start service ERS HTTP *Lbrule51013* |5\<Číslo\>instance 13 |51013 |
| SAP start service ERS HTTP *Lbrule51014* |5\<Číslo\>instance 14 |51014 |
| Vzdálená správa systému Windows (WinRM) *Lbrule5985* | |5985 |
| Sdílení souborů *Lbrule445* | |445 |

**Tabulka 1:** Čísla portů instancí ASCS SAP NetWeaver ABAP

Potom vytvořte tyto koncové body vyrovnávání zatížení pro porty SAP NetWeaver Java SCS:

| Název pravidla vyrovnávání služby/zatížení | Výchozí čísla portů | Konkrétní porty pro (instance SCS s číslem instance 01) (ERS s 11) |
| --- | --- | --- |
| Enqueue server / *lbrule3201* |32\<Číslo instance\> |3201 |
| Server brány / *lbrule3301* |33\<Číslo instance\> |3301 |
| Server zpráv Java / *lbrule3900* |39\<Číslo instance\> |3901 |
| Server zpráv HTTP / *Lbrule8101* |81\<Číslo instance\> |8101 |
| Služba SAP start SCS HTTP / *Lbrule50113* |5\<Číslo\>instance 13 |50113 |
| Služba SAP start SCS HTTPS / *Lbrule50114* |5\<Číslo\>instance 14 |50114 |
| Replikace fronty / *Lbrule50116* |5\<Číslo\>instance 16 |50116 |
| SAP start servis ERS HTTP *Lbrule51113* |5\<Číslo\>instance 13 |51113 |
| SAP start servis ERS HTTP *Lbrule51114* |5\<Číslo\>instance 14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| Sdílení souborů *Lbrule445* | |445 |

**Tabulka 2:** Čísla portů instancí Java SCS SAP NetWeaver

![Obrázek 5: Výchozí pravidla vyrovnávání zatížení ASCS/SCS pro interní vyrovnávání zatížení Azure][sap-ha-guide-figure-3004]

_**Obrázek 5:** Výchozí pravidla vyrovnávání zatížení ASCS/SCS pro interní vyrovnávání zatížení Azure_

Nastavte IP adresu nástroje pro vyrovnávání zatížení pr1-lb-dbms na IP adresu názvu virtuálního hostitele instance DBMS.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Změna výchozích pravidel vyrovnávání zatížení ASCS/SCS pro interní vyrovnávání zatížení Azure

Pokud chcete použít různá čísla pro instance SAP ASCS nebo SCS, musíte změnit názvy a hodnoty jejich portů z výchozích hodnot.

1. Na webu Azure Portal vyberte >  ** \<SID\>-lb-ascs**pravidla**vyrovnávání zatížení**.
2. Pro všechna pravidla vyrovnávání zatížení, která patří do instance SAP ASCS nebo SCS, změňte tyto hodnoty:

   * Name (Název)
   * Port
   * Back-end port

   Chcete-li například změnit výchozí číslo instance ASCS z 00 na 31, je třeba provést změny pro všechny porty uvedené v tabulce 1.

   Zde je příklad aktualizace pro port *lbrule3200*.

   ![Obrázek 6: Změna výchozích pravidel vyrovnávání zatížení ASCS/SCS pro interní vyrovnávání zatížení Azure][sap-ha-guide-figure-3005]

   _**Obrázek 6:** Změna výchozích pravidel vyrovnávání zatížení ASCS/SCS pro interní vyrovnávání zatížení Azure_

## <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Přidání virtuálních počítačů Windows do domény

Po přiřazení statické IP adresy virtuálním počítačům přidejte virtuální počítače do domény.

![Obrázek 7: Přidání virtuálního počítače do domény][sap-ha-guide-figure-3006]

_**Obrázek 7:** Přidání virtuálního počítače do domény_

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Přidání položek registru v obou uzlech clusteru instance SAP ASCS/SCS

Azure Load Balancer má interní vyrovnávání zatížení, který zavře připojení, když jsou připojení nečinná po nastavenou dobu (časový limit nečinnosti). Pracovní procesy SAP v instanci dialogu otevírají připojení k procesu zařazení SAP, jakmile je třeba odeslat první požadavek fronty/vyřazení z fronty. Tato připojení obvykle zůstávají vytvořena, dokud se nerestartuje pracovní proces nebo proces zařazení do fronty. Pokud je však připojení nečinné po nastavenou dobu, interní vyrovnávání zatížení Azure ukončí připojení. To není problém, protože pracovní proces SAP obnoví připojení k procesu zařazení do fronty, pokud již neexistuje. Tyto aktivity jsou dokumentovány v vývoji trasování procesů SAP, ale vytvářejí velké množství dalšího obsahu v těchto trasování. Je vhodné změnit protokol TCP/IP `KeepAliveTime` a `KeepAliveInterval` na obou uzlech clusteru. Zkombinujte tyto změny v parametrech Protokolu TCP/IP s parametry profilu SAP, které jsou popsány dále v článku.

Chcete-li přidat položky registru v obou uzlech clusteru instance SAP ASCS/SCS, přidejte nejprve tyto položky registru systému Windows v obou uzlech clusteru Systému Windows pro SAP ASCS/SCS:

| Cesta | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parametry |
| --- | --- |
| Název proměnné |`KeepAliveTime` |
| Typ proměnné |REG_DWORD (desetinné číslo) |
| Hodnota |120000 |
| Odkaz na dokumentaci |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabulka 3:** Změna prvního parametru Protokolu TCP/IP

Potom přidejte tuto položku registru systému Windows na obou uzlech clusteru Systému Windows pro SAP ASCS/SCS:

| Cesta | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parametry |
| --- | --- |
| Název proměnné |`KeepAliveInterval` |
| Typ proměnné |REG_DWORD (desetinné číslo) |
| Hodnota |120000 |
| Odkaz na dokumentaci |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Tabulka 4:** Změna druhého parametru Protokolu TCP/IP

Chcete-li použít změny, restartujte oba uzly clusteru.

## <a name="set-up-a-windows-server-failover-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Nastavení clusteru s podporou převzetí služeb při selhání systému Windows Server pro instanci SAP ASCS/SCS

Nastavení clusteru s podporou převzetí služeb při selhání systému Windows Server pro instanci SAP ASCS/SCS zahrnuje tyto úlohy:

- Shromážděte uzly clusteru v konfiguraci clusteru.
- Konfigurace důkaz čehož připojili se ke složce clusteru.

### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Shromáždění uzlů clusteru v konfiguraci clusteru

1. V Průvodci přidáním role a funkcí přidejte clustering s podporou převzetí služeb při selhání do obou uzlů clusteru.
2. Nastavte cluster s podporou převzetí služeb při selhání pomocí Správce clusteru s podporou převzetí služeb při selhání. Ve Správci clusteru s podporou převzetí služeb při selhání vyberte **vytvořit cluster**a přidejte pouze název prvního clusteru (uzlu A). Ještě nepřidávejte druhý uzel; přidáte druhý uzel v pozdějším kroku.

   ![Obrázek 8: Přidání názvu serveru nebo virtuálního počítače prvního uzlu clusteru][sap-ha-guide-figure-3007]

   _**Obrázek 8:** Přidání názvu serveru nebo virtuálního počítače prvního uzlu clusteru_

3. Zadejte název sítě (název virtuálního hostitele) clusteru.

   ![Obrázek 9: Zadejte název clusteru][sap-ha-guide-figure-3008]

   _**Obrázek 9:** Zadejte název clusteru._

4. Po vytvoření clusteru spusťte ověřovací test clusteru.

   ![Obrázek 10: Spuštění kontroly ověření clusteru][sap-ha-guide-figure-3009]

   _**Obrázek 10:** Spuštění kontroly ověření clusteru_

   V tomto okamžiku procesu můžete ignorovat všechna upozornění týkající se disků. Později přidáte důkaz čehož připojili jste sdílené složky a sdílené disky SIOS. V této fázi se nemusíte starat o kvorum.

   ![Obrázek 11: Nebyl nalezen disk kvora.][sap-ha-guide-figure-3010]

   _**Obrázek 11:** Nebyl nalezen žádný disk kvora._

   ![Obrázek 12: Základní prostředek clusteru potřebuje novou IP adresu][sap-ha-guide-figure-3011]

   _**Obrázek 12:** Základní prostředek clusteru potřebuje novou adresu IP_

5. Změňte adresu IP základní clusterové služby. Cluster nelze spustit, dokud nezměníte IP adresu základní clusterové služby, protože ADRESA IP serveru odkazuje na jeden z uzlů virtuálního počítače. Udělejte to na stránce **Vlastnosti** prostředku IP základní clusterové služby.

   Například potřebujeme přiřadit IP adresu (v našem příkladu 10.0.0.42) pro název virtuálního hostitele clusteru pr1-ascs-vir.

   ![Obrázek 13: V dialogovém okně Vlastnosti změňte adresu IP][sap-ha-guide-figure-3012]

   _**Obrázek 13:** V dialogovém okně **Vlastnosti** změňte adresu IP_

   ![Obrázek 14: Přiřazení adresy IP, která je vyhrazena pro cluster][sap-ha-guide-figure-3013]

   _**Obrázek 14:** Přiřazení adresy IP, která je vyhrazena pro cluster_

6. Přenesete název virtuálního hostitele clusteru do režimu online.

   ![Obrázek 15: Základní služba clusteru je v provozu se správnou IP adresou][sap-ha-guide-figure-3014]

   _**Obrázek 15:** Základní služba clusteru je v provozu se správnou ip adresou_

7. Přidejte druhý uzel clusteru.

   Nyní, když je základní clusterová služba v provozu, můžete přidat druhý uzel clusteru.

   ![Obrázek 16 Přidání druhého uzlu clusteru][sap-ha-guide-figure-3015]

   _**Obrázek 16:** Přidání druhého uzlu clusteru_

8. Zadejte název druhého hostitele uzlu clusteru.

   ![Obrázek 17: Zadání názvu hostitele druhého uzlu clusteru][sap-ha-guide-figure-3016]

   _**Obrázek 17:** Zadejte název hostitele druhého uzlu clusteru._

   > [!IMPORTANT]
   > Ujistěte se, že *není* zaškrtnuté políčko **Přidat do clusteru všechny způsobilé úložiště.**  
   >
   >

   ![Obrázek 18: Nezaškrtávat políčko][sap-ha-guide-figure-3017]

   _**Obrázek 18:** Nezaškrtávat políčko *not*_

   Upozornění týkající se kvora a disků můžete ignorovat. Kvorum nastavíte a disk budete sdílet později, jak je popsáno v [části Instalace sios datakeeper clusteru Edition pro disk sdílení clusteru SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

   ![Obrázek 19: Ignorovat upozornění týkající se kvora disku][sap-ha-guide-figure-3018]

   _**Obrázek 19:** Ignorovat upozornění týkající se kvora disku_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Konfigurace důkazu sdílení souborů clusteru

Konfigurace sdílené složky clusteru zahrnuje tyto úkoly:

- Vytvořte sdílenou složku.
- Nastavte kvorum důkaz ní sdílení souborů ve Správci clusteru s podporou převzetí služeb při selhání.

#### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Vytvoření sdílené složky

1. Vyberte důkaz čehož it share souboru místo disku kvora. SIOS DataKeeper tuto možnost podporuje.

   V příkladech v tomto článku je důkaz čehož it na serveru Služby Active Directory nebo DNS, který běží v Azure. Důkaz ní se nazývá domcontr-0. Vzhledem k tomu, že byste nakonfigurovali připojení VPN k Azure (přes bránu VPN nebo Azure ExpressRoute), vaše služba Active Directory nebo DNS je místní a není vhodná ke spuštění důkazní hodu sdílené složky.

   > [!NOTE]
   > Pokud vaše služba Active Directory nebo DNS běží pouze místně, nekonfigurujte důkaz sledování sdílené složky v operačním systému Active Directory nebo DNS Windows, který běží místně. Latence sítě mezi uzly clusteru spuštěná v Azure a službou Active Directory nebo místně DNS může být příliš velká a může způsobit problémy s připojením. Nezapomeňte nakonfigurovat důkaz pro sdílení souborů na virtuálním počítači Azure, který běží v blízkosti uzlu clusteru.  
   >
   >

   Jednotka kvora potřebuje alespoň 1 024 MB volného místa. Pro jednotku kvora doporučujeme 2 048 MB volného místa.

2. Přidejte objekt názvu clusteru.

   ![Obrázek 20: Přiřazení oprávnění ke sdílené složce pro objekt názvu clusteru][sap-ha-guide-figure-3019]

   _**Obrázek 20:** Přiřazení oprávnění ke sdílené složce pro objekt názvu clusteru_

   Ujistěte se, že oprávnění zahrnují oprávnění ke změně dat ve sdílené složce pro objekt názvu clusteru (v našem příkladu pr1-ascs-vir$).

3. Chcete-li přidat objekt názvu clusteru do seznamu, vyberte **přidat**. Změňte filtr a zkontrolujte, zda nechcete mít počítačové objekty, kromě objektů uvedených na obrázku 22.

   ![Obrázek 21: Změna typů objektů tak, aby zahrnovaly počítače][sap-ha-guide-figure-3020]

   _**Obrázek 21:** Změna **typů objektů** tak, aby zahrnovaly počítače_

   ![Obrázek 22: Zaškrtněte políčko Počítače][sap-ha-guide-figure-3021]

   _**Obrázek 22:** Zaškrtněte políčko **Počítače.**_

4. Zadejte objekt názvu clusteru, jak je znázorněno na obrázku 21. Vzhledem k tomu, že záznam již byl vytvořen, můžete změnit oprávnění, jak je znázorněno na obrázku 20.

5. Vyberte kartu **Zabezpečení** sdílené položky a nastavte podrobnější oprávnění pro objekt názvu clusteru.

   ![Obrázek 23: Nastavení atributů zabezpečení objektu názvu clusteru v kvoru sdílené složky][sap-ha-guide-figure-3022]

   _**Obrázek 23:** Nastavení atributů zabezpečení objektu názvu clusteru v kvoru sdílené složky_

#### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Nastavení kvora důkazního názoru sdílené složky ve Správci clusteru s podporou převzetí služeb při selhání

1. Otevřete Průvodce konfigurací nastavení kvora.

   ![Obrázek 24: Spuštění Průvodce konfigurací kvora clusteru][sap-ha-guide-figure-3023]

   _**Obrázek 24:** Spuštění Průvodce konfigurací kvora clusteru_

2. Na stránce **Vybrat možnost konfigurace kvora** vyberte **vybrat důkaz kvora**.

   ![Obrázek 25: Konfigurace kvora, ze kterých si můžete vybrat][sap-ha-guide-figure-3024]

   _**Obrázek 25:** Konfigurace kvora, ze kterých si můžete vybrat_

3. Na stránce **Vybrat důkaz kvora** vyberte **Konfigurovat důkaz sdílení souborů**.

   ![Obrázek 26: Vyberte důkaz čehož připojili se ke složce sdílení souborů.][sap-ha-guide-figure-3025]

   _**Obrázek 26:** Vyberte důkaz sdílení souborů_

4. Zadejte cestu UNC ke sdílené složce \\(v našem příkladu domcontr-0\FSW). Chcete-li zobrazit seznam změn, které můžete provést, vyberte **možnost Další**.

   ![Obrázek 27: Definování umístění sdílené složky pro sdílenou složku s kopií clusteru][sap-ha-guide-figure-3026]

   _**Obrázek 27:** Definujte umístění sdílené složky pro sdílenou složku s kopií clusteru_

5. Vyberte požadované změny a pak vyberte **Další**. Je třeba úspěšně překonfigurovat konfiguraci clusteru, jak je znázorněno na obrázku 28:  

   ![Obrázek 28: Potvrzení, že jste překonfigurovali cluster][sap-ha-guide-figure-3027]

   _**Obrázek 28:** Potvrzení, že jste překonfigurovali cluster_

Po úspěšné instalaci clusteru windows převzetí služeb při selhání, je třeba změnit některé prahové hodnoty tak, aby přizpůsobit zjišťování převzetí služeb při selhání na podmínky v Azure. Parametry, které mají být změněny, jsou popsány v [prahových hodnotách sítě clusteru s podporou převzetí služeb při selhání .][tuning-failover-cluster-network-thresholds] Za předpokladu, že vaše dva virtuální počítače, které tvoří konfiguraci clusteru Windows pro ASCS/SCS, jsou ve stejné podsíti, změňte následující parametry na tyto hodnoty:

- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- Délka historie směrování = 30

Tato nastavení byla testována se zákazníky a nabízejí dobrý kompromis. Jsou dostatečně odolné, ale také poskytují převzetí služeb při selhání, které je dostatečně rychlé v reálných chybových stavech v softwaru SAP nebo v selhání uzlu nebo virtuálního počítače.

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Instalace aplikace SIOS DataKeeper Cluster Edition pro disk sdílení clusteru SAP ASCS/SCS

Nyní máte funkční konfiguraci clusteringu Windows Server s podporou převzetí služeb při selhání v Azure. Chcete-li nainstalovat instanci SAP ASCS/SCS, potřebujete prostředek sdíleného disku. V Azure nemůžete vytvořit prostředky sdíleného disku, které potřebujete. SIOS DataKeeper Cluster Edition je řešení jiného výrobce, které můžete použít k vytvoření sdílených diskových prostředků.

Instalace aplikace SIOS DataKeeper Cluster Edition pro disk sdílení clusteru SAP ASCS/SCS zahrnuje tyto úkoly:

- Přidejte rozhraní Microsoft .NET Framework 3.5.
- Nainstalujte SIOS DataKeeper.
- Nastavte SIOS DataKeeper.

### <a name="add-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Přidat rozhraní .NET Framework 3.5
Rozhraní .NET Framework 3.5 není automaticky aktivováno nebo nainstalováno v systému Windows Server 2012 R2. Vzhledem k tomu, že SIOS DataKeeper vyžaduje .NET být na všech uzlech, kde nainstalujete DataKeeper, je nutné nainstalovat rozhraní .NET Framework 3.5 na hostovaném operačním systému všech virtuálních počítačů v clusteru.

Existují dva způsoby, jak přidat rozhraní .NET Framework 3.5:

- Průvodce přidáním rolí a funkcí v systému Windows, jak je znázorněno na obrázku 29:

  ![Obrázek 29: Instalace rozhraní .NET Framework 3.5 pomocí Průvodce přidáním rolí a funkcí][sap-ha-guide-figure-3028]

  _**Obrázek 29:** Instalace rozhraní .NET Framework 3.5 pomocí Průvodce přidáním rolí a funkcí_

  ![Obrázek 30: Indikátor průběhu instalace při instalaci rozhraní .NET Framework 3.5 pomocí Průvodce přidáním rolí a funkcí][sap-ha-guide-figure-3029]

  _**Obrázek 30:** Indikátor průběhu instalace při instalaci rozhraní .NET Framework 3.5 pomocí Průvodce přidáním rolí a funkcí_

- Použijte nástroj příkazového řádku dism.exe. Pro tento typ instalace je třeba získat přístup k adresáři SxS na instalačním médiu systému Windows. Na příkazovém řádku se zvýšenými oprávněními zadejte tento příkaz:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a>Instalace datového měřiče SIOS

Nainstalujte sios DataKeeper Cluster Edition na každém uzlu v clusteru. Chcete-li vytvořit virtuální sdílené úložiště pomocí SIOS DataKeeper, vytvořte synchronizované zrcadlo a pak simulujte sdílené úložiště clusteru.

Před instalací softwaru SIOS vytvořte uživatele domény DataKeeperSvc.

> [!NOTE]
> Přidejte uživatele domény DataKeeperSvc do skupiny Místní správce v obou uzlech clusteru.
>
>

Instalace SIOS DataKeeper:

1. Nainstalujte software SIOS do obou uzlů clusteru.

   ![Instalační program SIOS][sap-ha-guide-figure-3030]

   ![Obrázek 31: První stránka instalace SIOS DataKeeper][sap-ha-guide-figure-3031]

   _**Obrázek 31:** První stránka instalace SIOS DataKeeper_

2. V dialogovém okně vyberte **Ano**.

   ![Obrázek 32: DataKeeper vás informuje, že služba bude zakázána][sap-ha-guide-figure-3032]

   _**Obrázek 32:** DataKeeper vás informuje, že služba bude zakázána_

3. V dialogovém okně doporučujeme vybrat **účet domény nebo serveru**.

   ![Obrázek 33: Výběr uživatele pro SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**Obrázek 33:** Výběr uživatele pro SIOS DataKeeper_

4. Zadejte uživatelské jméno a heslo účtu domény, které jste vytvořili pro SIOS DataKeeper.

   ![Obrázek 34: Zadejte uživatelské jméno domény a heslo pro instalaci SIOS DataKeeper][sap-ha-guide-figure-3034]

   _**Obrázek 34:** Zadejte uživatelské jméno domény a heslo pro instalaci SIOS DataKeeper_

5. Nainstalujte licenční klíč pro instanci SIOS DataKeeper, jak je znázorněno na obrázku 35.

   ![Obrázek 35: Zadejte licenční klíč SIOS DataKeeper][sap-ha-guide-figure-3035]

   _**Obrázek 35:** Zadejte licenční klíč SIOS DataKeeper_

6. Po zobrazení výzvy restartujte virtuální počítač.

### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>Nastavit sios datakeeper

Po instalaci SIOS DataKeeper na obou uzlech spusťte konfiguraci. Cílem konfigurace je mít synchronní replikaci dat mezi další disky, které jsou připojeny ke každému z virtuálních počítačů.

1. Spusťte nástroj Správa a konfigurace datakeeperu a vyberte **příkaz Připojit server**.

   ![Obrázek 36: Nástroj pro správu a konfiguraci datových brankářů SIOS][sap-ha-guide-figure-3036]

   _**Obrázek 36:** Nástroj pro správu a konfiguraci datových brankářů SIOS_

2. Zadejte název nebo adresu TCP/IP prvního uzlu, ke kterému by se měl nástroj Pro správu a konfiguraci připojit, a v druhém kroku druhý uzel.

   ![Obrázek 37: Vložte název nebo adresu TCP/IP prvního uzlu, ke kterému by se měl nástroj Pro správu a konfiguraci připojit, a ve druhém kroku druhý uzel][sap-ha-guide-figure-3037]

   _**Obrázek 37:** Vložte název nebo adresu TCP/IP prvního uzlu, ke kterému by se měl nástroj Pro správu a konfiguraci připojit, a ve druhém kroku druhý uzel_

3. Vytvořte úlohu replikace mezi dvěma uzly.

   ![Obrázek 38: Vytvoření úlohy replikace][sap-ha-guide-figure-3038]

   _**Obrázek 38:** Vytvoření úlohy replikace_

   Průvodce vás provede procesem vytváření úlohy replikace.

4. Definujte název úlohy replikace.

   ![Obrázek 39: Definování názvu úlohy replikace][sap-ha-guide-figure-3039]

   _**Obrázek 39:** Definovat název úlohy replikace_

   ![Obrázek 40: Definování základních dat pro uzel, který by měl být aktuální zdrojový uzel][sap-ha-guide-figure-3040]

   _**Obrázek 40:** Definujte základní data pro uzel, který by měl být aktuální zdrojový uzel_

5. Definujte název, adresu TCP/IP a svazek disku cílového uzlu.

   ![Obrázek 41: Definování názvu, adresy TCP/IP a objemu disku aktuálního cílového uzlu][sap-ha-guide-figure-3041]

   _**Obrázek 41:** Definujte název, adresu TCP/IP a svazek disku aktuálního cílového uzlu_

6. Definujte kompresní algoritmy. V našem příkladu doporučujeme komprimovat datový proud replikace. Zejména v situacích resynchronizace komprese replikačního proudu výrazně snižuje dobu synchronizace. Komprese používá prostředky procesoru a paměti RAM virtuálního počítače. S tím, jak se zvyšuje rychlost komprese, se zvyšuje i objem používaných prostředků procesoru. Toto nastavení můžete upravit později.

7. Další nastavení, které je třeba zkontrolovat, je, zda replikace probíhá asynchronně nebo synchronně. Při ochraně konfigurací SAP ASCS/SCS je nutné použít synchronní replikaci.  

   ![Obrázek 42: Definování podrobností replikace][sap-ha-guide-figure-3042]

   _**Obrázek 42:** Definování podrobností replikace_

8. Definujte, zda má být svazek replikovaný úlohou replikace reprezentován ke konfiguraci clusteru s podporou převzetí služeb při selhání systému Windows Server jako sdílený disk. Pro konfiguraci SAP ASCS/SCS vyberte **Ano,** aby cluster Windows viděl replikovaný svazek jako sdílený disk, který může použít jako svazek clusteru.

   ![Obrázek 43: Výběrem možnosti Ano nastavte replikovaný svazek jako svazek clusteru.][sap-ha-guide-figure-3043]

   _**Obrázek 43:** Výběrem **možnosti Ano** nastavte replikovaný svazek jako svazek clusteru._

   Po vytvoření svazku nástroj Správa a konfigurace datakeeperu zobrazí, že úloha replikace je aktivní.

   ![Obrázek 44: Synchronní zrcadlení DataKeeper pro disk sdílené složky SAP ASCS/SCS je aktivní][sap-ha-guide-figure-3044]

   _**Obrázek 44:** Synchronní zrcadlení DataKeeper pro disk sdílení SAP ASCS/SCS je aktivní_

   Správce clusteru s podporou převzetí služeb při selhání nyní zobrazuje disk jako disk DataKeeper, jak je znázorněno na obrázku 45:

   ![Obrázek 45: Správce clusteru s podporou převzetí služeb při selhání zobrazuje disk, který datakeeper replikoval][sap-ha-guide-figure-3045]

   _**Obrázek 45:** Správce clusteru s podporou převzetí služeb při selhání zobrazuje disk, který datový modul replikoval._

## <a name="next-steps"></a>Další kroky

* [Instalace sap netweaveru HA pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdíleného disku pro instanci SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
