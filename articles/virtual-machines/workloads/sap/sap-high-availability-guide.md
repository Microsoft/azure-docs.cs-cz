---
title: Azure vysoké dostupnosti virtuálních počítačů pro SAP NetWeaver | Microsoft Docs
description: Průvodce vysokou dostupností pro SAP NetWeaver ve virtuálních počítačích Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
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
ms.openlocfilehash: e65f38b6fb4f5434c840af1866ccf09671111f3e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver"></a>Azure vysoké dostupnosti virtuálních počítačů pro SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfigurace s vysokou dostupností více SID SAP)


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



Virtuální počítače Azure je řešení pro organizace, které potřebují výpočty, úložiště a síťové prostředky ve minimálního čas a bez zdlouhavé nákup cykly. Virtuální počítače Azure můžete použít k nasazení classic aplikace jako na základě SAP NetWeaver ABAP, Java a ABAP + Java zásobníku. Rozšiřte spolehlivosti a dostupnosti bez dalších místních prostředků. Virtuální počítače Azure podporuje připojení mezi různými místy, takže virtuální počítače Azure můžete integrovat do místní domény vaší organizace, privátních cloudů a šířku systému SAP.

V tomto článku jsme zahrnují kroky, které můžete provést nasazení SAP systémů s vysokou dostupností v Azure pomocí modelu nasazení Azure Resource Manager. Můžeme vás provede procesem tyto hlavní úlohy:

* Nalezení správné SAP poznámky a příručky pro instalaci, uvedené v [prostředky] [ sap-ha-guide-2] části. Tento článek doplňuje SAP instalace dokumentace a poznámky k SAP, což jsou primární prostředky, které vám mohou pomoci instalace a nasazení SAP software na specifické platformy.
* Přečtěte si rozdíly mezi modelem nasazení Azure Resource Manager a modelu nasazení Azure classic.
* Další informace o Windows Server Failover Clustering režimů kvora, takže můžete vybrat model, který je nejvhodnější pro vaše nasazení Azure.
* Další informace o Windows Server Failover Clustering sdíleného úložiště do služby Azure.
* Zjistěte, jak pomoci chránit jeden bod o selhání součásti jako Advanced obchodní aplikace programování (ABAP) SAP centrální služby (ASC) / SAP centrální služby (SCS) a databázové systémy (databázového systému) a redundantní komponenty, například aplikace SAP Server v Azure.
* Postupujte podle podrobný příklad k instalaci a konfiguraci systému SAP vysoké dostupnosti v clusteru Windows Server Failover Clustering v Azure pomocí Azure Resource Manager.
* Další informace o dalších krocích, které jsou potřeba použít Windows Server Failover Clustering v Azure, ale které nejsou potřebné v místním nasazení.

Zjednodušit nasazení a konfigurace v tomto článku používáme šablony Resource Manageru SAP třívrstvá vysokou dostupnost. Šablony automatizovat nasazení celé infrastruktury, které potřebujete pro SAP systém vysoké dostupnosti. Infrastruktura podporuje také SAP aplikace výkonu standardní (protokoly SAP) velikosti vašeho systému SAP.

## <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Požadavky
Než začnete, ujistěte se, že splňujete požadavky, které jsou popsané v následujících částech. Také, nezapomeňte zkontrolovat všechny prostředky uvedené v [prostředky] [ sap-ha-guide-2] části.

V tomto článku používáme šablon Azure Resource Manageru pro [třívrstvá SAP NetWeaver pomocí disků spravované](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md/). Užitečné Přehled šablon naleznete v tématu [šablon SAP Azure Resource Manageru](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Prostředky
Tyto články popisuje nasazení SAP v Azure:

* [Azure virtuálních počítačů, plánování a implementace pro SAP NetWeaver][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP NetWeaver][deployment-guide]
* [Nasazení virtuálních počítačů databázového systému Azure pro SAP NetWeaver][dbms-guide]
* [Azure vysoké dostupnosti virtuálních počítačů pro SAP NetWeaver (Tato příručka)][sap-ha-guide]

> [!NOTE]
> Kdykoli je to možné, můžeme vám dát odkaz odkazující Průvodce instalací SAP (najdete v článku [SAP instalace příručky][sap-installation-guides]). Informace o procesu instalace a požadavky je vhodné si pozorně přečtěte příručky instalace SAP NetWeaver. Tento článek se týká pouze konkrétní úlohy pro počítače se systémem SAP NetWeaver, které můžete použít s virtuálními počítači Azure.
>
>

Tyto poznámky SAP souvisí s tématem SAP v Azure:

| Poznámka: číslo | Nadpis |
| --- | --- |
| [1928533] |Aplikace SAP v Azure: podporované produkty a velikosti |
| [2015553] |SAP na platformě Microsoft Azure: podporovat požadavky |
| [1999351] |Rozšířené monitorování Azure pro SAP |
| [2178632] |Klíč monitorování metriky pro SAP na platformě Microsoft Azure |
| [1999351] |Virtualizace v systému Windows: rozšířené monitorování |
| [2243692] |Používání úložiště Azure Premium SSD pro instanci databázového systému SAP |

Další informace o [omezení předplatná Azure][azure-subscription-service-limits-subscription], včetně obecné výchozí omezení a maximální omezení.

## <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Vysoká dostupnost SAP s Azure Resource Manager a modelu nasazení Azure classic
Azure Resource Manager a modelech nasazení Azure classic se liší v těchto oblastech:

- Skupiny prostředků
- Závislost nástroje pro vyrovnávání zatížení Azure interní na skupina prostředků Azure
- Podpora pro scénáře více SID SAP

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Skupiny prostředků
V Azure Resource Manager, můžete použít skupin prostředků ke správě všechny prostředky aplikace ve vašem předplatném Azure. Integrovaný přístup, všechny prostředky mají stejný životní cyklus za ve skupině prostředků. Například všechny prostředky jsou vytvořeny ve stejnou dobu a jsou odstraněny ve stejnou dobu. Další informace o [skupinách prostředků](../../../azure-resource-manager/resource-group-overview.md#resource-groups).

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Závislost nástroje pro vyrovnávání zatížení Azure interní na skupina prostředků Azure

V modelu nasazení Azure classic je závislost mezi nástroje pro vyrovnávání zatížení Azure interní (služba Vyrovnávání zatížení Azure) a cloudové služby. Každý nástroj pro vyrovnávání zatížení interní musí jeden cloudové služby.

V Azure Resource Manager, každý prostředků Azure je nutné umístit do skupiny prostředků Azure, a to je platný pro vyrovnávání zatížení Azure také. Ale není nutné mít jednu skupinu prostředků Azure na nástroje pro vyrovnávání zatížení Azure, například jedna skupina prostředků Azure může obsahovat více Vyrovnávání zatížení Azure. Prostředí je jednodušší a flexibilnější. 

### <a name="support-for-sap-multi-sid-scenarios"></a>Podpora pro scénáře více SID SAP

V Azure Resource Manager, můžete nainstalovat víc SAP systému identifikátor (SID) ASC nebo SCS instancí v jednom clusteru. SID více instancí je možné, protože obsahuje podporu pro více IP adres pro každý nástroj pro vyrovnávání zatížení Azure interní.

Model nasazení Azure classic, postupujte podle postupů popsaných v [SAP NetWeaver v Azure: instancí clusteringu ASC nebo SCS SAP pomocí služby Windows Server Failover Clustering v Azure pomocí s DataKeeper](http://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Důrazně doporučujeme použít model nasazení Azure Resource Manageru pro SAP instalací. Nabízí spoustu výhod, které nejsou k dispozici v modelu nasazení classic. Další informace o Azure [modely nasazení][virtual-machines-azure-resource-manager-architecture-benefits-arm].   
>
>

## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server Failover Clustering
Windows Server Failover Clustering je základem SAP ASC nebo SCS instalace vysokou dostupnost a databázového systému v systému Windows.

Cluster s podporou převzetí služeb při selhání je skupina 1 + n nezávislých serverů (uzlů) které vzájemně spolupracují a zvyšují tak dostupnost aplikací a služeb. Pokud dojde k selhání uzlu, Windows Server Failover Clustering vypočítá počet chyb, ke kterým dochází při zachování pořádku cluster, který poskytuje aplikacím a službám. Můžete z různých kvora režimy k dosažení clustering převzetí služeb při selhání.

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Režim kvora
Při použití služby Windows Server Failover Clustering, můžete vybrat ze čtyř režimů kvora:

* **Většina uzlů**. Každý uzel clusteru hlasovat. Cluster funguje pouze s většinou hlasů, který je s víc než poloviny hlasy. Doporučujeme tuto možnost pro clustery, které mají lichý počet uzlů. Například může selhat tři uzly v clusteru s podporou sedmi a nepřesahuje clusteru dosahuje většině a pokračuje v činnosti.  
* **Většina uzlů a disků**. Každý uzel a určený disk (disk s kopií clusteru) v úložišti clusteru můžete hlasovat, když jsou k dispozici a komunikace. Cluster funguje pouze s většinou hlasů, který je s víc než poloviny hlasy. Tento režim má smysl v prostředí clusteru s sudé číslo uzlů. Pokud se polovina uzlů a disku jsou online, cluster zůstane v dobrém stavu.
* **Uzel a sdílených složek většina**. Každý uzel plus určené sdílené složky (soubor určující sdílenou složku), kterou vytvoří hlasovat, bez ohledu na to, jestli jsou k dispozici uzlů a sdílení souborů a v komunikaci. Cluster funguje pouze s většinou hlasů, který je s víc než poloviny hlasy. Tento režim má smysl v prostředí clusteru s sudé číslo uzlů. Je podobná režimu Většina uzlů a disků, ale používá určující sdílené složky namísto disku s kopií clusteru. Tento režim je snadno implementovat, ale pokud sdílená samotné není vysoce dostupný, může být jediný bod selhání.
* **Bez většiny: Na disku jenom**. Cluster má kvorum, pokud jeden uzel je k dispozici a komunikace s konkrétním diskem v úložišti clusteru. Pouze uzly, které jsou také v komunikaci s tento disk může připojit ke clusteru. Doporučujeme vám, nepoužívejte tento režim.
 

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Windows Server převzetí služeb při selhání Clustering na místě
Obrázek 1 ukazuje dva uzly clusteru. Pokud síťové připojení mezi uzly selže a jak zůstat uzly nahoru a spuštěna, disky kvora nebo soubor sdílet Určuje, který uzel bude pokračovat v poskytování aplikace a služby clusteru. Uzel, který má přístup k disku nebo sdílené složky kvora je uzlu, který zajistí, že služby pokračovat.

Protože tento příklad používá dva uzly clusteru, použijeme Režim kvora Majority sdílené složky souborů a uzlu. Většina uzlů a disků je taky platná možnost. V produkčním prostředí doporučujeme použít disk kvora. Technologie systému sítě a úložiště můžete nastavit jej jako vysoce dostupný.

![Obrázek 1: Příklad konfigurace služby systému Windows Server Failover Clustering pro SAP ASC nebo SCS v Azure][sap-ha-guide-figure-1000]

_**Obrázek 1:** příklad konfigurace služby systému Windows Server Failover Clustering pro SAP ASC nebo SCS v Azure_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Sdílené úložiště
Obrázek 1 zobrazuje i sdílené úložiště dvěma uzly clusteru. V clusteru služby místní sdílené úložiště rozpoznat všechny uzly v clusteru sdíleného úložiště. Mechanismus uzamčení chrání data před poškozením. Všechny uzly, můžete zjistit, pokud jiný uzel selže. V případě selhání jednoho uzlu na zbývající uzel převezme vlastnictví prostředků úložiště a zajišťuje dostupnost služby.

> [!NOTE]
> Nepotřebujete sdílené disky pro zajištění vysoké dostupnosti s některými aplikacemi databázového systému, třeba s SQL serverem. SQL serveru Always On replikuje databázového systému souborů protokolu a data z místního disku jednoho uzlu na místní disk jiným uzlem clusteru. Konfigurace clusteru systému Windows v takovém případě nemusí sdíleného disku.
>
>

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Sítě a překladu
Klientské počítače přístup clusteru přes virtuální IP adresu a název virtuálního hostitele, který poskytuje DNS server. Uzly místně a DNS server může zpracovat více IP adres.

V typické instalace můžete používat dva nebo více připojení k síti:

* Vyhrazené připojení k úložišti
* Cluster interní síťové připojení pro prezenční signál
* Veřejné síti, který klienti používat pro připojení ke clusteru

## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Windows Server Failover Clustering v Azure
Porovnání s úplné nasazení systému nebo privátního cloudu, virtuálních počítačů Azure vyžaduje další kroky konfigurace, Windows Server Failover Clustering. Při sestavování sdíleném disku clusteru, budete muset nastavit několik IP adresy a názvy virtuálních hostitelů pro SAP ASC nebo SCS instanci.

V tomto článku probereme klíčové koncepty a další kroky potřebné k vytvoření clusteru služby SAP centrální služby vysoké dostupnosti v Azure. Ukážeme vám, jak nastavit nástroj třetí strany s DataKeeper a konfiguraci nástroje pro vyrovnávání zatížení Azure interní. Tyto nástroje slouží k vytvoření clusteru s podporou převzetí služeb při selhání systému Windows s určující sdílené složce v Azure.

![Obrázek 2: Windows Server Failover Clustering konfiguraci v Azure bez sdíleného disku][sap-ha-guide-figure-1001]

_**Obrázek 2:** konfiguraci služby Windows Server Failover Clustering v Azure bez sdíleného disku_

### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Sdílený disk v Azure s DataKeeper s
Třeba clusteru sdíleného úložiště pro instanci SAP ASC nebo SCS vysokou dostupnost. Od září 2016 není Azure nabízí sdílené úložiště, které můžete použít k vytvoření clusteru sdíleného úložiště. Software třetích stran s DataKeeper Cluster Edition slouží k vytvoření zrcadlené úložiště, která simuluje sdílené úložiště clusteru. Řešení SIOS poskytuje data v reálném čase synchronní replikace. Toto je, jak můžete vytvořit prostředek sdíleného disku pro cluster:

1. Připojte další disk pro každý z virtuálních počítačů (VM) v konfiguraci clusteru systému Windows.
2. V obou uzlech virtuální počítač spusťte s DataKeeper Cluster Edition.
3. Nakonfigurujte s DataKeeper Cluster Edition, aby ho zrcadlí obsah další disk připojený svazek ze zdrojového virtuálního počítače na další disk připojený objem cílového virtuálního počítače. S DataKeeper abstrahuje zdrojové a cílové místní svazky a k jejich zobrazení na Windows Server Failover Clustering jako jeden sdílený disk.

Přečtěte si další informace o [s DataKeeper](http://us.sios.com/products/datakeeper-cluster/).

![Obrázek 3: Konfigurace služby systému Windows Server Failover Clustering v Azure pomocí DataKeeper s][sap-ha-guide-figure-1002]

_**Obrázek 3:** konfigurace služby systému Windows Server Failover Clustering v Azure pomocí DataKeeper s_

> [!NOTE]
> Pro zajištění vysoké dostupnosti se některé produkty, databázového systému, jako je SQL Server není třeba sdílené disky. SQL serveru Always On replikuje databázového systému souborů protokolu a data z místního disku jednoho uzlu na místní disk jiným uzlem clusteru. Konfigurace clusteru systému Windows v takovém případě nemusí sdíleného disku.
>
>

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Rozpoznání názvu v Azure
Azure Cloudová platforma nenabízí možnost konfigurovat virtuální IP adresy, například plovoucí IP adresy. Je třeba nastavit virtuální IP adresy k dosažení prostředek clusteru v cloudu alternativní řešení.
Azure má k nástroji pro vyrovnávání zatížení pro vnitřní ve službě Vyrovnávání zatížení Azure. S nástrojem pro vyrovnávání zatížení pro vnitřní klienti moci clusteru připojit přes virtuální IP adresu clusteru.
Je třeba nasadit nástroj pro vyrovnávání zatížení interní ve skupině prostředků, který obsahuje uzly clusteru. Potom nakonfigurujte všechny nezbytné port předávání pravidla s sondy porty nástroje pro vyrovnávání zatížení interní.
Můžete se klienti připojují prostřednictvím název virtuálního hostitele. DNS server přeloží IP adresu clusteru a port obslužných rutin vyrovnávání interní služby load předávání do aktivního uzlu clusteru.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> SAP NetWeaver vysoké dostupnosti v Azure infrastruktury jako služba (IaaS)
K dosažení vysoké dostupnosti aplikace SAP pro SAP softwarové komponenty, jako je třeba chránit následující součásti:

* Instance aplikačního serveru SAP
* Instance SAP ASC nebo SCS
* Server databázového systému

Další informace o ochraně SAP součásti ve scénářích s vysokou dostupností najdete v tématu [virtuální počítače Azure plánování a implementace pro SAP NetWeaver][planning-guide-11].

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Vysoká dostupnost SAP aplikační Server
Obvykle nepotřebujete specifického řešení vysoké dostupnosti pro instance aplikačního serveru SAP a dialogové okno. Dosažení vysoké dostupnosti pomocí redundance a nakonfigurujete více instancí dialogové okno v různých instancí virtuálních počítačů Azure. Měli byste mít aspoň dvě instance SAP aplikace nainstalované v dvě instance virtuálních počítačů Azure.

![Obrázek 4: Vysoké dostupnosti SAP aplikační Server][sap-ha-guide-figure-2000]

_**Obrázek 4:** vysokou dostupnost SAP aplikační Server_

Je nutné umístit všechny virtuální počítače, které instance aplikačního serveru SAP hostitele ve stejném Azure dostupnosti nastavit. Nastavení Azure dostupnosti zajišťuje, že:

* Všechny virtuální počítače jsou součástí stejné domény upgradu. Upgradovací doméně, například zajišťuje, že virtuální počítače se neaktualizují ve stejnou dobu během plánované údržby. výpadků.
* Všechny virtuální počítače jsou součástí stejné domény selhání. Domény selhání, například zajišťuje nasazení virtuálních počítačů, tak, aby žádný jediný bod selhání má vliv na dostupnost všech virtuálních počítačů.

Další informace o tom, jak [Správa dostupnosti virtuálních počítačů][virtual-machines-manage-availability].

Pouze nespravovaná disk: účet úložiště Azure je potenciální jediný bod selhání, je důležité mít alespoň dva účty úložiště Azure, ve kterých jsou distribuovány aspoň dva virtuální počítače. V ideální instalační program by se nasadí disky každého virtuálního počítače, na kterém běží instance SAP dialogové okno jiný účet úložiště.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Instance SAP ASC nebo SCS vysokou dostupnost
Obrázek 5 je příklad instance SAP ASC nebo SCS vysokou dostupnost.

![Obrázek 5: Vysoká dostupnost SAP ASC nebo SCS instance][sap-ha-guide-figure-2001]

_**Obrázek 5:** vysokou dostupnost SAP ASC nebo SCS instance_

#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> SAP ASC nebo SCS instance vysoká dostupnost s Windows Server Failover Clustering v Azure
Porovnání s úplné nasazení systému nebo privátního cloudu, virtuálních počítačů Azure vyžaduje další kroky konfigurace, Windows Server Failover Clustering. K vytvoření clusteru s podporou převzetí služeb při selhání systému Windows, musíte pro clustering instance SAP ASC nebo SCS sdíleném disku clusteru, několik IP adres, několik názvy virtuálních hostitelů a k nástroji pro vyrovnávání zatížení Azure interní. To probereme podrobněji později v článku.

![Obrázek 6: Windows Server Failover Clustering pro konfiguraci SAP ASC nebo SCS v Azure pomocí DataKeeper s][sap-ha-guide-figure-1002]

_**Obrázek 6:** Windows Server Failover Clustering SAP ASC nebo SCS konfigurace v Azure pomocí DataKeeper s_

### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Instance databázového systému vysokou dostupnost
Systému správy databáze je také jeden kontaktní bod v systému SAP. Potřebujete chránit pomocí řešení vysoké dostupnosti. Obrázek 7 znázorňuje řešení vysoké dostupnosti SQL serveru Always On v Azure, Windows Server Failover Clustering a Azure interní nástroj pro vyrovnávání zatížení. SQL serveru Always On replikuje pomocí replikace vlastní databázového systému databázového systému data a soubory protokolu. V takovém případě můžete nebudete potřebovat clusteru sdílené disky, což zjednodušuje celý nastavení.

![Obrázek 7: Příklad databázového systému vysoké dostupnosti SAP, s SQL serveru Always On][sap-ha-guide-figure-2003]

_**Obrázek 7:** příklad databázového systému vysoké dostupnosti SAP, s SQL serveru Always On_

Další informace o clusteringu SQL Server v Azure pomocí modelu nasazení Azure Resource Manager najdete v těchto článcích:

* [Konfigurace skupiny dostupnosti Always On v Azure Virtual Machines ručně pomocí Resource Manager] [virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Konfigurace k nástroji pro vyrovnávání zatížení Azure interní pro skupiny dostupnosti Always On v Azure] [virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Scénáře nasazení vysoké dostupnosti začátku do konce

### <a name="deployment-scenario-using-architectural-template-1"></a>Scénář nasazení pomocí architektury šablony 1

Obrázek 8 ukazuje příklad architektury SAP NetWeaver vysoké dostupnosti v Azure pro **jeden** systému SAP. Tento scénář je nastavit takto:

- Jeden vyhrazený cluster se používá pro SAP ASC nebo SCS instanci.
- Jeden vyhrazený cluster se používá pro instanci databázového systému.
- Instance aplikačního serveru SAP nasazených ve svých vlastních vyhrazených virtuálních počítačích.

![Obrázek 8: SAP vysokou dostupnost architektury šablony 1 vyhrazeném clusteru pro ASC nebo SCS a databázového systému][sap-ha-guide-figure-2004]

_**Obrázek 8:** SAP architektury 1 šablony vysokou dostupnost, vyhrazené clustery pro ASC nebo SCS a databázového systému_

### <a name="deployment-scenario-using-architectural-template-2"></a>Scénář nasazení pomocí architektury šablony 2

Obrázek 9 ukazuje příklad architektury SAP NetWeaver vysoké dostupnosti v Azure pro **jeden** systému SAP. Tento scénář je nastavit takto:

- Jeden vyhrazený cluster se používá pro **i** instance SAP ASC nebo SCS a databázového systému.
- Instance aplikačního serveru SAP jsou nasazeny v vlastní vyhrazených virtuálních počítačích.

![Obrázek 9: SAP vysokou dostupnost architektury šablony 2, přičemž vyhrazeném clusteru pro ASC nebo SCS a vyhrazeném clusteru pro databázového systému][sap-ha-guide-figure-2005]

_**Obrázek 9:** SAP vysokou dostupnost architektury šablony 2, přičemž vyhrazeném clusteru pro ASC nebo SCS a vyhrazeném clusteru pro databázového systému_

### <a name="deployment-scenario-using-architectural-template-3"></a>Scénář nasazení pomocí architektury 3 šablony

Obrázek 10 ukazuje příklad architektury SAP NetWeaver vysoké dostupnosti v Azure pro **dva** SAP systémy, s &lt;SID1&gt; a &lt;SID2&gt;. Tento scénář je nastavit takto:

- Jeden vyhrazený cluster se používá pro **i** instance SAP ASC nebo SCS SID1 *a* instance SAP ASC nebo SCS SID2 (jeden cluster).
- Jeden vyhrazený cluster se používá pro SID1 databázového systému a jiné vyhrazeném clusteru se používá pro SID2 databázového systému (dva clustery).
- Instance aplikačního serveru SAP systému SAP SID1 mít vlastní vyhrazených virtuálních počítačích.
- Instance aplikačního serveru SAP systému SAP SID2 mít vlastní vyhrazených virtuálních počítačích.

![Obrázek 10: Vysoká dostupnost architektury šablony 3, s clusterem vyhrazené pro různé ASC nebo SCS instance SAP][sap-ha-guide-figure-6003]

_**Obrázek 10:** SAP vysokou dostupnost architektury šablony 3, s clusterem vyhrazené pro různé instance ASC nebo SCS_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Příprava infrastruktury

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Příprava infrastruktury pro architektury šablony 1
Šablony Azure Resource Manageru pro SAP zjednodušit nasazení požadované prostředky.

Třívrstvá šablony ve službě Správce prostředků Azure také podporují scénáře vysoké dostupnosti, například architektury 1 šablony, která má dva clustery. Každý cluster je SAP jediný bod selhání pro SAP ASC nebo SCS a databázového systému.

Zde je, kde můžete získat šablon Azure Resource Manageru ukázkový scénář, který jsme popisují v tomto článku:

* [Obrázek pro Azure Marketplace.](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Obrázek pro Azure Marketplace pomocí spravovaných disků](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Vlastní image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Vlastní image pomocí spravovaných disků](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Příprava infrastruktury na architektury šablona 1:

- Na portálu Azure na **parametry** okno v **SYSTEMAVAILABILITY** vyberte **HA**.

  ![Obrázek 11: Nastavit SAP vysokou dostupnost Azure Resource Manageru parametry][sap-ha-guide-figure-3000]

_**Obrázek 11:** nastavit parametry SAP vysokou dostupnost Azure Resource Manager_


  Vytvoření šablony:

  * **Virtuální počítače**:
    * Virtuální počítače aplikačního serveru SAP: <*SAPSystemSID*> - di - <*číslo*>
    * ASC nebo SCS clusteru virtuálních počítačů: <*SAPSystemSID*> - ASC - <*číslo*>
    * Cluster databázového systému: <*SAPSystemSID*> - db - <*číslo*>

  * **Síťové karty pro všechny virtuální počítače s přidružené IP adresy**:
    * <*SAPSystemSID*> - nic - di - <*číslo*>
    * <*SAPSystemSID*> - nic - ASC - <*číslo*>
    * <*SAPSystemSID*> - nic - db - <*číslo*>

  * **Účty služby Azure storage (pouze nespravovaná disků)**

  * **Skupiny dostupnosti** pro:
    * Virtuální počítače aplikačního serveru SAP: <*SAPSystemSID*> - avset - di
    * SAP ASC nebo SCS clusteru virtuálních počítačů: <*SAPSystemSID*> - avset - ASC
    * Virtuální počítače clusteru databázového systému: <*SAPSystemSID*> - avset - db

  * **Nástroje pro vyrovnávání zatížení Azure interní**:
    * S všechny porty pro instanci ASC nebo SCS a IP adresu <*SAPSystemSID*> - lb - ASC
    * S všechny porty pro SQL Server databázového systému a IP adresu <*SAPSystemSID*> - lb - db

  * **Skupina zabezpečení sítě**: <*SAPSystemSID*> - nsg - ASC-0  
    * S k externí protokol RDP (Remote Desktop) portu k <*SAPSystemSID*> - ASC - 0 virtuálního počítače

> [!NOTE]
> Jsou všechny IP adresy síťové karty a nástroje pro vyrovnávání zatížení Azure interní **dynamické** ve výchozím nastavení. Změnit tak, aby **statické** IP adresy. Jsme popisují, jak to udělat později v článku.
>
>

### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Nasazení virtuálních počítačů s připojením k podnikové síti (mezi různými místy) používat v produkčním prostředí
Pro produkční systémy SAP, nasaďte virtuální počítače Azure s [připojení k podnikové síti (mezi různými místy)] [ planning-guide-2.2] pomocí Azure Site-to-Site VPN nebo Azure ExpressRoute.

> [!NOTE]
> Můžete použít instanci Azure Virtual Network. Virtuální síť a podsíť již byly vytvořeny a připravený.
>
>

1.  Na portálu Azure na **parametry** okno v **NEWOREXISTINGSUBNET** vyberte **existující**.
2.  V **SUBNETID** pole, přidejte úplný řetězec připravené Azure sítě SubnetID, kam je plánujete nasadit virtuální počítače Azure.
3.  Chcete-li získat seznam všech podsítí síť Azure, spusťte tento příkaz prostředí PowerShell:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
  ```

  **ID** pole ukazuje **SUBNETID**.
4. Chcete-li získat seznam všech **SUBNETID** hodnoty, spusťte tento příkaz prostředí PowerShell:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
  ```

  **SUBNETID** vypadá podobně jako tento:

  ```
  /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
  ```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Nasadit jenom pro cloud instance SAP pro testovací a demo
Můžete nasadit systém SAP vysoké dostupnosti v modelu nasazení jenom cloudu. Tento typ nasazení je primárně užitečné pro ukázku a testovací případy použití. Ji není vhodná pro produkční případy použití.

- Na portálu Azure na **parametry** okno v **NEWOREXISTINGSUBNET** vyberte **nové**. Ponechte **SUBNETID** pole prázdná.

  Šablona SAP Azure Resource Manager automaticky vytvoří virtuální síť Azure a podsíť.

> [!NOTE]
> Musíte také nasadit alespoň jeden vyhrazený virtuální počítač pro Active Directory a DNS ve stejné instanci Azure Virtual Network. Šablona nepodporuje vytvoření těchto virtuálních počítačů.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Příprava infrastruktury pro architektury šablony 2

Tato šablona Azure Resource Manageru pro SAP můžete zjednodušit nasazování požadované infrastruktury prostředků pro SAP architektury šablony 2.

Zde je, kde můžete získat šablon Azure Resource Manageru pro tento scénář nasazení:

* [Obrázek pro Azure Marketplace.](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Obrázek pro Azure Marketplace pomocí spravovaných disků](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Vlastní image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Vlastní image pomocí spravovaných disků](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Příprava infrastruktury pro architektury 3 šablony

Můžete Příprava infrastruktury a nakonfigurovat SAP pro **více SID**. Například můžete přidat další instance SAP ASC nebo SCS do *existující* konfigurace clusteru. Další informace najdete v tématu [nakonfigurovat další instance SAP ASC nebo SCS do stávající konfigurace clusteru pro vytvoření konfigurace aplikace SAP více SID ve službě Správce prostředků Azure][sap-ha-multi-sid-guide].

Pokud chcete vytvořit nový cluster více SID, můžete použít více SID [šablony rychlý start na Githubu](https://github.com/Azure/azure-quickstart-templates).
K vytvoření nového clusteru více SID, musíte nasadit následující tři šablony:

* [ASC nebo SCS šablony](#ASCS-SCS-template)
* [Šablona databáze](#database-template)
* [Šablona servery aplikací](#application-servers-template)

Následující sekce obsahují další informace o šablonách a parametry, které je třeba zadat v šablonách.

#### <a name="ASCS-SCS-template"></a> ASC nebo SCS šablony

Šablona ASC nebo SCS nasadí dva virtuální počítače, které můžete použít k vytvoření clusteru převzetí služeb při selhání systému Windows Server, který je hostitelem více instancí ASC nebo SCS.

Nastavení v šabloně více SID ASC nebo SCS [ASC nebo SCS více SID šablony] [ sap-templates-3-tier-multisid-xscs-marketplace-image] nebo [ASC nebo SCS více SID šablony pomocí disků spravované] [ sap-templates-3-tier-multisid-xscs-marketplace-image-md], zadejte hodnoty následujících parametrů:

  - **Předpona prostředků**.  Nastaví předponu prostředku, který se používá k předpony všechny prostředky, které jsou vytvořené během nasazení. Protože prostředky nepatří do systému jenom jeden SAP, není SID systému SAP jeden předponu prostředku.  Předpona, která musí být v rozmezí **tři až šest znaků**.
  - **Stack – typ**. Vyberte typ zásobníku systému SAP. V závislosti na typu zásobníku nástroj pro vyrovnávání zatížení Azure má jeden (ABAP nebo Java pouze) nebo dvě (ABAP + Java) privátní IP adresy na systému SAP.
  -  **Typ operačního systému**. Vyberte verzi operačního systému z virtuálních počítačů.
  -  **Počet systému SAP**. Vyberte počet SAP systémy, které chcete nainstalovat v tomto clusteru.
  -  **Dostupnost systému**. Vyberte **HA**.
  -  **Uživatelské jméno správce a heslo správce**. Vytvořte nového uživatele, který lze použít k přihlášení k počítači.
  -  **Nový nebo existující podsíť**. Nastavte, jestli mají být vytvořeny nové virtuální sítě a podsítě, nebo se použije existující podsítí. Pokud již máte virtuální síť, která je připojen k síti na pracovišti, vyberte **existující**.
  -  **Id podsítě**. Nastavte ID podsítě, ke kterému má být připojen virtuální počítače. Vyberte podsíť virtuální privátní sítě (VPN) nebo ExpressRoute virtuální sítě pro virtuální počítač připojit k síti na pracovišti. ID obvykle vypadá takto:

   /subscriptions/ <*id předplatného*> /resourceGroups/ <*název skupiny prostředků*> /providers/Microsoft.Network/virtualNetworks/ <*název virtuální sítě*> /subnets/ <*název podsítě.*>

Šablona nasadí jednu instanci nástroj pro vyrovnávání zatížení Azure, která podporuje více systémů SAP.

- Instance ASC jsou nakonfigurované pro instance číslo 00, 10, 20...
- Instance SCS jsou nakonfigurované pro instance číslo 01, 11, 21...
- Instance ASC zařazování replikace serveru (YBRAT) (pouze Linux) jsou nakonfigurované pro čísla instance 02, 12, 22...
- Instance SCS YBRAT (pouze Linux) jsou nakonfigurované pro instance číslo 03, 13, 23...

Nástroje pro vyrovnávání zatížení obsahuje 1 (2 pro Linux) VIP(s), 1 x virtuální IP adresa pro ASC nebo SCS a 1 x virtuální IP adresa pro YBRAT (pouze Linux).

Následující seznam obsahuje všechny pravidla (kde x je číslo systému SAP, například 1, 2, 3...) pro vyrovnávání zatížení:
- Porty specifické pro systém Windows pro každý systém SAP: 445, 5985
- Porty ASC (čísla instance x0): 32 × 0, 36 x 0, 39 x 0, 81 x 0, 5 x 013, 5 x 014, 5 x 016
- Porty SCS (čísla instance x1): 32 x 1, 33 x 1, 39 x 1, 81 x 1, 5 x 113, 5 x 114, 5 x 116
- ASC YBRAT portů v systému Linux (čísla instance x2): 33 x 2, 5 x 213, 5 x 214, 5 x 216
- SCS YBRAT portů v systému Linux (čísla instance x3): 33 x 3, 5 x 313, 5 x 314, 5 x 316

Nástroje pro vyrovnávání zatížení je nakonfigurovaný na použití následující porty testu (kde x je číslo systému SAP, například 1, 2, 3...):
- Port testu nástroje pro vyrovnávání zatížení ASC nebo SCS interní: 620 x 0
- Interní YBRAT načíst port testu vyrovnávání (pouze Linux): 621 x 2

#### <a name="database-template"></a> Šablona databáze

Šablona databáze nasadí jednu nebo dvě virtuální počítače, které můžete použít k instalaci systému správy relačních databází (RDBMS) pro systém SAP. Například pokud nasadíte šablonu ASC nebo SCS pro pět systémy SAP, budete muset nasadit pětkrát této šablony.

Nastavení v šabloně více SID databáze [databáze více SID šablony] [ sap-templates-3-tier-multisid-db-marketplace-image] nebo [databáze více SID šablony pomocí disků spravované] [ sap-templates-3-tier-multisid-db-marketplace-image-md], zadejte hodnoty následujících parametrů:

  -  **Id systému SAP**. Zadejte ID systému SAP systému SAP, který chcete nainstalovat. Identifikátor se použije jako předpona pro prostředky, které jsou nasazeny.
  -  **Typ operačního systému**. Vyberte verzi operačního systému z virtuálních počítačů.
  -  **Hodnota DbType**. Vyberte typ databáze, kterou chcete nainstalovat na clusteru. Vyberte **SQL** Pokud chcete nainstalovat Microsoft SQL Server. Vyberte **HANA** Pokud budete chtít na virtuální počítače nainstalovat SAP HANA. Je nutné vybrat typ správný operační systém: vyberte **Windows** pro SQL a vyberte distribuční Linux pro HANA. Vyrovnávání zatížení Azure, která je připojena k virtuálním počítačům, bude nakonfigurován pro podporu typ vybrané databáze:
    * **SQL**. Nástroje pro vyrovnávání zatížení bude Vyrovnávání zatížení port 1433. Ujistěte se, že tento port použít pro vaše instalační program SQL serveru Always On.
    * **HANA**. Nástroje pro vyrovnávání zatížení bude porty 35015 a 35017 Vyrovnávání zatížení. Nainstalujte SAP HANA číslem instance **50**.
    Nástroje pro vyrovnávání zatížení bude používat port testu 62550.
  -  **Velikost systému SAP**. Nastavte počet protokoly SAP bude poskytovat nový systém. Pokud si nejste jisti kolik protokoly SAP, systém bude vyžadovat, požádejte SAP technologie partnera nebo systémový integrátor.
  -  **Dostupnost systému**. Vyberte **HA**.
  -  **Uživatelské jméno správce a heslo správce**. Vytvořte nového uživatele, který lze použít k přihlášení k počítači.
  -  **Id podsítě**. Zadejte ID podsítě, která jste použili při nasazení ASC nebo SCS šablony nebo ID podsítě, která byla vytvořena jako součást nasazení ASC nebo SCS šablony.

#### <a name="application-servers-template"></a> Šablona servery aplikací

Šablony servery aplikace nasadí dvě nebo více virtuálních počítačů, které lze použít jako instance aplikačního serveru SAP jeden systému SAP. Například pokud nasadíte šablonu ASC nebo SCS pro pět systémy SAP, budete muset nasadit pětkrát této šablony.

Nastavení v šabloně SID více serverů aplikace [šablony SID více serverů aplikace] [ sap-templates-3-tier-multisid-apps-marketplace-image] nebo [šablony SID více serverů aplikace pomocí spravovaných disků] [ sap-templates-3-tier-multisid-apps-marketplace-image-md], zadejte hodnoty následujících parametrů:

  -  **Id systému SAP**. Zadejte ID systému SAP systému SAP, který chcete nainstalovat. Identifikátor se použije jako předpona pro prostředky, které jsou nasazeny.
  -  **Typ operačního systému**. Vyberte verzi operačního systému z virtuálních počítačů.
  -  **Velikost systému SAP**. Počet je nový systém bude poskytovat protokoly SAP. Pokud si nejste jisti kolik protokoly SAP, systém bude vyžadovat, požádejte SAP technologie partnera nebo systémový integrátor.
  -  **Dostupnost systému**. Vyberte **HA**.
  -  **Uživatelské jméno správce a heslo správce**. Vytvořte nového uživatele, který lze použít k přihlášení k počítači.
  -  **Id podsítě**. Zadejte ID podsítě, která jste použili při nasazení ASC nebo SCS šablony nebo ID podsítě, která byla vytvořena jako součást nasazení ASC nebo SCS šablony.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Virtuální síť Azure
V našem příkladu adresní prostor virtuální sítě Azure je 10.0.0.0/16. Existuje jedna podsíť s názvem **podsíť**, rozsah adres 10.0.0.0/24. Všechny virtuální počítače a interní služby load balancer nasazených v této virtuální síti.

> [!IMPORTANT]
> Nemáte žádné změny nastavení sítě v hostovaném operačním systému. To zahrnuje IP adresy, servery DNS a podsítě. Nakonfigurujte všechna nastavení sítě v Azure. Službu Dynamic Host Configuration Protocol (DHCP) rozšíří nastavení.
>
>

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS IP adresy

Pokud chcete nastavit požadované DNS IP adresy, proveďte následující kroky.

1.  Na portálu Azure na **servery DNS** okno, ujistěte se, že virtuální sítě **servery DNS** je možnost nastavena na **vlastní DNS**.
2.  Vyberte nastavení na základě typu sítě, které máte. Další informace najdete v následujících materiálech:
    * [Připojení k podnikové síti (mezi různými místy)][planning-guide-2.2]: Přidejte IP adresy na místní servery DNS.  
    Můžete rozšířit na místní servery DNS pro virtuální počítače, které jsou spuštěné v Azure. V tomto scénáři můžete přidat IP adresy Azure virtuální počítače, na které spouštíte služby DNS.
    * [Čistě cloudové nasazení][planning-guide-2.1]: nasazení se další virtuální počítač ve stejné instanci virtuální sítě, která slouží jako DNS server. Přidání IP adres virtuální počítače Azure, které jste nastavili ke spouštění služby DNS.

    ![Obrázek 12: Servery DNS nakonfigurujte pro virtuální síť Azure][sap-ha-guide-figure-3001]

    _**Obrázek 12:** konfigurace DNS serverů pro virtuální síť Azure_

  > [!NOTE]
  > Pokud změníte IP adresy serverů DNS, musíte restartovat virtuální počítače Azure k použití změny a šířit nové servery DNS.
  >
  >

V našem příkladu služba DNS nainstalovaná a nakonfigurovaná na těchto virtuálních počítačích Windows:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Název síťové karty | Statická IP adresa |
| --- | --- | --- | --- |
| První server DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Druhý server DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Názvy hostitelů a statické IP adresy pro clusterové instance SAP ASC nebo SCS a Clusterové instance databázového systému

Pro místní nasazení je třeba tyto názvy vyhrazené hostitele a IP adresy:

| Název role virtuálních hostitelů | Název virtuálního hostitele | Virtuální statickou IP adresu |
| --- | --- | --- |
| SAP ASC nebo SCS první clusteru virtuální hostitel název (pro správu clusteru) |PR1. ASC vir |10.0.0.42 |
| Název virtuálního hostitele instance SAP ASC nebo SCS |PR1. ASC sap |10.0.0.43 |
| Databázového systému SAP druhý cluster virtuálního hostitele název (Správa clusteru) |pr1-dbms-vir |10.0.0.32 |

Při vytváření clusteru, vytvořit názvy virtuálních hostitelů **pr1. ASC vir** a **pr1. databázového systému vir** a přidružené IP adresy, které spravují samotného clusteru. Informace o tom, jak to udělat najdete v tématu [shromažďovat uzly clusteru v konfiguraci clusteru][sap-ha-guide-8.12.1].

Můžete ručně vytvořit další dva názvy virtuálních hostitelů, **pr1. ASC sap** a **pr1. databázového systému sap**a přidružené IP adresy na serveru DNS. SAP ASC nebo SCS skupinu prostředků clusteru a skupinu prostředků clusteru databázového systému používají tyto prostředky. Informace o tom, jak to udělat najdete v tématu [vytvořte název virtuálního hostitele pro clusterovou instanci SAP ASC nebo SCS][sap-ha-guide-9.1.1].

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Nastavení statické IP adresy pro virtuální počítače SAP
Poté, co nasadíte virtuální počítače pro použití v clusteru, musíte nastavit statické IP adresy pro všechny virtuální počítače. To udělat v konfiguraci virtuální sítě Azure a není v hostovaném operačním systému.

1.  Na portálu Azure vyberte **skupiny prostředků** > **síťové karty** > **nastavení** > **IP adresu**.
2.  Na **IP adresy** okno, v části **přiřazení**, vyberte **statické**. V **IP adresu** zadejte IP adresu, která chcete použít.

  > [!NOTE]
  > Pokud změníte IP adresa síťové karty, budete muset restartovat virtuální počítače Azure na použití změny.  
  >
  >

  ![Obrázek 13: Nastavení statické IP adresy pro síťové karty každého virtuálního počítače][sap-ha-guide-figure-3002]

  _**Obrázek 13:** nastavení statické IP adresy pro síťové karty každého virtuálního počítače_

  Tento krok opakujte pro všechna síťová rozhraní, které se pro všechny virtuální počítače, včetně virtuálních počítačů, které chcete použít pro vaši službu Active Directory a DNS.

V našem příkladu máme tyto virtuální počítače a statické IP adresy:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Název síťové karty | Statická IP adresa |
| --- | --- | --- | --- |
| První instance aplikačního serveru SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Druhá instance aplikačního serveru SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Poslední instance aplikačního serveru SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Prvním uzlu clusteru pro instanci ASC nebo SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Druhý uzel clusteru pro instanci ASC nebo SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Prvním uzlu clusteru pro instanci databázového systému |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Druhý uzel clusteru pro instanci databázového systému |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Nastavit statickou IP adresu pro nástroj pro vyrovnávání zatížení Azure interní

Šablona SAP Azure Resource Manager vytvoří pro vyrovnávání zatížení Azure interní, který se používá pro SAP ASC nebo SCS instance clusteru a clusteru databázového systému.

> [!IMPORTANT]
> IP adresa název virtuálního hostitele SAP ASC nebo SCS je stejný jako IP adresu služby Vyrovnávání zatížení interní SAP ASC nebo SCS: **pr1-lb Asc**.
> IP adresa virtuální název tohoto databázového systému je stejné jako IP adresa služby Vyrovnávání zatížení interní databázového systému: **databázového systému pr1 lb**.
>
>

Chcete-li nastavit statickou IP adresu pro nástroj pro vyrovnávání zatížení Azure interní:

1.  Počáteční nasazení nastaví IP adresa služby Vyrovnávání zatížení pro vnitřní **dynamické**. Na portálu Azure na **IP adresy** okno, v části **přiřazení**, vyberte **statické**.
2.  Nastavení IP adresy služby Vyrovnávání zatížení pro vnitřní **pr1-lb Asc** na název virtuálního hostitele instance SAP ASC nebo SCS adresu IP.
3.  Nastavení IP adresy služby Vyrovnávání zatížení pro vnitřní **databázového systému pr1 lb** na IP adresu název virtuálního hostitele instance databázového systému.

  ![Obrázek 14: Nastavení statické IP adresy pro vyrovnávání zatížení pro vnitřní pro instance SAP ASC nebo SCS][sap-ha-guide-figure-3003]

  _**Obrázek 14:** nastavení statické IP adresy pro vyrovnávání zatížení pro vnitřní pro instance SAP ASC nebo SCS_

V našem příkladu máme dvě Azure interní služby load balancer, které mají tyto statické IP adresy:

| Role služby Vyrovnávání zatížení Azure interní | Název nástroje pro vyrovnávání zatížení Azure interní | Statická IP adresa |
| --- | --- | --- |
| SAP ASC nebo SCS instanci interní zátěže. |PR1-lb ASC |10.0.0.43 |
| Databázového systému SAP interní nástroj pro vyrovnávání zatížení |pr1-lb-dbms |10.0.0.33 |


### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Výchozí pravidla pro vyrovnávání zatížení Azure interní Vyrovnávání zatížení ASC nebo SCS

Šablona SAP Azure Resource Manager vytvoří porty, které potřebujete:
* Instance ABAP ASC pomocí výchozí instance číslo **00**
* Instance Java SCS, s výchozí instance číslo **01**

Při instalaci instance SAP ASC nebo SCS, musíte použít výchozí instanci číslo **00** pro vaše ABAP ASC instance a číslo instance výchozí **01** instance Java SCS.

Dále vytvořte požadované interní koncové body pro SAP NetWeaver porty pro vyrovnávání zatížení.

Pokud chcete vytvořit požadované interní služby load vyrovnávání koncové body, nejdřív vytvořte tyto koncové body pro SAP NetWeaver ABAP ASC porty pro vyrovnávání zatížení:

| Název pravidla vyrovnávání služby nebo zatížení | Výchozí čísla portů | Konkrétní porty (ASC instance číslem instance 00) (YBRAT s 10) |
| --- | --- | --- |
| Zařadit Server / *lbrule3200* |32 <*InstanceNumber*> |3200 |
| Server zpráv ABAP / *lbrule3600* |36 <*InstanceNumber*> |3600 |
| Zpráva Vnitřní ABAP / *lbrule3900* |39 <*InstanceNumber*> |3900 |
| Server HTTP zpráv nebo *Lbrule8100* |81 <*InstanceNumber*> |8100 |
| SAP spuštění služby ASC HTTP / *Lbrule50013* |5 <*InstanceNumber*> 13 |50013 |
| SAP spuštění služby ASC HTTPS nebo *Lbrule50014* |5 <*InstanceNumber*> 14 |50014 |
| Zařazování replikace nebo *Lbrule50016* |5 <*InstanceNumber*> 16 |50016 |
| SAP spuštění služby YBRAT HTTP *Lbrule51013* |5 <*InstanceNumber*> 13 |51013 |
| SAP spuštění služby YBRAT HTTP *Lbrule51014* |5 <*InstanceNumber*> 14 |51014 |
| Win RM *Lbrule5985* | |5985 |
| Sdílení souborů *Lbrule445* | |445 |

_**Tabulka 1:** portu čísla instance SAP NetWeaver ABAP ASC_

Pak vytvořte tyto koncové body pro SAP NetWeaver Java SCS porty pro vyrovnávání zatížení:

| Název pravidla vyrovnávání služby nebo zatížení | Výchozí čísla portů | Konkrétní porty (SCS instance číslem instance 01) (YBRAT s 11) |
| --- | --- | --- |
| Zařadit Server / *lbrule3201* |32 <*InstanceNumber*> |3201 |
| Server brány nebo *lbrule3301* |33 <*InstanceNumber*> |3301 |
| Server zpráv Java / *lbrule3900* |39 <*InstanceNumber*> |3901 |
| Server HTTP zpráv nebo *Lbrule8101* |81 <*InstanceNumber*> |8101 |
| SAP spuštění služby SCS HTTP / *Lbrule50113* |5 <*InstanceNumber*> 13 |50113 |
| SAP spuštění služby SCS HTTPS nebo *Lbrule50114* |5 <*InstanceNumber*> 14 |50114 |
| Zařazování replikace nebo *Lbrule50116* |5 <*InstanceNumber*> 16 |50116 |
| SAP spuštění služby YBRAT HTTP *Lbrule51113* |5 <*InstanceNumber*> 13 |51113 |
| SAP spuštění služby YBRAT HTTP *Lbrule51114* |5 <*InstanceNumber*> 14 |51114 |
| Win RM *Lbrule5985* | |5985 |
| Sdílení souborů *Lbrule445* | |445 |

_**Tabulka 2:** portu čísla instance SAP NetWeaver Java SCS_

![Obrázek 15: Pravidla pro vyrovnávání zatížení Azure interní Vyrovnávání zatížení výchozí ASC nebo SCS][sap-ha-guide-figure-3004]

_**Obrázek 15:** ASC nebo SCS výchozí pravidla pro vyrovnávání zatížení Azure interní Vyrovnávání zatížení_

Nastavení IP adresy služby Vyrovnávání zatížení **databázového systému pr1 lb** na IP adresu název virtuálního hostitele instance databázového systému.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Změňte pravidla pro vyrovnávání zatížení Azure interní Vyrovnávání zatížení výchozí ASC nebo SCS

Pokud chcete používat jiný čísla pro SAP ASC nebo SCS instancí, je třeba změnit názvy a hodnoty jejich porty z výchozí hodnoty.

1.  Na portálu Azure vyberte  **< *SID*> ASC - lb – nástroj pro vyrovnávání zatížení** > **pravidla Vyrovnávání zatížení**.
2.  Pro všechna pravidla, které náleží do instance SAP ASC nebo SCS Vyrovnávání zatížení změňte tyto hodnoty:

  * Název
  * Port
  * Back-end port

  Například pokud chcete změnit výchozí číslo instance ASC od 00 do 31, budete muset provést změny pro všechny porty uvedené v tabulce 1.

  Tady je příklad aktualizace pro port *lbrule3200*.

  ![Obrázek 16: Změňte pravidla pro vyrovnávání zatížení Azure interní Vyrovnávání zatížení výchozí ASC nebo SCS][sap-ha-guide-figure-3005]

  _**Obrázek 16:** změňte pravidla pro vyrovnávání zatížení Azure interní Vyrovnávání zatížení výchozí ASC nebo SCS_

### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Přidejte virtuální počítače s Windows do domény

Když přiřadíte statickou IP adresu pro virtuální počítače, přidejte virtuální počítače k doméně.

![Obrázek 17: Přidáte virtuální počítač k doméně][sap-ha-guide-figure-3006]

_**Obrázek 17:** přidat virtuální počítač k doméně_

### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Přidat položky registru na obou uzlů clusteru instance SAP ASC nebo SCS

Azure Vyrovnávání zatížení má interní nástroj, zavře připojení při připojení jsou nastavte dobu nečinnosti, čas (časový limit nečinnosti). SAP pracovních procesů v dialogovém okně Otevřít připojení instance SAP zařadit do fronty zpracovat při první zařazování/dequeue požadavku musí být odeslána. Tato připojení obvykle zůstat zavedené do pracovní proces nebo proces zařazování restartuje. Ale pokud se jedná o připojení na nastavenou dobu nečinnosti, nástroje pro vyrovnávání zatížení Azure interní zavře připojení. To není problém, protože pracovní proces SAP připojení do procesu zařazování obnoví, pokud už existuje. Tyto aktivity jsou popsané v trasování vývojáře procesů SAP, ale uživatel vytvořit velké množství další obsah v těchto trasování. Je vhodné změnit TCP/IP `KeepAliveTime` a `KeepAliveInterval` na obou uzlů clusteru. Kombinací těchto změn v parametrech TCP/IP s parametry profil SAP, popsanou dále v článku.

Chcete-li přidat položky registru na obou uzlů clusteru instance SAP ASC nebo SCS, nejprve přidejte tyto položky registru systému Windows na obou uzlů clusteru systému Windows pro SAP ASC nebo SCS:

| Cesta | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Název proměnné |`KeepAliveTime` |
| Typ proměnné |REG_DWORD (decimální): |
| Hodnota |120000 |
| Odkaz na dokumentaci |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

_**Tabulka 3:** změnit první parametr TCP/IP_

Pak přidejte této položky registru systému Windows na obou uzlů clusteru systému Windows pro SAP ASC nebo SCS:

| Cesta | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Název proměnné |`KeepAliveInterval` |
| Typ proměnné |REG_DWORD (decimální): |
| Hodnota |120000 |
| Odkaz na dokumentaci |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

_**Tabulka 4:** změnit druhý parametr protokolu TCP/IP_

**Aby se změny projevily, restartujte obou uzlů clusteru**.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Nastavení clusteru Windows Server Failover Clustering pro instance SAP ASC nebo SCS

Nastavení clusteru s podporou služby Windows Server Failover Clustering pro instance SAP ASC nebo SCS zahrnuje tyto úlohy:

- Shromažďování uzly clusteru v konfiguraci clusteru
- Konfigurace určující sdílenou složku clusteru

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Shromažďovat uzly clusteru v konfiguraci clusteru

1.  Přidat Role a funkce průvodce přidejte clusteringu obou uzlů clusteru převzetí služeb při selhání.
2.  Nastavení clusteru převzetí služeb při selhání pomocí Správce clusteru převzetí služeb při selhání. Ve Správci clusteru převzetí služeb při selhání vyberte **vytvořením clusteru**a poté přidejte pouze název první clusteru uzlu A. Nepřidávejte druhého uzlu ještě; v pozdější fázi budete přidání druhého uzlu.

  ![Obrázek 18: Přidejte název serveru nebo virtuálního počítače na prvním uzlu clusteru][sap-ha-guide-figure-3007]

  _**Obrázek 18:** přidejte název serveru nebo virtuálního počítače na prvním uzlu clusteru_

3.  Zadejte název sítě (název hostitele virtuálního) clusteru.

  ![Obrázek 19: Zadejte název clusteru][sap-ha-guide-figure-3008]

  _**Obrázek 19:** zadejte název clusteru_

4.  Po vytvoření clusteru, spusťte test ověření clusteru.

  ![Obrázek 20: Spustit kontrolu ověření clusteru][sap-ha-guide-figure-3009]

  _**Obrázek 20:** spustit kontrolu ověření clusteru_

  Můžete ignorovat jakékoli upozornění o discích v tomto okamžiku v procesu. Přidáte, že určující sdílené složce a SIOS sdílené disky později. V této fázi nemusíte obávat o kvora.

  ![Obrázek 21: Nenajde žádný disk kvora][sap-ha-guide-figure-3010]

  _**Obrázek 21:** nenajde žádný disk kvora_

  ![Obrázek 22: Prostředek clusteru jádra potřebuje novou IP adresu][sap-ha-guide-figure-3011]

  _**Obrázek 22:** prostředku clusteru jádra potřebuje novou IP adresu_

5.  Změna IP adresy jádra Clusterové služby. Clusteru nelze spustit dokud změnit IP adresu clusteru služby jádra, protože IP adresa serveru odkazuje na jednom z uzlů virtuálního počítače. To udělat na **vlastnosti** stránky prostředek IP základní služby clusteru.

  Například je potřeba přiřadit IP adresu (v našem příkladu **10.0.0.42**) pro název virtuálního hostitele clusteru **pr1. ASC vir**.

  ![Obrázek 23: V dialogovém okně vlastností změňte IP adresu][sap-ha-guide-figure-3012]

  _**Obrázek 23:** v **vlastnosti** dialogové okno pole, změna IP adresy_

  ![Obrázek 24: Přiřadíte IP adresu, která je vyhrazena pro cluster][sap-ha-guide-figure-3013]

  _**Obrázek 24:** přiřadit IP adresu, která je vyhrazena pro cluster_

6.  Uveďte název virtuálního hostitele clusteru online.

  ![Obrázek 25: Základní služby clusteru je zapnutý a běží a s správnou IP adres][sap-ha-guide-figure-3014]

  _**Obrázek 25:** základní služby clusteru je zapnutý a běží a s správnou IP adres_

7.  Přidání druhého uzlu clusteru.

  Nyní když základní Clusterová služba spuštěná, můžete přidat druhý uzel clusteru.

  ![Obrázek 26: Přidání druhého uzlu clusteru][sap-ha-guide-figure-3015]

  _**Obrázek 26:** přidání druhého uzlu clusteru_

8.  Zadejte název pro druhý hostitele uzlu clusteru.

  ![Obrázek 27: Zadejte název hostitele druhého uzlu clusteru][sap-ha-guide-figure-3016]

  _**Obrázek 27:** zadejte název hostitele druhého uzlu clusteru_

  > [!IMPORTANT]
  > Ujistěte se, který **přidat do clusteru veškeré oprávněné úložiště** zaškrtávací políčko je **není** vybrané.  
  >
  >

  ![Obrázek 28: Nevybírejte zaškrtávací políčko][sap-ha-guide-figure-3017]

  _**Obrázek 28:** provést **není** vyberte zaškrtávací pole_

  Můžete ignorovat upozornění o kvora a disky. Můžete nastavit kvora a sdílet disk později, jak je popsáno v [instalace s DataKeeper Cluster Edition u disku clusteru sdílení, SAP ASC nebo SCS][sap-ha-guide-8.12.3].

  ![Obrázek 29: Ignorujte upozornění o disku kvora][sap-ha-guide-figure-3018]

  _**Obrázek 29:** ignorovat upozornění o disku kvora_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Konfigurovat určující sdílenou složku clusteru

Konfigurace určující sdílenou složku clusteru zahrnuje tyto úlohy:

- Vytvoření sdílené složky
- Nastavení kvora určující sdílené složky souborů ve Správci clusteru převzetí služeb při selhání

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Vytvoření sdílené složky

1.  Vyberte složku s kopií místo disk kvora. Tuto volbu podporuje DataKeeper s.

  V příkladech v tomto článku je určující sdílenou složku na serveru Active Directory a DNS, který běží v Azure. Určující sdílená složka se označuje jako **domcontr-0**. Vzhledem k tomu, že by jste nakonfigurovali připojení VPN do Azure (prostřednictvím sítě Site-to-Site VPN nebo Azure ExpressRoute), vaše/DNS služby Active Directory service je místní a není vhodné pro spuštění souboru sdílet s kopií clusteru.

  > [!NOTE]
  > Pokud služby Active Directory a DNS používá jenom v místě, není konfigurace vaší určující sdílenou složku v operačním systému Windows Active Directory a DNS, který běží na místě. Latence sítě mezi uzly clusteru, které jsou spuštěné v Azure a Active Directory a DNS v místním může být příliš velký a způsobit problémy s připojením. Nezapomeňte nakonfigurovat určující sdílenou složku na virtuální počítač Azure se blíží uzlu clusteru se systémem.  
  >
  >

  Jednotka kvora vyžaduje alespoň 1 024 MB volného místa. Doporučujeme, abyste hodnotu 2 048 MB volného místa pro disk kvora.

2.  Přidejte objekt názvu clusteru.

  ![Obrázek 30: Přiřadíte oprávnění pro sdílenou složku pro objekt názvu clusteru][sap-ha-guide-figure-3019]

  _**Obrázek 30:** přiřadit oprávnění pro sdílenou složku pro objekt názvu clusteru_

  Ujistěte se, že oprávnění zahrnout oprávnění pro změnu data ve sdílené složce pro objekt názvu clusteru (v našem příkladu **pr1. ASC vir$**).

3.  Chcete-li přidat objekt názvu clusteru do seznamu, vyberte **přidat**. Změna filtru k vyhledání počítačových objektů, kromě těch, které znázorňuje obrázek 31.

  ![Obrázek 31: Změňte typy objektů pro zahrnutí počítačů][sap-ha-guide-figure-3020]

  _**Obrázek 31:** změnit typy objektů pro zahrnutí počítačů_

  ![Obrázek 32: Zaškrtněte políčko počítače][sap-ha-guide-figure-3021]

  _**Obrázek 32:** vyberte **počítače** zaškrtávací políčko_

4.  Objekt názvu clusteru zadejte, jak ukazuje obrázek 31. Protože byl vytvořen záznam, můžete změnit oprávnění, jak ukazuje obrázek 30.

5.  Vyberte **zabezpečení** podrobnější kartě do sdílené složky a potom nastavte oprávnění pro objekt názvu clusteru.

  ![Obrázek 33: Nastavte atributy zabezpečení pro objekt názvu clusteru na sdílenou složku kvora souboru][sap-ha-guide-figure-3022]

  _**Obrázek 33:** nastavit atributy zabezpečení pro objekt názvu clusteru v souboru sdílenou složku kvora_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Nastavení kvora určující sdílené složky souborů ve Správci clusteru převzetí služeb při selhání

1.  Otevřete kvora nastavení Průvodce konfigurací.

  ![Obrázek 34: Spuštění Průvodce konfigurace kvora clusteru nastavení][sap-ha-guide-figure-3023]

  _**Obrázek 34:** spustit Průvodce konfigurace kvora clusteru nastavení_

2.  Na **vyberte konfiguraci kvora** vyberte **vybrat určující disk kvora**.

  ![Obrázek 35: Konfigurací kvora, které můžete vybrat z][sap-ha-guide-figure-3024]

  _**Obrázek 35:** konfigurací kvora, můžete si vybrat z_

3.  Na **vybrat určující disk kvora** vyberte **nakonfigurovat určující sdílenou složku**.

  ![Obrázek 36: Vyberte sdílenou složku][sap-ha-guide-figure-3025]

  _**Obrázek 36:** vyberte sdílenou složku_

4.  Zadejte cestu UNC ke sdílené složce (v našem příkladu \\domcontr 0\FSW). Pokud chcete zobrazit seznam změn, můžete provést, vyberte **Další**.

  ![Obrázek 37: Zadejte umístění pro sdílení souborů pro sdílenou složku s kopií clusteru][sap-ha-guide-figure-3026]

  _**Obrázek 37:** zadejte umístění pro sdílení souborů pro sdílenou složku s kopií clusteru_

5.  Vyberte požadované změny a pak vyberte **Další**. Je třeba úspěšně znovu nakonfigurovat konfiguraci clusteru, jak ukazuje obrázek 38.  

  ![Obrázek 38: Potvrzení, že jste jste změnili konfiguraci clusteru][sap-ha-guide-figure-3027]

  _**Obrázek 38:** potvrzení, že jste jste změnili konfiguraci clusteru_

Po úspěšném nainstalování clusteru převzetí služeb při selhání systému Windows, třeba změny provedené některé prahové hodnoty pro přizpůsobení převzetí služeb při selhání detekce podmínky v Azure. Parametry, které chcete změnit, jsou popsané v tomto blogu: https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/ . Za předpokladu, že dva virtuální počítače, které sestavení konfigurace clusteru systému Windows pro ASC nebo SCS jsou ve stejné podsíti, je nutné změnit tak, aby tyto hodnoty následujících parametrů:
- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Tato nastavení byly testovány s zákazníků a poskytuje dobrý ohrožení chcete být odolní dostatečně na jedné straně. Na druhé straně se tato nastavení fast poskytuje dostatek převzetí služeb při selhání ve skutečné chybové stavy na SAP selhání uzlu nebo virtuálního počítače nebo softwaru. 

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Instalace s DataKeeper Cluster Edition pro SAP ASC nebo SCS disk clusteru sdílené složky

Teď máte fungující konfiguraci služby Windows Server Failover Clustering v Azure. Ale instalaci instance SAP ASC nebo SCS, budete potřebovat prostředek sdíleného disku. Nelze vytvořit sdílené síťové prostředky, které potřebujete v Azure. S DataKeeper Cluster Edition je řešení třetí strany, které můžete použít k vytvoření sdílené síťové prostředky.

Instalace s DataKeeper Cluster Edition pro sdílenou složku disk clusteru SAP ASC nebo SCS zahrnuje tyto úlohy:

- Přidání rozhraní .NET Framework 3.5
- Instalace SIOS DataKeeper
- Nastavení služby s DataKeeper

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Přidání rozhraní .NET Framework 3.5
Rozhraní Microsoft .NET Framework 3.5 se automaticky aktivovat nebo není nainstalovaná v systému Windows Server 2012 R2. Protože DataKeeper s vyžaduje rozhraní .NET Framework na všech uzlech, jež nainstalujete DataKeeper, musíte nainstalovat rozhraní .NET Framework 3.5 v hostovaném operačním systému všech virtuálních počítačů v clusteru.

Chcete-li přidat rozhraní .NET Framework 3.5 dvěma způsoby:

- Použití funkce Průvodce přidáním rolí a v systému Windows, jak ukazuje obrázek 39.

  ![Obrázek 39: Instalace rozhraní .NET Framework 3.5 pomocí funkce Průvodce přidáním rolí a][sap-ha-guide-figure-3028]

  _**Obrázek 39:** instalace rozhraní .NET Framework 3.5 pomocí funkce Průvodce přidáním rolí a_

  ![Obrázek 40: Průběh instalace panelu při instalaci rozhraní .NET Framework 3.5 pomocí Přidat role a funkce Průvodce][sap-ha-guide-figure-3029]

  _**Obrázek 40:** průběh instalace panelu při instalaci rozhraní .NET Framework 3.5 pomocí Přidat role a funkce Průvodce_

- Použijte nástroj příkazového řádku nástroje dism.exe. Pro tento typ instalace musíte pro přístup k adresáři SxS na instalačním médiu systému Windows. Na příkazovém řádku se zvýšenými oprávněními zadejte:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Nainstalujte SIOS DataKeeper

Nainstalujte na každém uzlu v clusteru s DataKeeper Cluster Edition. Pokud chcete vytvořit virtuální sdílené úložiště s s DataKeeper, vytvořte synchronizoval zrcadlení a pak simulovat sdílené úložiště clusteru.

Před instalací softwaru SIOS vytvořit uživatele domény **DataKeeperSvc**.

> [!NOTE]
> Přidat **DataKeeperSvc** uživateli **místního správce** v obou uzlů clusteru.
>
>

Instalace s DataKeeper:

1.  Nainstalujte SIOS software do obou uzlů clusteru.

  ![Instalační program SIOS][sap-ha-guide-figure-3030]

  ![41 obrázek: První stránka instalace s DataKeeper][sap-ha-guide-figure-3031]

  _**Obrázek 41:** první stránka s DataKeeper instalace_

2.  V dialogovém okně znázorňuje obrázek 42 vyberte **Ano**.

  ![Obrázek 42: DataKeeper vás upozorní, že služba bude zakázán][sap-ha-guide-figure-3032]

  _**Obrázek 42:** DataKeeper informuje, že služba bude zakázán_

3.  V dialogovém okně zobrazí obrázek 43 doporučujeme, abyste vybrali **účet domény nebo serveru**.

  ![Obrázek 43: Výběr uživatele s DataKeeper][sap-ha-guide-figure-3033]

  _**Obrázek 43:** výběr uživatele s DataKeeper_

4.  Zadejte uživatelské jméno pro účet domény a hesla, které jste vytvořili pro DataKeeper s.

  ![Obrázek 44: Zadejte uživatelské jméno domény a heslo pro instalace s DataKeeper][sap-ha-guide-figure-3034]

  _**Obrázek 44:** zadejte uživatelské jméno domény a heslo pro instalaci s DataKeeper_

5.  Licenční klíč pro instanci s DataKeeper nainstalujte, jak ukazuje obrázek 45.

  ![45 obrázek: Zadejte klíč licence s DataKeeper][sap-ha-guide-figure-3035]

  _**Obrázek 45:** zadejte klíč DataKeeper s licencí_

6.  Po zobrazení výzvy restartujte virtuální počítač.

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Nastavení pro zařízení s DataKeeper

Po instalaci s DataKeeper oba uzly, budete muset spustit konfiguraci. Cílem konfigurace je tak, aby měl synchronní data replikace mezi další disky připojené ke každému z virtuálních počítačů.

1.  Spusťte nástroj Správa DataKeeper a konfigurace a potom vyberte **připojit Server**. (V obrázku 46, tato možnost je v kroužku červeně.)

  ![46 obrázek: Nástroj Konfigurace a SIOS DataKeeper správy][sap-ha-guide-figure-3036]

  _**Obrázek 46:** s DataKeeper správu a konfiguraci nástroje_

2.  Zadejte název nebo adresu TCP/IP prvního uzlu, který nástroj Správa a konfigurace by se měly připojit k a v druhém kroku, ve druhém uzlu.

  ![Obrázek 47: Vložit název nebo adresu TCP/IP prvního uzlu pro správu a konfigurační nástroj by měl připojit a v druhém kroku, ve druhém uzlu][sap-ha-guide-figure-3037]

  _**Obrázek 47:** vložit název nebo adresu TCP/IP prvního uzlu nástroj pro správu a konfiguraci by měl připojit a v druhém kroku, ve druhém uzlu_

3.  Vytvoření úlohy replikace mezi dvěma uzly.

  ![Obrázek 48: Vytvoření úlohy replikace][sap-ha-guide-figure-3038]

  _**Obrázek 48:** vytvořit úlohu replikace_

  Průvodce vás provede procesem vytvoření úlohy replikace.
4.  Zadejte název, adresa TCP/IP a svazku zdrojový uzel.

  ![Obrázek 49: Definujte název úlohy replikace][sap-ha-guide-figure-3039]

  _**Obrázek 49:** definujte název úlohy replikace_

  ![Obrázek 50: Zadejte základní data pro uzel, které by měly být aktuální zdrojový uzel][sap-ha-guide-figure-3040]

  _**Obrázek 50:** zadat základní data pro uzel, které by měly být aktuální zdrojový uzel_

5.  Zadejte název, adresa TCP/IP a svazku cílový uzel.

  ![Obrázek 51: Zadejte základní data pro uzel, které by měly být aktuální cílový uzel][sap-ha-guide-figure-3041]

  _**Obrázek 51:** zadat základní data pro uzel, které by měly být aktuální cílový uzel_

6.  Definujte algoritmy komprese. V našem příkladu doporučujeme kompresi datového proudu replikace. Hlavně v situacích, opakované synchronizace kompresi datového proudu replikace výrazně snižuje dobu Opakovaná synchronizace. Všimněte si, že komprese používá prostředky procesoru a paměť RAM virtuálního počítače. Jako hodnota se zvyšuje rychlost komprese, takže nemá objem prostředků procesoru, které používá. Můžete také upravit toto nastavení později.

7.  Další nastavení, které je potřeba zkontrolovat se, zda dojde k replikaci synchronně nebo asynchronně. *Pokud budete chránit SAP ASC nebo SCS konfigurace, je nutné použít synchronní replikace*.  

  ![Obrázek 52: Definování podrobnosti k replikaci][sap-ha-guide-figure-3042]

  _**Obrázek 52:** definovat podrobnosti k replikaci_

8.  Zadejte, zda svazek, který se replikují úlohou replikace by měly být zastoupeny ke konfiguraci clusteru Windows Server Failover Clustering jako sdílený disk. Pro konfiguraci SAP ASC nebo SCS vyberte **Ano** tak, aby Windows cluster uvidí replikované svazek jako sdílený disk, který můžete použít jako svazek clusteru.

  ![Obrázek 53: Vyberte možnost Ano nastavit replikované svazek jako svazek clusteru][sap-ha-guide-figure-3043]

  _**Obrázek 53:** vyberte **Ano** nastavit replikované svazek jako svazek clusteru_

  Po vytvoření svazku nástroj DataKeeper Správa a konfigurace ukazuje, že je úloha replikace aktivní.

  ![Obrázek 54: DataKeeper synchronní zrcadlení pro sdílenou složku disk SAP ASC nebo SCS je aktivní][sap-ha-guide-figure-3044]

  _**Obrázek 54:** DataKeeper synchronní zrcadlení pro SAP ASC nebo SCS sdílet disk je aktivní_

  Správce clusteru převzetí služeb při selhání teď disk jako disk s DataKeeper ukazuje, jak ukazuje obrázek 55.

  ![Obrázek 55: Správce clusteru převzetí služeb při selhání zobrazuje na disk, který DataKeeper replikovat][sap-ha-guide-figure-3045]

  _**Obrázek 55:** Správce clusteru převzetí služeb při selhání disku ukazuje, že DataKeeper replikovat_

## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Instalace systému SAP NetWeaver

Instalace databázového systému jsme nebude popisují nastavení lišit v závislosti na tom, které můžete použít systém databázového systému. Ale předpokládáme, že jsou aspekty vysoké dostupnosti s databázového systému řešit pomocí funkce, které různých výrobců databázového systému podpora pro Azure. Například Always On nebo zrcadlení databáze systému SQL Server a Oracle Data Guard pro databáze Oracle. Ve scénáři, které používáme v tomto článku jsme nebyla přidat další ochranu do databázového systému.

Když různé služby databázového systému interakci s tímto typem Clusterované konfigurace SAP ASC nebo SCS v Azure neexistují žádné zvláštní požadavky.

> [!NOTE]
> Postupy instalace systémů SAP NetWeaver ABAP, Java systémy a systémy ABAP + Java jsou téměř shodné. Nejdůležitější rozdíl je, že systému SAP ABAP má jednu instanci ASC. V systému SAP Java má jednu instanci SCS. V systému SAP ABAP + Java obsahuje jednu instanci ASC a jednu instanci SCS spuštěné ve stejné skupině clusteru převzetí služeb při selhání Microsoft. Případné rozdíly instalace pro každou SAP NetWeaver instalace zásobníku se výslovně uveden. Můžete předpokládat, že všechny ostatní části jsou stejné.  
>
>

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Instalace s vysokou dostupností ASC nebo SCS instance SAP

> [!IMPORTANT]
> Nezapomeňte toto stránkovacím souboru na svazcích DataKeeper zrcadlení. DataKeeper nepodporuje zrcadlové svazky. Můžete ponechat stránkovacím souboru na dočasné jednotce D Azure virtuálního počítače, který je výchozí. Pokud není již existuje, přesuňte stránkovací soubor Windows jednotku D: virtuálního počítače Azure.
>
>

Instalace s vysokou dostupností ASC nebo SCS instance SAP zahrnuje tyto úlohy:

- Vytváření název virtuálního hostitele pro SAP ASC nebo SCS skupinu prostředků clusteru
- Instalace prvního uzlu clusteru SAP
- Úprava profilu SAP ASC nebo SCS instance
- Přidání port testu
- Otevřete port testu brány firewall systému Windows

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Vytvořte název virtuálního hostitele pro SAP ASC nebo SCS skupinu prostředků clusteru

1.  Ve Správci DNS systému Windows vytvořte záznam DNS pro název virtuálního hostitele ASC nebo SCS instance.

  > [!IMPORTANT]
  > IP adresa, která přiřadíte název virtuálního hostitele ASC nebo SCS instance musí být stejná jako adresa IP, který jste přiřadili k vyrovnávání zatížení Azure (**<*SID*> - lb - Asc**).  
  >
  >

  IP adresa virtuální název hostitele SAP ASC nebo SCS (**pr1. ASC sap**) je stejný jako IP adresu služby Vyrovnávání zatížení Azure (**pr1-lb Asc**).

  ![Obrázek 56: Definování položky DNS pro virtuální název clusteru SAP ASC nebo SCS a adresa TCP/IP][sap-ha-guide-figure-3046]

  _**Obrázek 56:** definovat položku DNS pro virtuální název clusteru SAP ASC nebo SCS a adresa TCP/IP_

2.  Chcete-li definovat přiřazené název virtuálního hostitele IP adresy, vyberte **Správce DNS** > **domény**.

  ![Obrázek 57: Nový virtuální název a adresu TCP/IP pro konfiguraci clusteru SAP ASC nebo SCS][sap-ha-guide-figure-3047]

  _**Obrázek 57:** nový virtuální název a TCP/IP adres pro konfiguraci clusteru SAP ASC nebo SCS_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Instalace prvního uzlu clusteru SAP

1.  Provést první možnost uzlu clusteru na uzlu clusteru A. Například **pr1-ASC-0** hostitele.
2.  Ponechat výchozí porty pro vyrovnávání zatížení Azure interní, vyberte:

  * **Systém ABAP**: **Asc** instance číslo **00**
  * **Java systému**: **SCS** instance číslo **01**
  * **ABAP + Java systému**: **Asc** instance číslo **00** a **SCS** instance číslo **01**

  Pokud chcete použít instanci čísla než 00 pro instanci ABAP ASC a 01 pro instanci Java SCS, nejdřív je potřeba změnit Azure interní výchozí Vyrovnávání zatížení pravidel, popsaných v [změňte pravidla pro vyrovnávání zatížení výchozí ASC nebo SCS Nástroje pro vyrovnávání zatížení Azure interní][sap-ha-guide-8.9].

Další několik úloh nejsou popsané v dokumentaci standardní instalace SAP.

> [!NOTE]
> Dokumentaci k instalaci SAP popisuje postup instalace prvního uzlu clusteru ASC nebo SCS.
>
>

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Upravit profil SAP ASC nebo SCS instance

Je nutné přidat nový parametr profilu. Parametr profil zabrání připojení mezi SAP pracovní procesy a serverem zařazování zavření po nečinnosti příliš dlouho. Jsme uvedených scénář v [přidat položky registru na obou uzlů clusteru instance SAP ASC nebo SCS][sap-ha-guide-8.11]. V této části zavedli jsme taky dvě změny některé základní parametry připojení TCP/IP. V druhém kroku, je nutné nastavit zařadit server k odeslání `keep_alive` signál, aby připojení není dosáhl nástroje pro vyrovnávání zatížení Azure vnitřní limit nečinnosti.

Úprava profilu SAP instance ASC nebo SCS:

1.  Tento parametr profil přidáte do profilu instance SAP ASC nebo SCS:

  ```
  enque/encni/set_so_keepalive = true
  ```
  V našem příkladu je cesta:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

  Například pro SAP SCS instance profilu a odpovídajících cesta:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2.  Aby se změny projevily, restartujte instanci /SCS ASC SAP.

#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Přidat port testu

Pomocí nástroje pro vyrovnávání zatížení interní test funkce usnadňují konfiguraci celý cluster pracovat s Vyrovnávání zatížení Azure. Nástroje pro vyrovnávání zatížení Azure interní obvykle distribuuje příchozí zatížení rovnoměrně mezi zúčastněných virtuálních počítačů. Ale tato funkce nebude pracovat v některé konfigurace clusteru vzhledem k tomu, že pouze jedna instance je aktivní. Druhá instance je pasivní a nemůže přijímat úlohy. Funkce kontroly pomáhá při nástroje pro vyrovnávání zatížení Azure interní přiřadí pracovní jenom na aktivní instance. Pomocí funkce testu nástroje pro vyrovnávání zatížení pro vnitřní může zjistit, které instancí jsou aktivní a pak cíle pouze instance se zatížením.

Chcete-li přidat port testu:

1.  Zkontrolujte aktuální **ProbePort** nastavení spuštěním následujícího příkazu Powershellu. Spouštění ho do jedné z virtuálních počítačů v konfiguraci clusteru.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
  ```

2.  Definujte port testu. Výchozí číslo portu testu je **0**. V našem příkladu používáme port testu **62000**.

  ![Obrázek 58: Port testu konfigurace clusteru je 0. ve výchozím nastavení][sap-ha-guide-figure-3048]

  _**Obrázek 58:** výchozí port test konfigurace clusteru je 0._

  Číslo portu je definována v šablonách SAP Azure Resource Manager. Můžete přiřadit číslo portu v prostředí PowerShell.

  Chcete-li nastavit novou hodnotu ProbePort **SAP <*SID*> IP** prostředek clusteru, spusťte následující skript prostředí PowerShell. Aktualizujte proměnné prostředí PowerShell pro vaše prostředí. Po spuštění skriptu budete vyzváni, restartujte skupina clusteru SAP k aktivaci změny.

  ```PowerShell
  $SAPSID = "PR1"      # SAP <SID>
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  Clear-Host
  $SAPClusterRoleName = "SAP $SAPSID"
  $SAPIPresourceName = "SAP $SAPSID IP"
  $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
  $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
  $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
  $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
  $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
  $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
  $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

  $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

  Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
  Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

  Write-Host
  Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
  Write-Host
  Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
  Write-Host

  $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

  Write-Host

  $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

  if($ActivateChanges -eq "yes"){
  Write-Host
  Write-Host "Activating changes..." -ForegroundColor Cyan

  Write-Host
  write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
  Stop-ClusterResource -Name $SAPIPresourceName
  sleep 5

  Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
  Start-ClusterGroup -Name $SAPClusterRoleName

  Write-Host "New ProbePort parameter is active." -ForegroundColor Green
  Write-Host

  Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
  Write-Host
  Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
  }else
  {
  Write-Host "Changes are not activated."
  }
  ```

  Po přepnutí **SAP <*SID* >**  clusteru role online, ověřte, že **ProbePort** nastavena na novou hodnotu.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

  ```

  ![Obrázek 59: Probe port clusteru po nastavení nová hodnota][sap-ha-guide-figure-3049]

  _**Obrázek 59:** testu port clusteru po nastavení nová hodnota_

#### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Otevřete port testu brány firewall systému Windows

Budete muset otevřít port testu brány firewall systému Windows na obou uzlů clusteru. Pomocí následujícího skriptu otevřít port testu brány firewall systému Windows. Aktualizujte proměnné prostředí PowerShell pro vaše prostředí.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** je nastaven na **62000**. Teď můžete přístup ke sdílené složce  **\\\ascsha-clsap\sapmnt** z jiných hostitelů, například jako z **ascsha dbas**.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Instalovat instanci databáze

Chcete-li nainstalovat instanci databáze, postupujte podle procesu popsaného v dokumentaci k instalaci SAP.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Instalaci druhého uzlu clusteru

K instalaci druhé clusteru, postupujte podle kroků v Průvodci instalací SAP.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Změnit typ spuštění instance služby Windows YBRAT SAP

Změnit typ spuštění služby Windows YBRAT SAP **automaticky (zpožděné spuštění)** na obou uzlů clusteru.

![Obrázek 60: Změňte typ služby pro instance SAP YBRAT na zpožděné automaticky][sap-ha-guide-figure-3050]

_**Obrázek 60:** změnit typ služby pro instance SAP YBRAT pro odložené automatické_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Instalace serveru primární aplikace SAP

Nainstalovat instanci serveru primární aplikace (Pa) <*SID*> - di - 0 na virtuálním počítači, který jste určili pro hostování Pa ADRESAMI. Neexistují žádné závislosti na Azure nebo DataKeeper specifická nastavení.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Instalace serveru SAP další aplikace

Nainstalujte SAP serveru pro další aplikace (AAS) na všechny virtuální počítače, které jste označili k hostování instance aplikačního serveru SAP. Například na <*SID*> - di - 1 pro <*SID*> - di -&lt;n&gt;.

> [!NOTE]
> To dokončí instalaci systému SAP NetWeaver vysokou dostupnost. Pokračujte dále testování převzetí služeb při selhání.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testovací převzetí služeb při selhání SAP ASC nebo SCS instance a SIOS replikace
Je snadné pro testování a monitorování SAP ASC nebo SCS instance převzetí služeb při selhání a replikaci disku SIOS pomocí nástroje Správce clusteru převzetí služeb při selhání a s DataKeeper správy a konfigurace.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> V uzlu clusteru A je spuštěna instance SAP ASC nebo SCS

**SAP PR1** skupina clusteru běží na uzlu clusteru A. Například na **pr1-ASC-0**. Přiřadit sdílené diskové jednotce S, která je součástí systému **SAP PR1** skupina clusteru a který instance ASC nebo SCS používá A. uzlu v clusteru

![Obrázek 61: Správce clusteru převzetí služeb při selhání: Skupina clusteru SAP < SID > běží na uzlu clusteru A][sap-ha-guide-figure-5000]

_**Obrázek 61:** Správce clusteru převzetí služeb při selhání: SAP <*SID*> Skupina clusteru běží na uzlu clusteru A_

Nástroje pro správu s DataKeeper a konfiguraci uvidíte, že dat sdíleného disku je synchronně replikovat ze zdrojového svazku jednotky S na uzlu clusteru A jednotky cílový svazek S na uzlu clusteru B. Například se replikují z **pr1-ASC-0 [10.0.0.40]** k **pr1-ASC-1 [10.0.0.41]**.

![Obrázek 62: V DataKeeper s replikovat místní svazek z uzlu clusteru A k uzlu clusteru B][sap-ha-guide-figure-5001]

_**Obrázek 62:** v DataKeeper s replikovat místní svazek z uzlu clusteru A k uzlu clusteru B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Převzetí služeb při selhání z uzlu A k uzlu B

1.  Vyberte jednu z těchto možností k zahájení převzetí služeb při selhání SAP <*SID*> Skupina clusteru z uzlu clusteru A k uzlu clusteru B:
  - Pomocí Správce clusteru převzetí služeb při selhání  
  - Pomocí prostředí PowerShell pro Cluster převzetí služeb při selhání

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPClusterGroup = "SAP $SAPSID"
  Move-ClusterGroup -Name $SAPClusterGroup

  ```
2.  Restartovat A uzlu clusteru v rámci hostovaného operačního systému Windows (tím se vyvolá automatické převzetí služeb při selhání z SAP <*SID*> Skupina clusteru z uzlu A k uzlu B).  
3.  Restartovat uzel A cluster z portálu Azure (tím se vyvolá automatické převzetí služeb při selhání z SAP <*SID*> Skupina clusteru z uzlu A k uzlu B).  
4.  Restartujte počítač A uzlu clusteru pomocí prostředí Azure PowerShell (tím se vyvolá automatické převzetí služeb při selhání z SAP <*SID*> Skupina clusteru z uzlu A k uzlu B).

  Po převzetí služeb při selhání, SAP <*SID*> Skupina clusteru běží na uzlu clusteru B. Například je spuštěn na **pr1-ASC-1**.

  ![Obrázek 63: Ve Správci clusteru převzetí služeb při selhání skupiny clusteru SAP < SID > běží na uzlu clusteru B][sap-ha-guide-figure-5002]

  _**Obrázek 63**: ve Správci clusteru převzetí služeb při selhání, SAP <*SID*> Skupina clusteru běží na uzlu clusteru B_

  Sdílený disk je teď připojené v clusteru uzlu B. s DataKeeper se replikuje data ze zdrojového svazku jednotky S na uzlu clusteru B na cílový svazek jednotky S na uzlu clusteru A. Například je replikace z **pr1-ASC-1 [10.0.0.41]** k **pr1-ASC-0 [10.0.0.40]**.

  ![Obrázek 64: SIOS DataKeeper z uzlu clusteru B A uzlu v clusteru se replikuje místního svazku][sap-ha-guide-figure-5003]

  _**Obrázek 64:** s DataKeeper replikuje místní svazek z uzlu clusteru B A uzlu v clusteru_
