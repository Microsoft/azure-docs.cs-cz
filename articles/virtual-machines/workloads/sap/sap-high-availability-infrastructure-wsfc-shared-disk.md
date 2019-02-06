---
title: Příprava infrastruktury Azure pro SAP HA pomocí Windows cluster převzetí služeb při selhání a sdíleného disku pro SAP ASCS/SCS | Dokumentace Microsoftu
description: Zjistěte, jak připravit infrastrukturu Azure pro SAP HA pomocí Windows cluster převzetí služeb při selhání a sdíleného disku pro instanci SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 86de7fd5dcd9885d68204c4ef335e1b61a26574e
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747726"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Připravit infrastrukturu Azure pro SAP HA pomocí Windows cluster převzetí služeb při selhání a sdíleného disku pro SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfigurace vysoké dostupnosti SAP s několika SID)

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

Tento článek popisuje kroky, které můžete podniknout pro přípravu instalace a konfigurace vysoké dostupnosti systému SAP na převzetí služeb při selhání clusteru Windows s využitím infrastrukturu Azure *disk sdíleného clusteru* jako možnost pro clustering Instance SAP ASCS.

## <a name="prerequisites"></a>Požadavky

Než začnete s instalací, projděte si tento článek:

* [Průvodce architekturou: Instance SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdíleného disku clusteru][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Příprava infrastruktury pro architektury šablony 1
Šablony Azure Resource Manageru pro SAP zjednodušit nasazení požadované prostředky.

Třívrstvé šablony v Azure Resource Manageru také podporuje scénáře vysoké dostupnosti. Například architektury 1 šablony má dva clustery. Každý cluster se SAP jediný bod selhání pro SAP ASCS/SCS a DBMS.

Zde je, kde lze získat šablony Azure Resource Manageru ukázkový scénář, který popisujeme v tomto článku:

* [Image z Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Tržiště imagí Azure pomocí Azure Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Vlastní image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Vlastní image pomocí Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Příprava infrastruktury architektury šablona 1:

- Na webu Azure Portal v **parametry** podokno v **SYSTEMAVAILABILITY** vyberte **HA**.

  ![Obrázek 1: Nastavení parametrů Azure Resource Manageru vysoké dostupnosti SAP][sap-ha-guide-figure-3000]

_**Obrázek 1:** Nastavení parametrů Azure Resource Manageru vysoké dostupnosti SAP_


  Vytvoření šablony:

  * **Virtuální počítače**:
    * Virtuální počítače aplikačního serveru SAP: \<SAPSystemSID\>-di-\<Number\>
    * ASC/SCS clusteru virtuálních počítačů: \<SAPSystemSID\>-ascs-\<Number\>
    * Systém DBMS clusteru: \<SAPSystemSID\>-db-\<Number\>

  * **Síťové karty pro všechny virtuální počítače s přidružené IP adresy**:
    * \<SAPSystemSID\>-nic-di-\<Number\>
    * \<SAPSystemSID\>-nic-ascs-\<Number\>
    * \<SAPSystemSID\>-nic-db-\<Number\>

  * **Účty úložiště Azure (pouze nespravovaných disků)**:

  * **Skupiny dostupnosti** pro:
    * Virtuální počítače aplikačního serveru SAP: \<SAPSystemSID\>-avset-di
    * SAP ASCS/SCS clusteru virtuálních počítačů: \<SAPSystemSID\>-avset-ascs
    * Systém DBMS clusteru virtuálních počítačů: \<SAPSystemSID\>-avset-db

  * **Nástroje pro vyrovnávání zatížení Azure interní**:
    * Se všemi porty pro instanci ASCS/SCS IP adresa \<SAPSystemSID\>ascs - lb
    * Se všemi porty pro SQL Server DBMS a IP adresu \<SAPSystemSID\>- lb-db

  * **Skupina zabezpečení sítě**: \<SAPSystemSID\>-nsg-ascs-0  
    * S otevřít externí protokolu RDP (Remote Desktop) port \<SAPSystemSID\>0 - ascs virtuálního počítače

> [!NOTE]
> Dynamické ve výchozím nastavení jsou všechny IP adresy síťových karet a Azure interní služby load balancer. Je změňte na statické IP adresy. Zjistíte, jak to udělat později v tomto článku.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Nasazení virtuálních počítačů s připojením k podnikové síti (mezi různými místy) pro použití v produkčním prostředí
Pro produkční systémy SAP, nasaďte virtuální počítače Azure s [připojení k podnikové síti (mezi různými místy)] [ planning-guide-2.2] pomocí Azure VPN Gateway nebo Azure ExpressRoute.

> [!NOTE]
> Vaše instance Azure Virtual Network můžete použít. Virtuální síť a podsíť již byly vytvořeny a připraveny.
>
>

1.  Na webu Azure Portal v **parametry** podokno v **NEWOREXISTINGSUBNET** vyberte **existující**.
2.  V **SUBNETID** přidejte úplný řetězec ID podsítě připravené síť Azure, jež chcete nasadit virtuální počítače Azure.
3.  Pokud chcete získat seznam všech podsítí síť Azure, spusťte tento příkaz Powershellu:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
  ```

  **ID** pole zobrazí hodnotu pro ID podsítě.
4. Pokud chcete získat seznam všech hodnot ID podsítě, spusťte tento příkaz Powershellu:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
  ```

  ID podsítě vypadá takto:

  ```
  /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
  ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Výhradně cloudový instance SAP pro testování a Ukázka nasazení
Vysoká dostupnost systému SAP v čistě cloudové nasazení modelu můžete nasadit. Tento typ nasazení se především užitečné pro ukázky a testovací případy použití. Není vhodné pro případy použití v produkčním prostředí.

- Na webu Azure Portal v **parametry** podokno v **NEWOREXISTINGSUBNET** vyberte **nové**. Nechte **SUBNETID** prázdné pole.

  Šablony SAP Azure Resource Manageru automaticky vytvoří virtuální síť Azure a podsíť.

> [!NOTE]
> Také je nutné nasadit alespoň jeden vyhrazený virtuální počítač pro službu Active Directory a DNS ve stejné instanci Azure Virtual Network. Šablona nevytváří tyto virtuální počítače.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Příprava infrastruktury pro architektury šablony 2

Můžete použít tuto šablonu Azure Resource Manageru pro SAP, která pomůže zjednodušit nasazování prostředků infrastruktury požadované pro SAP architektury šablonu 2.

Zde je, kde lze získat šablony Azure Resource Manageru pro tento scénář nasazení:

* [Image z Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Tržiště imagí Azure s použitím spravovaných disků](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Vlastní image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Vlastní image pomocí Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Příprava infrastruktury pro architektury šablony 3

Můžete připravit infrastrukturu a konfigurace SAP s několika SID. Například můžete přidat další instanci SAP ASCS/SCS do *existující* konfigurace clusteru. Další informace najdete v tématu [nakonfigurovat další instanci SAP ASCS/SCS pro existující konfigurace clusteru pro vytvoření konfigurace několika identifikátorů SID SAP v Azure Resource Manageru][sap-ha-multi-sid-guide].

Pokud chcete vytvořit nový cluster s několika SID, můžete použít s několika SID [šablony quickstart na Githubu](https://github.com/Azure/azure-quickstart-templates).

Chcete-li vytvořit nový cluster s několika SID, je nutné nasadit následující tři šablony:

* [Šablona ASCS/SCS](#ASCS-SCS-template)
* [Databázové šablony](#database-template)
* [Šablona aplikace servery](#application-servers-template)

Následující části obsahují více podrobností o šablonách a parametry, které je potřeba zadat v šablonách.

### <a name="ASCS-SCS-template"></a> Šablona ASCS/SCS

Šablona ASCS/SCS nasadí dva virtuální počítače, které můžete použít k vytvoření clusteru převzetí služeb při selhání Windows serveru, který je hostitelem více instancí ASCS/SCS.

Nastavení v šabloně několika identifikátorů SID ASCS/SCS [šablony s několika SID ASCS/SCS] [ sap-templates-3-tier-multisid-xscs-marketplace-image] nebo [ASCS/SCS několika identifikátorů SID šablony s použitím spravovaných disků] [ sap-templates-3-tier-multisid-xscs-marketplace-image-md], zadejte hodnoty následujících parametrů:

  - **Předpona prostředků**:  Nastavte zdroj předpona, která se používá jako předpona všechny prostředky, které jsou vytvořeny během nasazení. Protože prostředky nepatří do jediného systému SAP, předpona prostředku není identifikátor SID jednoho systému SAP.  Předpona, která musí být mezi tři až šest znaků.
  - **Stack – typ**: Vyberte typ zásobníku systému SAP. V závislosti na typu zásobníku Azure Load Balancer má jeden (ABAP a Java pouze) nebo dvě (ABAP + Java) privátních IP adres na systému SAP.
  -  **Typ operačního systému**: Vyberte operační systém z virtuálních počítačů.
  -  **Počet systémů SAP**: Vyberte počet systémů SAP, které chcete nainstalovat v tomto clusteru.
  -  **Dostupnost systému**: Vyberte **HA**.
  -  **Uživatelské jméno Admin a heslo správce**: Vytvoření nového uživatele, který můžete použít k přihlášení k počítači.
  -  **Nové nebo existující podsíti**: Nastavte, zda chcete vytvořit novou virtuální síť a podsíť nebo použijte existující podsíť. Pokud již máte virtuální síť, která je připojená k vaší místní síti, vyberte **existující**.
  -  **Id podsítě**: Pokud chcete nasadit virtuální počítač do existující virtuální síť ve kterých máte definované podsíti virtuálního počítače by se měla přiřadit k pojmenování ID tuto konkrétní podsíť. ID obvykle vypadá takto:

   /subscriptions/\<id předplatného\>/resourceGroups/\<název skupiny prostředků\>/providers/Microsoft.Network/virtualNetworks/\<název virtuální sítě\>/subnets/ \<název podsítě\>

Šablona nasadí jednu instanci Azure Load Balancer, která podporuje několik systémů SAP vyhrazené:

- Instance ASC se konfigurují pro číslo instance 00, 10, 20...
- Instance SCS se konfigurují pro instanci číslo od 01, 11, 21...
- Instance ASCS zařadit do fronty replikace serveru (Lajících) (pouze Linux) se konfigurují pro číslo instance 02, 12, 22...
- Instance SCS Lajících (pouze Linux) se konfigurují pro číslo instance 03, 13, 23...

Nástroje pro vyrovnávání zatížení obsahuje 1 VIP(s) (2 pro Linux), 1 x virtuální IP adresu pro ASCS/SCS a 1 x virtuální IP adresu pro Lajících (pouze Linux).

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a> Porty SAP ASCS/SCS
Následující seznam obsahuje všechna pravidla (kde x je počet systému SAP, například 1, 2, 3...) pro vyrovnávání zatížení:
- Windows specifické porty protokolu pro každý systém SAP: 445, 5985
- Porty ASCS (číslo instance x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Porty SCS (číslo instance x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Porty ASCS Lajících v Linuxu (číslo instance x2): 33x2, 5x213, 5x214, 5x216
- Porty SCS Lajících v Linuxu (číslo instance x3): 33x3, 5x313, 5x314, 5x316

Nástroje pro vyrovnávání zatížení je nakonfigurován na použití následující porty sondy (kde x je počet systému SAP, například 1, 2, 3...):
- Portu sondy nástroje pro vyrovnávání zatížení interní ASCS/SCS: 620x0
- Zatížení Lajících interní nástroje pro vyrovnávání portu sondy (pouze Linux): 621x2

### <a name="database-template"></a> Databázové šablony

Databáze šablona nasadí jeden nebo dva virtuální počítače, které můžete použít k instalaci systému pro správu relačních databází (RDBMS) pro jeden systému SAP. Například pokud nasadíte šablonu ASCS/SCS pro pět systémů SAP, musíte nasadit tuto šablonu pětkrát.

V šabloně několika identifikátorů SID databázi nastavit [šablona databází s několika SID] [ sap-templates-3-tier-multisid-db-marketplace-image] nebo [databáze s několika SID šablony s použitím spravovaných disků] [ sap-templates-3-tier-multisid-db-marketplace-image-md], zadejte hodnoty následujících parametrů:

  -  **Id systému SAP**: Zadejte ID systému SAP systému SAP, který chcete nainstalovat. ID se používá jako předpona pro prostředky, které jsou nasazené.
  -  **Typ operačního systému**: Vyberte operační systém z virtuálních počítačů.
  -  **Hodnota DbType**: Vyberte typ databáze, kterou chcete nainstalovat na clusteru. Vyberte **SQL** Pokud chcete nainstalovat Microsoft SQL Server. Vyberte **HANA** Pokud budete chtít nainstalovat SAP HANA na virtuálních počítačích. Ujistěte se, že vyberete typ správný operační systém. Vyberte **Windows** pro SQL a vyberte Linuxovou distribuci pro HANA. Nástroj Azure Load Balancer, která je připojená k virtuálním počítačům je nakonfigurována pro podporu typ vybrané databáze:
    * **SQL**: Vyrovnávání zatížení nástroje pro vyrovnávání zatížení port 1433. Ujistěte se, že tento port použít pro nastavení technologie AlwaysOn systému SQL Server.
    * **HANA**: Načítání nástroje pro vyrovnávání – nástroj pro vyrovnávání zatížení porty 35015 a 35017. Ujistěte se, že instalace SAP HANA s instancí číslo **50**.
    Nástroje pro vyrovnávání zatížení používá port testu 62550.
  -  **Velikost systému SAP**: Nastavte počet protokoly SAP poskytuje nový systém. Pokud si nejste jisti kolik protokoly SAP bude systém vyžadovat, požádejte SAP technologické partnery nebo systémový integrátor.
  -  **Dostupnost systému**: Vyberte **HA**.
  -  **Uživatelské jméno Admin a heslo správce**: Vytvoření nového uživatele, který můžete použít k přihlášení k počítači.
  -  **Id podsítě**: Zadejte ID podsítě, který jste použili při nasazení ASCS/SCS šablony nebo ID podsítě, který byl vytvořen jako součást nasazení šablony ASCS/SCS.

### <a name="application-servers-template"></a> Šablona aplikace servery

Šablona servery aplikace nasadí dvě nebo více virtuálních počítačů, které může sloužit jako aplikační Server SAP instance systému SAP jeden. Například pokud nasadíte šablonu ASCS/SCS pro pět systémů SAP, musíte nasadit tuto šablonu pětkrát.

Nastavení v šabloně několika identifikátorů SID servery aplikace [šablony s několika SID servery aplikace] [ sap-templates-3-tier-multisid-apps-marketplace-image] nebo [šablona s několika SID servery aplikací pomocí Managed Disks] [ sap-templates-3-tier-multisid-apps-marketplace-image-md], zadejte hodnoty následujících parametrů:

  -  **Id systému SAP**: Zadejte ID systému SAP systému SAP, který chcete nainstalovat. ID se používá jako předpona pro prostředky, které jsou nasazené.
  -  **Typ operačního systému**: Vyberte operační systém z virtuálních počítačů.
  -  **Velikost systému SAP**: Počet protokoly SAP poskytuje nový systém. Pokud si nejste jisti kolik protokoly SAP bude systém vyžadovat, požádejte SAP technologické partnery nebo systémový integrátor.
  -  **Dostupnost systému**: Vyberte **HA**.
  -  **Uživatelské jméno Admin a heslo správce**: Vytvoření nového uživatele, který můžete použít k přihlášení k počítači.
  -  **Id podsítě**: Zadejte ID podsítě, který jste použili při nasazení ASCS/SCS šablony nebo ID podsítě, který byl vytvořen jako součást nasazení šablony ASCS/SCS.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Virtuální síť Azure
V našem příkladu je instance Azure Virtual Network adresní prostor 10.0.0.0/16. Existuje jedna podsíť nazývanou podsíť, s rozsahem adres 10.0.0.0/24. Všechny virtuální počítače a interní služby load balancer, jsou nasazené v této virtuální síti.

> [!IMPORTANT]
> Neprovádějte žádné změny k nastavení sítě v hostovaném operačním systému. To zahrnuje IP adresy serverů DNS a podsítě. Konfigurace nastavení sítě v Azure. Služba Dynamic Host Configuration Protocol (DHCP) rozšíří vaše nastavení.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS IP addresses

Chcete-li nastavit požadované DNS IP adresy, proveďte následující kroky:

1.  Na webu Azure Portal v **servery DNS** podokno, ujistěte se, že vaše virtuální síť **servery DNS** je možnost nastavená na **vlastního DNS**.
2.  Vyberte nastavení na základě typu sítě, které máte. Další informace najdete v následujících materiálech:
    * [Připojení k podnikové síti (mezi různými místy)][planning-guide-2.2]: Přidejte IP adresy na místních serverech DNS.  
    Můžete rozšířit místní servery DNS pro virtuální počítače, na kterých běží v Azure. V tomto scénáři můžete přidat IP adresy virtuálních počítačů Azure na které spouštíte službu DNS.
    * Pro nasazení virtuálních počítačů, které jsou izolovány v Azure: Nasaďte další virtuální počítač ve stejné virtuální síti instanci, která slouží jako DNS server. Přidání IP adresy virtuální počítače Azure, které jste nastavili až do spuštění služby DNS.

    ![Obrázek 2: Konfigurace serverů DNS pro Azure Virtual Network][sap-ha-guide-figure-3001]

    _**Obrázek 2:** Konfigurace serverů DNS pro Azure Virtual Network_

  > [!NOTE]
  > Pokud změníte IP adresy serverů DNS, budete muset restartovat virtuální počítače Azure na použití změny a šíření nových serverů DNS.
  >
  >

V našem příkladu služba DNS nainstalovaná a nakonfigurovaná na těchto virtuálních počítačích Windows:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Název síťové karty | Statická IP adresa |
| --- | --- | --- | --- |
| První server DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Druhý server DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Názvy hostitelů a statické IP adresy pro clusterovou instanci SAP ASCS/SCS a Clusterové instance DBMS

Pro místní nasazení je třeba tyto názvy vyhrazené hostitele a IP adresy:

| Virtuální hostitel název role | Název virtuálního hostitele | Virtuální statická IP adresa |
| --- | --- | --- |
| SAP ASCS/SCS první cluster virtuální název hostitele (Správa clusteru) |pr1-ascs-vir |10.0.0.42 |
| Název virtuálního hostitele instanci SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| K SAP DBMS druhý cluster virtuálního hostitele název (Správa clusteru) |pr1-dbms-vir |10.0.0.32 |

Při vytváření clusteru vytvořte virtuálního hostitele názvy pr1-ascs-vir a pr1. dbms vir a přidružené IP adresy, které spravují samotného clusteru. Informace o tom, jak to provést, najdete v tématu [shromažďovat uzly clusteru v konfiguraci clusteru][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Další dva názvy virtuálních hostitelů, pr1 ascs sap a pr1 databázového systému sap a přidružené IP adresy, můžete ručně vytvořit na serveru DNS. Tyto prostředky používat Clusterované instance SAP ASCS/SCS a Clusterové instance DBMS. Informace o tom, jak to provést, najdete v tématu [vytvořte název virtuálního hostitele pro clusterovou instanci SAP ASCS/SCS][sap-ha-guide-9.1.1].

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Nastavení statické IP adresy pro SAP virtual machines
Po nasazení virtuálních počítačů pro použití v clusteru, je nutné nastavit statických IP adres pro všechny virtuální počítače. To udělat v konfiguraci Azure Virtual Network a ne v hostovaném operačním systému.

1.  Na webu Azure Portal, vyberte **skupiny prostředků** > **síťová karta** > **nastavení** > **IP adresu**.
2.  V **IP adresy** podokně v části **přiřazení**vyberte **statické**. V **IP adresu** zadejte IP adresu, kterou chcete použít.

  > [!NOTE]
  > Pokud změníte IP adresu síťové karty, budete muset restartovat virtuální počítače Azure na použití změny.  
  >
  >

  ![Obrázek 3: Nastavení statické IP adresy pro síťové karty jednotlivých virtuálních počítačů][sap-ha-guide-figure-3002]

  _**Obrázek 3:** Nastavení statické IP adresy pro síťové karty jednotlivých virtuálních počítačů_

  Tento krok opakujte pro všechna síťová rozhraní, které se pro všechny virtuální počítače, včetně virtuálních počítačů, které chcete použít pro vaši službu Active Directory a DNS.

V našem příkladu máme tyto virtuální počítače a statické IP adresy:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Název síťové karty | Statická IP adresa |
| --- | --- | --- | --- |
| První instanci aplikačního serveru SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Druhou instanci aplikační Server SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Poslední instanci aplikačního serveru SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Prvním uzlu clusteru pro instanci ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Druhý uzel clusteru pro instanci ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Prvním uzlu clusteru pro instanci DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Druhý uzel clusteru pro instanci DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Nastavit statickou IP adresu nástroje pro vyrovnávání zatížení Azure interní

Vytvoří šablona SAP Azure Resource Manageru Azure interní nástroj pro vyrovnávání zatížení, který se používá pro SAP ASCS/SCS instanci clusteru a clusteru DBMS.

> [!IMPORTANT]
> IP adresa SAP ASCS/SCS název virtuálního hostitele je stejný jako IP adresu interního nástroje SAP ASCS/SCS: pr1-lb ASC.
> IP adresa virtuální název tohoto správce databáze je stejný jako IP adresu interního nástroje DBMS: pr1-lb-dbms.
>
>

Nastavení statické IP adresy Azure interního nástroje load balancer:

1.  Počáteční nasazení nastaví IP adresu interního nástroje pro vyrovnávání na **dynamické**. Na webu Azure Portal na **IP adresy** podokně v části **přiřazení**vyberte **statické**.
2.  Nastavit IP adresu interního nástroje **pr1-lb-ascs** IP adresu virtuálního hostitele název instance SAP ASCS/SCS.
3.  Nastavit IP adresu interního nástroje **pr1-lb-dbms** IP adresu virtuálního hostitele název instance databázového systému.

  ![Obrázek 4: Nastavení statické IP adresy pro interní služby load balancer pro instanci SAP ASCS/SCS][sap-ha-guide-figure-3003]

  _**Obrázek 4:** Nastavení statické IP adresy pro interní služby load balancer pro instanci SAP ASCS/SCS_

V našem příkladu máme dvě Azure interní služby load balancer, které mají tyto statické IP adresy:

| Role Vyrovnávání zatížení Azure interní | Název nástroje pro vyrovnávání zatížení Azure interní | Statická IP adresa |
| --- | --- | --- |
| SAP ASCS/SCS instance interního nástroje load balancer |pr1-lb-ascs |10.0.0.43 |
| K SAP DBMS interní nástroj pro vyrovnávání zatížení |pr1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Výchozí pravidla pro Azure interního nástroje pro vyrovnávání zatížení ASCS/SCS

SAP Azure Resource Manageru šablony vytvoří porty, které potřebujete:
* Instance ABAP ASCS pomocí výchozí instance číslo 00
* Instanci Java SCS pomocí výchozí instance číslo 01

Při instalaci instance SAP ASCS/SCS, musíte použít číslo výchozí instance 00 ABAP ASCS instance a číslo instance výchozí 01 pro vaši instanci Java SCS.

Dále vytvořte požadované interní koncové body pro SAP NetWeaver porty Vyrovnávání zatížení.

K vytvoření požadované interního vyrovnávání koncové body, nejdřív vytvořte tyto koncové body pro porty SAP NetWeaver ABAP ASCS pro vyrovnávání zatížení:

| Název pravidla Vyrovnávání zatížení/služby | Výchozí čísla portů | Konkrétní porty (ASCS instance s instancí číslo 00) (Lajících s 10) |
| --- | --- | --- |
| Server zařadit do fronty / *lbrule3200* |32\<InstanceNumber\> |3200 |
| Server ABAP zpráv / *lbrule3600* |36\<InstanceNumber\> |3600 |
| Interní zpráva ABAP / *lbrule3900* |39\<InstanceNumber\> |3900 |
| Server zpráv HTTP / *Lbrule8100* |81\<InstanceNumber\> |8100 |
| SAP spustit službu ASCS HTTP / *Lbrule50013* |5\<InstanceNumber\>13 |50013 |
| SAP spustit službu ASCS HTTPS / *Lbrule50014* |5\<InstanceNumber\>14 |50014 |
| Zařazení do fronty replikace / *Lbrule50016* |5\<InstanceNumber\>16 |50016 |
| SAP spustit službu HTTP Lajících *Lbrule51013* |5\<InstanceNumber\>13 |51013 |
| SAP spustit službu HTTP Lajících *Lbrule51014* |5\<InstanceNumber\>14 |51014 |
| Vzdálená správa Windows (WinRM) *Lbrule5985* | |5985 |
| Sdílené složky *Lbrule445* | |445 |

**Tabulka 1:** Čísla portů instancí SAP NetWeaver ABAP ASCS

Vytvořte tyto koncové body pro SAP NetWeaver Java SCS porty pro vyrovnávání zatížení:

| Název pravidla Vyrovnávání zatížení/služby | Výchozí čísla portů | Konkrétní porty (SCS instance s instancí číslo 01) (Lajících s 11) |
| --- | --- | --- |
| Server zařadit do fronty / *lbrule3201* |32\<InstanceNumber\> |3201 |
| Server brány / *lbrule3301* |33\<InstanceNumber\> |3301 |
| Server zpráv Java / *lbrule3900* |39\<InstanceNumber\> |3901 |
| Server zpráv HTTP / *Lbrule8101* |81\<InstanceNumber\> |8101 |
| SAP spustit službu SCS HTTP / *Lbrule50113* |5\<InstanceNumber\>13 |50113 |
| SAP spustit službu SCS HTTPS / *Lbrule50114* |5\<InstanceNumber\>14 |50114 |
| Zařazení do fronty replikace / *Lbrule50116* |5\<InstanceNumber\>16 |50116 |
| SAP spustit službu HTTP Lajících *Lbrule51113* |5\<InstanceNumber\>13 |51113 |
| SAP spustit službu HTTP Lajících *Lbrule51114* |5\<InstanceNumber\>14 |51114 |
| Služba WinRM *Lbrule5985* | |5985 |
| Sdílené složky *Lbrule445* | |445 |

**Tabulka 2:** Čísla portů instancí SAP NetWeaver Java SCS

![Obrázek 5: Výchozí pravidla pro Azure interního nástroje pro vyrovnávání zatížení ASCS/SCS][sap-ha-guide-figure-3004]

_**Obrázek 5:** Výchozí pravidla pro Azure interního nástroje pro vyrovnávání zatížení ASCS/SCS_

Nastavte IP adresu zatížení vyrovnávání pr1-lb-DBMS na IP adresu virtuálního hostitele název instance databázového systému.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Změna pravidel nástroje pro vyrovnávání zatížení Azure interní Vyrovnávání zatížení výchozí ASC/SCS

Pokud chcete použít různá čísla pro instance SAP ASCS nebo SCS, musíte změnit názvy a hodnoty jejich porty z výchozí hodnoty.

1.  Na webu Azure Portal, vyberte  **\<SID\>nástroj pro vyrovnávání zatížení -lb ascs** > **pravidla Vyrovnávání zatížení**.
2.  Pro všechny zátěže pravidla, která patří k instanci SAP ASCS nebo SCS změňte tyto hodnoty:

  * Název
  * Port
  * Port back-end

  Například pokud chcete změnit výchozí číslo instance ASCS od 00 do 31, budete muset provést změny pro všechny porty uvedené v tabulce 1.

  Tady je příklad příkazu update pro port *lbrule3200*.

  ![Obrázek 6: Změna pravidel nástroje pro vyrovnávání zatížení Azure interní Vyrovnávání zatížení výchozí ASC/SCS][sap-ha-guide-figure-3005]

  _**Obrázek 6:** Změna pravidel nástroje pro vyrovnávání zatížení Azure interní Vyrovnávání zatížení výchozí ASC/SCS_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Přidání Windows virtuálních počítačů k doméně

Po přiřazení statické IP adresy k virtuálním počítačům, přidejte virtuální počítače k doméně.

![Obrázek 7: Přidat virtuální počítač k doméně][sap-ha-guide-figure-3006]

_**Obrázek 7:** Přidat virtuální počítač k doméně_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Přidat položky registru v obou uzlů clusteru z instance SAP ASCS/SCS

Nástroj Azure Load Balancer má interního nástroje load balancer, že čas zavře připojení po určitou dobu nastavit jsou nečinné připojení (časový limit nečinnosti). SAP pracovní procesy v dialogovém okně otevřená připojení instancí SAP zařadit do fronty zpracovat jako první zařadit do fronty/dequeue požadavku musí být odeslán. Tato připojení zůstanou obvykle zavedené až do pracovního procesu nebo restartování procesu zařazení do fronty. Ale pokud je připojení nastavte dobu nečinnosti, nástroje pro vyrovnávání zatížení Azure interní zavře připojení. To není problém, protože pracovní proces SAP obnoví připojení k procesu zařazení do fronty, pokud už existuje. Tyto aktivity jsou popsány v trasování pro vývojáře SAP procesů, ale vytvářet velké množství další obsah v těchto trasování. Je vhodné změnit TCP/IP `KeepAliveTime` a `KeepAliveInterval` na oba uzly clusteru. Sloučit tyto změny v protokolu TCP/IP parametry s parametry profilu SAP, je popsáno dále v tomto článku.

Chcete-li přidat položky registru v obou uzlů clusteru z instance SAP ASCS/SCS, nejprve přidejte tyto položky registru Windows na oba uzly clusteru Windows pro SAP ASCS/SCS:

| Cesta | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Název proměnné |`KeepAliveTime` |
| Typ proměnné |REG_DWORD (decimální) |
| Hodnota |120000 |
| Odkaz na dokumentaci |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabulka 3:** Změnit první parametr protokolu TCP/IP

Pak přidejte tato položka registru Windows na oba uzly clusteru Windows pro SAP ASCS/SCS:

| Cesta | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Název proměnné |`KeepAliveInterval` |
| Typ proměnné |REG_DWORD (decimální) |
| Hodnota |120000 |
| Odkaz na dokumentaci |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Tabulka 4:** Změňte druhý parametr protokolu TCP/IP

Aby se změny projevily, restartujte obou uzlů clusteru.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Nastavení clusteru převzetí služeb při selhání Windows serveru pro instanci SAP ASCS/SCS

Nastavení clusteru převzetí služeb při selhání Windows serveru pro instanci SAP ASCS/SCS zahrnuje tyto úlohy:

- Shromážděte uzly clusteru v konfiguraci clusteru.
- Nakonfigurujte určující sdílenou složku clusteru.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Shromažďovat uzly clusteru v konfiguraci clusteru

1.  Přidat Role a funkce průvodce přidejte clusteringu obou uzlů clusteru převzetí služeb při selhání.
2.  Nastavení clusteru převzetí služeb při selhání pomocí Správce clusteru převzetí služeb při selhání. V modulu Správce clusteru převzetí služeb při selhání vyberte **vytvořit Cluster**a pak přidejte název první clusteru (uzly A). Bez přidání druhého uzlu ještě; Přidání druhého uzlu v pozdějším kroku.

  ![Obrázek 8: Přidat název serveru nebo na virtuálním počítači na prvním uzlu clusteru][sap-ha-guide-figure-3007]

  _**Obrázek 8:** Přidat název serveru nebo na virtuálním počítači na prvním uzlu clusteru_

3.  Zadejte síťový název (název hostitele virtuálního) clusteru.

  ![Obrázek 9: Zadejte název clusteru][sap-ha-guide-figure-3008]

  _**Obrázek 9:** Zadejte název clusteru_

4.  Po vytvoření clusteru, spusťte test ověření clusteru.

  ![Obrázek 10: Spusťte kontrolu ověření clusteru][sap-ha-guide-figure-3009]

  _**Obrázek 10:** Spusťte kontrolu ověření clusteru_

  Můžete ignorovat jakékoli upozornění o discích v tuto chvíli v procesu. Přidáte určující sdílené složky a SIOS sdílené disky později. V této fázi se nemusíte obávat o kvorum.

  ![Obrázek 11: Nenajde žádný disk kvora][sap-ha-guide-figure-3010]

  _**Obrázek 11:** Nenajde žádný disk kvora_

  ![Obrázek 12: Základní prostředky clusteru potřebuje novou IP adresu][sap-ha-guide-figure-3011]

  _**Obrázek 12:** Základní prostředky clusteru potřebuje novou IP adresu_

5.  Změna IP adresy clusteru služby jádra. Clusteru nelze spustit dokud nezměníte IP adresu clusteru služby jádra, protože IP adresa serveru odkazuje na jednom uzlu virtuálního počítače. To udělat na **vlastnosti** stránky Clusterová služba core IP prostředku.

  Potřebujeme například chcete přiřadit IP adresu (v našem příkladu 10.0.0.42) pro clusteru virtuální hostitel název pr1-ascs-vir.

  ![Obrázek 13: V dialogovém okně Vlastnosti změňte IP adresu][sap-ha-guide-figure-3012]

  _**Obrázek 13:** V **vlastnosti** dialogové okno pole, změňte IP adresu_

  ![Obrázek 14: Přiřaďte IP adresu, která je vyhrazena pro cluster][sap-ha-guide-figure-3013]

  _**Obrázek 14:** Přiřaďte IP adresu, která je vyhrazena pro cluster_

6.  Přeneste virtuální hostitel název clusteru online.

  ![Obrázek 15: Clusterová služba core je spuštěný, správnou IP adresou][sap-ha-guide-figure-3014]

  _**Obrázek 15:** Clusterová služba core je spuštěný, správnou IP adresou_

7.  Přidání druhého uzlu clusteru.

  Teď, když jádro Clusterová služba je spuštěná, můžete přidat druhý uzel clusteru.

  ![Přidat obrázek 16 druhém uzlu clusteru][sap-ha-guide-figure-3015]

  _**Obrázek 16:** Přidání druhého uzlu clusteru_

8.  Zadejte název druhého uzlu clusterů hostitelů.

  ![Obrázek 17: Zadejte název hostitele druhý uzel clusteru][sap-ha-guide-figure-3016]

  _**Obrázek 17:** Zadejte název hostitele druhý uzel clusteru_

  > [!IMPORTANT]
  > Ujistěte se, že **přidat do clusteru veškeré oprávněné úložiště** zaškrtávací políčko je *není* vybrané.  
  >
  >

  ![Obrázek 18: Nevybírejte zaškrtávací políčko][sap-ha-guide-figure-3017]

  _**Obrázek 18:** Proveďte *není* vyberte zaškrtávací políčko_

  Můžete ignorovat varování o kvora a disky. Můžete nastavit kvora a sdílet disk později, jak je popsáno v [nainstalovat SIOS DataKeeper Cluster Edition pro disk clusteru sdílená složka s SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

  ![Obrázek 19: Ignorovat upozornění na disku kvora][sap-ha-guide-figure-3018]

  _**Obrázek 19:** Ignorovat upozornění na disku kvora_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Konfigurovat určující sdílenou složku clusteru

Konfigurace určující sdílené složky clusteru zahrnuje tyto úlohy:

- Vytvoření sdílené složky.
- Nastavení kvora s kopií clusteru sdílené složky souboru v modulu Správce clusteru převzetí služeb při selhání.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Vytvoření sdílené složky

1.  Vyberte určující sdílená složka místo disk kvora. SIOS DataKeeper podporuje tuto možnost.

  V příkladech v tomto článku se určující sdílená složka na serveru služby Active Directory nebo DNS, na kterém běží v Azure. Určující sdílená složka se nazývá domcontr 0. Vzhledem k tomu, že by jste nakonfigurovali připojení VPN k Azure (přes Azure ExpressRoute nebo VPN Gateway), služby Active Directory a DNS je místní a není vhodná pro spuštění určující sdílená složka.

  > [!NOTE]
  > Pokud vaše služba Active Directory nebo DNS používá pouze v místním, nekonfigurujte služby Active Directory nebo Windows DNS operačního systému, na kterém běží v místním vaše určující sdílenou složku. Latence sítě mezi uzly clusteru, které jsou spuštěné v Azure Active Directory nebo DNS v místním může být příliš velký a způsobit problémy s připojením. Je potřeba nakonfigurovat určující sdílenou složku na virtuálním počítači Azure, na kterém běží blízko uzlu clusteru.  
  >
  >

  Disk kvora potřebuje aspoň 1 024 MB volného místa. Doporučujeme, abyste 2 048 MB volného místa pro disk kvora.

2.  Přidejte objekt názvu clusteru.

  ![Obrázek 20: Přiřazení oprávnění pro sdílenou složku pro objekt názvu clusteru][sap-ha-guide-figure-3019]

  _**Obrázek 20:** Přiřazení oprávnění pro sdílenou složku pro objekt názvu clusteru_

  Ujistěte se, že oprávnění zahrnují oprávnění ke změně dat ve sdílené složce pro objekt názvu clusteru (v našem příkladu pr1. ascs vir$).

3.  Chcete-li přidat objekt názvu clusteru do seznamu, **přidat**. Změňte filtr, aby se vyhledávat objekty počítačů, kromě těch uvedených v obrázek 22.

  ![Obrázek 21: Změnit typy objektů, které chcete zahrnout počítače][sap-ha-guide-figure-3020]

  _**Obrázek 21:** Změna **typy objektů** zahrnout počítače_

  ![Obrázek 22: Zaškrtněte políčko počítače][sap-ha-guide-figure-3021]

  _**Obrázek 22:** Vyberte **počítače** zaškrtávací políčko_

4.  Zadejte objekt názvu clusteru, jak ukazuje obrázek 21. Protože byl vytvořen záznam, můžete změnit oprávnění, jak je znázorněno na obrázku 20.

5.  Vyberte **zabezpečení** podrobnější kartu sdílené složky a pak nastavte oprávnění pro objekt názvu clusteru.

  ![Obrázek 23: Nastavit atributy zabezpečení pro objekt názvu clusteru na sdílenou složku kvora souboru][sap-ha-guide-figure-3022]

  _**Obrázek 23:** Nastavit atributy zabezpečení pro objekt názvu clusteru na sdílenou složku kvora souboru_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Nastavení kvora s kopií clusteru sdílené složky souboru v modulu Správce clusteru převzetí služeb při selhání

1.  Otevřít kvora nastavení Průvodce konfigurací.

  ![Obrázek 24: Spuštění Průvodce konfigurací kvora clusteru nastavení][sap-ha-guide-figure-3023]

  _**Obrázek 24:** Spuštění Průvodce konfigurací kvora clusteru nastavení_

2.  Na **vybrat možnosti konfigurace kvora** stránce **vybrat určující disk kvora**.

  ![Obrázek 25: Konfigurace kvora, které si můžete vybrat z][sap-ha-guide-figure-3024]

  _**Obrázek 25:** Konfigurace kvora, které si můžete vybrat z_

3.  Na **vybrat určující disk kvora** stránce **nakonfigurovat určující sdílenou složku souboru**.

  ![Obrázek 26: Vybrat určující sdílenou složku][sap-ha-guide-figure-3025]

  _**Obrázek 26:** Vybrat určující sdílenou složku_

4.  Zadejte cestu UNC ke sdílené složce (v našem příkladu \\domcontr 0\FSW). Pokud chcete zobrazit seznam změn, můžete provést, vyberte **Další**.

  ![Obrázek 27: Definovat umístění sdílené složky souboru pro sdílenou složku s kopií clusteru][sap-ha-guide-figure-3026]

  _**Obrázek 27:** Definovat umístění sdílené složky souboru pro sdílenou složku s kopií clusteru_

5.  Vyberte možnost změny a pak vyberte **Další**. Je třeba úspěšně znovu nakonfigurovat konfiguraci clusteru, jak ukazuje obrázek 28:  

  ![Obrázek 28: Potvrzení, že jste změnili konfiguraci clusteru][sap-ha-guide-figure-3027]

  _**Obrázek 28:** Potvrzení, že jste změnili konfiguraci clusteru_

Po úspěšné instalaci clusteru převzetí služeb při selhání Windows, budete muset změnit některé prahové hodnoty tak, aby se přizpůsobit převzetí služeb při selhání detekce podmínky v Azure. Změnit parametry jsou dokumentovány v článku [ladění prahové hodnoty sítě clusteru převzetí služeb při selhání][tuning-failover-cluster-network-thresholds]. Za předpokladu, že dva virtuální počítače, které tvoří konfiguraci clusteru Windows pro ASCS/SCS jsou ve stejné podsíti, změňte následující parametry na tyto hodnoty:

- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Tato nastavení se testují se zákazníky a nabízejí dobrý ohrožení zabezpečení. Jsou dostatečně odolné, ale také poskytují převzetí služeb při selhání, který je dostatečně rychle, aby se ve skutečné chybové podmínky softwaru SAP nebo v uzlu nebo selhání virtuálního počítače.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Instalace SIOS DataKeeper Cluster Edition pro sdílenou složku disk clusteru SAP ASCS/SCS

Nyní máte funkční konfigurace clusteringu převzetí služeb při selhání Windows serveru v Azure. Pokud chcete nainstalovat instanci SAP ASCS/SCS, musíte prostředek sdíleného disku. Nelze vytvořit sdílené síťové prostředky, které potřebujete v Azure. SIOS DataKeeper Cluster Edition je řešení třetích stran, které můžete použít k vytvoření sdílené síťové prostředky.

Instalace pro sdílenou složku disk clusteru SAP ASCS/SCS SIOS DataKeeper Cluster Edition zahrnuje tyto úlohy:

- Přidáte rozhraní Microsoft .NET Framework 3.5.
- Nainstalujte SIOS DataKeeper.
- SIOS DataKeeper nastavení.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Přidání rozhraní .NET Framework 3.5
Rozhraní .NET framework 3.5 není automaticky aktivuje nebo nainstalovaný v systému Windows Server 2012 R2. Protože SIOS DataKeeper vyžaduje .NET na všech uzlech, ve kterém nainstalujete DataKeeper, je nutné nainstalovat rozhraní .NET Framework 3.5 na hostovaný operační systém všech virtuálních počítačů v clusteru.

Existují dva způsoby, jak přidat rozhraní .NET Framework 3.5:

- Pomocí funkce Průvodce přidáním rolí a ve Windows, jak ukazuje obrázek 29:

  ![Obrázek 29: Instalace rozhraní .NET Framework 3.5 pomocí funkce Průvodce přidáním rolí a][sap-ha-guide-figure-3028]

  _**Obrázek 29:** Instalace rozhraní .NET Framework 3.5 pomocí funkce Průvodce přidáním rolí a_

  ![Obrázek 30: Průběh instalace panelu při instalaci rozhraní .NET Framework 3.5 pomocí funkce Průvodce přidáním rolí a][sap-ha-guide-figure-3029]

  _**Obrázek 30:** Průběh instalace panelu při instalaci rozhraní .NET Framework 3.5 pomocí funkce Průvodce přidáním rolí a_

- Pomocí nástroje dism.exe příkazového řádku. Pro tento typ instalace potřebujete přístup k adresáři SxS na instalačním médiu nástroje Windows. Na příkazovém řádku se zvýšenými oprávněními zadejte tento příkaz:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Nainstalujte SIOS DataKeeper

SIOS DataKeeper Cluster Edition nainstalujte na každý uzel v clusteru. Vytvořit virtuální sdílené úložiště se SIOS Datakeeperem, synchronizované zrcadlení a potom simulovat clusteru sdíleného úložiště.

Před instalací softwaru SIOS vytvořte DataKeeperSvc doména uživatel.

> [!NOTE]
> Přidejte uživatele domény DataKeeperSvc do skupiny Local Administrator na obou uzlů clusteru.
>
>

Chcete-li nainstalovat SIOS DataKeeper:

1.  Nainstalujte SIOS software na oba uzly clusteru.

  ![SIOS instalačního programu][sap-ha-guide-figure-3030]

  ![Obrázek 31: První stránka Instalace SIOS DataKeeper][sap-ha-guide-figure-3031]

  _**Obrázek 31:** První stránka Instalace SIOS DataKeeper_

2.  V dialogovém okně vyberte **Ano**.

  ![Obrázek 32: DataKeeper vás informuje, že služba se deaktivuje.][sap-ha-guide-figure-3032]

  _**Obrázek 32:** DataKeeper vás informuje, že služba se deaktivuje._

3.  V dialogovém okně, doporučujeme vám, že vyberete **účet domény nebo serveru**.

  ![Obrázek 33: Výběr uživatele pro SIOS DataKeeper][sap-ha-guide-figure-3033]

  _**Obrázek 33:** Výběr uživatele pro SIOS DataKeeper_

4.  Zadejte uživatelské jméno účtu domény a heslo, které jste vytvořili pro SIOS DataKeeper.

  ![Obrázek 34: Zadejte doménu uživatelské jméno a heslo pro instalaci SIOS DataKeeper][sap-ha-guide-figure-3034]

  _**Obrázek 34:** Zadejte doménu uživatelské jméno a heslo pro instalaci SIOS DataKeeper_

5.  Licenční klíč pro vaši instanci SIOS DataKeeper nainstalujte, jak ukazuje obrázek 35.

  ![Obrázek 35: Zadejte svůj SIOS DataKeeper licenční klíč][sap-ha-guide-figure-3035]

  _**Obrázek 35:** Zadejte svůj SIOS DataKeeper licenční klíč_

6.  Po zobrazení výzvy restartujte virtuální počítač.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Nastavit SIOS DataKeeper

Po instalaci SIOS DataKeeper na oba uzly spusťte konfiguraci. Cíl konfigurace je, aby synchronní replikaci dat mezi další disky, které jsou připojeny ke každému z virtuálních počítačů.

1.  Spusťte nástroj Správa DataKeeper a konfigurace a pak vyberte **připojení serveru**.

  ![Obrázek 36: SIOS DataKeeper Správa a konfigurace nástroje][sap-ha-guide-figure-3036]

  _**Obrázek 36:** SIOS DataKeeper Správa a konfigurace nástroje_

2.  Zadejte název nebo adresu protokolu TCP/IP na prvním uzlu, Správa a konfigurace nástroje by měla připojit na a druhý krok, druhý uzel.

  ![Obrázek 37: Vložit název nebo adresa protokolu TCP/IP na prvním uzlu pro správu a konfigurační nástroj má připojit na a druhý krok, druhý uzel][sap-ha-guide-figure-3037]

  _**Obrázek 37:** Vložit název nebo adresa protokolu TCP/IP na prvním uzlu pro správu a konfigurační nástroj má připojit na a druhý krok, druhý uzel_

3.  Vytvoření úlohy replikace mezi dvěma uzly.

  ![Obrázek 38: Vytvoření úlohy replikace][sap-ha-guide-figure-3038]

  _**Obrázek 38:** Vytvoření úlohy replikace_

  Průvodce vás provede procesem vytvoření úlohy replikace.

4.  Definujte název úlohy replikace.

  ![Obrázek 39: Definujte název úlohy replikace][sap-ha-guide-figure-3039]

  _**Obrázek 39:** Definujte název úlohy replikace_

  ![Obrázek 40: Definuje základní data pro uzel, který by měl být aktuální zdrojový uzel][sap-ha-guide-figure-3040]

  _**Obrázek 40:** Definuje základní data pro uzel, který by měl být aktuální zdrojový uzel_

5.  Zadejte název, adresa TCP/IP a diskový svazek cílový uzel.

  ![Obrázek 41: Zadejte název, adresa TCP/IP a diskový svazek aktuální cílový uzel][sap-ha-guide-figure-3041]

  _**Obrázek 41:** Zadejte název, adresa TCP/IP a diskový svazek aktuální cílový uzel_

6.  Definujte algoritmy komprese. V našem příkladu doporučujeme kompresi streamu replikace. Zejména v situacích, opětovné synchronizace komprese streamu replikace výrazně snižuje čas synchronizace. Komprese používá prostředky procesoru a paměť RAM virtuálního počítače. Jak se zvyšuje rychlost komprese zločinců se stejně objem prostředků procesoru, které se používají. Můžete upravit tato nastavení později.

7.  Další nastavení, které je potřeba zkontrolovat je, zda dojde k replikaci synchronně nebo asynchronně. Když chráníte konfigurace SAP ASCS/SCS, je nutné použít synchronní replikace.  

  ![Obrázek 42: Zadejte podrobnosti replikace][sap-ha-guide-figure-3042]

  _**Obrázek 42:** Zadejte podrobnosti replikace_

8.  Definujte, jestli by měly být zastoupeny svazek, který se replikuje pomocí replikace úlohy ke konfiguraci clusteru převzetí služeb při selhání Windows serveru jako sdíleného disku. Konfigurace SAP ASCS/SCS, vyberte **Ano** tak, aby Windows cluster považuje replikovaného svazku sdíleného disku, který můžete použít jako svazek clusteru.

  ![Obrázek 43: Vyberte Ano. Pokud chcete nastavit replikovaného svazku jako svazek clusteru][sap-ha-guide-figure-3043]

  _**Obrázek 43:** Vyberte **Ano** nastavení replikovaného svazku jako svazek clusteru_

  Po vytvoření svazku DataKeeper Správa a konfigurace nástroje ukazuje, že úloha replikace je aktivní.

  ![Obrázek 44: Synchronní zrcadlení DataKeeper SAP ASCS/SCS sdílené složky disku je aktivní][sap-ha-guide-figure-3044]

  _**Obrázek 44:** Synchronní zrcadlení DataKeeper SAP ASCS/SCS sdílené složky disku je aktivní_

  Správce clusteru převzetí služeb při selhání disku jako disku DataKeeper teď zobrazuje, jak ukazuje obrázek 45:

  ![Obrázek 45: Správce clusteru převzetí služeb při selhání se zobrazí na disku, který DataKeeper replikovaná][sap-ha-guide-figure-3045]

  _**Obrázek 45:** Správce clusteru převzetí služeb při selhání se zobrazí na disku, který DataKeeper replikovaná_

## <a name="next-steps"></a>Další postup

* [Instalace SAP NetWeaver HA pomocí Windows cluster převzetí služeb při selhání a sdíleného disku pro instanci SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
