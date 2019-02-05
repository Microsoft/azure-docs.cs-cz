---
title: Instance SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdílené složky v Azure | Dokumentace Microsoftu
description: Zjistěte, jak seskupit do clusteru SAP ASCS/SCS instance clusteru převzetí služeb při selhání Windows s využitím sdílené složky v Azure.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6c45ca62c1b734dcbcf854fdea4aa44e603d3908
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55693820"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dv2-series
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general

[sap-installation-guides]:http://service.sap.com/instguides

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
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

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

[1869038]:https://launchpad.support.sap.com/#/notes/1869038 

# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-file-share-in-azure"></a>Instance SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdílené složky v Azure

> ![Windows][Logo_Windows] Windows
>

Windows Server, převzetí služeb při selhání clustering je základem instalace SAP ASCS/SCS vysokou dostupnost a DBMS ve Windows.

Cluster převzetí služeb při selhání je skupina 1 + n nezávislých serverů (uzly), které vzájemně spolupracují a zvyšují tak dostupnost aplikací a služeb. Pokud dojde k selhání uzlu, Windows serveru, převzetí služeb při selhání clustering vypočítá počet chyb, které může dojít a přitom zachovává cluster v pořádku pro poskytování aplikací a služeb. K dosažení clustering převzetí služeb při selhání můžete vybírat kvora různé režimy.

## <a name="prerequisites"></a>Požadavky
Než začnete s úkoly, které jsou popsané v tomto článku, projděte si tento článek:

* [Architektura vysoké dostupnosti služby Azure Virtual Machines a scénáře pro SAP NetWeaver][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
> Clusteringu SAP ASCS/SCS instance pomocí sdílené složky je podporováno pro SAP NetWeaver 7.40 (a novější), s 7.49 SAP jádra (nebo novější).
>


## <a name="windows-server-failover-clustering-in-azure"></a>Windows Server, převzetí služeb při selhání řízení clusterů v Azure

Ve srovnání s nasazení holých počítačů nebo privátní cloud, Azure Virtual Machines vyžaduje další kroky konfigurace clusteringu převzetí služeb při selhání Windows serveru. Při vytváření clusteru, je nutné nastavit několik IP adresy a názvy virtuálních hostitelů pro instanci SAP ASCS/SCS.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Překlad názvů v Azure a název virtuálního hostitele clusteru

Cloudové platformy Azure nenabízí možnost nakonfigurovat virtuální IP adresy, jako je například plovoucí IP adresy. Je nutné nastavit virtuální IP adresu prostředku clusteru v cloudu dosáhnout alternativní řešení. 

Služba Azure Load Balancer poskytuje *interního nástroje load balancer* pro Azure. Pomocí interní služby load balancer klienti oslovit clusteru přes virtuální IP adresu clusteru. 

Nasazení interního nástroje load balancer ve skupině prostředků, který obsahuje uzly clusteru. Nakonfigurujte všechny nezbytné port pravidla předávání s použitím sondy porty interní služby load balancer. Můžete se klienti připojují prostřednictvím název virtuálního hostitele. DNS server překládá IP adresu clusteru. Interní služby load balancer zajišťuje přesměrování portu do aktivního uzlu clusteru.

![Obrázek 1: Konfigurace systému Windows Server Failover Clustering v Azure bez sdíleného disku][sap-ha-guide-figure-1001]

_**Obrázek 1:** Windows Server, převzetí služeb při selhání clustering konfiguraci v Azure bez sdíleného disku_

## <a name="sap-ascsscs-ha-with-file-share"></a>SAP ASCS/SCS HA se sdílenou složkou

SAP vyvinuté nový přístup a alternativu sdílené disky pro clusteringu SAP ASCS/SCS instance clusteru převzetí služeb při selhání Windows v clusteru. Nemusíte používat cluster sdílených disků, můžete použít sdílené složky protokolu SMB pro nasazení SAP globální hostitelské soubory.

> [!NOTE]
> Sdílené složky protokolu SMB se o alternativu k použití clusteru sdílené disky pro clusteringu SAP ASCS/SCS instancí.  
>

Tato architektura se konkrétní následujícími způsoby:

* Centrální služby SAP (procesech svůj vlastní soubor strukturu a zpráv a zařadit do fronty) jsou oddělené od SAP globální hostitelské soubory.
* Centrální služby SAP běžet pod instanci SAP ASCS/SCS.
* Instanci SAP ASCS/SCS je v clusteru a je přístupný pomocí \<název virtuálního hostitele ASCS/SCS\> název virtuálního hostitele.
* SAP globální soubory jsou umístěny ve sdílené složce protokolu SMB a jsou přístupné pomocí \<globální hostitele SAP\> název hostitele: \\\\&lt;Globální hostitele SAP&gt;\sapmnt\\&lt;SID&gt;\SYS\....
* Instance SAP ASCS/SCS je nainstalovaná na místním disku, na oba uzly clusteru.
* \<Název virtuálního hostitele ASCS/SCS\> názvu sítě se liší od &lt;globální hostitele SAP&gt;.

![Obrázek 2: SAP ASCS/SCS HA architektury s využitím sdílené složky SMB][sap-ha-guide-figure-8004]

_**Obrázek 2:** Nové SAP ASCS/SCS HA architektury s využitím sdílené složky protokolu SMB_

Předpoklady pro sdílené složky SMB:

* Protokol SMB 3.0 (nebo novější).
* Možnost nastavit přístup k Active Directory seznamů řízení (ACL) pro skupiny uživatelů služby Active Directory a `computer$` objekt počítače.
* Sdílené složky musí být povolené HA:
    * Disky používané k ukládání souborů nesmí být kritickým prvkem způsobujícím selhání.
    * Server nebo výpadky virtuálních počítačů nezpůsobí výpadek ve sdílené složce.

SAP \<SID\> Clusterové role neobsahuje sdílenými disky nebo obecný prostředek sdílené složky clusteru.


![Obrázek 3: SAP \<SID\> Clusterové role materiály týkající se používání sdílené složky][sap-ha-guide-figure-8005]

_**Obrázek 3:** SAP &lt;SID&gt; Clusterové role materiály týkající se používání sdílené složky_


## <a name="scale-out-file-shares-with-storage-spaces-direct-in-azure-as-an-sapmnt-file-share"></a>Horizontální navýšení kapacity sdílených složek pomocí prostorů úložiště s přímým v Azure jako sdílenou složku SAPMNT

Horizontální navýšení kapacity sdílené složky můžete použít k hostování a chránit soubory globální hostitele SAP. Horizontální navýšení kapacity sdílené složky také nabízí vysoce dostupnou službu sdílené složky souboru SAPMNT.

![Obrázek 4: Horizontální navýšení kapacity sdílené složky použitým k zamknutí SAP globální hostitelské soubory][sap-ha-guide-figure-8006]

_**Obrázek 4:** Horizontální navýšení kapacity sdílené použitým k zamknutí SAP globální hostitelské soubory_

> [!IMPORTANT]
> Horizontální navýšení kapacity sdílené složky jsou plně podporovány v cloudu Microsoft Azure a v místních prostředích.
>

Horizontální navýšení kapacity sdílené nabízí vysoce dostupné a škálovatelné vodorovně SAPMNT sdílené složky.

Prostory úložiště – přímé se používá jako sdílený disk pro horizontální navýšení kapacity sdílené složky. Prostory úložiště – přímé můžete použít k vytvoření vysoce dostupné a škálovatelné úložiště pomocí serverů s místním úložištěm. Sdílené úložiště, který se používá pro horizontální navýšení kapacity sdílené složky, například pro SAP globální hostitelské soubory, není jediný bod selhání.

> [!IMPORTANT]
>Pokud jste *nejsou* plánujete nastavit zotavení po havárii doporučujeme používat horizontální navýšení kapacity sdílené složky jako řešení pro vysoce dostupné sdílené složky v Azure.
>

### <a name="sap-prerequisites-for-scale-out-file-shares-in-azure"></a>Požadavky na SAP pro horizontální navýšení kapacity sdílené složky v Azure

Určený horizontální navýšení kapacity sdílené složky systému musí splňovat následující požadavky:

* Alespoň dva uzly pro horizontální navýšení kapacity sdílené složky clusteru.
* Každý uzel musí mít alespoň dva místní disky.
* Z důvodu výkonu, je nutné použít *zrcadlení odolnosti proti chybám*:
    * Obousměrné zrcadlení pro horizontální navýšení kapacity sdílené složky se dvěma uzly clusteru.
    * Trojcestné zrcadlení pro horizontální navýšení kapacity sdílené s uzly clusteru (nejméně tři).
* Doporučujeme, tři (nebo více) uzlů clusteru pro horizontální navýšení kapacity sdílené s trojcestné zrcadlení.
    Tato instalace nabízí další škálovatelnost a odolnost další úložiště než instalace horizontální navýšení kapacity sdílených složek se dvěma uzly clusteru a dvoucestného zrcadlení.
* Je nutné použít Azure prémiové disky.
* Doporučujeme použít Azure Managed Disks.
* Doporučujeme formátovat svazky s využitím odolný systém souborů (ReFS).
    * Další informace najdete v tématu [1869038 Poznámka SAP - podpory SAP pro systém souborů ReFs] [ 1869038] a [výběr systému souborů] [ planning-volumes-s2d-choosing-filesystem] kapitoly Plánování svazky do prostorů úložiště s přímým článku.
    * Ujistěte se, že nainstalujete [kumulativní aktualizace pro Microsoft KB4025334][kb4025334].
* Velikosti řady DS nebo DSv2-Series virtuálních počítačů Azure můžete použít.
* Výkon dobré sítě mezi virtuálními počítači, které je potřeba pro prostory úložiště – přímé synchronizace disk, použijte typ virtuálního počítače, který má alespoň "vysoká" šířka pásma sítě.
    Další informace najdete v tématu [DSv2-Series] [ dv2-series] a [řady DS-Series] [ ds-series] specifikace.
* Doporučujeme, abyste si rezervovat některé volné kapacity ve fondu úložiště. Opuštění některé volné kapacity ve fondu úložiště poskytuje prostor svazky pro "místní oprava" proti selhání jednotky. To vylepšuje výkon a zabezpečení dat.  Další informace najdete v tématu [Volba velikosti svazku][choosing-the-size-of-volumes-s2d].
* Horizontální navýšení kapacity sdílené virtuální počítače Azure se musí nasadit ve vlastní sadě dostupnosti Azure.
* Není nutné ke konfiguraci nástroje pro vyrovnávání zatížení Azure interní název síťové sdílené složky souboru horizontální navýšení kapacity, například pro \<globální hostitele SAP\>. Týká se \<název virtuálního hostitele ASCS/SCS\> SAP ASCS/SCS instance nebo pro správce databáze. Horizontální navýšení kapacity sdílené horizontálně navyšuje jeho kapacita zatížení mezi všechny uzly clusteru. \<Globální hostitele SAP\> používá místní IP adresu pro všechny uzly clusteru.


> [!IMPORTANT]
> Nelze přejmenovat SAPMNT sdílené složky, která odkazuje na \<globální hostitele SAP\>. SAP se podporuje jenom sdílenou složku název "sapmnt."
>
> Další informace najdete v tématu [Poznámka SAP 2492395 – může mění sapmnt název sdílené složky?][2492395]

### <a name="configure-sap-ascsscs-instances-and-a-scale-out-file-share-in-two-clusters"></a>Konfigurace SAP ASCS/SCS instance a škálování na více instancí souboru sdílet ve dvou clusterech

Můžete nasadit instance SAP ASCS/SCS do jednoho clusteru, s vlastní SAP \<SID\> Clusterové role. V takovém případě konfigurovat sdílené složky souborů horizontální navýšení kapacity na jiném clusteru, s jinou rolí clusteru.

> [!IMPORTANT]
>V tomto scénáři je instanci SAP ASCS/SCS nakonfigurovaná pro přístup k hostiteli globální SAP pomocí cesty UNC \\ \\ &lt;globální hostitele SAP&gt;\sapmnt\\&lt;SID&gt;\SYS\.
>

![Obrázek 5: Instanci SAP ASCS/SCS a horizontální navýšení kapacity sdílené nasazené ve dvou clusterech][sap-ha-guide-figure-8007]

_**Obrázek 5:** Instance SAP ASCS/SCS a horizontální navýšení kapacity sdílené nasazené ve dvou clusterech_

> [!IMPORTANT]
> V cloudu Azure nastavte každý cluster, který se používá pro SAP a škálování na víc systémů souborů, které sdílené složky musí být nasazený ve vlastní skupině dostupnosti Azure. Tím se zajistí distribuované umístění clusteru virtuálních počítačů napříč základní infrastrukturu Azure.
>

## <a name="generic-file-share-with-sios-datakeeper-as-cluster-shared-disks"></a>Obecné sdílené složky se SIOS Datakeeperem jako cluster sdílených disků


> [!IMPORTANT]
> Doporučujeme, abyste řešení sdílené složky souboru horizontální navýšení kapacity pro vysoce dostupné sdílené složky.
>
> Pokud chcete také nastavit zotavení po havárii pro vaše vysoce dostupné sdílené složky, musíte použít obecné sdílené složky a SISO DataKeeper pro sdílenými disky.
>

Obecné sdílené složky je další možností pro zajištění vysoce dostupné sdílené složky.

V takovém případě můžete použít SIOS řešení třetí strany jako disk sdíleného clusteru.

## <a name="next-steps"></a>Další postup

* [Příprava infrastruktury Azure pro SAP HA pomocí Windows převzetí služeb při selhání clusteru a sdílenou složku pro instanci SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-file-share]
* [Instalace SAP NetWeaver HA ve Windows převzetí služeb při selhání clusteru a soubor sdílené složce pro instanci SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
* [Nasazení prostorů úložiště s přímým horizontální navýšení kapacity souborového serveru dvěma uzly UPD úložiště v Azure][deploy-sofs-s2d-in-azure]
* [Prostory úložiště s přímým přístupem ve Windows serveru 2016][s2d-in-win-2016]
* [Podrobné informace: Přímé svazky v prostorech úložiště][deep-dive-volumes-in-s2d]
