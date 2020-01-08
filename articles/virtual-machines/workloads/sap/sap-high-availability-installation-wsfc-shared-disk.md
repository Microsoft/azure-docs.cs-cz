---
title: Instalace SAP NetWeaver HA do clusteru s podporou převzetí služeb při selhání systému Windows a sdíleného disku pro instanci SAP ASCS/SCS v Azure | Microsoft Docs
description: Naučte se, jak nainstalovat SAP NetWeaver HA do clusteru s podporou převzetí služeb při selhání systému Windows a sdíleného disku pro instanci SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bfbff1f95eaad41813ee0741a6b133dccdae181d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647522"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Instalace SAP NetWeaver HA do clusteru s podporou převzetí služeb při selhání systému Windows a sdíleného disku pro instanci SAP ASCS/SCS v Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

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
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md



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

Tento článek popisuje, jak nainstalovat a nakonfigurovat systém SAP s vysokou dostupností v Azure pomocí clusteru s podporou převzetí služeb při selhání Windows serveru a sdíleného disku clusteru pro clustering instance SAP ASCS/SCS.

## <a name="prerequisites"></a>Požadavky

Než začnete s instalací, přečtěte si tyto dokumenty:

* [Průvodce architekturou: cluster a instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdíleného disku clusteru][sap-high-availability-guide-wsfc-shared-disk]

* [Příprava infrastruktury Azure pro SAP HA pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdíleného disku pro instanci SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

Nepopisujeme instalaci DBMS v tomto článku, protože se nastavení liší v závislosti na použitém systému DBMS. Předpokládáme, že se systémy DBMS s vysokou dostupností řeší s funkcemi, které pro Azure podporují různí dodavatelé DBMS. Příklady jsou funkce AlwaysOn nebo zrcadlení databáze pro SQL Server a Oracle data Guard pro databáze Oracle. Ve scénáři, který používáme v tomto článku, nepřidáme do systému DBMS větší ochranu.

Neexistují žádné zvláštní požadavky, pokud různé služby DBMS komunikují s Clusterovou konfigurací SAP ASCS nebo SCS v Azure.

> [!NOTE]
> Postupy instalace systémů SAP NetWeaver ABAP, systémů Java a ABAP + Java jsou skoro stejné. Nejvýznamnějším rozdílem je, že systém SAP ABAP má jednu instanci ASCS. Systém SAP Java má jednu instanci SCS. Systém SAP ABAP + Java má jednu instanci ASCS a jednu instanci SCS spuštěnou ve stejné skupině clusteru s podporou převzetí služeb při selhání společnosti Microsoft. Všechny rozdíly v instalacích pro jednotlivé instalační zásobníky SAP NetWeaver jsou výslovně zmíněné. Můžete předpokládat, že všechny ostatní části jsou stejné.  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Instalace SAP s vysokou dostupností instance ASCS/SCS

> [!IMPORTANT]
> Ujistěte se, že se stránkovací soubor neumísťuje na zrcadlené svazky s datakeeping. DataKeeper nepodporuje zrcadlené svazky. Stránkovací soubor můžete ponechat na dočasné jednotce na virtuálním počítači Azure, což je výchozí nastavení. Pokud tam ještě není, přesuňte soubor stránky Windows na jednotku D virtuálního počítače Azure.
>
>

Instalace SAP s vysokou dostupností instance ASCS/SCS zahrnuje tyto úlohy:

* Vytvořte název virtuálního hostitele pro clusterovanou instanci SAP ASCS/SCS.
* Nainstalujte první uzel clusteru SAP.
* Upravte profil SAP instance ASCS/SCS.
* Přidejte port sondy.
* Otevřete port testu brány Windows Firewall.

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Vytvoří název virtuálního hostitele pro clusterovanou instanci SAP ASCS/SCS.

1. Ve Správci DNS systému Windows vytvořte záznam DNS pro název virtuálního hostitele instance ASCS/SCS.

   > [!IMPORTANT]
   > IP adresa, kterou přiřadíte názvu virtuálního hostitele instance ASCS/SCS, musí být stejná jako IP adresa, kterou jste přiřadili Azure Load Balancer (\<SID\>-9,1-ASCS).  
   >
   >

   IP adresa virtuálního hostitele SAP ASCS/SCS (PR1-ASCS-SAP) se shoduje s IP adresou Azure Load Balancer (PR1-9,1-ASCS).

   ![Obrázek 1: definování položky DNS pro virtuální název clusteru SAP ASCS/SCS a adresu protokolu TCP/IP][sap-ha-guide-figure-3046]

   _**Obrázek 1:** Zadejte položku DNS pro virtuální název clusteru SAP ASCS/SCS a adresu TCP/IP._

2. Pokud chcete definovat IP adresu, která je přiřazená k názvu virtuálního hostitele, vyberte > **doména** **Správce DNS** .

   ![Obrázek 2: nový virtuální název a adresa TCP/IP pro konfiguraci clusteru SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _**Obrázek 2:** Nový virtuální název a adresa TCP/IP pro konfiguraci clusteru SAP ASCS/SCS_

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Instalace prvního uzlu clusteru SAP

1. Provede první možnost uzlu clusteru v uzlu clusteru A. Například na hostiteli PR1-ASCS-0 *.
2. Pokud chcete zachovat výchozí porty pro interní nástroj pro vyrovnávání zatížení Azure, vyberte:

   * **ABAP System**: **ASCS** instance číslo **00**
   * **Java System**: **SCS** instance číslo **01**
   * **ABAP + Java System**: **ASCS** instance číslo **00** a **SCS** instance číslo **01**

   Pokud chcete pro instanci ABAP ASCS použít jiné než 00 instancí instance a 01 pro instanci Java SCS, změňte nejdříve výchozí pravidla vyrovnávání zatížení interní služby Load Balancer Azure. Další informace najdete v tématu [Změna výchozích pravidel vyrovnávání zatížení ASCS/SCS pro interní nástroj pro vyrovnávání zatížení Azure][sap-ha-guide-8.9].

Několik dalších úloh není popsáno v dokumentaci standardní instalace SAP.

> [!NOTE]
> Dokumentace k instalaci SAP popisuje, jak nainstalovat první uzel clusteru ASCS/SCS.
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Úprava profilu SAP instance ASCS/SCS

Nejdřív přidejte nový parametr profilu. Parametr Profile brání propojení mezi pracovními procesy SAP a serverem fronty v době, kdy jsou nečinné příliš dlouho. Uvádíme scénář problému v části [Přidání položek registru na obou uzlech clusteru instance SAP ASCS/SCS][sap-ha-guide-8.11]. V této části také zavádíme dvě změny některých základních parametrů připojení TCP/IP. V druhém kroku je potřeba nastavit server fronty tak, aby odesílal `keep_alive` signál, aby připojení nedosáhla prahové hodnoty nečinnosti interního nástroje pro vyrovnávání zatížení Azure.

Postup úpravy profilu SAP instance ASCS/SCS:

1. Přidejte tento parametr profilu do profilu instance SAP ASCS/SCS:

   ```
   enque/encni/set_so_keepalive = true
   ```
   V našem příkladu je cesta:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Například na profil instance SAP SCS a odpovídající cestu:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Chcete-li změny použít, restartujte instanci SAP ASCS/SCS.

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Přidat port testu paměti

Pomocí funkce testu interního nástroje pro vyrovnávání zatížení můžete zajistit, aby celá konfigurace clusteru fungovala s Azure Load Balancer. Interní nástroj pro vyrovnávání zatížení Azure obvykle distribuuje příchozí zatížení rovnoměrně mezi zúčastněnými virtuálními počítači.

 To ale v některých konfiguracích clusteru nebude fungovat, protože je aktivní jenom jedna instance. Druhá instance je pasivní a nemůže přijmout žádnou z úloh. Funkce sondy pomáhá, když interní nástroj pro vyrovnávání zatížení Azure přiřadí práci pouze k aktivní instanci. Nástroj pro vyrovnávání zatížení může pomocí funkce sondy zjistit, které instance jsou aktivní, a potom cílit jenom na instanci s úlohou.

Postup přidání portu sondy:

1. Spusťte následující příkaz prostředí PowerShell a ověřte aktuální hodnotu **ProbePort** :

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

   Příkaz spusťte v rámci jednoho z virtuálních počítačů v konfiguraci clusteru.

2. Definujte port testu paměti. Výchozí číslo portu testu je 0. V našem příkladu používáme port testu 62000.

   ![Obrázek 3: ve výchozím nastavení je port testu konfigurace clusteru 0.][sap-ha-guide-figure-3048]

   _**Obrázek 3:** Výchozí port testu konfigurace clusteru je 0._

   Číslo portu je definované v šablonách SAP Azure Resource Manager. V PowerShellu můžete přiřadit číslo portu.

   Pokud chcete nastavit novou hodnotu ProbePort pro prostředek clusteru IP\> SAP \<SID, spusťte následující skript prostředí PowerShell a aktualizujte proměnné PowerShellu pro vaše prostředí:

   ```powershell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure internal load balancer

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

   Po převedení\> role clusteru SAP \<SID ověřte, že je **ProbePort** nastavená na novou hodnotu.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```
   Po spuštění skriptu se zobrazí výzva k restartování skupiny clusterů SAP, aby se změny aktivovaly.

   ![Obrázek 4: test portu clusteru po nastavení nové hodnoty][sap-ha-guide-figure-3049]

   _**Obrázek 4:** Test portu clusteru po nastavení nové hodnoty_

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>Otevřete port testu brány Windows Firewall.

Na obou uzlech clusteru otevřete port testu brány Windows Firewall. Pomocí následujícího skriptu otevřete port sondy brány Windows Firewall. Aktualizujte proměnné PowerShellu pro vaše prostředí.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** je nastavená na **62000**. Nyní můžete ke sdílené složce přistupovat \\\ascsha-clsap\sapmnt z jiných hostitelů, například z ascsha-specializující.

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Instalace instance databáze

Pokud chcete nainstalovat instanci databáze, postupujte podle kroků popsaných v dokumentaci k instalaci SAP.

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Instalace druhého uzlu clusteru

Pokud chcete nainstalovat druhý cluster, postupujte podle kroků popsaných v instalační příručce SAP.

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Změna počátečního typu instance služby SAP OLAJÍCÍCH systému Windows

Změňte počáteční typ služby SAP OLAJÍCÍCH Windows na **automaticky (zpožděné spuštění)** na obou uzlech clusteru.

![Obrázek 5: Změna typu služby pro instanci SAP OLAJÍCÍCH na zpožděné automatické][sap-ha-guide-figure-3050]

_**Obrázek 5:** Změnit typ služby pro instanci SAP OLAJÍCÍCH na zpožděné automatické_

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>Instalace primárního aplikačního serveru SAP

Nainstalujte primární aplikační server (PAS) \<identifikátor SID\>-di-0 na virtuálním počítači, který jste určili pro hostování PAS. V Azure neexistují žádné závislosti. Neexistují žádná nastavení specifická pro datakeeping.

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>Instalace dalšího aplikačního serveru SAP

Nainstalujte další aplikační Server SAP (AAS) na všechny virtuální počítače, které jste určili pro hostování instance aplikačního serveru SAP. Například v \<SID\>-di-1 na \<SID\>-di-&lt;n&gt;.

> [!NOTE]
> Tím se dokončí instalace systému SAP NetWeaver s vysokou dostupností. Dále pokračujte v testování převzetí služeb při selhání.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>Test převzetí služeb při selhání instance SAP ASCS/SCS a s replikaci
Můžete snadno testovat a monitorovat převzetí služeb při selhání instance SAP ASCS/SCS a replikaci s využitím disku pomocí Správce clusteru s podporou převzetí služeb při selhání a nástroje pro správu a konfiguraci s podporou datakeepers.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>Instance SAP ASCS/SCS je spuštěná na uzlu clusteru A.

Clusterová skupina SAP PR1 je spuštěná v uzlu clusteru A. Například na PR1-ASCS-0. Přiřaďte sdílenou diskovou jednotku S, která je součástí skupiny clusterů SAP PR1, na uzel cluster A. Instance ASCS/SCS také používá diskovou jednotku S. 

![Obrázek 6: Správce clusteru s podporou převzetí služeb při selhání: Skupina clusteru\> \<SID služby SAP je spuštěná na uzlu clusteru A][sap-ha-guide-figure-5000]

_**Obrázek 6:** Správce clusteru s podporou převzetí služeb při selhání: Skupina clusterů\> SAP \<SID je spuštěná na uzlu clusteru A_

V nástroji pro správu a konfiguraci s daty můžete vidět, že data sdíleného disku se synchronně replikují ze zdrojového svazku na uzlu clusteru A na cílovou jednotku svazku v uzlu clusteru B. Například je replikován z PR1-ASCS-0 [10.0.0.40] na PR1-ASCS-1 [10.0.0.41].

![Obrázek 7: v rámci s datakeepy replikuje místní svazek z uzlu clusteru A na uzel clusteru B.][sap-ha-guide-figure-5001]

_**Obrázek 7:** Replikace místního svazku v rámci s DataKeeper z uzlu clusteru A na uzel clusteru B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>Převzetí služeb při selhání z uzlu A na uzel B

1. Vyberte jednu z těchto možností pro zahájení převzetí služeb při selhání\> skupiny clusterů SAP \<z uzlu clusteru A na uzel clusteru B:
   - Správce clusteru s podporou převzetí služeb při selhání  
   - PowerShellový cluster s podporou převzetí služeb

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Restartujte uzel clusteru A v hostovaném operačním systému Windows. Tím se spustí automatické převzetí služeb při selhání\> skupiny clusterů SAP \<z uzlu A na uzel B.  
3. Restartujte uzel clusteru A z Azure Portal. Tím se spustí automatické převzetí služeb při selhání\> skupiny clusterů SAP \<z uzlu A na uzel B.  
4. Restartujte uzel clusteru A pomocí Azure PowerShell. Tím se spustí automatické převzetí služeb při selhání\> skupiny clusterů SAP \<z uzlu A na uzel B.

   Po převzetí služeb při selhání je skupina clusteru SAP \<SID\> spuštěná na uzlu clusteru B. Například je spuštěný v PR1-ASCS-1.

   ![Obrázek 8: v Správce clusteru s podporou převzetí služeb při selhání skupina clusterů\> \<SID služby SAP spuštěna na uzlu clusteru B][sap-ha-guide-figure-5002]

   _**Obrázek 8**: v Správce clusteru s podporou převzetí služeb při selhání skupina clusterů\> \<SID služby SAP spuštěna na uzlu clusteru B_

   Sdílený disk je nyní připojen k uzlu clusteru B. s zdrojem dat je replikace dat ze zdrojového svazku na clusteru B do cílové jednotky svazku v uzlu clusteru A. Například se replikuje z PR1-ASCS-1 [10.0.0.41] na PR1-ASCS-0 [10.0.0.40].

   ![Obrázek 9: s DataKeeper replikuje místní svazek z uzlu clusteru B na uzel clusteru A.][sap-ha-guide-figure-5003]

   _**Obrázek 9:** S-datakeeping replikuje místní svazek z uzlu clusteru B na uzel clusteru A._
