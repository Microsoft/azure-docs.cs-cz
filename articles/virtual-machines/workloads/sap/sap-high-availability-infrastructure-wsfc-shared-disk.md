---
title: Příprava infrastruktury Azure pro SAP HA použití clusteru převzetí služeb při selhání se systémem Windows a sdíleného disku pro SAP ASC nebo SCS | Microsoft Docs
description: Zjistěte, jak připravit infrastrukturu Azure pro SAP HA pomocí clusteru převzetí služeb při selhání se systémem Windows a sdíleného disku pro SAP ASC nebo SCS instance.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
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
ms.openlocfilehash: 25d3d01e12132165cc9e12032ba0f6e7a2f15070
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Příprava infrastruktury Azure pro SAP HA pomocí clusteru převzetí služeb při selhání se systémem Windows a sdíleného disku pro SAP ASC nebo SCS

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
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfigurace s vysokou dostupností více SID SAP)

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

Tento článek popisuje kroky, je třeba provést pro instalaci a konfiguraci systému SAP vysoké dostupnosti na clusteru s podporou převzetí služeb při selhání systému Windows pomocí připravit infrastrukturu Azure *sdíleného disku clusteru* jako možnost clusteringu s podporou Instance SAP ASC.

## <a name="prerequisites"></a>Požadavky

Před zahájením instalace, projděte si v tomto článku:

* [Průvodce architekturou: clusteru instance SAP ASC nebo SCS pomocí sdíleného disku clusteru v clusteru s podporou převzetí služeb při selhání systému Windows][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Příprava infrastruktury pro architektury šablony 1
Šablony Azure Resource Manageru pro SAP zjednodušit nasazení požadované prostředky.

Třívrstvá šablony ve službě Správce prostředků Azure také podporuje scénáře vysoké dostupnosti. Například architektury 1 šablony má dva clustery. Každý cluster je SAP jediný bod selhání pro SAP ASC nebo SCS a databázového systému.

Zde je, kde můžete získat šablon Azure Resource Manageru ukázkový scénář, který jsme popisují v tomto článku:

* [Obrázek pro Azure Marketplace.](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Obrázek pro Azure Marketplace pomocí Azure spravované disky](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Vlastní image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Vlastní image pomocí spravovaných disků](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Příprava infrastruktury na architektury šablona 1:

- Na portálu Azure v **parametry** podokně, v **SYSTEMAVAILABILITY** vyberte **HA**.

  ![Obrázek 1: Nastavení parametrů Azure Resource Manager SAP vysokou dostupnost][sap-ha-guide-figure-3000]

_**Obrázek 1:** nastavit parametry SAP vysokou dostupnost Azure Resource Manager_


  Vytvoření šablony:

  * **Virtuální počítače**:
    * Virtuální počítače aplikačního serveru SAP: \<SAPSystemSID\>- di -\<číslo\>
    * ASC nebo SCS clusteru virtuálních počítačů: \<SAPSystemSID\>- Asc-\<číslo\>
    * Cluster databázového systému: \<SAPSystemSID\>- db-\<číslo\>

  * **Síťové karty pro všechny virtuální počítače s přidružené IP adresy**:
    * \<SAPSystemSID\>- nic-di -\<číslo\>
    * \<SAPSystemSID\>- nic-Asc -\<číslo\>
    * \<SAPSystemSID\>- nic-db -\<číslo\>

  * **Účty služby Azure storage (pouze nespravovaná disky)**:

  * **Skupiny dostupnosti** pro:
    * Virtuální počítače aplikačního serveru SAP: \<SAPSystemSID\>- avset di
    * SAP ASC nebo SCS clusteru virtuálních počítačů: \<SAPSystemSID\>- avset ASC
    * Virtuální počítače clusteru databázového systému: \<SAPSystemSID\>- avset-db

  * **Nástroje pro vyrovnávání zatížení Azure interní**:
    * S všechny porty pro instanci ASC nebo SCS a IP adresu \<SAPSystemSID\>- lb ASC
    * S všechny porty pro SQL Server databázového systému a IP adresu \<SAPSystemSID\>- lb-db

  * **Skupina zabezpečení sítě**: \<SAPSystemSID\>- nsg-ASC-0  
    * S k externí protokol RDP (Remote Desktop) portu k \<SAPSystemSID\>0 - ASC virtuálního počítače

> [!NOTE]
> Všechny IP adresy síťové karty a nástroje pro vyrovnávání zatížení Azure interní jsou dynamické ve výchozím nastavení. Je změňte na statické IP adresy. Jsme popisují, jak to udělat později v článku.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Nasazení virtuálních počítačů s připojením k podnikové síti (mezi různými místy) používat v produkčním prostředí
Pro produkční systémy SAP, nasaďte virtuální počítače Azure s [připojení k podnikové síti (mezi různými místy)] [ planning-guide-2.2] pomocí Azure VPN Gateway nebo Azure ExpressRoute.

> [!NOTE]
> Můžete použít instanci Azure Virtual Network. Virtuální síť a podsíť již byly vytvořeny a připravený.
>
>

1.  Na portálu Azure v **parametry** podokně, v **NEWOREXISTINGSUBNET** vyberte **existující**.
2.  V **SUBNETID** přidejte úplný řetězec ID podsítě vaší připravené síť Azure, kam je plánujete nasadit virtuální počítače Azure.
3.  Chcete-li získat seznam všech podsítí síť Azure, spusťte tento příkaz prostředí PowerShell:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
  ```

  **ID** poli se zobrazí hodnota pro ID podsítě.
4. Chcete-li získat seznam všech hodnot ID podsítě, spusťte tento příkaz prostředí PowerShell:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
  ```

  ID podsítě vypadá takto:

  ```
  /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
  ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Nasadit jenom pro cloud instance SAP pro testovací a demo
Můžete nasadit systém SAP vysoké dostupnosti v modelu nasazení jenom cloudu. Tento typ nasazení je primárně užitečné pro ukázku a testovací případy použití. Ji není vhodná pro produkční případy použití.

- Na portálu Azure v **parametry** podokně, v **NEWOREXISTINGSUBNET** vyberte **nové**. Ponechte **SUBNETID** pole prázdná.

  Šablona SAP Azure Resource Manager automaticky vytvoří virtuální síť Azure a podsíť.

> [!NOTE]
> Musíte také nasadit alespoň jeden vyhrazený virtuální počítač pro službu Active Directory a DNS ve stejné instanci Azure Virtual Network. Šablona nepodporuje vytvoření těchto virtuálních počítačů.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Příprava infrastruktury pro architektury šablony 2

Tato šablona Azure Resource Manageru pro SAP můžete zjednodušit nasazení infrastruktury požadované prostředky pro SAP architektury šablony 2.

Zde je, kde můžete získat šablon Azure Resource Manageru pro tento scénář nasazení:

* [Obrázek pro Azure Marketplace.](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Obrázek pro Azure Marketplace pomocí spravovaných disků](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Vlastní image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Vlastní image pomocí spravovaných disků](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Příprava infrastruktury pro architektury 3 šablony

Můžete Příprava infrastruktury a nakonfigurovat SAP pro více identifikátor zabezpečení SID. Například můžete přidat další instance SAP ASC nebo SCS do *existující* konfigurace clusteru. Další informace najdete v tématu [nakonfigurovat další instance SAP ASC nebo SCS pro existující konfigurace clusteru pro vytvoření konfigurace aplikace SAP více SID ve službě Správce prostředků Azure][sap-ha-multi-sid-guide].

Pokud chcete vytvořit nový cluster více SID, můžete použít více SID [šablony rychlý start na Githubu](https://github.com/Azure/azure-quickstart-templates).

K vytvoření nového clusteru více SID, musíte nasadit následující tři šablony:

* [ASC nebo SCS šablony](#ASCS-SCS-template)
* [Šablona databáze](#database-template)
* [Šablona servery aplikací](#application-servers-template)

Následující sekce obsahují další informace o šablonách a parametry, které je třeba zadat v šablonách.

### <a name="ASCS-SCS-template"></a> ASC nebo SCS šablony

Šablona ASC nebo SCS nasadí dva virtuální počítače, které můžete použít k vytvoření clusteru převzetí služeb při selhání systému Windows Server, který je hostitelem více instancí ASC nebo SCS.

Nastavení v šabloně ASC nebo SCS více SID [ASC nebo SCS více SID šablony] [ sap-templates-3-tier-multisid-xscs-marketplace-image] nebo [ASC nebo SCS více SID šablony s použitím spravovaných disků] [ sap-templates-3-tier-multisid-xscs-marketplace-image-md], zadejte hodnoty následujících parametrů:

  - **Předpona prostředků**: Nastavte předponu prostředku, který se používá k předpony všechny prostředky, které jsou vytvořené během nasazení. Protože prostředky nepatří do systému jenom jeden SAP, není SID systému SAP jeden předponu prostředku.  Předpona, která musí být mezi tři až šest znaků.
  - **Stack – typ**: Vyberte typ zásobníku systému SAP. V závislosti na typu zásobníku nástroj pro vyrovnávání zatížení Azure má jeden (ABAP nebo Java pouze) nebo dvě (ABAP + Java) privátní IP adresy na systému SAP.
  -  **Typ operačního systému**: Vyberte verzi operačního systému z virtuálních počítačů.
  -  **Počet systému SAP**: Vyberte počet SAP systémy, které chcete nainstalovat v tomto clusteru.
  -  **Dostupnost systému**: vyberte **HA**.
  -  **Uživatelské jméno správce a heslo správce**: Vytvořte nového uživatele, který lze použít k přihlášení k počítači.
  -  **Nový nebo existující podsíť**: nastavte, zda chcete vytvořit novou virtuální síť a podsíť nebo použijte existující podsítí. Pokud již máte virtuální síť, která je připojen k síti na pracovišti, vyberte **existující**.
  -  **Id podsítě**: nastavit ID podsítě, ke kterému má být připojen virtuální počítače. Vyberte podsíť virtuální sítě VPN nebo ExpressRoute připojit virtuální počítač k síti na pracovišti. ID obvykle vypadá takto:

   /subscriptions/\<id předplatného\>/resourceGroups/\<název skupiny prostředků\>/providers/Microsoft.Network/virtualNetworks/\<název virtuální sítě\>/subnets/ \<název podsítě.\>

Šablona nasadí jednu instanci nástroj pro vyrovnávání zatížení Azure, která podporuje více systémů SAP:

- Instance ASC jsou nakonfigurované pro instance číslo 00, 10, 20...
- Instance SCS jsou nakonfigurované pro instance číslo 01, 11, 21...
- Instance ASC zařazování replikace serveru (YBRAT) (pouze Linux) jsou nakonfigurované pro čísla instance 02, 12, 22...
- Instance SCS YBRAT (pouze Linux) jsou nakonfigurované pro instance číslo 03, 13, 23...

Nástroje pro vyrovnávání zatížení obsahuje 1 VIP(s) (2 pro Linux), 1 x virtuální IP adresa pro ASC nebo SCS a 1 x virtuální IP adresa pro YBRAT (pouze Linux).

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a> SAP ASC nebo SCS porty
Následující seznam obsahuje všechny pravidla (kde x je číslo systému SAP, například 1, 2, 3...) pro vyrovnávání zatížení:
- Porty specifické pro systém Windows pro každý systém SAP: 445, 5985
- Porty ASC (čísla instance x0): 32 × 0, 36 x 0, 39 x 0, 81 x 0, 5 x 013, 5 x 014, 5 x 016
- Porty SCS (čísla instance x1): 32 x 1, 33 x 1, 39 x 1, 81 x 1, 5 x 113, 5 x 114, 5 x 116
- ASC YBRAT portů v systému Linux (čísla instance x2): 33 x 2, 5 x 213, 5 x 214, 5 x 216
- SCS YBRAT portů v systému Linux (čísla instance x3): 33 x 3, 5 x 313, 5 x 314, 5 x 316

Nástroje pro vyrovnávání zatížení je nakonfigurovaný na použití následující porty testu (kde x je číslo systému SAP, například 1, 2, 3...):
- Port testu nástroje pro vyrovnávání zatížení ASC nebo SCS interní: 620 x 0
- Interní YBRAT načíst port testu vyrovnávání (pouze Linux): 621 x 2

### <a name="database-template"></a> Šablona databáze

Šablona databáze nasadí jednu nebo dvě virtuální počítače, které můžete použít k instalaci systému správy relačních databází (RDBMS) pro systém SAP. Například pokud nasadíte šablonu ASC nebo SCS pro pět systémy SAP, budete muset nasadit pětkrát této šablony.

Nastavení v šabloně více SID databáze [databáze více SID šablony] [ sap-templates-3-tier-multisid-db-marketplace-image] nebo [databáze více SID šablony s použitím spravovaných disky] [ sap-templates-3-tier-multisid-db-marketplace-image-md], zadejte hodnoty následujících parametrů:

  -  **Id systému SAP**: Zadejte ID systému SAP systému SAP, který chcete nainstalovat. Identifikátor se používá jako předpona pro prostředky, které jsou nasazeny.
  -  **Typ operačního systému**: Vyberte verzi operačního systému z virtuálních počítačů.
  -  **Hodnota DbType**: Vyberte typ databáze, kterou chcete nainstalovat na clusteru. Vyberte **SQL** Pokud chcete nainstalovat Microsoft SQL Server. Vyberte **HANA** Pokud budete chtít na virtuální počítače nainstalovat SAP HANA. Ujistěte se, že jste vybrali správný operační systém typu. Vyberte **Windows** pro SQL a vyberte distribuční Linux pro HANA. Azure pro vyrovnávání zatížení, která je připojena k virtuálním počítačům je nakonfigurován pro podporu typ vybrané databáze:
    * **SQL**: port Vyrovnávání zatížení nástroje pro vyrovnávání zatížení 1433. Ujistěte se, že tento port použít pro vaše instalace technologie AlwaysOn systému SQL Server.
    * **HANA**: 35015 a 35017 porty Vyrovnávání zatížení nástroje pro vyrovnávání zatížení. Nainstalujte SAP HANA číslem instance **50**.
    Služba Vyrovnávání zatížení používá port testu 62550.
  -  **Velikost systému SAP**: nastavte počet protokoly SAP poskytuje nový systém. Pokud si nejste jisti kolik protokoly SAP vyžaduje systém, obraťte se na partnera technologie SAP nebo systémový integrátor.
  -  **Dostupnost systému**: vyberte **HA**.
  -  **Uživatelské jméno správce a heslo správce**: Vytvořte nového uživatele, který lze použít k přihlášení k počítači.
  -  **Id podsítě**: Zadejte ID podsítě, která jste použili při nasazení ASC nebo SCS šablony nebo ID podsítě, která byla vytvořena jako součást nasazení ASC nebo SCS šablony.

### <a name="application-servers-template"></a> Šablona servery aplikací

Šablony servery aplikace nasadí dvě nebo více virtuálních počítačů, které lze použít jako instance aplikačního serveru SAP jeden systému SAP. Například pokud nasadíte šablonu ASC nebo SCS pro pět systémy SAP, budete muset nasadit pětkrát této šablony.

Nastavení v šabloně SID více serverů aplikace [šablony SID více serverů aplikace] [ sap-templates-3-tier-multisid-apps-marketplace-image] nebo [šablony SID více serverů aplikace pomocí spravované disky] [ sap-templates-3-tier-multisid-apps-marketplace-image-md], zadejte hodnoty následujících parametrů:

  -  **Id systému SAP**: Zadejte ID systému SAP systému SAP, který chcete nainstalovat. Identifikátor se používá jako předpona pro prostředky, které jsou nasazeny.
  -  **Typ operačního systému**: Vyberte verzi operačního systému z virtuálních počítačů.
  -  **Velikost systému SAP**: počet protokoly SAP poskytuje nový systém. Pokud si nejste jisti kolik protokoly SAP vyžaduje systém, obraťte se na partnera technologie SAP nebo systémový integrátor.
  -  **Dostupnost systému**: vyberte **HA**.
  -  **Uživatelské jméno správce a heslo správce**: Vytvořte nového uživatele, který lze použít k přihlášení k počítači.
  -  **Id podsítě**: Zadejte ID podsítě, která jste použili při nasazení ASC nebo SCS šablony nebo ID podsítě, která byla vytvořena jako součást nasazení ASC nebo SCS šablony.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Virtuální síť Azure
V našem příkladu adresní prostor virtuální sítě Azure instance je 10.0.0.0/16. Existuje jedna podsíť s názvem podsíti s rozsahem adres 10.0.0.0/24. Všechny virtuální počítače a interní služby load balancer nasazených v této virtuální síti.

> [!IMPORTANT]
> Nemáte žádné změny nastavení sítě v hostovaném operačním systému. To zahrnuje IP adresy, servery DNS a podsítě. Nakonfigurujte všechna nastavení sítě v Azure. Službu Dynamic Host Configuration Protocol (DHCP) rozšíří nastavení.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS IP adresy

Pokud chcete nastavit požadované DNS IP adresy, proveďte následující kroky:

1.  Na portálu Azure v **servery DNS** podokně, ujistěte se, že virtuální sítě **servery DNS** je možnost nastavena na **vlastní DNS**.
2.  Vyberte nastavení na základě typu sítě, které máte. Další informace najdete v následujících materiálech:
    * [Připojení k podnikové síti (mezi různými místy)][planning-guide-2.2]: Přidejte IP adresy na místní servery DNS.  
    Můžete rozšířit na místní servery DNS pro virtuální počítače, které jsou spuštěné v Azure. V tomto scénáři můžete přidat IP adresy Azure virtuální počítače, na které spouštíte služby DNS.
    * [Čistě cloudové nasazení][planning-guide-2.1]: nasazení se další virtuální počítač ve stejné instanci virtuální sítě, která slouží jako DNS server. Přidejte IP adresy virtuální počítače Azure, které jste nastavili než spouštění služby DNS.

    ![Obrázek 2: Konfigurace serverů DNS pro virtuální síť Azure][sap-ha-guide-figure-3001]

    _**Obrázek 2:** konfigurace DNS serverů pro virtuální síť Azure_

  > [!NOTE]
  > Pokud změníte IP adresy serverů DNS, musíte restartovat virtuální počítače Azure k použití změny a šířit nové servery DNS.
  >
  >

V našem příkladu služba DNS nainstalovaná a nakonfigurovaná na těchto virtuálních počítačích Windows:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Název síťové karty | Statická IP adresa |
| --- | --- | --- | --- |
| První server DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Druhý server DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Názvy hostitelů a statické IP adresy pro clusterové instance SAP ASC nebo SCS a Clusterové instance databázového systému

Pro místní nasazení je třeba tyto názvy vyhrazené hostitele a IP adresy:

| Název role virtuálních hostitelů | Název virtuálního hostitele | Virtuální statickou IP adresu |
| --- | --- | --- |
| SAP ASC nebo SCS první clusteru virtuální hostitel název (pro správu clusteru) |PR1. ASC vir |10.0.0.42 |
| Název virtuálního hostitele instance SAP ASC nebo SCS |PR1. ASC sap |10.0.0.43 |
| Databázového systému SAP druhý cluster virtuálního hostitele název (Správa clusteru) |pr1-dbms-vir |10.0.0.32 |

Při vytváření clusteru, vytvořte virtuálního hostitele názvy pr1-ASC vir a pr1. databázového systému vir a přidružené IP adresy, které spravovat samotného clusteru. Informace o tom, jak to udělat najdete v tématu [shromažďovat uzly clusteru v konfiguraci clusteru][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Další dva názvy virtuálních hostitelů, pr1. ASC sap a pr1 databázového systému sap a přidružené IP adresy, můžete ručně vytvořit na serveru DNS. SAP ASC nebo SCS skupinu prostředků clusteru a skupinu prostředků clusteru databázového systému používají tyto prostředky. Informace o tom, jak to udělat najdete v tématu [vytvořte název virtuálního hostitele pro clusterovou instanci SAP ASC nebo SCS][sap-ha-guide-9.1.1].

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Nastavení statické IP adresy pro virtuální počítače SAP
Poté, co nasadíte virtuální počítače pro použití v clusteru, musíte nastavit statické IP adresy pro všechny virtuální počítače. To udělat v konfiguraci virtuální sítě Azure a není v hostovaném operačním systému.

1.  Na portálu Azure vyberte **skupiny prostředků** > **síťové karty** > **nastavení** > **IP adresu**.
2.  V **IP adresy** podokně v části **přiřazení**, vyberte **statické**. V **IP adresu** zadejte IP adresu, která chcete použít.

  > [!NOTE]
  > Pokud změníte IP adresa síťové karty, budete muset restartovat virtuální počítače Azure na použití změny.  
  >
  >

  ![Obrázek 3: Nastavení statické IP adresy pro síťové karty každého virtuálního počítače][sap-ha-guide-figure-3002]

  _**Obrázek 3:** nastavení statické IP adresy pro síťové karty každého virtuálního počítače_

  Tento krok opakujte pro všechna síťová rozhraní, které se pro všechny virtuální počítače, včetně virtuálních počítačů, které chcete použít pro vaši službu Active Directory nebo DNS.

V našem příkladu máme tyto virtuální počítače a statické IP adresy:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Název síťové karty | Statická IP adresa |
| --- | --- | --- | --- |
| První instanci aplikačního serveru SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Druhá instance aplikačního serveru SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Poslední instance serveru SAP aplikace |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Prvním uzlu clusteru pro instanci ASC nebo SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Druhý uzel clusteru pro instanci ASC nebo SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Prvním uzlu clusteru pro instanci databázového systému |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Druhý uzel clusteru pro instanci databázového systému |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Nastavit statickou IP adresu pro nástroj pro vyrovnávání zatížení Azure interní

Šablona SAP Azure Resource Manager vytvoří pro vyrovnávání zatížení Azure interní, který se používá pro SAP ASC nebo SCS instance clusteru a clusteru databázového systému.

> [!IMPORTANT]
> IP adresa název virtuálního hostitele SAP ASC nebo SCS je stejný jako IP adresu služby Vyrovnávání zatížení interní SAP ASC nebo SCS: pr1-lb ASC.
> IP adresa virtuální název tohoto databázového systému je stejné jako IP adresa služby Vyrovnávání zatížení interní databázového systému: pr1-lb-databázového systému.
>
>

Chcete-li nastavit statickou IP adresu pro nástroj pro vyrovnávání zatížení Azure interní:

1.  Počáteční nasazení nastaví IP adresa služby Vyrovnávání zatížení pro vnitřní **dynamické**. Na portálu Azure na **IP adresy** podokně v části **přiřazení**, vyberte **statické**.
2.  Nastavení IP adresy služby Vyrovnávání zatížení pro vnitřní **pr1-lb Asc** na název virtuálního hostitele instance SAP ASC nebo SCS adresu IP.
3.  Nastavení IP adresy služby Vyrovnávání zatížení pro vnitřní **databázového systému pr1 lb** na IP adresu název virtuálního hostitele instance databázového systému.

  ![Obrázek 4: Nastavení statické IP adresy pro vyrovnávání zatížení pro vnitřní pro instance SAP ASC nebo SCS][sap-ha-guide-figure-3003]

  _**Obrázek 4:** nastavení statické IP adresy pro vyrovnávání zatížení pro vnitřní pro instance SAP ASC nebo SCS_

V našem příkladu máme dvě Azure interní služby load balancer, které mají tyto statické IP adresy:

| Role služby Vyrovnávání zatížení Azure interní | Název nástroje pro vyrovnávání zatížení Azure interní | Statická IP adresa |
| --- | --- | --- |
| SAP ASC nebo SCS instanci interní zátěže. |PR1-lb ASC |10.0.0.43 |
| Databázového systému SAP interní nástroj pro vyrovnávání zatížení |pr1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Výchozí pravidla pro vyrovnávání zatížení Azure interní Vyrovnávání zatížení ASC nebo SCS

Šablona SAP Azure Resource Manager vytvoří porty, které potřebujete:
* Instance ABAP ASC pomocí výchozí instance číslo 00
* Instance Java SCS, s výchozí instance číslo 01

Při instalaci instance SAP ASC nebo SCS, je nutné použít výchozí instanci číslo 00 pro vaše ABAP ASC instance a číslo instance výchozí 01 instance Java SCS.

Dále vytvořte požadované interní koncové body pro SAP NetWeaver porty pro vyrovnávání zatížení.

Pokud chcete vytvořit požadované interní služby load vyrovnávání koncové body, nejdřív vytvořte tyto koncové body pro SAP NetWeaver ABAP ASC porty pro vyrovnávání zatížení:

| Název pravidla vyrovnávání služby nebo zatížení | Výchozí čísla portů | Konkrétní porty (ASC instance číslem instance 00) (YBRAT s 10) |
| --- | --- | --- |
| Zařadit server / *lbrule3200* |32\<InstanceNumber\> |3200 |
| Server zpráv ABAP / *lbrule3600* |36\<InstanceNumber\> |3600 |
| Interní ABAP zpráva nebo *lbrule3900* |39\<InstanceNumber\> |3900 |
| Server zpráv HTTP / *Lbrule8100* |81\<InstanceNumber\> |8100 |
| SAP spustit službu ASC HTTP / *Lbrule50013* |5\<InstanceNumber\>13 |50013 |
| SAP spustit službu ASC HTTPS nebo *Lbrule50014* |5\<InstanceNumber\>14 |50014 |
| Zařazování replikace nebo *Lbrule50016* |5\<InstanceNumber\>16 |50016 |
| SAP spustit službu HTTP YBRAT *Lbrule51013* |5\<InstanceNumber\>13 |51013 |
| SAP spustit službu HTTP YBRAT *Lbrule51014* |5\<InstanceNumber\>14 |51014 |
| Vzdálená správa systému Windows (WinRM) *Lbrule5985* | |5985 |
| Sdílené složky *Lbrule445* | |445 |

**Tabulka 1:** portu čísla instance SAP NetWeaver ABAP ASC

Pak vytvořte tyto koncové body pro SAP NetWeaver Java SCS porty pro vyrovnávání zatížení:

| Název pravidla vyrovnávání služby nebo zatížení | Výchozí čísla portů | Konkrétní porty (SCS instance číslem instance 01) (YBRAT s 11) |
| --- | --- | --- |
| Zařadit server / *lbrule3201* |32\<InstanceNumber\> |3201 |
| Server brány nebo *lbrule3301* |33\<InstanceNumber\> |3301 |
| Server zpráv Java / *lbrule3900* |39\<InstanceNumber\> |3901 |
| Server zpráv HTTP / *Lbrule8101* |81\<InstanceNumber\> |8101 |
| SAP spustit službu SCS HTTP / *Lbrule50113* |5\<InstanceNumber\>13 |50113 |
| SAP spustit službu SCS HTTPS nebo *Lbrule50114* |5\<InstanceNumber\>14 |50114 |
| Zařazování replikace nebo *Lbrule50116* |5\<InstanceNumber\>16 |50116 |
| SAP spustit službu HTTP YBRAT *Lbrule51113* |5\<InstanceNumber\>13 |51113 |
| SAP spustit službu HTTP YBRAT *Lbrule51114* |5\<InstanceNumber\>14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| Sdílené složky *Lbrule445* | |445 |

**Tabulka 2:** portu čísla instance SAP NetWeaver Java SCS

![Obrázek 5: Pravidla pro vyrovnávání zatížení Azure interní Vyrovnávání zatížení výchozí ASC nebo SCS][sap-ha-guide-figure-3004]

_**Obrázek 5:** ASC nebo SCS výchozí pravidla pro vyrovnávání zatížení Azure interní Vyrovnávání zatížení_

Nastavte IP adresu zatížení vyrovnávání pr1-lb-databázového systému na IP adresu název virtuálního hostitele instance databázového systému.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Změňte pravidla pro vyrovnávání zatížení Azure interní Vyrovnávání zatížení výchozí ASC nebo SCS

Pokud chcete používat jiný čísla pro SAP ASC nebo SCS instancí, je třeba změnit názvy a hodnoty jejich porty z výchozí hodnoty.

1.  Na portálu Azure vyberte  **\<SID\>nástroj pro vyrovnávání zatížení -lb Asc** > **pravidla Vyrovnávání zatížení**.
2.  Pro všechna pravidla, které náleží do instance SAP ASC nebo SCS Vyrovnávání zatížení změňte tyto hodnoty:

  * Název
  * Port
  * Back-end port

  Například pokud chcete změnit výchozí číslo instance ASC od 00 do 31, budete muset provést změny pro všechny porty uvedené v tabulce 1.

  Tady je příklad aktualizace pro port *lbrule3200*.

  ![Obrázek 6: Změňte pravidla pro vyrovnávání zatížení Azure interní Vyrovnávání zatížení výchozí ASC nebo SCS][sap-ha-guide-figure-3005]

  _**Obrázek 6:** změňte pravidla pro vyrovnávání zatížení Azure interní Vyrovnávání zatížení výchozí ASC nebo SCS_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Přidejte virtuální počítače s Windows do domény

Když přiřadíte statickou IP adresu pro virtuální počítače, přidejte virtuální počítače k doméně.

![Obrázek 7: Přidejte virtuální počítač k doméně][sap-ha-guide-figure-3006]

_**Obrázek 7:** přidat virtuální počítač k doméně_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Přidat položky registru na obou uzlů clusteru instance SAP ASC nebo SCS

Azure Vyrovnávání zatížení má interní nástroj, zavře připojení při připojení jsou nastavte dobu nečinnosti, čas (časový limit nečinnosti). SAP pracovních procesů v dialogovém okně Otevřít připojení instance SAP zařadit do fronty zpracovat při první zařazování/dequeue požadavku musí být odeslána. Tato připojení obvykle zůstat zavedené do pracovní proces nebo proces zařazování restartuje. Ale pokud se jedná o připojení na nastavenou dobu nečinnosti, nástroje pro vyrovnávání zatížení Azure interní zavře připojení. To není problém, protože pracovní proces SAP připojení do procesu zařazování obnoví, pokud už existuje. Tyto aktivity jsou popsané v trasování vývojáře procesů SAP, ale uživatel vytvořit velké množství další obsah v těchto trasování. Je vhodné změnit TCP/IP `KeepAliveTime` a `KeepAliveInterval` na obou uzlů clusteru. Kombinací těchto změn v parametrech TCP/IP s parametry profil SAP, popsanou dále v článku.

Chcete-li přidat položky registru na obou uzlů clusteru instance SAP ASC nebo SCS, nejprve přidejte tyto položky registru systému Windows na obou uzlů clusteru systému Windows pro SAP ASC nebo SCS:

| Cesta | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Název proměnné |`KeepAliveTime` |
| Typ proměnné |REG_DWORD (decimální): |
| Hodnota |120000 |
| Odkaz na dokumentaci |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabulka 3:** změnit první parametr TCP/IP

Pak přidejte této položky registru systému Windows na obou uzlů clusteru systému Windows pro SAP ASC nebo SCS:

| Cesta | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Název proměnné |`KeepAliveInterval` |
| Typ proměnné |REG_DWORD (decimální): |
| Hodnota |120000 |
| Odkaz na dokumentaci |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Tabulka 4:** změnit druhý parametr protokolu TCP/IP

Aby se změny projevily, restartujte obou uzlů clusteru.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Nastavení clusteru s podporou převzetí služeb při selhání Windows serveru pro instanci SAP ASC nebo SCS

Nastavení clusteru s podporou převzetí služeb při selhání Windows serveru pro instanci SAP ASC nebo SCS zahrnuje tyto úlohy:

- Shromažďujte uzly clusteru v konfiguraci clusteru.
- Nakonfigurujte určující sdílenou složku clusteru.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Shromažďovat uzly clusteru v konfiguraci clusteru

1.  Přidat Role a funkce průvodce přidejte clusteringu obou uzlů clusteru převzetí služeb při selhání.
2.  Nastavení clusteru převzetí služeb při selhání pomocí Správce clusteru převzetí služeb při selhání. Ve Správci clusteru převzetí služeb při selhání vyberte **vytvořením clusteru**a poté přidejte pouze název první clusteru (uzel A). Nepřidávejte druhého uzlu ještě; Přidání druhého uzlu do pozdějšího kroku.

  ![Obrázek 8: Přidejte název serveru nebo virtuálního počítače na prvním uzlu clusteru][sap-ha-guide-figure-3007]

  _**Obrázek 8:** přidejte název serveru nebo virtuálního počítače na prvním uzlu clusteru_

3.  Zadejte název sítě (název hostitele virtuálního) clusteru.

  ![Obrázek 9: Zadejte název clusteru][sap-ha-guide-figure-3008]

  _**Obrázek 9:** zadejte název clusteru_

4.  Po vytvoření clusteru, spusťte test ověření clusteru.

  ![Obrázek 10: Spustit kontrolu ověření clusteru][sap-ha-guide-figure-3009]

  _**Obrázek 10:** spustit kontrolu ověření clusteru_

  Můžete ignorovat jakékoli upozornění o discích v tomto okamžiku v procesu. Přidáte, že určující sdílené složce a SIOS sdílené disky později. V této fázi nemusíte obávat o kvora.

  ![Obrázek 11: Nenajde žádný disk kvora][sap-ha-guide-figure-3010]

  _**Obrázek 11:** nenajde žádný disk kvora_

  ![Obrázek 12: Prostředku clusteru jádra potřebuje novou IP adresu][sap-ha-guide-figure-3011]

  _**Obrázek 12:** prostředku clusteru jádra potřebuje novou IP adresu_

5.  Změna IP adresy jádra Clusterové služby. Clusteru nelze spustit dokud změnit IP adresu clusteru služby jádra, protože IP adresa serveru odkazuje na jednom z uzlů virtuálního počítače. To udělat na **vlastnosti** stránky prostředek IP základní služby clusteru.

  Například je potřeba přiřadit IP adresu (v našem příkladu 10.0.0.42) pro v clusteru virtuální hostitel název pr1-ASC vir.

  ![Obrázek 13: V dialogovém okně vlastností změňte IP adresu][sap-ha-guide-figure-3012]

  _**Obrázek 13:** v **vlastnosti** dialogové okno pole, změna IP adresy_

  ![Obrázek 14: Přiřadíte IP adresu, která je vyhrazena pro cluster][sap-ha-guide-figure-3013]

  _**Obrázek 14:** přiřadit IP adresu, která je vyhrazena pro cluster_

6.  Uveďte název virtuálního hostitele clusteru online.

  ![Obrázek 15: Základní služby clusteru je spuštěná, se správnou adresou IP][sap-ha-guide-figure-3014]

  _**Obrázek 15:** základní služby clusteru je spuštěná, se správnou adresou IP_

7.  Přidání druhého uzlu clusteru.

  Nyní když základní Clusterová služba spuštěná, můžete přidat druhý uzel clusteru.

  ![Obrázek 16 přidat druhý uzel clusteru][sap-ha-guide-figure-3015]

  _**Obrázek 16:** přidání druhého uzlu clusteru_

8.  Zadejte název pro druhý hostitele uzlu clusteru.

  ![Obrázek 17: Zadejte název hostitele druhého uzlu clusteru][sap-ha-guide-figure-3016]

  _**Obrázek 17:** zadejte název hostitele druhého uzlu clusteru_

  > [!IMPORTANT]
  > Ujistěte se, který **přidat do clusteru veškeré oprávněné úložiště** zaškrtávací políčko je *není* vybrané.  
  >
  >

  ![Obrázek 18: Nevybírejte zaškrtávací políčko][sap-ha-guide-figure-3017]

  _**Obrázek 18:** provést *není* vyberte zaškrtávací pole_

  Můžete ignorovat upozornění o kvora a disky. Můžete nastavit kvora a sdílet disk později, jak je popsáno v [nainstalovat s DataKeeper Cluster Edition pro počítače s diskem sdílené složky clusteru SAP ASC nebo SCS][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

  ![Obrázek 19: Ignorujte upozornění o disku kvora][sap-ha-guide-figure-3018]

  _**Obrázek 19:** ignorovat upozornění o disku kvora_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Konfigurovat určující sdílenou složku clusteru

Konfigurace určující sdílenou složku clusteru zahrnuje tyto úlohy:

- Vytvoření sdílené složky.
- Nastavení kvora určující sdílené složky souborů ve Správci clusteru převzetí služeb při selhání.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Vytvoření sdílené složky

1.  Vyberte složku s kopií místo disk kvora. Tuto volbu podporuje DataKeeper s.

  V příkladech v tomto článku je určující sdílenou složku na serveru služby Active Directory nebo DNS, který běží v Azure. Sdílenou složku se nazývá domcontr-0. Vzhledem k tomu, že by jste nakonfigurovali připojení VPN do Azure (prostřednictvím brány sítě VPN nebo Azure ExpressRoute), služby Active Directory nebo DNS je na místě a není vhodné pro spuštění určující sdílenou složku.

  > [!NOTE]
  > Pokud služby Active Directory nebo DNS používá jenom v místě, není konfigurace vaší určující sdílenou složku na služby Active Directory nebo DNS Windows operačního systému, který běží na místě. Latence sítě mezi uzly clusteru, které jsou spuštěné v Azure a služby Active Directory nebo DNS v místě může být příliš velký a způsobit problémy s připojením. Nezapomeňte nakonfigurovat určující sdílenou složku na virtuální počítač Azure se blíží uzlu clusteru se systémem.  
  >
  >

  Jednotka kvora vyžaduje alespoň 1 024 MB volného místa. Doporučujeme, abyste hodnotu 2 048 MB volného místa pro disk kvora.

2.  Přidejte objekt názvu clusteru.

  ![Obrázek 20: Přiřadíte oprávnění pro sdílenou složku pro objekt názvu clusteru][sap-ha-guide-figure-3019]

  _**Obrázek 20:** přiřadit oprávnění pro sdílenou složku pro objekt názvu clusteru_

  Ujistěte se, že oprávnění zahrnout oprávnění pro změnu data ve sdílené složce pro objekt názvu clusteru (v našem příkladu pr1. ASC vir$).

3.  Chcete-li přidat objekt názvu clusteru do seznamu, vyberte **přidat**. Změna filtru k vyhledání počítačových objektů, kromě těch, které znázorňuje obrázek 22.

  ![Obrázek 21: Změnit typy objektů pro zahrnutí počítačů][sap-ha-guide-figure-3020]

  _**Obrázek 21:** změnu **typy objektů** pro zahrnutí počítačů_

  ![Obrázek 22: Zaškrtněte políčko počítače][sap-ha-guide-figure-3021]

  _**Obrázek 22:** vyberte **počítače** zaškrtávací políčko_

4.  Objekt názvu clusteru zadejte, jak ukazuje obrázek 21. Protože byl vytvořen záznam, můžete změnit oprávnění, jak je znázorněno na obrázku 20.

5.  Vyberte **zabezpečení** podrobnější kartě do sdílené složky a potom nastavte oprávnění pro objekt názvu clusteru.

  ![Obrázek 23: Nastavte atributy zabezpečení pro objekt názvu clusteru na soubor sdílenou složku kvora][sap-ha-guide-figure-3022]

  _**Obrázek 23:** nastavit atributy zabezpečení pro objekt názvu clusteru v souboru sdílenou složku kvora_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Nastavení kvora určující sdílené složky souborů ve Správci clusteru převzetí služeb při selhání

1.  Otevřete kvora nastavení Průvodce konfigurací.

  ![Obrázek 24: Spuštění Průvodce konfigurace kvora clusteru nastavení][sap-ha-guide-figure-3023]

  _**Obrázek 24:** spustit Průvodce konfigurace kvora clusteru nastavení_

2.  Na **vybrat možnosti konfigurace kvora** vyberte **vybrat určující disk kvora**.

  ![Obrázek 25: Konfigurací kvora, které můžete vybrat z][sap-ha-guide-figure-3024]

  _**Obrázek 25:** konfigurací kvora, můžete si vybrat z_

3.  Na **vybrat určující disk kvora** vyberte **nakonfigurovat určující sdílenou složku**.

  ![Obrázek 26: Vyberte sdílenou složku][sap-ha-guide-figure-3025]

  _**Obrázek 26:** vyberte sdílenou složku_

4.  Zadejte cestu UNC ke sdílené složce (v našem příkladu \\domcontr 0\FSW). Pokud chcete zobrazit seznam změn, můžete provést, vyberte **Další**.

  ![Obrázek 27: Zadejte umístění pro sdílení souborů pro sdílenou složku s kopií clusteru][sap-ha-guide-figure-3026]

  _**Obrázek 27:** zadejte umístění pro sdílení souborů pro sdílenou složku s kopií clusteru_

5.  Vyberte požadované změny a pak vyberte **Další**. Je třeba úspěšně znovu nakonfigurovat konfiguraci clusteru, jak ukazuje obrázek 28:  

  ![Obrázek 28: Potvrzení, že jste jste změnili konfiguraci clusteru][sap-ha-guide-figure-3027]

  _**Obrázek 28:** potvrzení, že jste jste změnili konfiguraci clusteru_

Po úspěšné instalaci clusteru převzetí služeb při selhání systému Windows, budete muset změnit některé prahové hodnoty tak, aby se přizpůsobit převzetí služeb při selhání detekce podmínky v Azure. Parametry, které chcete změnit jsou dokumentovány v článku [ladění prahové hodnoty sítě clusteru převzetí služeb při selhání][tuning-failover-cluster-network-thresholds]. Za předpokladu, že dva virtuální počítače, které tvoří konfiguraci clusteru systému Windows pro ASC nebo SCS jsou ve stejné podsíti, změňte následující parametry tyto hodnoty:

- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Tato nastavení byly testovány s zákazníků a nabízejí dobrý ohrožení zabezpečení. Jsou dostatečně odolné, ale také obsahují převzetí služeb při selhání, který je dostatečně rychlé ve skutečné chybové stavy v softwaru SAP nebo v uzlu nebo selhání virtuálního počítače.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Instalace s DataKeeper Cluster Edition pro SAP ASC nebo SCS disk clusteru sdílené složky

Teď máte fungující konfiguraci clustering převzetí služeb při selhání systému Windows Server v Azure. K instalaci instance SAP ASC nebo SCS, budete potřebovat prostředek sdíleného disku. Nelze vytvořit sdílené síťové prostředky, které potřebujete v Azure. S DataKeeper Cluster Edition je řešení třetí strany, které můžete použít k vytvoření sdílené síťové prostředky.

Instalace s DataKeeper Cluster Edition pro sdílenou složku disk clusteru SAP ASC nebo SCS zahrnuje tyto úlohy:

- Přidání rozhraní Microsoft .NET Framework 3.5.
- Nainstalujte SIOS DataKeeper.
- Nastavte s DataKeeper.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Přidání rozhraní .NET Framework 3.5
Rozhraní .NET framework 3.5 se automaticky aktivovat nebo není nainstalovaná v systému Windows Server 2012 R2. Protože DataKeeper s vyžaduje rozhraní .NET na všech uzlech, kde instalujete DataKeeper, musíte nainstalovat rozhraní .NET Framework 3.5 v hostovaném operačním systému všech virtuálních počítačů v clusteru.

Chcete-li přidat rozhraní .NET Framework 3.5 dvěma způsoby:

- Použití funkce Průvodce přidáním rolí a v systému Windows, jak ukazuje obrázek 29:

  ![Obrázek 29: Instalace rozhraní .NET Framework 3.5 pomocí Přidat role a funkce Průvodce][sap-ha-guide-figure-3028]

  _**Obrázek 29:** instalace rozhraní .NET Framework 3.5 pomocí Přidat role a funkce Průvodce_

  ![Obrázek 30: Průběh instalace panelu při instalaci rozhraní .NET Framework 3.5 pomocí Přidat role a funkce Průvodce][sap-ha-guide-figure-3029]

  _**Obrázek 30:** průběh instalace panelu při instalaci rozhraní .NET Framework 3.5 pomocí Přidat role a funkce Průvodce_

- Pomocí nástroje dism.exe příkazového řádku. Pro tento typ instalace musíte pro přístup k adresáři SxS na instalačním médiu systému Windows. Na příkazovém řádku se zvýšenými oprávněními zadejte tento příkaz:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Nainstalujte SIOS DataKeeper

Nainstalujte na každém uzlu v clusteru s DataKeeper Cluster Edition. Pokud chcete vytvořit virtuální sdílené úložiště s s DataKeeper, vytvořte synchronizoval zrcadlení a pak simulovat sdílené úložiště clusteru.

Před instalací softwaru SIOS vytvořte DataKeeperSvc uživatele domény.

> [!NOTE]
> Přidejte uživatele domény DataKeeperSvc do skupiny místního správce na obou uzlů clusteru.
>
>

Instalace s DataKeeper:

1.  Nainstalujte SIOS software do obou uzlů clusteru.

  ![Instalační program SIOS][sap-ha-guide-figure-3030]

  ![31 obrázek: První stránka instalace s DataKeeper][sap-ha-guide-figure-3031]

  _**Obrázek 31:** první stránka s DataKeeper instalace_

2.  V dialogovém okně vyberte **Ano**.

  ![Obrázek 32: DataKeeper vás upozorní, že služba bude zakázán][sap-ha-guide-figure-3032]

  _**Obrázek 32:** DataKeeper informuje, že služba bude zakázán_

3.  V dialogovém okně doporučujeme, abyste vybrali **účet domény nebo serveru**.

  ![Obrázek 33: Výběr uživatele s DataKeeper][sap-ha-guide-figure-3033]

  _**Obrázek 33:** výběr uživatele s DataKeeper_

4.  Zadejte uživatelské jméno pro účet domény a heslo, které jste vytvořili pro DataKeeper s.

  ![Obrázek 34: Zadejte uživatelské jméno domény a heslo pro instalace s DataKeeper][sap-ha-guide-figure-3034]

  _**Obrázek 34:** zadejte uživatelské jméno domény a heslo pro instalaci s DataKeeper_

5.  Licenční klíč pro instanci s DataKeeper, nainstalujte, jak ukazuje obrázek 35.

  ![Obrázek 35: Zadejte klíč DataKeeper s licencí][sap-ha-guide-figure-3035]

  _**Obrázek 35:** zadejte klíč DataKeeper s licencí_

6.  Po zobrazení výzvy restartujte virtuální počítač.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Nastavení pro zařízení s DataKeeper

Po instalaci s DataKeeper oba uzly, spusťte konfigurace. Cílem konfigurace je tak, aby měl synchronní data replikace mezi další disky, které jsou připojené ke každému z virtuálních počítačů.

1.  Spusťte nástroj Správa DataKeeper a konfigurace a potom vyberte **připojit Server**.

  ![36 obrázek: Nástroj Konfigurace a SIOS DataKeeper správy][sap-ha-guide-figure-3036]

  _**Obrázek 36:** s DataKeeper správu a konfiguraci nástroje_

2.  Zadejte název nebo adresu TCP/IP prvního uzlu, který nástroj Správa a konfigurace by se měly připojit k a v druhém kroku, ve druhém uzlu.

  ![Obrázek 37: Vložit název nebo adresu TCP/IP prvního uzlu pro správu a konfigurační nástroj by měl připojit a v druhém kroku, ve druhém uzlu][sap-ha-guide-figure-3037]

  _**Obrázek 37:** vložit název nebo adresu TCP/IP prvního uzlu nástroj pro správu a konfiguraci by měl připojit a v druhém kroku, ve druhém uzlu_

3.  Vytvoření úlohy replikace mezi dvěma uzly.

  ![Obrázek 38: Vytvoření úlohy replikace][sap-ha-guide-figure-3038]

  _**Obrázek 38:** vytvořit úlohu replikace_

  Průvodce vás provede procesem vytvoření úlohy replikace.

4.  Definujte název úlohy replikace.

  ![Obrázek 39: Definujte název úlohy replikace][sap-ha-guide-figure-3039]

  _**Obrázek 39:** definujte název úlohy replikace_

  ![Obrázek 40: Zadejte základní data pro uzel, které by měly být aktuální zdrojový uzel][sap-ha-guide-figure-3040]

  _**Obrázek 40:** zadat základní data pro uzel, které by měly být aktuální zdrojový uzel_

5.  Zadejte název, adresa TCP/IP a svazku cílový uzel.

  ![Obrázek 41: Zadejte název, adresa TCP/IP a svazku aktuální cílový uzel][sap-ha-guide-figure-3041]

  _**Obrázek 41:** zadat název, adresa TCP/IP a svazku aktuální cílový uzel_

6.  Definujte algoritmy komprese. V našem příkladu doporučujeme kompresi datového proudu replikace. Hlavně v situacích, opakované synchronizace kompresi datového proudu replikace výrazně snižuje dobu Opakovaná synchronizace. Komprese používá prostředky procesoru a paměť RAM virtuálního počítače. Jak zvyšuje rychlost komprese, takže nemá objem prostředků procesoru, které se používají. Můžete upravit toto nastavení později.

7.  Další nastavení, které je potřeba zkontrolovat se, zda dojde k replikaci synchronně nebo asynchronně. Když chráníte SAP ASC nebo SCS konfigurace, je nutné použít synchronní replikace.  

  ![Obrázek 42: Definování podrobnosti k replikaci][sap-ha-guide-figure-3042]

  _**Obrázek 42:** definovat podrobnosti k replikaci_

8.  Zadejte, zda svazek, který se replikují úlohou replikace by měly být zastoupeny ke konfiguraci clusteru převzetí služeb při selhání systému Windows Server jako sdílený disk. Pro konfiguraci SAP ASC nebo SCS vyberte **Ano** tak, aby Windows cluster uvidí replikované svazek jako sdílený disk, který můžete použít jako svazek clusteru.

  ![Obrázek 43: Vyberte možnost Ano nastavit replikované svazek jako svazek clusteru][sap-ha-guide-figure-3043]

  _**Obrázek 43:** vyberte **Ano** nastavit replikované svazek jako svazek clusteru_

  Po vytvoření svazku nástroj DataKeeper Správa a konfigurace ukazuje, že je úloha replikace aktivní.

  ![Obrázek 44: DataKeeper synchronní zrcadlení pro sdílenou složku disk SAP ASC nebo SCS je aktivní][sap-ha-guide-figure-3044]

  _**Obrázek 44:** DataKeeper synchronní zrcadlení pro SAP ASC nebo SCS sdílet disk je aktivní_

  Správce clusteru převzetí služeb při selhání teď disk jako disk s DataKeeper ukazuje, jak ukazuje obrázek 45:

  ![Obrázek 45: Správce clusteru převzetí služeb při selhání zobrazuje na disk, který DataKeeper replikovat][sap-ha-guide-figure-3045]

  _**Obrázek 45:** Správce clusteru převzetí služeb při selhání disku ukazuje, že DataKeeper replikovat_

## <a name="next-steps"></a>Další postup

* [Instalovat SAP NetWeaver HA pomocí clusteru převzetí služeb při selhání se systémem Windows a sdíleného disku pro instance SAP ASC nebo SCS][sap-high-availability-installation-wsfc-shared-disk]
