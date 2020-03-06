---
title: Infrastruktura Azure pro SAP ASCS/SCS se službou WSFC & sdíleným diskem | Microsoft Docs
description: Naučte se připravit infrastrukturu Azure pro SAP HA pomocí clusteru s podporou převzetí služeb při selhání Windows a sdíleného disku pro instanci SAP ASCS/SCS.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395292"
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


> ![Windows][Logo_Windows] Windows
>

Tento článek popisuje kroky, které můžete provést při přípravě infrastruktury Azure pro instalaci a konfiguraci systému SAP s vysokou dostupností na clusteru s podporou převzetí služeb při selhání s Windows pomocí *sdíleného disku clusteru* jako možnosti CLUSTERINGU instance SAP ASCS.

## <a name="prerequisites"></a>Předpoklady

Než začnete s instalací, přečtěte si tento článek:

* [Průvodce architekturou: cluster a instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdíleného disku clusteru][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Příprava infrastruktury na šablonu architektury 1
Šablony Azure Resource Manager pro SAP usnadňují nasazení požadovaných prostředků.

Šablony tří vrstev v Azure Resource Manager také podporují scénáře s vysokou dostupností. Například šablona architektury 1 obsahuje dva clustery. Každý cluster představuje jediný bod SAP pro SAP ASCS/SCS a DBMS.

Tady je postup, kde můžete získat šablony Azure Resource Manager pro ukázkový scénář, který popisujeme v tomto článku:

* [Obrázek Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Azure Marketplace Image pomocí Azure Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Vlastní image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Vlastní image pomocí Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Příprava infrastruktury pro architekturu šablony 1:

- V Azure Portal v podokně **parametry** v poli **SYSTEMAVAILABILITY** vyberte **ha**.

  ![Obrázek 1: nastavení parametrů Azure Resource Manager pro vysokou dostupnost SAP][sap-ha-guide-figure-3000]

_**Obrázek 1:** Nastavení Azure Resource Manager parametrů pro vysokou dostupnost SAP_


  Šablony vytvoří:

  * **Virtuální počítače**:
    * Virtuální počítače SAP aplikačního serveru: \<SAPSystemSID\>-di-\<číslo\>
    * Virtuální počítače s clustery ASCS/SCS: \<SAPSystemSID\>-ASCS-\<číslo\>
    * Cluster DBMS: \<SAPSystemSID\>-DB-\<číslo\>

  * **Síťové karty pro všechny virtuální počítače s přidruženými IP adresami**:
    * \<SAPSystemSID\>-nic-di-\<číslo\>
    * \<SAPSystemSID\>-nic-ASCS-\<číslo\>
    * \<SAPSystemSID\>-nic-DB-\<číslo\>

  * **Účty služby Azure Storage (jenom nespravované disky)** :

  * **Skupiny dostupnosti** pro:
    * Virtuální počítače SAP aplikačního serveru: \<SAPSystemSID\>-avset-di
    * Virtuální počítače s clustery SAP ASCS/SCS: \<SAPSystemSID\>-avset-ASCS
    * DBMS clusterové virtuální počítače: \<SAPSystemSID\>-avset-DB

  * **Interní nástroj pro vyrovnávání zatížení Azure**:
    * Všechny porty pro instanci ASCS/SCS a IP adresu \<SAPSystemSID\>-9,1-ASCS
    * Se všemi porty pro SQL Server DBMS a IP adresou \<SAPSystemSID\>-9,1-dB

  * **Skupina zabezpečení sítě**: \<SAPSystemSID\>-NSG-ASCS-0  
    * S otevřeným externím portem protokol RDP (Remote Desktop Protocol) (RDP) pro virtuální počítač \<SAPSystemSID\>-ASCS-0

> [!NOTE]
> Všechny IP adresy síťových karet a služeb interního nástroje pro vyrovnávání zatížení Azure jsou ve výchozím nastavení dynamické. Změňte je na statické IP adresy. Popisujeme, jak postupovat později v článku.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Nasazení virtuálních počítačů s připojením k podnikové síti (mezi místními) pro použití v produkčním prostředí
V případě produkčních systémů SAP nasaďte virtuální počítače Azure s připojením k podnikové síti pomocí Azure VPN Gateway nebo Azure ExpressRoute.

> [!NOTE]
> Můžete použít instanci Azure Virtual Network. Virtuální síť a podsíť už jsou vytvořené a připravené.
>
>

1. V Azure Portal v podokně **parametry** v poli **NEWOREXISTINGSUBNET** vyberte **existující**.
2. Do pole **SUBNETID** přidejte úplný řetězec připraveného ID podsítě sítě Azure, kde plánujete nasazení virtuálních počítačů Azure.
3. Pokud chcete získat seznam všech podsítí sítě Azure, spusťte tento příkaz PowerShellu:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   Pole **ID** zobrazuje hodnotu ID podsítě.
4. Pokud chcete získat seznam všech hodnot ID podsítě, spusťte tento příkaz PowerShellu:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   ID podsítě vypadá takto:

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Nasazení pouze cloudových instancí SAP pro test a ukázku
Systém SAP s vysokou dostupností můžete nasadit do modelu nasazení pouze pro Cloud. Tento druh nasazení je vhodný hlavně pro ukázkové a testovací případy použití. Nehodí se pro případy použití v produkčním prostředí.

- V Azure Portal v podokně **parametry** v poli **NEWOREXISTINGSUBNET** vyberte možnost **Nový**. Pole **SUBNETID** ponechte prázdné.

  Šablona SAP Azure Resource Manager automaticky vytvoří virtuální síť a podsíť Azure.

> [!NOTE]
> Musíte taky nasadit aspoň jeden vyhrazený virtuální počítač pro službu Active Directory a službu DNS ve stejné instanci Azure Virtual Network. Šablona nevytváří tyto virtuální počítače.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Příprava infrastruktury na šablonu architektury 2

Tuto šablonu Azure Resource Manager pro SAP můžete použít k jednoduššímu nasazení požadovaných prostředků infrastruktury pro architekturu SAP architektonické šablony 2.

Tady je místo, kde můžete získat Azure Resource Manager šablony pro tento scénář nasazení:

* [Obrázek Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Azure Marketplace Image pomocí Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Vlastní image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Vlastní image pomocí Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Příprava infrastruktury na šablonu architektury 3

Můžete připravit infrastrukturu a nakonfigurovat SAP pro více identifikátorů SID. Do *existující* konfigurace clusteru můžete například přidat další instanci SAP ASCS/SCS. Další informace najdete v tématu [Konfigurace další instance SAP ASCS/SCS pro existující konfiguraci clusteru pro vytvoření konfigurace SAP multi-SID v Azure Resource Manager][sap-ha-multi-sid-guide].

Pokud chcete vytvořit nový cluster s více identifikátory SID, můžete použít [šablony pro rychlý Start pro více SID na GitHubu](https://github.com/Azure/azure-quickstart-templates).

Chcete-li vytvořit nový cluster s více identifikátory SID, je nutné nasadit následující tři šablony:

* [Šablona ASCS/SCS](#ASCS-SCS-template)
* [Šablona databáze](#database-template)
* [Šablona aplikačních serverů](#application-servers-template)

Následující části obsahují další podrobnosti o šablonách a parametrech, které je třeba zadat v šablonách.

### <a name="ASCS-SCS-template"></a>Šablona ASCS/SCS

Šablona ASCS/SCS nasadí dva virtuální počítače, které můžete použít k vytvoření clusteru s podporou převzetí služeb při selhání se systémem Windows Server, který je hostitelem více instancí ASCS/SCS.

Šablonu ASCS/SCS s více identifikátory SID nastavíte tak, že v šabloně [ASCS/SCS SID][sap-templates-3-tier-multisid-xscs-marketplace-image] nebo [ASCS/SCS multi-SID pomocí Managed disks][sap-templates-3-tier-multisid-xscs-marketplace-image-md]zadáte hodnoty pro následující parametry:

- **Předpona prostředku**: nastavte předponu prostředku, která se používá k vytvoření předpony všech prostředků, které se vytvoří během nasazení. Vzhledem k tomu, že prostředky nepatří pouze k jednomu systému SAP, předpona prostředku není identifikátor SID jednoho systému SAP.  Předpona musí mít tři až šest znaků.
- **Typ zásobníku**: Vyberte typ zásobníku systému SAP. V závislosti na typu zásobníku Azure Load Balancer má jednu (jenom ABAP nebo Java) nebo dvě privátní IP adresy (ABAP + Java) na jeden systém SAP.
- **Typ operačního**systému: Vyberte operační systém virtuálních počítačů.
- **Počet systémů SAP**: Vyberte počet systémů SAP, které chcete v tomto clusteru nainstalovat.
- **Dostupnost systému**: vyberte **ha**.
- **Uživatelské jméno správce a heslo správce**: vytvořte nového uživatele, který se dá použít k přihlášení k počítači.
- **Nová nebo existující podsíť**: Nastavte, jestli se má vytvořit nová virtuální síť a podsíť, nebo jestli se má použít stávající podsíť. Pokud už máte virtuální síť, která je připojená k vaší místní síti, vyberte **existující**.
- **ID podsítě**: Pokud chcete nasadit virtuální počítač do existující virtuální sítě, kde máte definovanou podsíť, ke které je potřeba přiřadit virtuální počítač, pojmenujte ID této konkrétní podsítě. ID obvykle vypadá takto:

  ID předplatného/Subscriptions/\<\>/resourceGroups/\<název skupiny prostředků\>/providers/Microsoft.Network/virtualNetworks/\<virtuální sítě název\>/subnets/\<název podsítě\>

Šablona nasadí jednu instanci Azure Load Balancer, která podporuje více systémů SAP:

- Instance ASCS jsou nakonfigurované pro instance číslo 00, 10, 20...
- Instance SCS jsou nakonfigurované pro číslo instance 01, 11, 21...
- Instance serveru pro replikaci ASCS ve frontě (jenom Linux) se konfigurují pro instance číslo 02, 12, 22...
- Instance SCS OLAJÍCÍCH (pouze Linux) jsou nakonfigurované pro instance číslo 03, 13, 23...

Vyrovnávání zátěže obsahuje 1 VIP (2 pro Linux), 1x VIP pro ASCS/SCS a 1x VIP pro OLAJÍCÍCH (jenom Linux).

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a>Porty SAP ASCS/SCS
Následující seznam obsahuje všechna pravidla vyrovnávání zatížení (kde x je číslo systému SAP, například 1, 2, 3...):
- Porty specifické pro systém Windows pro každý systém SAP: 445, 5985
- ASCS porty (číslo instance x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS porty (číslo instance x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS OLAJÍCÍCH porty na platformě Linux (číslo instance X2): 33x2, 5x213, 5x214, 5x216
- SCS OLAJÍCÍCH porty na platformě Linux (číslo instance X3): 33x3, 5x313, 5x314, 5x316

Nástroj pro vyrovnávání zatížení je nakonfigurován tak, aby používal následující zkušební porty (kde x je číslo systému SAP, například 1, 2, 3...):
- ASCS/SCS interního vyrovnávání zatížení – port testu paměti 620x0
- OLAJÍCÍCH interního nástroje pro vyrovnávání zatížení (jenom Linux): 621x2

### <a name="database-template"></a>Šablona databáze

Šablona databáze nasadí jeden nebo dva virtuální počítače, které můžete použít k instalaci systému pro správu relačních databází (RDBMS) pro jeden systém SAP. Pokud například nasadíte šablonu ASCS/SCS pro pět systémů SAP, budete muset tuto šablonu nasadit pětkrát.

Pokud chcete nastavit šablonu pro více identifikátorů SID databáze, v šabloně [Database multi-SID][sap-templates-3-tier-multisid-db-marketplace-image] nebo [šabloně s více identifikátory sid databáze pomocí Managed disks][sap-templates-3-tier-multisid-db-marketplace-image-md]zadejte hodnoty pro následující parametry:

- **ID systému SAP**: Zadejte ID systému SAP systému SAP, který chcete nainstalovat. ID se používá jako předpona pro nasazené prostředky.
- **Typ operačního**systému: Vyberte operační systém virtuálních počítačů.
- **DbType**: Vyberte typ databáze, kterou chcete na clusteru nainstalovat. Pokud chcete nainstalovat Microsoft SQL Server, vyberte **SQL** . Pokud plánujete instalaci SAP HANA na virtuálních počítačích, vyberte **Hana** . Ujistěte se, že jste vybrali správný typ operačního systému. Vyberte **Windows** for SQL a vyberte distribuci Linux pro Hana. Azure Load Balancer, která je připojená k virtuálním počítačům, je nakonfigurovaná tak, aby podporovala vybraný typ databáze:
  * **SQL**: port vyrovnávání zatížení vyrovnávání zatížení 1433. Nezapomeňte použít tento port pro instalaci SQL Server AlwaysOn.
  * **Hana**: porty vyrovnávání zatížení pro vyrovnávání zatížení 35015 a 35017. Nezapomeňte nainstalovat SAP HANA s číslem instance **50**.
  Nástroj pro vyrovnávání zatížení používá port testu 62550.
- **Velikost systému SAP**: nastavte počet SAP, který nový systém poskytuje. Pokud si nejste jistí, kolik SAP systém vyžaduje, požádejte svého partnera technologie SAP nebo systémový integrátor.
- **Dostupnost systému**: vyberte **ha**.
- **Uživatelské jméno správce a heslo správce**: vytvořte nového uživatele, který se dá použít k přihlášení k počítači.
- **ID podsítě**: Zadejte ID podsítě, kterou jste použili během nasazování šablony ASCS/SCS, nebo ID podsítě, která byla vytvořena jako součást nasazení šablony ASCS/SCS.

### <a name="application-servers-template"></a>Šablona aplikačních serverů

Šablona aplikačních serverů nasadí dva nebo víc virtuálních počítačů, které se dají použít jako instance aplikačního serveru SAP pro jeden systém SAP. Pokud například nasadíte šablonu ASCS/SCS pro pět systémů SAP, budete muset tuto šablonu nasadit pětkrát.

Chcete-li nastavit šablonu multi-SID aplikačních serverů, v šabloně multi-SID [aplikačních][sap-templates-3-tier-multisid-apps-marketplace-image] serverů nebo [aplikačních serverů s více SID pomocí Managed disks][sap-templates-3-tier-multisid-apps-marketplace-image-md]zadejte hodnoty pro následující parametry:

  -  **ID systému SAP**: Zadejte ID systému SAP systému SAP, který chcete nainstalovat. ID se používá jako předpona pro nasazené prostředky.
  -  **Typ operačního**systému: Vyberte operační systém virtuálních počítačů.
  -  **Velikost systému SAP**: počet SAP, který nový systém poskytuje. Pokud si nejste jistí, kolik SAP systém vyžaduje, požádejte svého partnera technologie SAP nebo systémový integrátor.
  -  **Dostupnost systému**: vyberte **ha**.
  -  **Uživatelské jméno správce a heslo správce**: vytvořte nového uživatele, který se dá použít k přihlášení k počítači.
  -  **ID podsítě**: Zadejte ID podsítě, kterou jste použili během nasazování šablony ASCS/SCS, nebo ID podsítě, která byla vytvořena jako součást nasazení šablony ASCS/SCS.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Virtual Network Azure
V našem příkladu je adresní prostor instance služby Azure Virtual Network 10.0.0.0/16. Existuje jedna podsíť s názvem podsíť s rozsahem adres 10.0.0.0/24. Všechny virtuální počítače a interní nástroje pro vyrovnávání zatížení se nasazují v této virtuální síti.

> [!IMPORTANT]
> V hostovaném operačním systému neprovádějte žádné změny nastavení sítě. Patří sem IP adresy, servery DNS a podsíť. Nakonfigurujte všechna nastavení sítě v Azure. Služba DHCP (Dynamic Host Configuration Protocol) rozšíří vaše nastavení.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>IP adresy DNS

Chcete-li nastavit požadované IP adresy DNS, proveďte následující kroky:

1. V Azure Portal v podokně **servery DNS** se ujistěte, že je možnost **servery DNS** virtuální sítě nastavená na **vlastní DNS**.
2. Vyberte nastavení podle typu sítě, kterou máte. Další informace najdete v následujících zdrojích:
   * Přidejte IP adresy místních serverů DNS.  
   Místní servery DNS můžete rozmístit do virtuálních počítačů, které běží v Azure. V takovém scénáři můžete přidat IP adresy virtuálních počítačů Azure, na kterých spouštíte službu DNS.
   * Pro nasazení virtuálních počítačů, které jsou izolované v Azure: nasaďte další virtuální počítač ve stejné instanci Virtual Network, která slouží jako server DNS. Přidejte IP adresy virtuálních počítačů Azure, které jste nastavili pro spuštění služby DNS.

   ![Obrázek 2: Konfigurace serverů DNS pro Azure Virtual Network][sap-ha-guide-figure-3001]

   _**Obrázek 2:** Konfigurace serverů DNS pro Azure Virtual Network_

   > [!NOTE]
   > Pokud změníte IP adresy serverů DNS, budete muset virtuální počítače Azure restartovat, aby se změna projevila a rozšířila nové servery DNS.
   >
   >

V našem příkladu je služba DNS nainstalovaná a nakonfigurovaná na těchto virtuálních počítačích s Windows:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Název síťové karty | Statická IP adresa |
| --- | --- | --- | --- |
| První server DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Druhý server DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Názvy hostitelů a statické IP adresy pro clusterované instance SAP ASCS/SCS a clusterované instance DBMS

Pro místní nasazení potřebujete tyto rezervované názvy hostitelů a IP adresy:

| Role názvu virtuálního hostitele | Název virtuálního hostitele | Virtuální statická IP adresa |
| --- | --- | --- |
| Název virtuálního hostitele SAP ASCS/SCS prvního clusteru (pro správu clusteru) |pr1-ascs-vir |10.0.0.42 |
| Název virtuálního hostitele pro instanci SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| SAP DBMS druhý název virtuálního hostitele clusteru (Správa clusteru) |pr1-dbms-vir |10.0.0.32 |

Při vytváření clusteru vytvořte názvy virtuálních hostitelů PR1-ASCS-Vir a PR1-DBMS-Vir a přidružené IP adresy, které spravují samotný cluster. Informace o tom, jak to provést, najdete v tématu [shromáždění uzlů clusteru v konfiguraci clusteru][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Na serveru DNS můžete ručně vytvořit další dva názvy virtuálních hostitelů, PR1-ASCS-SAP a PR1-DBMS-SAP a přidružené IP adresy. Tyto prostředky používají clusterovaná instance SAP ASCS/SCS a instance clusterovaného systému DBMS. Informace o tom, jak to provést, najdete v tématu [Vytvoření virtuálního hostitele pro clusterovanou instanci SAP ASCS/SCS][sap-ha-guide-9.1.1].

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Nastavení statických IP adres pro virtuální počítače SAP
Po nasazení virtuálních počítačů, které se mají použít v clusteru, je potřeba nastavit statické IP adresy pro všechny virtuální počítače. Provedete to v konfiguraci Azure Virtual Network, a ne v hostovaném operačním systému.

1. V Azure Portal vyberte **skupinu prostředků** > **Nastavení** > **Síťová karta** > **IP adresa**.
2. V podokně **IP adresy** v části **přiřazení**vyberte **static**. Do pole **IP adresa** zadejte IP adresu, kterou chcete použít.

   > [!NOTE]
   > Pokud změníte IP adresu síťové karty, budete muset virtuální počítače Azure restartovat, aby se změny projevily.  
   >
   >

   ![Obrázek 3: Nastavení statických IP adres pro síťovou kartu každého virtuálního počítače][sap-ha-guide-figure-3002]

   _**Obrázek 3:** Nastavte statické IP adresy pro síťovou kartu každého virtuálního počítače._

   Tento krok opakujte pro všechna síťová rozhraní, tedy pro všechny virtuální počítače, včetně virtuálních počítačů, které chcete použít pro službu Active Directory nebo DNS.

V našem příkladu máme tyto virtuální počítače a statické IP adresy:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Název síťové karty | Statická IP adresa |
| --- | --- | --- | --- |
| První instance aplikačního serveru SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Druhá instance aplikačního serveru SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| Tlačítka ... |Tlačítka ... |Tlačítka ... |Tlačítka ... |
| Poslední instance aplikačního serveru SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| První uzel clusteru pro instanci ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Druhý uzel clusteru pro instanci ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| První uzel clusteru pro instanci systému DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Druhý uzel clusteru pro instanci systému DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Nastavte statickou IP adresu pro interní nástroj pro vyrovnávání zatížení Azure.

Šablona SAP Azure Resource Manager vytvoří interní nástroj pro vyrovnávání zatížení Azure, který se používá pro cluster instancí SAP ASCS/SCS a cluster DBMS.

> [!IMPORTANT]
> IP adresa názvu virtuálního hostitele SAP ASCS/SCS je stejná jako IP adresa pro interní nástroj pro vyrovnávání zatížení SAP ASCS/SCS: PR1-9,1-ASCS.
> IP adresa virtuálního názvu systému DBMS je stejná jako IP adresa interního nástroje pro vyrovnávání zatížení systému DBMS: PR1-9,1-DBMS.
>
>

Nastavení statické IP adresy pro interní nástroj pro vyrovnávání zatížení Azure:

1. Počáteční nasazení nastaví IP adresu interního nástroje pro vyrovnávání zatížení na **dynamickou**. V Azure Portal v podokně **IP adresy** v části **přiřazení**vyberte **statické**.
2. Nastavte IP adresu interního nástroje pro vyrovnávání zatížení **PR1-9,1-ASCS** na IP adresu názvu virtuálního hostitele instance SAP ASCS/SCS.
3. Nastavte IP adresu interního nástroje pro vyrovnávání zatížení **PR1-9,1-DBMS** na IP adresu názvu virtuálního hostitele instance systému DBMS.

   ![Obrázek 4: Nastavení statických IP adres pro interní nástroj pro vyrovnávání zatížení pro instanci SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Obrázek 4:** Nastavení statických IP adres pro interní nástroj pro vyrovnávání zatížení pro instanci SAP ASCS/SCS_

V našem příkladu máme dva interní nástroje pro vyrovnávání zatížení Azure, které mají tyto statické IP adresy:

| Role interního nástroje pro vyrovnávání zatížení Azure | Název interního nástroje pro vyrovnávání zatížení Azure | Statická IP adresa |
| --- | --- | --- |
| Interní nástroj pro vyrovnávání zatížení instance SAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Interní nástroj pro vyrovnávání zatížení SAP DBMS |pr1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Výchozí pravidla vyrovnávání zatížení ASCS/SCS pro interní nástroj pro vyrovnávání zatížení Azure

Šablona SAP Azure Resource Manager vytvoří porty, které potřebujete:
* Instance ABAP ASCS s výchozí instancí Number 00
* Instance Java SCS s výchozím číslem instance 01

Při instalaci instance SAP ASCS/SCS je nutné použít výchozí číslo instance 00 pro instanci ABAP ASCS a výchozí číslo instance 01 pro instanci Java SCS.

Dále vytvořte požadované koncové body interního vyrovnávání zatížení pro porty SAP NetWeaver.

Chcete-li vytvořit požadované koncové body interního vyrovnávání zatížení, vytvořte nejprve tyto koncové body vyrovnávání zatížení pro porty ASCS SAP NetWeaver ABAP:

| Název pravidla služby/Vyrovnávání zatížení | Výchozí čísla portů | Konkrétní porty pro (ASCS instance s číslem instance 00) (OLAJÍCÍCH s 10) |
| --- | --- | --- |
| Server/ *lbrule3200* fronty |32\<číslo instance\> |3200 |
| Server zpráv ABAP/ *lbrule3600* |36\<číslo instance\> |3600 |
| Interní zpráva ABAP/ *lbrule3900* |39\<číslo instance\> |3900 |
| HTTP/ *Lbrule8100* serveru zpráv |81\<číslo instance\> |8100 |
| Služba SAP Start Service ASCS HTTP/ *Lbrule50013* |5\<číslo instance\>13 |50013 |
| Služba SAP Start Service ASCS HTTPS/ *Lbrule50014* |5\<číslo instance\>14 |50014 |
| Replikace do fronty/ *Lbrule50016* |5\<číslo instance\>16 |50016 |
| Služba SAP Start Service OLAJÍCÍCH HTTP *Lbrule51013* |5\<číslo instance\>13 |51013 |
| Služba SAP Start Service OLAJÍCÍCH HTTP *Lbrule51014* |5\<číslo instance\>14 |51014 |
| Vzdálená správa systému Windows (WinRM) *Lbrule5985* | |5985 |
| *Lbrule445* sdílení souborů | |445 |

**Tabulka 1:** Čísla portů instancí ASCS SAP NetWeaver ABAP

Pak vytvořte tyto koncové body vyrovnávání zatížení pro porty SAP NetWeaver Java SCS:

| Název pravidla služby/Vyrovnávání zatížení | Výchozí čísla portů | Konkrétní porty pro (instance SCS s číslem instance 01) (OLAJÍCÍCH s 11) |
| --- | --- | --- |
| Server/ *lbrule3201* fronty |32\<číslo instance\> |3201 |
| Server brány/ *lbrule3301* |33\<číslo instance\> |3301 |
| Server zpráv Java/ *lbrule3900* |39\<číslo instance\> |3901 |
| HTTP/ *Lbrule8101* serveru zpráv |81\<číslo instance\> |8101 |
| Služba SAP Start Service SCS HTTP/ *Lbrule50113* |5\<číslo instance\>13 |50113 |
| Služba SAP Start Service SCS HTTPS/ *Lbrule50114* |5\<číslo instance\>14 |50114 |
| Replikace do fronty/ *Lbrule50116* |5\<číslo instance\>16 |50116 |
| Služba SAP Start Service OLAJÍCÍCH HTTP *Lbrule51113* |5\<číslo instance\>13 |51113 |
| Služba SAP Start Service OLAJÍCÍCH HTTP *Lbrule51114* |5\<číslo instance\>14 |51114 |
| *Lbrule5985* WinRM | |5985 |
| *Lbrule445* sdílení souborů | |445 |

**Tabulka 2:** Čísla portů pro instance SAP NetWeaver Java SCS

![Obrázek 5: výchozí pravidla vyrovnávání zatížení ASCS/SCS pro interní nástroj pro vyrovnávání zatížení Azure][sap-ha-guide-figure-3004]

_**Obrázek 5:** Výchozí pravidla vyrovnávání zatížení ASCS/SCS pro interní nástroj pro vyrovnávání zatížení Azure_

Nastavte IP adresu nástroje pro vyrovnávání zatížení PR1-9,1-DBMS na IP adresu názvu virtuálního hostitele instance systému DBMS.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Změna výchozích pravidel vyrovnávání zatížení ASCS/SCS pro interní nástroj pro vyrovnávání zatížení Azure

Pokud chcete pro instance SAP ASCS nebo SCS používat odlišná čísla, musíte změnit názvy a hodnoty jejich portů z výchozích hodnot.

1. V Azure Portal vyberte **\<SID\>-9,1-ASCS load balancer** > **pravidla vyrovnávání**zatížení.
2. Pro všechna pravidla vyrovnávání zatížení, která patří do instance SAP ASCS nebo SCS, změňte tyto hodnoty:

   * Název
   * Port
   * Back-end port

   Například pokud chcete změnit výchozí číslo instance ASCS z 00 na 31, je nutné provést změny pro všechny porty uvedené v tabulce 1.

   Tady je příklad aktualizace pro port *lbrule3200*.

   ![Obrázek 6: Změna výchozích pravidel vyrovnávání zatížení ASCS/SCS pro interní nástroj pro vyrovnávání zatížení Azure][sap-ha-guide-figure-3005]

   _**Obrázek 6:** Změna výchozích pravidel vyrovnávání zatížení ASCS/SCS pro interní nástroj pro vyrovnávání zatížení Azure_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Přidání virtuálních počítačů s Windows do domény

Po přiřazení statické IP adresy k virtuálním počítačům přidejte virtuální počítače do domény.

![Obrázek 7: Přidání virtuálního počítače k doméně][sap-ha-guide-figure-3006]

_**Obrázek 7:** Přidání virtuálního počítače do domény_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Přidání položek registru na obou uzlech clusteru instance SAP ASCS/SCS

Azure Load Balancer má interní nástroj pro vyrovnávání zatížení, který ukončí připojení v případě, že jsou připojení po nastaveném časovém intervalu nečinné (nečinný časový limit). Pracovní procesy SAP v instancích dialogů otevřou připojení k procesu zařazení do fronty SAP ihned po odeslání první žádosti o zařazení do fronty nebo vyřazení z fronty. Tato připojení obvykle zůstanou zavedena, dokud se nerestartuje pracovní proces nebo proces zařazování do fronty. Pokud je ale připojení nečinné v nastaveném časovém intervalu, ukončí interní nástroj pro vyrovnávání zatížení Azure připojení. Nejedná se o problém, protože pracovní proces SAP znovu naváže připojení k procesu zařazování, pokud již neexistuje. Tyto aktivity jsou popsány v části vývojář sleduje procesy SAP, ale v těchto trasování vytvářejí velký objem obsahu navíc. Je vhodné změnit `KeepAliveTime` protokolu TCP/IP a `KeepAliveInterval` na obou uzlech clusteru. Zkombinujte tyto změny do parametrů protokolu TCP/IP s parametry profilu SAP, které jsou popsány dále v článku.

Chcete-li přidat položky registru na oba uzly clusteru instance SAP ASCS/SCS, přidejte tyto položky registru Windows na obou uzlech clusteru Windows pro SAP ASCS/SCS:

| Cesta | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Název proměnné |`KeepAliveTime` |
| Typ proměnné |REG_DWORD (desetinné číslo) |
| Hodnota |120000 |
| Odkaz na dokumentaci |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabulka 3:** Změna prvního parametru protokolu TCP/IP

Pak přidejte tuto položku registru Windows na uzlech clusteru Windows pro SAP ASCS/SCS:

| Cesta | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Název proměnné |`KeepAliveInterval` |
| Typ proměnné |REG_DWORD (desetinné číslo) |
| Hodnota |120000 |
| Odkaz na dokumentaci |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Tabulka 4:** Změna druhého parametru protokolu TCP/IP

Chcete-li změny použít, restartujte oba uzly clusteru.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Nastavení clusteru s podporou převzetí služeb při selhání Windows serveru pro instanci SAP ASCS/SCS

Nastavení clusteru s podporou převzetí služeb při selhání Windows serveru pro instanci SAP ASCS/SCS zahrnuje tyto úlohy:

- Shromáždění uzlů clusteru v konfiguraci clusteru.
- Nakonfigurujte určující sdílenou složku clusteru.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Shromáždění uzlů clusteru v konfiguraci clusteru

1. V Průvodci přidáním role a funkce přidejte Clustering s podporou převzetí služeb při selhání do obou uzlů clusteru.
2. Cluster s podporou převzetí služeb při selhání nastavte pomocí Správce clusteru s podporou převzetí služeb při selhání. V Správce clusteru s podporou převzetí služeb při selhání vyberte **vytvořit cluster**a pak přidejte jenom název prvního clusteru (Node a). Ještě Nepřidávat druhý uzel; druhý uzel přidáte v pozdějším kroku.

   ![Obrázek 8: Přidejte název serveru nebo virtuálního počítače prvního uzlu clusteru.][sap-ha-guide-figure-3007]

   _**Obrázek 8:** Přidejte název serveru nebo virtuálního počítače prvního uzlu clusteru._

3. Zadejte název sítě (název virtuálního hostitele) clusteru.

   ![Obrázek 9: zadejte název clusteru.][sap-ha-guide-figure-3008]

   _**Obrázek 9:** Zadejte název clusteru._

4. Po vytvoření clusteru spusťte test ověření clusteru.

   ![Obrázek 10: spuštění kontroly ověření clusteru][sap-ha-guide-figure-3009]

   _**Obrázek 10:** Spustit kontrolu ověření clusteru_

   V tomto okamžiku můžete v tomto okamžiku ignorovat všechna upozornění týkající se disků. Později přidáte určující sdílenou složku a sdílené disky s příponou. V této fázi si nemusíte dělat starosti s kvorem.

   ![Obrázek 11: nenašel se žádný disk kvora.][sap-ha-guide-figure-3010]

   _**Obrázek 11:** Nenašel se žádný disk kvora._

   ![Obrázek 12: základní prostředek clusteru potřebuje novou IP adresu.][sap-ha-guide-figure-3011]

   _**Obrázek 12:** Základní prostředek clusteru potřebuje novou IP adresu._

5. Změňte IP adresu základní Clusterové služby. Cluster se nemůže spustit, dokud nezměníte IP adresu základní Clusterové služby, protože IP adresa serveru odkazuje na jeden z uzlů virtuálních počítačů. Udělejte to na stránce **vlastností** prostředku IP základní Clusterové služby.

   Například potřebujeme přiřadit IP adresu (v našem příkladu 10.0.0.42) pro název virtuálního hostitele clusteru PR1-ASCS-vir.

   ![Obrázek 13: v dialogovém okně Vlastnosti změňte IP adresu.][sap-ha-guide-figure-3012]

   _**Obrázek 13:** V dialogovém okně **vlastnosti** změňte IP adresu._

   ![Obrázek 14: přiřazení IP adresy rezervované pro cluster][sap-ha-guide-figure-3013]

   _**Obrázek 14:** Přiřaďte IP adresu rezervovanou pro cluster._

6. Přepněte název virtuálního hostitele clusteru do režimu online.

   ![Obrázek 15: služba jádra clusteru je spuštěná a má správnou IP adresu.][sap-ha-guide-figure-3014]

   _**Obrázek 15:** Služba Cluster Core je spuštěná a má správnou IP adresu._

7. Přidejte druhý uzel clusteru.

   Teď, když je základní Clusterová služba v provozu, můžete přidat druhý uzel clusteru.

   ![Obrázek 16 přidání druhého uzlu clusteru][sap-ha-guide-figure-3015]

   _**Obrázek 16:** Přidat druhý uzel clusteru_

8. Zadejte název druhého hostitele uzlu clusteru.

   ![Obrázek 17: zadejte název hostitele druhého uzlu clusteru.][sap-ha-guide-figure-3016]

   _**Obrázek 17:** Zadejte název hostitele druhého uzlu clusteru._

   > [!IMPORTANT]
   > Ujistěte *se, že není zaškrtnuté* políčko **Přidat do clusteru veškeré oprávněné úložiště** .  
   >
   >

   ![Obrázek 18: zrušte zaškrtnutí políčka.][sap-ha-guide-figure-3017]

   _**Obrázek 18:** Nezaškrtávejte *políčko*_

   Můžete ignorovat upozornění týkající se kvora a disků. Pak nastavíte kvorum a později budete disk sdílet, jak je popsáno v tématu [Installing s Datakeeped Cluster Edition pro disk sdílené složky clusteru SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

   ![Obrázek 19: ignorování upozornění na kvorum disku][sap-ha-guide-figure-3018]

   _**Obrázek 19:** Ignorovat upozornění týkající se kvora disku_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Konfigurace určující sdílené složky clusteru

Konfigurace určující sdílené složky clusteru zahrnuje tyto úlohy:

- Vytvořte sdílenou složku.
- Nastavte kvorum určující sdílenou složku v Správce clusteru s podporou převzetí služeb při selhání.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Vytvoření sdílené složky

1. Vyberte místo na disku kvora určující sdílenou složku. S parametrem datakeeped je tato možnost podporována.

   V příkladech v tomto článku je určující sdílená složka ve službě Active Directory nebo DNS serveru, který běží v Azure. Určující sdílená složka se nazývá domcontr-0. Vzhledem k tomu, že jste nakonfigurovali připojení VPN k Azure (prostřednictvím VPN Gateway nebo Azure ExpressRoute), služba Active Directory nebo DNS je místní a není vhodná pro spuštění určující sdílené složky.

   > [!NOTE]
   > Pokud služba Active Directory nebo služba DNS běží jenom v místním prostředí, nekonfigurujte určující sdílenou složku ve službě Active Directory nebo v operačním systému Windows DNS, který je místně spuštěný. Latence sítě mezi uzly clusteru běžícími v Azure a službou Active Directory nebo místním DNS může být moc velká a způsobit problémy s připojením. Nezapomeňte nakonfigurovat určující sdílenou složku na virtuálním počítači Azure, na kterém běží blízko uzlu clusteru.  
   >
   >

   Jednotka kvora potřebuje alespoň 1 024 MB volného místa. Pro jednotku kvora doporučujeme 2 048 MB volného místa.

2. Přidejte objekt názvu clusteru.

   ![Obrázek 20: přiřazení oprávnění ke sdílené složce pro objekt názvu clusteru][sap-ha-guide-figure-3019]

   _**Obrázek 20:** Přiřaďte oprávnění ke sdílené složce pro objekt názvu clusteru._

   Ujistěte se, že oprávnění zahrnují autoritu pro změnu dat ve sdílené složce pro objekt názvu clusteru (v našem příkladu PR1-ASCS-vir $).

3. Pokud chcete do seznamu přidat objekt názvu clusteru, vyberte **Přidat**. Změňte filtr tak, aby kontroloval objekty počítače (kromě těch, které jsou znázorněny na obrázku 22).

   ![Obrázek 21: Změna typů objektů na zahrnutí počítačů][sap-ha-guide-figure-3020]

   _**Obrázek 21:** Změna **typů objektů** na zahrnutí počítačů_

   ![Obrázek 22: zaškrtnutí políčka počítače][sap-ha-guide-figure-3021]

   _**Obrázek 22:** Zaškrtněte políčko **počítače** ._

4. Zadejte objekt názvu clusteru, jak je znázorněno na obrázku 21. Vzhledem k tomu, že tento záznam již byl vytvořen, můžete změnit oprávnění, jak je znázorněno na obrázku 20.

5. Vyberte kartu **zabezpečení** sdílené složky a pak nastavte podrobnější oprávnění pro objekt názvu clusteru.

   ![Obrázek 23: nastavení atributů zabezpečení pro objekt názvu clusteru v kvoru sdílení souborů][sap-ha-guide-figure-3022]

   _**Obrázek 23:** Nastavení atributů zabezpečení pro objekt názvu clusteru v kvoru sdílení souborů_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Nastavte kvorum určující sdílenou složku v Správce clusteru s podporou převzetí služeb při selhání

1. Otevřete Průvodce konfigurací nastavení kvora.

   ![Obrázek 24: spuštění Průvodce konfigurací nastavení kvora clusteru][sap-ha-guide-figure-3023]

   _**Obrázek 24:** Spuštění Průvodce konfigurací nastavení kvora clusteru_

2. Na stránce **Vybrat možnosti konfigurace kvora** vyberte **Vybrat určující disk kvora**.

   ![Obrázek 25: konfigurace kvora, ze kterých si můžete vybrat][sap-ha-guide-figure-3024]

   _**Obrázek 25:** Konfigurace kvora, ze kterých si můžete vybrat_

3. Na stránce **Vybrat určující disk kvora** vyberte **Konfigurovat určující sdílenou složku**.

   ![Obrázek 26: vyberte určující sdílenou složku][sap-ha-guide-figure-3025]

   _**Obrázek 26:** Vybrat určující sdílenou složku_

4. Zadejte cestu UNC ke sdílené složce (v našem příkladu \\domcontr-0\FSW). Chcete-li zobrazit seznam změn, které lze provést, vyberte možnost **Další**.

   ![Obrázek 27: definování umístění sdílené složky pro sdílenou složku s kopií clusteru][sap-ha-guide-figure-3026]

   _**Obrázek 27:** Zadejte umístění sdílené složky pro sdílenou složku s kopií clusteru._

5. Vyberte požadované změny a pak klikněte na tlačítko **Další**. Musíte úspěšně znovu nakonfigurovat konfiguraci clusteru, jak je znázorněno na obrázku 28:  

   ![Obrázek 28: potvrzení, že jste cluster znovu nakonfigurovali][sap-ha-guide-figure-3027]

   _**Obrázek 28:** Potvrzení, že jste cluster znovu nakonfigurovali_

Po úspěšné instalaci clusteru s podporou převzetí služeb při selhání v systému Windows je třeba změnit některé prahové hodnoty, aby bylo zjišťování převzetí služeb při selhání v Azure možné. Parametry, které se mají změnit, jsou popsané v části [ladění prahových hodnot sítě clusteru s podporou převzetí služeb][tuning-failover-cluster-network-thresholds] Za předpokladu, že vaše dva virtuální počítače, které tvoří konfiguraci clusteru Windows pro ASCS/SCS, jsou ve stejné podsíti, změňte následující parametry na tyto hodnoty:

- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Tato nastavení byla testována se zákazníky a nabízejí dobré zabezpečení. Jsou dostatečně odolné, ale také poskytují převzetí služeb při selhání, které je dostatečně rychlé v reálných stavových chybách na softwaru SAP nebo v případě selhání uzlu nebo virtuálního počítače.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Instalace s tím, že Clusterová edice DataKeeper pro disk sdílené složky clusteru SAP ASCS/SCS

Teď máte v Azure fungující konfiguraci clusteringu s podporou převzetí služeb při selhání ve Windows serveru. K instalaci instance SAP ASCS/SCS potřebujete prostředek sdíleného disku. V Azure nemůžete vytvořit prostředky sdíleného disku, které potřebujete. V případě clusteru DataKeeper Edition je řešení třetí strany, které můžete použít k vytvoření prostředků sdíleného disku.

Instalace s tím, že Clusterová edice DataKeeper pro sdílený disk clusteru SAP ASCS/SCS zahrnuje tyto úlohy:

- Přidejte Microsoft .NET Framework 3,5.
- Nainstalujte s DataKeeper.
- Nastavte s datakeep.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Přidat .NET Framework 3,5
.NET Framework 3,5 není automaticky aktivován nebo nainstalován v systému Windows Server 2012 R2. Vzhledem k tomu, že s DataKeeper vyžaduje, aby rozhraní .NET bylo na všech uzlech, kde instalujete DataKeeper, musíte nainstalovat .NET Framework 3,5 v hostovaném operačním systému všech virtuálních počítačů v clusteru.

Existují dva způsoby, jak přidat .NET Framework 3,5:

- Pomocí Průvodce přidáním rolí a funkcí v systému Windows, jak je znázorněno na obrázku 29:

  ![Obrázek 29: instalace .NET Framework 3,5 pomocí Průvodce přidáním rolí a funkcí][sap-ha-guide-figure-3028]

  _**Obrázek 29:** Instalace .NET Framework 3,5 pomocí Průvodce přidáním rolí a funkcí_

  ![Obrázek 30: indikátor průběhu instalace při instalaci .NET Framework 3,5 pomocí Průvodce přidáním rolí a funkcí][sap-ha-guide-figure-3029]

  _**Obrázek 30:** Indikátor průběhu instalace při instalaci .NET Framework 3,5 pomocí Průvodce přidáním rolí a funkcí_

- Použijte nástroj příkazového řádku DISM. exe. Pro tento typ instalace budete potřebovat přístup k adresáři SxS na instalačním médiu Windows. Na příkazovém řádku se zvýšenými oprávněními zadejte tento příkaz:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a>Instalace s DataKeeper

Nainstalujte cluster s DataKeeper Edition do každého uzlu v clusteru. Pokud chcete vytvořit virtuální sdílené úložiště s využitím DataKeeper, vytvořte synchronizovaný zrcadlový svazek a pak Simulujte sdílené úložiště clusteru.

Než nainstalujete software s, vytvořte uživatele domény DataKeeperSvc.

> [!NOTE]
> Přidejte uživatele domény DataKeeperSvc do místní skupiny správců na obou uzlech clusteru.
>
>

Instalace s DataKeeper:

1. Nainstalujte software v obou uzlech clusteru.

   ![S instalačním programem][sap-ha-guide-figure-3030]

   ![Obrázek 31: první stránka Instalace s datakeeping][sap-ha-guide-figure-3031]

   _**Obrázek 31:** První stránka Instalace s datakeeping_

2. V dialogovém okně vyberte **Ano**.

   ![Obrázek 32: DataKeeper vás informuje, že služba bude zakázaná.][sap-ha-guide-figure-3032]

   _**Obrázek 32:** DataKeeper vás informuje, že služba bude zakázaná._

3. V dialogovém okně doporučujeme vybrat možnost **účet domény nebo serveru**.

   ![Obrázek 33: výběr uživatele pro s datakeeping][sap-ha-guide-figure-3033]

   _**Obrázek 33:** Výběr uživatele pro s datakeeping_

4. Zadejte uživatelské jméno a heslo účtu domény, které jste vytvořili pro datakeeping.

   ![Obrázek 34: zadejte uživatelské jméno a heslo domény pro instalaci s názvem s-Keep.][sap-ha-guide-figure-3034]

   _**Obrázek 34:** Zadejte uživatelské jméno a heslo domény pro instalaci s názvem s instalací._

5. Nainstalujte licenční klíč pro vaši instanci s tím, jak je znázorněno na obrázku 35.

   ![Obrázek 35: Zadejte svůj licenční klíč s klíčem DataKeeper.][sap-ha-guide-figure-3035]

   _**Obrázek 35:** Zadejte svůj licenční klíč s klíčem datakeeping._

6. Po zobrazení výzvy restartujte virtuální počítač.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>Nastavení s s datakeeping

Po instalaci s DataKeeper na oba uzly spusťte konfiguraci. Cílem konfigurace je synchronní replikace dat mezi dalšími disky, které jsou připojené ke každému virtuálnímu počítači.

1. Spusťte nástroj pro správu a konfiguraci datakeep a pak vyberte **připojit server**.

   ![Obrázek 36: s nástroje pro správu a konfiguraci datakeeping][sap-ha-guide-figure-3036]

   _**Obrázek 36:** Správa s nástrojem datakeeping a Configuration Tool_

2. Zadejte název nebo TCP/IP adresu prvního uzlu, ke kterému se má nástroj pro správu a konfiguraci připojit, a v druhém kroku druhý uzel.

   ![Obrázek 37: Vložte název nebo TCP/IP adresu prvního uzlu, ke kterému se má nástroj pro správu a konfiguraci připojit, a v druhém kroku druhý uzel][sap-ha-guide-figure-3037]

   _**Obrázek 37:** Vložte název nebo TCP/IP adresu prvního uzlu, ke kterému se má nástroj pro správu a konfiguraci připojit, a v druhém kroku druhý uzel._

3. Vytvořte úlohu replikace mezi dvěma uzly.

   ![Obrázek 38: vytvoření úlohy replikace][sap-ha-guide-figure-3038]

   _**Obrázek 38:** Vytvoření úlohy replikace_

   Průvodce vás provede procesem vytvoření úlohy replikace.

4. Zadejte název úlohy replikace.

   ![Obrázek 39: definování názvu úlohy replikace][sap-ha-guide-figure-3039]

   _**Obrázek 39:** Zadejte název úlohy replikace._

   ![Obrázek 40: definujte základní data pro uzel, který by měl být aktuálním zdrojovým uzlem.][sap-ha-guide-figure-3040]

   _**Obrázek 40:** Definujte základní data pro uzel, který by měl být aktuálním zdrojovým uzlem._

5. Zadejte název, TCP/IP adresu a diskový svazek cílového uzlu.

   ![Obrázek 41: zadejte název, TCP/IP adresu a diskový svazek aktuálního cílového uzlu.][sap-ha-guide-figure-3041]

   _**Obrázek 41:** Zadejte název, TCP/IP adresu a diskový svazek aktuálního cílového uzlu._

6. Definujte algoritmy komprese. V našem příkladu doporučujeme komprimovat datový proud replikace. Obzvláště v situacích opakované synchronizace se komprese streamu replikace výrazně zkracuje i doba opakované synchronizace. Komprese používá prostředky procesoru a paměti RAM virtuálního počítače. Jak se zvyšuje rychlost komprese, takže se použije svazek prostředků procesoru. Toto nastavení můžete upravit později.

7. Další nastavení, které je třeba ověřit, je, zda replikace probíhá asynchronně nebo synchronně. Při ochraně konfigurací SAP ASCS/SCS je nutné použít synchronní replikaci.  

   ![Obrázek 42: definování podrobností replikace][sap-ha-guide-figure-3042]

   _**Obrázek 42:** Definování podrobností replikace_

8. Určete, zda má být svazek replikovaný úlohou replikace reprezentován konfigurací clusteru s podporou převzetí služeb při selhání systému Windows Server jako sdílený disk. Pro konfiguraci SAP ASCS/SCS vyberte **Ano** , aby se cluster Windows mohl replikovat replikovaný svazek jako sdílený disk, který může použít jako svazek clusteru.

   ![Obrázek 43: vyberte Ano a nastavte replikovaný svazek jako svazek clusteru.][sap-ha-guide-figure-3043]

   _**Obrázek 43:** Vyberte **Ano** a nastavte replikovaný svazek jako svazek clusteru._

   Po vytvoření svazku se v nástroji pro správu a konfiguraci datakeepu zobrazí, že je úloha replikace aktivní.

   ![Obrázek 44: aktivní Synchronní zrcadlení datakeeping pro disk Share SAP ASCS/SCS je aktivní][sap-ha-guide-figure-3044]

   _**Obrázek 44:** Synchronní zrcadlení datakeeping pro disk sdílené složky SAP ASCS/SCS je aktivní._

   Správce clusteru s podporou převzetí služeb při selhání nyní zobrazuje disk jako datakeeping disk, jak je znázorněno na obrázku 45:

   ![Obrázek 45: Správce clusteru s podporou převzetí služeb při selhání zobrazuje disk, na který se replikuje datakeeping][sap-ha-guide-figure-3045]

   _**Obrázek 45:** Správce clusteru s podporou převzetí služeb při selhání zobrazuje disk, který replikuje datakeeping._

## <a name="next-steps"></a>Další kroky

* [Instalace SAP NetWeaver HA pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdíleného disku pro instanci SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
