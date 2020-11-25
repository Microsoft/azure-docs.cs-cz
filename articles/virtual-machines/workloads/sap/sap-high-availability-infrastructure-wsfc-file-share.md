---
title: Infrastruktura Azure pro SAP ASCS/SCS HA s&sdílenou složkou souborů služby WSFC | Microsoft Docs
description: Příprava infrastruktury Azure pro SAP s vysokou dostupností pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složky pro instance SAP ASCS/SCS
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 2ce38add-1078-4bb9-a1da-6f407a9bc910
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 62803bd450db351290bbc12d650d23a4148a4536
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022339"
---
# <a name="prepare-azure-infrastructure-for-sap-high-availability-by-using-a-windows-failover-cluster-and-file-share-for-sap-ascsscs-instances"></a>Příprava infrastruktury Azure na vysokou dostupnost pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složky pro instance SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[ms-blog-s2d-in-azure]:https://blogs.technet.microsoft.com/filecab/2016/05/05/s2dazuretp5/
[arm-sofs-s2d-managed-disks]:https://github.com/robotechredmond/301-storage-spaces-direct-md
[arm-sofs-s2d-non-managed-disks]:https://github.com/Azure/azure-quickstart-templates/tree/master/301-storage-spaces-direct
[deploy-cloud-witness]:https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[sap-installation-guides]:http://service.sap.com/instguides

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
[sap-high-availability-infrastructure-wsfc-shared-disk-default-ascs-ilb-rules]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-change-ascs-ilb-rules]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-add-win-domain]:sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c
[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md

[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f



[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfigurace s vysokou dostupností pro SAP multi-SID)


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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

Tento článek popisuje kroky přípravy infrastruktury Azure, které jsou potřeba k instalaci a konfiguraci systémů SAP s vysokou dostupností v clusteru služby Windows Server failover cluster (WSFC) pomocí sdílené složky se škálováním na více systémů jako možnosti clusteringu instancí SAP ASCS/SCS.

## <a name="prerequisite"></a>Požadavek

Než začnete s instalací, přečtěte si následující článek:

* [Průvodce architekturou: cluster SAP ASCS/SCS Instances v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdílené složky][sap-high-availability-guide-wsfc-file-share]


## <a name="host-names-and-ip-addresses"></a>Názvy hostitelů a IP adresy

| Role názvu virtuálního hostitele | Název virtuálního hostitele | Statická IP adresa | Skupina dostupnosti |
| --- | --- | --- | --- |
| Cluster ASCS/SCS prvního uzlu clusteru | ASCS-1 | 10.0.6.4 | ASCS jako |
| Druhý cluster node ASCS/SCS | ASCS – 2 | 10.0.6.5 | ASCS jako |
| Název sítě s clustery |ASCS – CL | 10.0.6.6 | neuvedeno |
| Název sítě clusteru SAP PR1 ASCS |PR1 – ASCS | 10.0.6.7 | neuvedeno |


**Tabulka 1**: cluster ASCS/SCS

| SLUŽBÁCH \<SID> | Číslo instance SAP ASCS/SCS |
| --- | --- |
| PR1 | 00 |

**Tabulka 2**: podrobnosti o instanci SAP ASCS/SCS


| Role názvu virtuálního hostitele | Název virtuálního hostitele | Statická IP adresa | Skupina dostupnosti |
| --- | --- | --- | --- |
| První uzel clusteru | SOFS-1 | 10.0.6.10 | SOFS jako |
| Druhý uzel clusteru | SOFS – 2 | 10.0.6.11 | SOFS jako |
| Třetí uzel clusteru | SOFS-3 | 10.0.6.12 | SOFS jako |
| Název sítě s clustery | SOFS – CL | 10.0.6.13 | neuvedeno |
| Název globálního hostitele SAP | sapglobal | Použít IP adresy všech uzlů clusteru | neuvedeno |

**Tabulka 3**: Scale-Out clusteru souborových serverů


## <a name="deploy-vms-for-an-sap-ascsscs-cluster-a-database-management-system-dbms-cluster-and-sap-application-server-instances"></a>Nasazení virtuálních počítačů pro cluster SAP ASCS/SCS, cluster systému správy databáze (DBMS) a instance aplikačního serveru SAP

K přípravě infrastruktury Azure proveďte následující:

* [Nasaďte virtuální počítače][sap-high-availability-infrastructure-wsfc-shared-disk].

* [Vytvoření a konfigurace nástroje pro vyrovnávání zatížení Azure pro SAP ASCS][sap-high-availability-infrastructure-wsfc-shared-disk-default-ascs-ilb-rules].

* [Pokud používáte server replikace fronty 2 (ERS2), proveďte konfiguraci Azure Load Balancer pro ERS2 ][sap-high-availability-infrastructure-wsfc-shared-disk-default-ascs-ilb-rules]. 

* [Přidejte virtuální počítače s Windows do domény][sap-high-availability-infrastructure-wsfc-shared-disk-add-win-domain].

* [Přidejte položky registru na oba uzly clusteru instance SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk-add-win-domain].

* Při použití Windows serveru 2016 doporučujeme nakonfigurovat [sdílené složky Azure v cloudu][deploy-cloud-witness].


## <a name="deploy-the-scale-out-file-server-cluster-manually"></a>Ruční nasazení clusteru souborových serverů Scale-Out 

Cluster souborových serverů Microsoft Scale-Out můžete nasadit ručně, jak je popsáno v blogu [prostory úložiště s přímým přístupem v Azure][ms-blog-s2d-in-azure], spuštěním následujícího kódu:  


```powershell
# Set an execution policy - all cluster nodes
Set-ExecutionPolicy Unrestricted

# Define Scale-Out File Server cluster nodes
$nodes = ("sofs-1", "sofs-2", "sofs-3")

# Add cluster and Scale-Out File Server features
Invoke-Command $nodes {Install-WindowsFeature Failover-Clustering, FS-FileServer -IncludeAllSubFeature -IncludeManagementTools -Verbose}

# Test cluster
Test-Cluster -node $nodes -Verbose

# Install cluster
$ClusterNetworkName = "sofs-cl"
$ClusterIP = "10.0.6.13"
New-Cluster -Name $ClusterNetworkName -Node $nodes –NoStorage –StaticAddress $ClusterIP -Verbose

# Set Azure Quorum
Set-ClusterQuorum –CloudWitness –AccountName gorcloudwitness -AccessKey <YourAzureStorageAccessKey>

# Enable Storage Spaces Direct
Enable-ClusterS2D

# Create Scale-Out File Server with an SAP global host name
# SAPGlobalHostName
$SAPGlobalHostName = "sapglobal"
Add-ClusterScaleOutFileServerRole -Name $SAPGlobalHostName
```

## <a name="deploy-scale-out-file-server-automatically"></a>Automaticky nasadit Scale-Out souborový server

Nasazení Scale-Outho souborového serveru můžete taky automatizovat pomocí šablon Azure Resource Manager v existující virtuální síti a prostředí Active Directory.

> [!IMPORTANT]
> Doporučujeme, abyste měli k dispozici tři nebo více uzlů clusteru pro Scale-Out souborový server s třícestným zrcadlením.
>
> V uživatelském rozhraní šablony Scale-Out Správce prostředků souborového serveru musíte zadat počet virtuálních počítačů.
>

### <a name="use-managed-disks"></a>Použití spravovaných disků

Azure Resource Manager šablona pro nasazení Scale-Out souborového serveru s Prostory úložiště s přímým přístupem a Managed Disks Azure je k dispozici na [GitHubu][arm-sofs-s2d-managed-disks].

Doporučujeme použít Managed Disks.

![Obrázek 1: obrazovka uživatelského rozhraní pro Scale-Out souborového serveru Správce prostředků šablona se spravovanými disky][sap-ha-guide-figure-8010]

_**Obrázek 1**: obrazovka uživatelského rozhraní pro Scale-Out souborového serveru správce prostředků šablona se spravovanými disky_

V šabloně udělejte toto:
1. V poli **počet virtuálních počítačů** zadejte minimální počet **2**.
2. Do pole **počet disků virtuálního počítače** zadejte minimální počet disků **3** (2 disky + 1 disk = 3 disky).
3. Do pole **název SOFS** zadejte název sítě globálního hostitele SAP, **sapglobalhost**.
4. Do pole **název sdílené složky** zadejte název sdílené složky **sapmnt**.

### <a name="use-unmanaged-disks"></a>Použít nespravované disky

Azure Resource Manager šablona pro nasazení Scale-Out souborového serveru s Prostory úložiště s přímým přístupem a nespravovanými disky Azure je k dispozici na [GitHubu][arm-sofs-s2d-non-managed-disks].

![Obrázek 2: obrazovka uživatelského rozhraní pro Scale-Out souborového serveru Azure Resource Manager šablony bez spravovaných disků][sap-ha-guide-figure-8011]

_**Obrázek 2**: obrazovka uživatelského rozhraní pro Scale-Out souborového serveru Azure Resource Manager šablony bez spravovaných disků_

V poli **typ účtu úložiště** vyberte **Premium Storage**. Všechna ostatní nastavení se shodují s nastavením pro služby Managed disks.

## <a name="adjust-cluster-timeout-settings"></a>Úprava nastavení časového limitu clusteru

Po úspěšné instalaci serveru Windows Scale-Out clusteru souborových serverů Přizpůsobte prahové hodnoty časového limitu pro detekci převzetí služeb při selhání na podmínky v Azure. Parametry, které se mají změnit, jsou popsané v části [ladění prahových hodnot sítě clusteru s podporou převzetí služeb][tuning-failover-cluster-network-thresholds] Za předpokladu, že jsou clusterové virtuální počítače ve stejné podsíti, změňte následující parametry na tyto hodnoty:

- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RouteHistoryLength = 30

Tato nastavení byla testována se zákazníky a nabízejí dobré zabezpečení. Jsou dostatečně odolné, ale také poskytují rychlé převzetí služeb při selhání v reálných stavových chybách nebo při selhání virtuálního počítače.

## <a name="next-steps"></a>Další kroky

* [Instalace vysoké dostupnosti SAP NetWeaver v clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složce pro instance SAP ASCS/SCS][sap-high-availability-installation-wsfc-file-share]
