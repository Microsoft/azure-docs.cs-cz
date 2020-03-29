---
title: Cluster SAP ASCS/SCS na WSFC pomocí sdílené složky v Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak clusterovat instanci SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdílené složky v Azure.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/24/2019
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 545bcd1fa521b945d822b7eb69945cf381bf480a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918661"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:../../dv2-dsv2-series.md
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general

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

[1869038]:https://launchpad.support.sap.com/#/notes/1869038 

# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-file-share-in-azure"></a>Cluster instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdílené složky v Azure

> ![Windows][Logo_Windows] Windows
>

Clustering s podporou převzetí služeb při selhání systému Windows Server je základem instalace SAP ASCS/SCS s vysokou dostupností a systému DBMS v systému Windows.

Cluster s podporou převzetí služeb při selhání je skupina 1+n nezávislých serverů (uzlů), které spolupracují na zvýšení dostupnosti aplikací a služeb. Pokud dojde k selhání uzlu, clustering s podporou převzetí služeb při selhání systému Windows Server vypočítá počet selhání, ke kterým může dojít, a stále udržuje cluster v pořádku za účelem poskytování aplikací a služeb. Můžete si vybrat z různých režimů kvora k dosažení clustering převzetí služeb při selhání.

## <a name="prerequisites"></a>Požadavky
Než začnete úkoly popsané v tomto článku, přečtěte si tento článek:

* [Architektura a scénáře azure virtuálních počítačů a scénáře pro SAP NetWeaver][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
> Clustering instance SAP ASCS/SCS pomocí sdílené složky je podporován pro SAP NetWeaver 7.40 (a novější), s jádrem SAP 7.49 (a novější).
>


## <a name="windows-server-failover-clustering-in-azure"></a>Clustering s podporou převzetí služeb při selhání windows serveru v Azure

Ve srovnání s nasazením úplného nebo privátního cloudu vyžadují virtuální počítače Azure další kroky ke konfiguraci clusteru s podporou převzetí služeb při selhání windows serveru. Při vytváření clusteru je třeba nastavit několik IP adres a názvů virtuálních hostitelů pro instanci SAP ASCS/SCS.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Překlad názvů v Azure a název virtuálního hostitele clusteru

Cloudová platforma Azure nenabízí možnost konfigurace virtuálních IP adres, jako jsou plovoucí IP adresy. Potřebujete alternativní řešení pro nastavení virtuální IP adresy pro dosažení prostředku clusteru v cloudu. 

Služba Azure Load Balancer poskytuje *interní vyrovnávání zatížení* pro Azure. S interním nástrojem pro vyrovnávání zatížení se klienti dostanou do clusteru přes virtuální IP adresu clusteru. 

Nasaďte interní vynakladač zatížení ve skupině prostředků, která obsahuje uzly clusteru. Potom nakonfigurujte všechna potřebná pravidla předávání portů pomocí portů sondy interního systému vyrovnávání zatížení. Klienti se mohou připojit prostřednictvím názvu virtuálního hostitele. Server DNS řeší adresu IP clusteru. Interní správce zatížení zpracovává předávání portů do aktivního uzlu clusteru.

![Obrázek 1: Konfigurace clusteru s podporou převzetí služeb při selhání windows serveru v Azure bez sdíleného disku][sap-ha-guide-figure-1001]

_**Obrázek 1:** Konfigurace clusteru s podporou převzetí služeb při selhání systému Windows Server v Azure bez sdíleného disku_

## <a name="sap-ascsscs-ha-with-file-share"></a>SAP ASCS/SCS HA se sdílením souborů

Společnost SAP vyvinula nový přístup a alternativu ke sdíleným diskům clusteru pro clusterování instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows. Namísto použití sdílených disků clusteru můžete použít sdílenou složku SMB k nasazení globálních hostitelských souborů SAP.

> [!NOTE]
> Sdílená složka SMB je alternativou k použití sdílených disků clusteru pro clusterování instancí SAP ASCS/SCS.  
>

Tato architektura je specifická následujícími způsoby:

* Centrální služby SAP (s vlastní strukturou souborů a procesy zpráv a zařazení do fronty) jsou odděleny od globálních hostitelských souborů SAP.
* Centrální služby SAP běží pod instancí SAP ASCS/SCS.
* Instance SAP ASCS/SCS je clusterovaná a \<je přístupná pomocí\> názvu virtuálního hostitele ASCS/SCS.
* Globální soubory SAP jsou umístěny ve sdílené složce \<SMB\> a jsou \\ \\ &lt;přístupné&gt;pomocí názvu globálního\\&lt;hostitele&gt;SAP:\.GLOBÁLNÍ HOSTITEL SAP \sapmnt SID \SYS ..
* Instance SAP ASCS/SCS je nainstalována na místním disku v obou uzlech clusteru.
* Název \<sítě virtuálního\> hostitele ASCS/SCS &lt;se&gt;liší od globálního hostitele SAP .

![Obrázek 2: Architektura SAP ASCS/SCS HA se sdílenou složkou SMB][sap-ha-guide-figure-8004]

_**Obrázek 2:** Nová architektura SAP ASCS/SCS HA se sdílenou složkou SMB_

Požadavky na sdílenou složku SMB:

* Protokol SMB 3.0 (nebo novější).
* Možnost nastavení seznamů řízení přístupu služby Active Directory (ACL) pro skupiny uživatelů služby Active Directory a objekt `computer$` počítače.
* Sdílená složka musí mít povolenou technologii HA:
    * Disky používané k ukládání souborů nesmí být jediným bodem selhání.
    * Prostoje serveru nebo virtuálního počítače nezpůsobí prostoje ve sdílené složce.

Role \<clusteru\> SAP SID neobsahuje sdílené disky clusteru ani obecný prostředek clusteru pro sdílení souborů.


![Obrázek 3: Prostředky role clusteru SAP \<SID\> pro použití sdílené složky][sap-ha-guide-figure-8005]

_**Obrázek 3:** Prostředky &lt;role&gt; clusteru SAP SID pro použití sdílené složky_


## <a name="scale-out-file-shares-with-storage-spaces-direct-in-azure-as-an-sapmnt-file-share"></a>Horizontální navýšení kapacity sdílených složek s storage spaces direct v Azure jako sdílená složka SAPMNT

Pomocí sdílené složky s horizontálním navýšením kapacity můžete hostovat a chránit globální hostitelské soubory SAP. Horizontální navýšení kapacity sdílené složky také nabízí vysoce dostupné SAPMNT sdílení souborů služby.

![Obrázek 4: Sdílení souborů s horizontálním navýšením kapacity používané k ochraně globálních hostitelských souborů SAP][sap-ha-guide-figure-8006]

_**Obrázek 4:** Sdílená složka s horizontálním navýšením kapacity používaná k ochraně globálních hostitelských souborů SAP_

> [!IMPORTANT]
> Sdílené složky s horizontálním navýšením kapacity jsou plně podporované v cloudu Microsoft Azure a v místních prostředích.
>

Sdílená složka s horizontálním navýšením kapacity nabízí vysoce dostupnou a horizontálně škálovatelnou sdílenou složku SAPMNT.

Storage Spaces Direct se používá jako sdílený disk pro sdílenou složku s horizontálním navýšením kapacity. Storage Spaces Direct můžete použít k vytvoření vysoce dostupného a škálovatelného úložiště pomocí serverů s místním úložištěm. Sdílené úložiště, které se používá pro sdílenou složku s horizontálním navýšením kapacity, například pro globální hostitelské soubory SAP, není jediným bodem selhání.

Při výběru úložiště prostory direct, zvažte tyto případy použití:

- Virtuální počítače používané k vytvoření clusteru Storage Spaces Direct je třeba nasadit v azure dostupnosti.
- Pro zotavení po havárii přímého clusteru prostorů úložiště můžete použít [služby Azure Site Recovery Services](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#replicated-machines---storage).
- Není podporováno roztažení clusteru Direct prostoru úložiště na příčku různých zón dostupnosti Azure.

### <a name="sap-prerequisites-for-scale-out-file-shares-in-azure"></a>Sap předpoklady pro horizontální navýšení kapacity sdílené složky v Azure

Chcete-li použít sdílenou složku s horizontálním navýšením kapacity, musí systém splňovat následující požadavky:

* Nejméně dva uzly clusteru pro sdílenou složku s horizontálním navýšením kapacity.
* Každý uzel musí mít alespoň dva místní disky.
* Z důvodu výkonu je nutné použít *zrcadlení odolnosti :*
    * Obousměrné zrcadlení pro sdílenou složku s horizontálním navýšením kapacity se dvěma uzly clusteru.
    * Třícestné zrcadlení pro sdílenou složku s horizontálním navýšením kapacity se třemi (nebo více) uzly clusteru.
* Doporučujeme tři (nebo více) uzlů clusteru pro sdílenou složku s horizontálním navýšením kapacity s třícestným zrcadlením.
    Toto nastavení nabízí větší škálovatelnost a větší odolnost úložiště než nastavení sdílené složky na horizontální navýšení kapacity se dvěma uzly clusteru a obousměrným zrcadlením.
* Disky Azure Premium je nutné použít.
* Doporučujeme používat spravované disky Azure.
* Doporučujeme formátovat svazky pomocí odolného systému souborů (ReFS).
    * Další informace naleznete v [tématu SAP Note 1869038 – podpora SAP pro souborový systém REFs][1869038] a v kapitole [Výběr systému souborů][planning-volumes-s2d-choosing-filesystem] v článku Plánování svazků v úložišti Direct.
    * Ujistěte se, že nainstalujete [kumulativní aktualizaci Microsoft KB4025334][kb4025334].
* Můžete použít velikosti virtuálních počítačů Azure řady DSnebo DSv2.
* Pro dobrý výkon sítě mezi virtuálními počítačemi, který je potřeba pro synchronizaci disku Storage Spaces Direct, použijte typ virtuálního počítače, který má alespoň "vysokou" šířku pásma sítě.
    Další informace naleznete ve specifikacích [řady DSv2][dv2-series] a [DS-Series.][ds-series]
* Doporučujeme rezervovat některé nepřidělené kapacity ve fondu úložiště. Ponechání některé nepřidělené kapacity ve fondu úložiště poskytuje svazky prostor pro opravu "na místě", pokud dojde k selhání jednotky. To zlepšuje bezpečnost dat a výkon.  Další informace naleznete v [tématu Výběr velikosti svazku][choosing-the-size-of-volumes-s2d].
* Není nutné konfigurovat Azure interní vyrovnávání zatížení pro horizontální navýšení kapacity název \<sítě sdílení\>souborů, například pro globálního hostitele SAP . To se provádí \<pro název\> virtuálního hostitele ASCS/SCS instance SAP ASCS/SCS nebo pro DBMS. Horizontální navýšení kapacity sdílené složky škáluje zatížení ve všech uzlech clusteru. \<Globální hostitel\> SAP používá místní IP adresu pro všechny uzly clusteru.


> [!IMPORTANT]
> Sdílenou složku SAPMNT nelze přejmenovat, \<což\>ukazuje na globálního hostitele SAP . SAP podporuje pouze název sdílené položky "sapmnt."
>
> Další informace naleznete v [tématu SAP Note 2492395 - Lze změnit název sdílené položky sapmnt?][2492395]

### <a name="configure-sap-ascsscs-instances-and-a-scale-out-file-share-in-two-clusters"></a>Konfigurace instancí SAP ASCS/SCS a sdílené složky s horizontálním navýšením kapacity ve dvou clusterech

Instance SAP ASCS/SCS můžete nasadit v jednom \<clusteru\> s vlastní rolí clusteru SAP SID. V takovém případě nakonfigurujete sdílenou složku horizontálního navýšení kapacity v jiném clusteru s jinou rolí clusteru.

> [!IMPORTANT]
>V tomto scénáři je instance SAP ASCS/SCS nakonfigurována pro \\ \\ &lt;přístup&gt;ke globálnímu\\&lt;hostiteli&gt;SAP pomocí globálního hostitele SAP cesty SAP \sapmnt SID \SYS\.
>

![Obrázek 5: Instance SAP ASCS/SCS a sdílená složka s horizontálním navýšením kapacity nasazené ve dvou clusterech][sap-ha-guide-figure-8007]

_**Obrázek 5:** Instance SAP ASCS/SCS a sdílená složka s horizontálním navýšením kapacity nasazené ve dvou clusterech_

> [!IMPORTANT]
> V cloudu Azure musí být každý cluster, který se používá pro SAP a sdílené složky s horizontálním navýšením kapacity, nasazený ve vlastní sadě dostupnosti Azure nebo napříč zónami dostupnosti Azure. Tím zajistíte distribuované umístění virtuálních počítačích clusteru napříč základní infrastrukturou Azure. Tato technologie podporuje nasazení zóny dostupnosti.
>

## <a name="generic-file-share-with-sios-datakeeper-as-cluster-shared-disks"></a>Obecná sdílená složka se službou SIOS DataKeeper jako sdílené disky clusteru


Obecná sdílená složka je další možností pro dosažení vysoce dostupné sdílené složky.

V takovém případě můžete použít řešení SIOS jiného výrobce jako sdílený disk clusteru.

## <a name="next-steps"></a>Další kroky

* [Příprava infrastruktury Azure pro SAP HA pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složky pro instanci SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-file-share]
* [Instalace sap netweaveru HA do clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složky pro instanci SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
* [Nasazení souborového serveru s horizontálním navýšením kapacity úložiště se dvěma uzny pro úložiště UPD v Azure][deploy-sofs-s2d-in-azure]
* [Prostory úložiště s přímým přístupem ve Windows Serveru 2016][s2d-in-win-2016]
* [Hloubkové prosazení: Svazky v úložných prostorech Direct][deep-dive-volumes-in-s2d]
