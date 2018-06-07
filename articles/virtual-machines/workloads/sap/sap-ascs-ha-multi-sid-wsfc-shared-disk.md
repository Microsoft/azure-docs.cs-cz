---
title: SAP ASC nebo SCS instance více SID vysoká dostupnost s Windows Server Failover Clustering a sdíleného disku v Azure | Microsoft Docs
description: Vysoká dostupnost více SID pro instance SAP ASC nebo SCS s Windows Server Failover Clustering a sdíleného disku na Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee5dc346def58ea7362a763d088145eb0d04a608
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656726"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md
[networking-limits-azure-resource-manager]:../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md


[sap-net-weaver-ports]:https://help.sap.com/viewer/ports
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-net-weaver-ports-ascs-scs-ports]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0f3ee255-b31e-4b8a-a95a-d9ed6200468b

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-install-ascs]:sap-high-availability-installation-wsfc-shared-disk.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]:sap-high-availability-installation-wsfc-shared-disk.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]:sap-high-availability-installation-wsfc-shared-disk.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]:sap-high-availability-installation-wsfc-shared-disk.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9


[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868

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

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png



[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-shared-disk-on-azure"></a>SAP ASC nebo SCS instance více SID vysoká dostupnost s Windows Server Failover Clustering a sdíleného disku na Azure

> ![Windows][Logo_Windows] Windows
>

V září 2016 společnost Microsoft vydala funkce, kde můžete spravovat víc virtuálních IP adres pomocí [pro vyrovnávání zatížení Azure interní][load-balancer-multivip-overview]. Tato funkce již existuje v Azure externím vyrovnáváním zatížení. 

Pokud máte nasazení SAP, musíte použít interní nástroj pro vytvoření konfigurace clusteru systému Windows pro instance služby Centrální SAP (ASC nebo SCS).

Tento článek se zaměřuje na postup přesunutí z jedné instalace ASC nebo SCS ke konfiguraci více SID SAP nainstalováním další instance SAP ASC nebo SCS clusteru do existujícího clusteru Windows Server Failover Clustering (WSFC) s sdíleného disku. Po dokončení tohoto procesu jste nakonfigurovali clusteru více SID služby SAP.

> [!NOTE]
> Tato funkce je k dispozici pouze v modelu nasazení Azure Resource Manager.
>
>Existuje omezení počtu privátní front-end IP adresy pro každý nástroj pro vyrovnávání zatížení Azure interní.
>
>Maximální počet instancí SAP ASC nebo SCS do jednoho clusteru služby WSFC se rovná maximální počet privátní front-end IP adresy pro každý nástroj pro vyrovnávání zatížení Azure interní.
>

Další informace o omezeních pro vyrovnávání zatížení, najdete v části "Privátní front-end IP adresu pro nástroj pro vyrovnávání zatížení" v [omezení sítě: Azure Resource Manager][networking-limits-azure-resource-manager].

## <a name="prerequisites"></a>Požadavky

Jste již nakonfigurovali cluster služby WSFC používat pro jednu instanci SAP ASC nebo SCS pomocí **sdílené složky**, jak je ukázáno v tomto diagramu.

![Instance SAP ASC nebo SCS vysokou dostupnost][sap-ha-guide-figure-6001]

> [!IMPORTANT]
> Nastavení musí splňovat následující podmínky:
> * Instance SAP ASC nebo SCS musejí sdílet stejný cluster služby WSFC.
> * Každý systém správy databáze (databázového systému) SID musí mít svůj vlastní vyhrazený cluster služby WSFC.
> * SAP aplikační servery, které patří do jednoho systému SAP SID musí mít vlastní vyhrazených virtuálních počítačích.

## <a name="sap-ascsscs-multi-sid-architecture-with-shared-disk"></a>Architektura více SID SAP ASC nebo SCS s sdíleného disku

Cílem je nainstalovat více ASC ABAP SAP nebo SAP Java SCS Clusterované instance ve stejném clusteru služby WSFC, jako ilustrované tady:

![Více instancí SAP ASC nebo SCS clusteru v Azure][sap-ha-guide-figure-6002]

Další informace o omezeních pro vyrovnávání zatížení, najdete v části "Privátní front-end IP adresu pro nástroj pro vyrovnávání zatížení" v [omezení sítě: Azure Resource Manager][networking-limits-azure-resource-manager].

Dokončení na šířku s dvěma systémy SAP vysoké dostupnosti bude vypadat takto:

![Nastavení vysoké dostupnosti více SID SAP s dvě systému SAP identifikátorů SID][sap-ha-guide-figure-6003]

## <a name="25e358f8-92e5-4e8d-a1e5-df7580a39cb0"></a> Příprava infrastruktury scénář více SID SAP

Příprava infrastruktury, můžete nainstalovat další instance SAP ASC nebo SCS s následujícími parametry:

| Název parametru | Hodnota |
| --- | --- |
| SAP ASC NEBO SCS SID |PR1-lb ASC |
| Databázového systému SAP interní nástroj pro vyrovnávání zatížení | PR5 |
| Název virtuálního hostitele SAP | pr5-sap-cl |
| SAP ASC nebo SCS virtuální hostitele IP adresu (IP adresa služby Vyrovnávání zatížení Další Azure) | 10.0.0.50 |
| Čísla instance SAP ASC nebo SCS | 50 |
| Port testu ILB pro další instance SAP ASC nebo SCS | 62350 |

> [!NOTE]
> Každou IP adresu pro SAP ASC nebo SCS instance clusteru, vyžaduje port jedinečný testu. Například pokud jednu IP adresu na Azure interní nástroj používá port testu 62300, žádné jiné IP adresy na tento nástroj pro vyrovnávání zatížení můžete použít port testu 62300.
>
>Pro naše účely protože port testu 62300 je rezervovaná, se používá port testu 62350.

V existujícím clusteru služby WSFC s dvěma uzly můžete nainstalovat další instance SAP ASC nebo SCS:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Statická IP adresa |
| --- | --- | --- |
| Prvním uzlu clusteru pro instanci ASC nebo SCS |pr1-ascs-0 |10.0.0.10 |
| Druhý uzel clusteru pro instanci ASC nebo SCS |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Vytvořte název virtuálního hostitele pro skupinu prostředků clusteru SAP ASC nebo SCS na serveru DNS

Položku DNS pro název virtuálního hostitele instance ASC nebo SCS můžete vytvořit pomocí následujících parametrů:

| Nový název virtuálního hostitele SAP ASC nebo SCS | Přidružené IP adresu |
| --- | --- | --- |
|pr5-sap-cl |10.0.0.50 |

Novým názvem hostitele a IP adresa se zobrazí ve Správci DNS, jak je znázorněno na následujícím snímku obrazovky:

![Správce DNS seznamu zvýraznění definované položky DNS pro novou SAP ASC nebo SCS clusteru virtuální název a adresu TCP/IP][sap-ha-guide-figure-6004]

> [!NOTE]
> Novou IP adresu, která přiřadíte název virtuálního hostitele další instance ASC nebo SCS musí být stejný jako novou IP adresu, který jste přiřadili ke službě Vyrovnávání zatížení SAP Azure.
>
>V našem scénáři je IP adresa 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Přidat existující Vyrovnávání zatížení Azure interní IP adresu pomocí prostředí PowerShell

Pokud chcete vytvořit více než jedna instance SAP ASC nebo SCS ve stejném clusteru služby WSFC, přidat existující Vyrovnávání zatížení Azure interní IP adresu pomocí prostředí PowerShell. Každou IP adresu vyžaduje vlastní pravidla Vyrovnávání zatížení, port testu, front-end fond IP adres a fond back-end.

Následující skript přidá novou IP adresu do existující pro vyrovnávání zatížení. Aktualizujte proměnné prostředí PowerShell pro vaše prostředí. Všechny požadované služby Vyrovnávání zatížení pravidla pro všechny porty SAP ASC nebo SCS vytvoří skript.

```powershell

# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure virtual network and subnet
$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add a second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzureRmLoadBalancer

# Get a new updated configuration
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Get an updated LP FrontendIpConfig
$FEConfig = Get-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add a back-end configuration into an existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzureRmLoadBalancer

# Get an updated config
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to the back-end pool
$BEPool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        #start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzureRmLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Po spuštění skriptu, výsledky se zobrazí na portálu Azure, jak je znázorněno na následujícím snímku obrazovky:

![Nový fond IP front-endu na portálu Azure][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Přidat disky do clusteru počítačů a konfiguraci disku SIOS clusteru sdílených složek.

Musíte přidat nový disk clusteru sdílené složky pro každou další instanci SAP ASC nebo SCS. Pro Windows Server 2012 R2 je disk sdílené složky clusteru služby WSFC aktuálně používán s DataKeeper softwarové řešení.

Udělejte toto:
1. Přidat další disk nebo disky stejnou velikost (které je třeba rozkládají) na všech uzlech clusteru a jejich formátování.
2. Nakonfigurujte s DataKeeper replikace úložiště.

Tento postup předpokládá, že jste již nainstalovali s DataKeeper u počítačů clusteru služby WSFC. Pokud jste ho nainstalovali, musíte teď nakonfigurovat replikace mezi počítači. Proces je podrobně popsány v [nainstalovat s DataKeeper Cluster Edition pro sdílenou složku disk clusteru SAP ASC nebo SCS][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].  

![DataKeeper synchronní zrcadlení pro nové SAP ASC nebo SCS sdílet disk][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-the-dbms-cluster"></a>Nasazení virtuálních počítačů pro SAP aplikační servery a cluster databázového systému

K dokončení Příprava infrastruktury pro druhý systému SAP, postupujte takto:

1. Nasazení vyhrazených virtuálních počítačích pro aplikační servery SAP a umístí každý svůj vlastní vyhrazený dostupnosti skupiny.
2. Nasazení vyhrazených virtuálních počítačích pro cluster databázového systému a umístí každý svůj vlastní vyhrazený dostupnosti skupiny.

## <a name="install-an-sap-netweaver-multi-sid-system"></a>Instalaci více SID systému SAP NetWeaver

Popis dokončení procesu instalace druhé systému SAP SID2 najdete v tématu [SAP NetWeaver HA instalace v systému Windows převzetí služeb při selhání clusteru a sdíleného disku pro instance SAP ASC nebo SCS][sap-high-availability-installation-wsfc-shared-disk].

Podrobný postup je následující:

1. [Instalace s vysokou dostupností ASC nebo SCS instance SAP][sap-high-availability-installation-wsfc-shared-disk-install-ascs].  
 V tomto kroku instalujete SAP s vysokou dostupností ASC nebo SCS instancí na stávající uzel clusteru služby WSFC 1.

2. [Upravit profil SAP instance ASC nebo SCS][sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile].

3. [Nakonfigurujte port testu][sap-high-availability-installation-wsfc-shared-disk-add-probe-port].  
 V tomto kroku nakonfigurujete prostředek clusteru SAP port testu SAP. SID2 IP pomocí prostředí PowerShell. Tuto konfiguraci proveďte v jednom z uzlů clusteru SAP ASC nebo SCS.

4. Nainstalujte instanci databáze.  
 K instalaci druhé clusteru, postupujte podle kroků v Průvodci instalací SAP.

5. Nainstalujte druhého uzlu clusteru.  
 V tomto kroku instalujete SAP s vysokou dostupností ASC nebo SCS instancí na stávající uzel clusteru služby WSFC 2. K instalaci druhé clusteru, postupujte podle kroků v Průvodci instalací SAP.

6. Otevřete porty brány Windows Firewall pro port SAP ASC nebo SCS instance a kontroly.  
    Na obou uzlů clusteru, které se používají pro instance SAP ASC nebo SCS otevíráte všechny porty brány Windows Firewall, které SAP ASC nebo SCS. Tyto porty instance SAP ASC nebo SCS jsou uvedeny v kapitole [SAP ASC nebo SCS porty][sap-net-weaver-ports-ascs-scs-ports].

    Seznam všech ostatních SAP portů najdete v tématu [porty TCP/IP všech produktů SAP][sap-net-weaver-ports].  

    Také otevřete port testu nástroje pro vyrovnávání Azure interní služby load, což je 62350 v tomto scénáři. Je popsán [v tomto článku][sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port].

7. [Změnit typ spuštění služby instance SAP vyhodnotit přijetí vyrovnání (YBRAT) Windows][sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type].

8. Instalace serveru primární aplikace SAP na nové vyhrazený virtuální počítač, jak je popsáno v Průvodci instalací SAP.  

9. Nainstalujte server další aplikace SAP na nové vyhrazený virtuální počítač, jak je popsáno v Průvodci instalací SAP.

10. [Testovací převzetí služeb při selhání SAP ASC nebo SCS instance a replikace SIOS][sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl].

## <a name="next-steps"></a>Další postup

- [Omezení sítě: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Nástroj pro vyrovnávání zatížení několika virtuálními IP adresami pro Azure.][load-balancer-multivip-overview]
