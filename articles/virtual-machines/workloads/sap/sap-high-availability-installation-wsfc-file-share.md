---
title: SAP NetWeaver instalaci vysoké dostupnosti na Windows převzetí služeb při selhání clusteru a sdílenou složku pro SAP ASCS/SCS instance v Azure | Dokumentace Microsoftu
description: SAP NetWeaver instalaci vysoké dostupnosti na Windows převzetí služeb při selhání clusteru a sdílenou složku pro instance SAP ASCS/SCS
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 71296618-673b-4093-ab17-b7a80df6e9ac
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4f9628be1d1f1d146ed0dbc5ebd9579f0512aeac
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997365"
---
# <a name="install-sap-netweaver-high-availability-on-a-windows-failover-cluster-and-file-share-for-sap-ascsscs-instances-on-azure"></a>Vysoká dostupnost SAP NetWeaver nainstalovat Windows převzetí služeb při selhání clusteru a sdílenou složku pro SAP ASCS/SCS instance v Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1596496]:https://launchpad.support.sap.com/#/notes/1596496

[sap-installation-guides]:http://service.sap.com/instguides

[sap-powershell-scrips]:https://github.com/Azure-Samples/sap-powershell

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[sofs-overview]:https://technet.microsoft.com/library/hh831349(v=ws.11).aspx
[new-in-win-2016-storage]:https://docs.microsoft.com/windows-server/storage/whats-new-in-storage

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[sap-blog-new-sap-cluster-resource-dll]:https://blogs.sap.com/2017/08/28/new-sap-cluster-resource-dll-is-available/
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host]:sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-official-ha-file-share-document]:https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfigurace vysoké dostupnosti SAP s několika SID)


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

Tento článek popisuje postup instalace a konfigurace vysoké dostupnosti systému SAP v Azure s Windows Server Failover Cluster (WSFC) a souborový Server se Škálováním jako možnost pro clusteringu SAP ASCS/SCS instancí.

## <a name="prerequisites"></a>Požadavky

Před zahájením instalace, najdete v následujících článcích:

* [Průvodce architekturou: Instance SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdílené složky][sap-high-availability-guide-wsfc-file-share]

* [Připravit infrastrukturu Azure SAP vysokou dostupnost pomocí Windows převzetí služeb při selhání clusteru a sdílenou složku pro instance SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-file-share]

Budete potřebovat následující spustitelných souborů a knihoven DLL od SAPU:
* SAP Software zřizování správce (SWPM) verze nástroje instalace SPS21 nebo novější.
* Stáhněte si nejnovější NTCLUST. Archiv – zvláštní administrativní oblast se nové SAP knihovny DLL prostředku clusteru. Nové SAP clusteru podpora vysoké dostupnosti SAP ASCS/SCS se sdílenou složkou knihovny DLL v clusteru převzetí služeb při selhání Windows serveru.

  Další informace o nový prostředek clusteru SAP knihovny DLL najdete v článku tohoto blogu: [Nový prostředek clusteru SAP knihovna DLL je k dispozici! ][sap-blog-new-sap-cluster-resource-dll].

Protože nastavení se liší v závislosti na DBMS použijete jsme popsané instalační program systému správy databáze (DBMS). Však předpokládáme, že aspekty vysoké dostupnosti se systémem DBMS se tak vyřeší, pomocí funkce, které podporují různí dodavatelé DBMS pro Azure. Tyto funkce patří AlwaysOn nebo zrcadlení databáze pro SQL Server a Oracle Data Guard pro databáze Oracle. Ve scénáři, které používáme v tomto článku jsme neměli přidat další ochranu správce databáze.

Nejsou žádné zvláštní požadavky při různých DBMS služby komunikují se tento druh Clusterované konfigurace SAP ASCS/SCS v Azure.

> [!NOTE]
> Postupy instalace systémů SAP NetWeaver ABAP, Java systémy a systémy ABAP + Java jsou téměř shodné. Nejdůležitější rozdíl je, že systém SAP ABAP má jednu instanci ASC. Systém SAP Java obsahuje jednu instanci SCS. Systém SAP ABAP + Java obsahuje jednu instanci ASCS a jednu instanci SCS spuštěné ve stejné skupině clusteru převzetí služeb při selhání Microsoft. Případné rozdíly instalace pro každý zásobník instalace SAP NetWeaver jsou explicitně uvedena. Můžete předpokládat, že všechny ostatní části jsou stejné.  
>
>

## <a name="install-an-ascsscs-instance-on-an-ascsscs-cluster"></a>Nainstalujte instanci ASCS/SCS v clusteru ASC/SCS

> [!IMPORTANT]
>
> V současné době nepodporuje nastavení vysoké dostupnosti s konfigurace sdílené složky souborů nástroj pro instalaci SAP SWPM. Některé ruční přijetí je proto nutné pro instalaci systému SAP (například k instalaci a cluster instanci SAP ASCS/SCS a konfiguraci samostatné hostitele globální SAP).  
>
> Není žádná změna v další instalační kroky instalace (a cluster) DBMS instance a aplikační servery SAP.
>

### <a name="install-an-ascsscs-instance-on-your-local-drive"></a>Nainstalujte instanci ASCS/SCS na místním disku

Nainstalujte instanci SAP ASCS/SCS *obě* uzly clusteru ASC/SCS. Nainstalujte ji na místní disk. V našem příkladu je místní disk C:\\, ale můžete použít jakékoli místní jednotce.  

Nainstalujte instanci, v nástroji SAP SWPM instalaci, přejděte na:

**\<Produkt >** > **\<DBMS >** > **instalace** > **aplikační Server ABAP** () nebo **Java**) > **distribuovaného systému** > **instanci ASCS/SCS**

> [!IMPORTANT]
> Scénář sdílenou složku v současné době nepodporuje instalační nástroj SAP SWPM. Můžete *nelze použít* následující instalační cesta:
>
> **\<Produkt >** > **\<DBMS >** > **instalace** > **aplikační Server ABAP** () nebo **Java**) > **vysoké dostupnosti systému** >...
>

### <a name="remove-sapmnt-and-create-an-saploc-file-share"></a>Odebrat SAPMNT a vytvořit sdílenou složku SAPLOC

SWMP do SAPMNT místní sdílené složky vytvořené v jednotce C:\\usr\\sap složky.

Odeberte sdílenou SAPMNT na *obě* ASCS/SCS uzly clusteru.

Spusťte následující skript prostředí PowerShell:

```PowerShell
Remove-SmbShare sapmnt -ScopeName * -Force
 ```

Pokud SAPLOC sdílená složka neexistuje, vytvořte ho na *obě* ASCS/SCS uzly clusteru.

Spusťte následující skript prostředí PowerShell:

```PowerShell
#Create SAPLOC share and set security
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"
$HostName = $env:computername
$SAPLocalAdminGroupName = "$HostName\SAP_LocalAdmin"
$SAPDisk = "C:"
$SAPusrSapPath = "$SAPDisk\usr\sap"

New-SmbShare -Name saploc -Path c:\usr\sap -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName , $SAPLocalAdminGroupName  
 ```

## <a name="prepare-an-sap-global-host-on-the-sofs-cluster"></a>Příprava SAP globální hostitele v clusteru SOFS

Vytvořte následující svazek a sdílenou složku v clusteru SOFS:

* Soubor SAP GLOBALHOST C:\ClusterStorage\Volume1\usr\sap\\<SID>\SYS\ struktura v clusteru SOFS sdílený svazek (CSV)

* SAPMNT sdílené složky

* Na SAPMNT sdílení souborů a složek s úplnou kontrolou pro nastavení zabezpečení:
    * \<Domény > \SAP_\<SID > _GlobalAdmin skupiny uživatelů
    * Objekty počítačů uzlu clusteru SAP ASCS/SCS \<domény > \ClusterNode1$ a \<domény > \ClusterNode2$

Vytvoření Sdíleného svazku clusteru s odolností, spusťte následující rutinu Powershellu na jednom uzlu clusteru SOFS:


```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName SAPPR1 -FileSystem CSVFS_ReFS -Size 5GB -ResiliencySettingName Mirror
```
Vytvořit SAPMNT a nastavit zabezpečení složky a sdílené složky, spusťte následující skript prostředí PowerShell na jednotlivé uzly clusteru SOFS:

```PowerShell
# Create SAPMNT on file share
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# SAP ASCS/SCS cluster nodes
$ASCSClusterNode1 = "ascs-1"
$ASCSClusterNode2 = "ascs-2"

# Define SAP ASCS/SCS cluster node computer objects
$ASCSClusterObjectNode1 = "$DomainName\$ASCSClusterNode1$"
$ASCSClusterObjectNode2 = "$DomainName\$ASCSClusterNode2$"

# Create usr\sap\.. folders on CSV
$SAPGlobalFolder = "C:\ClusterStorage\Volume1\usr\sap\$SAPSID\SYS"
New-Item -Path $SAPGlobalFOlder -ItemType Directory

$UsrSAPFolder = "C:\ClusterStorage\Volume1\usr\sap\"

# Create a SAPMNT file share and set share security
New-SmbShare -Name sapmnt -Path $UsrSAPFolder -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName, $ASCSClusterObjectNode1, $ASCSClusterObjectNode2 -ContinuouslyAvailable $false -CachingMode None -Verbose

# Get SAPMNT file share security settings
Get-SmbShareAccess sapmnt

# Set file and folder security
$Acl = Get-Acl $UsrSAPFolder

# Add a file security object of SAP_<sid>_GlobalAdmin group
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($SAPSIDGlobalAdminGroupName,"FullControl", 'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add  a security object of the clusternode1$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode1,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add a security object of the clusternode2$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode2,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Set security
Set-Acl $UsrSAPFolder $Acl -Verbose
 ```
## <a name="stop-ascsscs-instances-and-sap-services"></a>Zastavení instance ASCS/SCS a služby SAP

Proveďte následující kroky:
1. Zastavování instancí SAP ASCS/SCS na oba uzly clusteru ASC/SCS.
2. Zastavení služeb Windows SAP ASCS/SCS **SAP\<SID > _\<číslo instance >** na oba uzly clusteru.

## <a name="move-the-sys-folder-to-the-sofs-cluster"></a>Přesunout \SYS\... složky do clusteru SOFS

Proveďte následující kroky:
1. Zkopírujte složku SYS (například C:\usr\sap\\<SID>\SYS) z jednoho z ASC/SCS uzly clusteru do clusteru SOFS (třeba kvůli C:\ClusterStorage\Volume1\usr\sap\\<SID>\SYS).
2. Odstranit C:\usr\sap\\<SID>\SYS složku z obou uzlů clusteru ASC/SCS.

## <a name="update-the-cluster-security-setting-on-the-sap-ascsscs-cluster"></a>Aktualizace nastavení zabezpečení clusteru v clusteru SAP ASCS/SCS

Spusťte následující příkaz powershellu na jednom uzlu clusteru SAP ASCS/SCS:

```PowerShell
# Grant <DOMAIN>\SAP_<SID>_GlobalAdmin group access to the cluster

$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# Set full access for the <DOMAIN>\SAP_<SID>_GlobalAdmin group
Grant-ClusterAccess -User $SAPSIDGlobalAdminGroupName -Full

#Check security settings
Get-ClusterAccess
```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>Vytvořte virtuální hostitel název Clusterové instance SAP ASCS/SCS

Vytvoření názvu sítě clusteru SAP ASCS/SCS (například **pr1-ascs [10.0.6.7]**), jak je popsáno v [vytvořte virtuální hostitel název Clusterové instance SAP ASCS/SCS] [ sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host] .

## <a name="update-the-default-and-sap-ascsscs-instance-profile"></a>Aktualizovat výchozí a profil instance SAP ASCS/SCS

K použití nového názvu SAP ASCS/SCS virtuálního hostitele a SAP název globální hostitele, je nutné aktualizovat výchozí a profil instance SAP ASCS/SCS \<SID >_ASCS/SCS\<Nr >_<Host>.


| Staré hodnoty |  |
| --- | --- |
| Název hostitele SAP ASCS/SCS = globální hostitele SAP | ascs-1 |
| Název profilu instance SAP ASCS/SCS | PR1_ASCS00_ascs-1 |

| Nové hodnoty |  |
| --- | --- |
| Název hostitele SAP ASCS/SCS | **pr1-ascs** |
| Globální hostitele SAP | **sapglobal** |
| Název profilu instance SAP ASCS/SCS | PR1\_ASCS00\_**pr1 ASC** |

### <a name="update-sap-default-profile"></a>Aktualizovat profil výchozí SAP


| Název parametru | Hodnota parametru |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| rdisp/mshost | **pr1-ascs** |
| modul/serverhost | **pr1-ascs** |

### <a name="update-the-sap-ascsscs-instance-profile"></a>Aktualizovat profil instance SAP ASCS/SCS

| Název parametru | Hodnota parametru |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| DIR_PROFILE | \\\sapglobal\sapmnt\PR1\SYS\profile |
| _PF | $(DIR_PROFILE) \PR1\_ASCS00_ pr1-ASC |
| Restart_Program_02 = local$(_MS) pf=$(_PF) | **Start**_Program_02 = local$(_MS) pf=$(_PF) |
| SAPLOCALHOST | **pr1-ascs** |
| Restart_Program_03 = local$(_EN) pf=$(_PF) | **Start**_Program_03 = local$(_EN) pf=$(_PF) |
| gw/netstat_once | **0** |
| enque/encni/set_so_keepalive  | **Hodnota TRUE** |
| Služba/ha_check_node | **1** |

> [!IMPORTANT]
>Můžete použít **aktualizace SAPASCSSCSProfile** rutiny Powershellu pro automatizaci aktualizace profilu.
>
>Rutiny prostředí PowerShell podporuje instance SAP ABAP ASCS i SAP Java SCS.
>

Kopírování [ **SAPScripts.psm1** ] [ sap-powershell-scrips] s vaší místní jednotky C:\tmp a spusťte následující rutinu Powershellu:

```PowerShell
Import-Module C:\tmp\SAPScripts.psm1

Update-SAPASCSSCSProfile -PathToAscsScsInstanceProfile \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_ascs-1 -NewASCSHostName pr1-ascs -NewSAPGlobalHostName sapglobal -Verbose  
```

![Obrázek 1: SAPScripts.psm1 výstupu][sap-ha-guide-figure-8012]

_**Obrázek 1**: SAPScripts.psm1 výstupu_

## <a name="update-the-sidadm-user-environment-variable"></a>Aktualizace \<sid > ADM – proměnná prostředí uživatele

1. Aktualizace \<sid > adm uživatelského prostředí nové GLOBALHOST cestou UNC na *obě* ASCS/SCS uzly clusteru.
2. Přihlaste se jako \<sid > adm uživatele, a poté spusťte nástroj Regedit.exe.
3. Přejděte na **HKEY_CURRENT_USER** > **prostředí**a následně neaktualizují na novou hodnotu proměnné:

| Proměnná | Hodnota |
| --- | --- |
| RSEC_SSFS_DATAPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\data |
| RSEC_SSFS_KEYPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\key |
| SAPEXE | \\\\**sapglobal**\sapmnt\PR1\SYS\exe\uc\NTAMD64 |
| SAPLOCALHOST  | **pr1-ascs** |


## <a name="install-a-new-saprcdll-file"></a>Nainstalujte nový soubor saprc.dll

1. Instalace nové verze SAP prostředku clusteru, který podporuje scénář sdílenou složku.

2. Stáhněte si nejnovější NTCLUST. – Zvláštní administrativní oblast balíčku ze SAP Service Marketplace.

3. Rozbalte NTCLUS. – Zvláštní administrativní oblast na jednom z ASC/SCS uzly clusteru a pak spusťte následující příkaz z příkazového řádku pro instalaci nové saprc.dll souboru:

```
.\NTCLUST\insaprct.exe -yes -install
```

Nový soubor saprc.dll se nainstaluje do obou uzlů clusteru ASC/SCS.

Další informace najdete v tématu [1596496 Poznámka SAP – jak aktualizovat typ prostředku SAP knihovny DLL pro sledování prostředků clusteru][1596496].

## <a name="create-a-sap-sid-cluster-group-network-name-and-ip"></a>Vytvoření SAP <SID> skupiny, síťový název a IP clusteru

Chcete-li vytvořit SAP \<SID > Skupina clusteru, název sítě ASCS/SCS a odpovídající IP adresy, spusťte následující rutinu Powershellu:

```PowerShell
# Create SAP Cluster Group
$SAPSID = "PR1"
$SAPClusterGroupName = "SAP $SAPSID"
$SAPIPClusterResourceName = "SAP $SAPSID IP"
$SAPASCSNetworkName = "pr1-ascs"
$SAPASCSIPAddress = "10.0.6.7"
$SAPASCSSubnetMask = "255.255.255.0"

# Create an SAP ASCS instance virtual IP cluster resource
Add-ClusterGroup -Name $SAPClusterGroupName -Verbose

#Create an SAP ASCS virtual IP address
$SAPIPClusterResource = Add-ClusterResource -Name $SAPIPClusterResourceName -ResourceType "IP Address" -Group $SAPClusterGroupName -Verbose

# Set a static IP address
$param1 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,Address,$SAPASCSIPAddress
$param2 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,SubnetMask,$SAPASCSSubnetMask
$params = $param1,$param2
$params | Set-ClusterParameter

# Create a corresponding network name
$SAPNetworkNameClusterResourceName = $SAPASCSNetworkName
Add-ClusterResource -Name $SAPNetworkNameClusterResourceName -ResourceType "Network Name" -Group $SAPClusterGroupName -Verbose

# Set a network DNS name
$SAPNetworkNameClusterResource = Get-ClusterResource $SAPNetworkNameClusterResourceName
$SAPNetworkNameClusterResource | Set-ClusterParameter -Name Name -Value $SAPASCSNetworkName

#Check the updated values
$SAPNetworkNameClusterResource | Get-ClusterParameter

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPNetworkNameClusterResourceName -Dependency "[$SAPIPClusterResourceName]" -Verbose

#Start an SAP <SID> cluster group
Start-ClusterGroup -Name $SAPClusterGroupName -Verbose
```

## <a name="register-the-sap-start-service-on-both-nodes"></a>Zaregistrovat službu spuštění SAP na oba uzly

Opětovná registrace služby spuštění SAP ASCS/SCS tak, aby odkazovala na nový profil a cesta k profilu.

Opětovná registrace musí být spuštěn na *obě* ASCS/SCS uzly clusteru.

V příkazovém řádku se zvýšenými oprávněními spusťte následující příkaz:

```
C:\usr\sap\PR1\ASCS00\exe\sapstartsrv.exe -r -p \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_pr1-ascs -s PR1 -n 00 -U SAPCLUSTER\SAPServicePR1 -P mypasswd12 -e SAPCLUSTER\pr1adm
```

![Obrázek 2: Opětovná instalace služby SAP][sap-ha-guide-figure-8013]

_**Obrázek 2**: Opětovná instalace služby SAP_

Ujistěte se, že jsou správné parametry a potom vyberte **ruční** jako **typ spouštění**.

## <a name="stop-the-ascsscs-service"></a>Zastavit službu ASCS/SCS

Zastavit službu SAP ASCS/SCS SAP\<SID > _\<číslo instance > na obou ASCS/SCS uzly clusteru.

## <a name="create-a-new-sap-service-and-sap-instance-resources"></a>Vytvoření nové služby SAP a SAP instance prostředků

Pro dokončení vytváření prostředků SAP SAP\<SID > Skupina clusteru, vytvoříte následující prostředky:

* SAP \<SID > \<číslo instance > služby
* SAP \<SID > \<číslo instance > instance

Spusťte následující rutinu Powershellu:

```PowerShell
$SAPSID = "PR1"
$SAPInstanceNumber = "00"
$SAPNetworkNameClusterResourceName = "pr1-ascs"

$SAPServiceName = "SAP$SAPSID"+ "_" + $SAPInstanceNumber

$SAPClusterGroupName = "SAP $SAPSID"
$SAPServiceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Service"

$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPServiceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Service" -SeparateMonitor -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name ServiceName -Value $SAPServiceName

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPNetworkNameClusterResourceName]" -Verbose

$SAPInstanceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Instance"

# Create SAP instance cluster resource
$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPInstanceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Resource" -SeparateMonitor -Verbose

#Set SAP instance cluster resource parameters
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystemName -Value $SAPSID -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystem -Value $SAPInstanceNumber -Verbose

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPServiceClusterResourceName]" -Verbose
```

## <a name="add-a-probe-port"></a>Přidejte port testu

Konfigurace portu sondy SAP. identifikátor SID IP prostředek clusteru SAP pomocí prostředí PowerShell. Spustit tuto konfiguraci na jeden z uzlů clusteru SAP ASCS/SCS, jak je popsáno [v tomto článku][sap-high-availability-installation-wsfc-shared-disk-add-probe-port].

## <a name="install-an-ers-instance-on-both-cluster-nodes"></a>Nainstalujte instanci Lajících v obou uzlů clusteru

Nainstalovat instanci serveru replikace zařadit do fronty (Lajících) *obě* uzly clusteru ASC/SCS. V nabídce SWPM podle cesta této instalace:

**\<Produkt >** > **\<DBMS >** > **instalace** > **instance další systému SAP**  >  **Instanci serveru replikace zařadit do fronty**

## <a name="install-a-dbms-instance-and-sap-application-servers"></a>Nainstalujte instanci databázového systému a aplikační servery SAP

Dokončení instalace systému SAP při instalaci:
* Instance databázového systému.
* Primární server aplikace SAP.
* Další aplikační server SAP.

## <a name="next-steps"></a>Další postup

* [Nainstalovat ASCS/SCS instance clusteru převzetí služeb při selhání se žádné sdílené disky – oficiální SAP pokyny pro vysokou dostupnost sdílené složky][sap-official-ha-file-share-document]

* [Prostory úložiště s přímým přístupem ve Windows serveru 2016][s2d-in-win-2016]

* [Souborový Server se Škálováním pro data aplikací – přehled][sofs-overview]

* [Co je nového v úložišti ve Windows serveru 2016][new-in-win-2016-storage]
