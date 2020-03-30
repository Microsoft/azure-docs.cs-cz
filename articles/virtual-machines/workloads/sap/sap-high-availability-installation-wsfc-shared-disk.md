---
title: Instalace sap netweaveru HA do clusteru s podporou převzetí služeb při selhání systému Windows a sdíleného disku pro instanci SAP ASCS/SCS v Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nainstalovat SAP NetWeaver HA do clusteru s podporou převzetí služeb při selhání systému Windows a sdíleného disku pro instanci SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e50733c843dfd21e35572f00fc6690e1e84aba97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279829"
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

Tento článek popisuje, jak nainstalovat a nakonfigurovat vysoce dostupnost systému SAP v Azure pomocí clusteru s podporou převzetí služeb při selhání windows serveru a sdíleného disku clusteru pro clusterování instance SAP ASCS/SCS.

## <a name="prerequisites"></a>Požadavky

Než začnete s instalací, přečtěte si tyto dokumenty:

* [Průvodce architekturou: Cluster instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdíleného disku clusteru][sap-high-availability-guide-wsfc-shared-disk]

* [Příprava infrastruktury Azure pro SAP HA pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdíleného disku pro instanci SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

V tomto článku nepopisujeme nastavení DBMS, protože nastavení se liší v závislosti na systému DBMS, který používáte. Předpokládáme, že obavy o vysokou dostupnost s DBMS jsou řešeny s funkcemi, které různé dodavatele DBMS podporují pro Azure. Příklady jsou AlwaysOn nebo zrcadlení databáze pro SQL Server a Oracle Data Guard pro databáze Oracle. Ve scénáři, který používáme v tomto článku, nepřidáme další ochranu do systému DBMS.

Neexistují žádné zvláštní aspekty při různých službách DBMS interakci s clusterované SAP ASCS nebo Konfigurace SCS v Azure.

> [!NOTE]
> Instalační postupy systémů SAP NetWeaver ABAP, Java systémů a ABAP+Java jsou téměř identické. Nejvýznamnější rozdíl je, že systém SAP ABAP má jednu instanci ASCS. Systém SAP Java má jednu instanci SCS. Systém SAP ABAP+Java má jednu instanci ASCS a jednu instanci SCS spuštěnou ve stejné skupině clusterů microsoft u převzetí služeb při selhání. Všechny rozdíly v instalaci pro každý instalační balíček SAP NetWeaver jsou výslovně uvedeny. Můžete předpokládat, že všechny ostatní části jsou stejné.  
>
>

## <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Instalace SAP s vysokou dostupností instance ASCS/SCS

> [!IMPORTANT]
> Ujistěte se, že neumístíte stránkovací soubor na zrcadlené svazky SIOS DataKeeper. DataKeeper nepodporuje zrcadlené svazky. Stránkovací soubor můžete ponechat na dočasné jednotce D virtuálního počítače Azure, což je výchozí. Pokud ještě není k dispozici, přesuňte stránkovací soubor Windows tak, aby řídil D vašeho virtuálního počítače Azure.
>
>

Instalace SAP s vysokou dostupností instance ASCS/SCS zahrnuje tyto úkoly:

* Vytvořte název virtuálního hostitele pro clusterovnou instanci SAP ASCS/SCS.
* Nainstalujte uzel prvního clusteru SAP.
* Upravte profil SAP instance ASCS/SCS.
* Přidejte port sondy.
* Otevřete port sondy brány firewall systému Windows.

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Vytvoření názvu virtuálního hostitele pro clusterovnou instanci SAP ASCS/SCS

1. Ve Správci SLUŽBY DNS systému Windows vytvořte položku DNS pro název virtuálního hostitele instance ASCS/SCS.

   > [!IMPORTANT]
   > IP adresa, kterou přiřadíte k názvu virtuálního hostitele instance ASCS/SCS, musí být stejná\<jako\>IP adresa, kterou jste přiřadili k nástroji Provyklad a disektovi (SID -lb-ascs).  
   >
   >

   IP adresa virtuálního názvu hostitele SAP ASCS/SCS (pr1-ascs-sap) je stejná jako IP adresa nástroje Azure Load Balancer (pr1-lb-ascs).

   ![Obrázek 1: Definování položky DNS pro virtuální název clusteru SAP ASCS/SCS a adresu TCP/IP][sap-ha-guide-figure-3046]

   _**Obrázek 1:** Definování položky DNS pro virtuální název clusteru SAP/SCS a adresu TCP/IP_

2. Chcete-li definovat adresu IP přiřazenou názvu virtuálního hostitele, vyberte**doménu** **správce** > DNS .

   ![Obrázek 2: Nový virtuální název a adresa TCP/IP pro konfiguraci clusteru SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _**Obrázek 2:** Nový virtuální název a adresa TCP/IP pro konfiguraci clusteru SAP ASCS/SCS_

### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Instalace prvního uzlu clusteru SAP

1. Spusťte první možnost uzlu clusteru v uzlu Clusteru A. Například na pr1-ascs-0*host.
2. Pokud chcete zachovat výchozí porty pro interní systém vyrovnávání zatížení Azure, vyberte:

   * **Systém ABAP**: **Instance ASCS** číslo **00**
   * **Java systém**: **SCS** instance číslo **01**
   * **Systém ABAP+Java**: **Instance ASCS** číslo **00** a Číslo instance **SCS** **01**

   Chcete-li použít čísla instancí než 00 pro instanci Ascs ABAP a 01 pro instanci Java SCS, nejprve změňte výchozí pravidla vyrovnávání zatížení Azure internal load balancer. Další informace najdete [v tématu Změna výchozích pravidel vyrovnávání zatížení ASCS/SCS pro interní vyrovnávání zatížení Azure][sap-ha-guide-8.9].

Několik dalších úkolů není popsáno ve standardní dokumentaci k instalaci SAP.

> [!NOTE]
> Instalační dokumentace SAP popisuje, jak nainstalovat první uzel clusteru ASCS/SCS.
>
>

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Změna profilu SAP instance ASCS/SCS

Nejprve přidejte nový parametr profilu. Parametr profilu zabraňuje připojení mezi pracovní procesy SAP a enqueue server uzavření, když jsou nečinné příliš dlouho. Problémový scénář uvádíme v [části Přidat položky registru na obou uzlech clusteru instance SAP ASCS/SCS][sap-ha-guide-8.11]. V této části také zavádíme dvě změny některých základních parametrů připojení TCP/IP. Ve druhém kroku je třeba nastavit server fronty `keep_alive` pro odeslání signálu tak, aby připojení nedosáhne prahové hodnoty nečinnosti interního vyvažovače zatížení Azure.

Chcete-li upravit profil SAP instance ASCS/SCS:

1. Přidejte tento parametr profilu do profilu instance SAP ASCS/SCS:

   ```
   enque/encni/set_so_keepalive = true
   ```
   V našem příkladu je cesta:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Například do profilu instance SAP SCS a odpovídající cesty:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Chcete-li změny použít, restartujte instanci SAP ASCS/SCS.

### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Přidání portu sondy

Pomocí funkce sondy nástroje pro vyrovnávání zatížení můžete celou konfiguraci clusteru zpracovat pomocí nástroje Azure Load Balancer. Nástroj pro interní vyrovnávání zatížení Azure obvykle distribuuje příchozí úlohy rovnoměrně mezi zúčastněné virtuální počítače.

 V některých konfiguracích clusteru to však nebude fungovat, protože je aktivní pouze jedna instance. Druhá instance je pasivní a nemůže přijmout žádné úlohy. Funkce sondy pomáhá, když interní vyrovnávání zatížení Azure přiřadí práci pouze aktivní instanci. Pomocí funkce sondy může interní vyvažovač zatížení zjistit, které instance jsou aktivní, a pak cílit pouze na instanci s úlohou.

Přidání portu sondy:

1. Zkontrolujte aktuální hodnotu **ProbePort** spuštěním následujícího příkazu PowerShellu:

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

   Spusťte příkaz z jednoho z virtuálních počítačů v konfiguraci clusteru.

2. Definujte port sondy. Výchozí číslo portu sondy je 0. V našem příkladu používáme port sondy 62000.

   ![Obrázek 3: Port konfigurační sondy clusteru je ve výchozím nastavení 0][sap-ha-guide-figure-3048]

   _**Obrázek 3:** Výchozí port konfigurační sondy clusteru je 0_

   Číslo portu je definováno v šablonách SAP Azure Resource Manager. Číslo portu můžete přiřadit v prostředí PowerShell.

   Chcete-li nastavit novou hodnotu \<ProbePort pro prostředek clusteru IP SAP SID,\> spusťte následující skript prostředí PowerShell a aktualizujte proměnné prostředí PowerShell pro vaše prostředí:

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

   Po přepnete roli clusteru SAP \<SID\> do režimu online, ověřte, zda je **probeport** nastaven na novou hodnotu.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```
   Po spuštění skriptu budete vyzváni k restartování skupiny clusteru SAP k aktivaci změn.

   ![Obrázek 4: Sonda portu clusteru po nastavení nové hodnoty][sap-ha-guide-figure-3049]

   _**Obrázek 4:** Sonda portu clusteru po nastavení nové hodnoty_

### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>Otevření portu sondy brány firewall systému Windows

Otevřete port sondy brány firewall systému Windows v obou uzlech clusteru. Pomocí následujícího skriptu otevřete port sondy brány firewall systému Windows. Aktualizujte proměnné prostředí PowerShell pro vaše prostředí.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** je nastaven na **62000**. Nyní můžete získat přístup \\ke sdílené složce \ascsha-clsap\sapmnt od jiných hostitelů, například z ascsha-dbas.

## <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Instalace instance databáze

Chcete-li nainstalovat instanci databáze, postupujte podle procesu popsaného v dokumentaci k instalaci SAP.

## <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Instalace druhého uzlu clusteru

Chcete-li nainstalovat druhý cluster, postupujte podle pokynů popsaných v instalační příručce SAP.

## <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Změna typu spuštění instance služby SAP ERS windows

Změňte typ spuštění služby SAP ERS Windows na **automatické (zpožděné spuštění)** v obou uzlech clusteru.

![Obrázek 5: Změna typu služby pro instanci SAP ERS na zpožděnou automatickou][sap-ha-guide-figure-3050]

_**Obrázek 5:** Změna typu služby pro instanci SAP ERS na zpožděnou automatickou_

## <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>Instalace primárního aplikačního serveru SAP

Nainstalujte instanci \<primárního aplikačního serveru (PAS) SID\>-di-0 na virtuální počítač, který jste určili k hostování PAS. Neexistují žádné závislosti na Azure. Neexistují žádná nastavení specifická pro datakeeper.

## <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>Instalace dalšího aplikačního serveru SAP

Nainstalujte sap další aplikační server (AAS) na všechny virtuální počítače, které jste určili pro hostování instance aplikačního serveru SAP. Například na \<SID\>-di-1 až \<SID\>-di-&lt;n&gt;.

> [!NOTE]
> Tím je dokončena instalace vysoce dostupnosti systému SAP NetWeaver. Dále pokračujte v testování převzetí služeb při selhání.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>Testování převzetí služeb při selhání instance SAP ASCS/SCS a replikace SIOS
Je snadné testovat a monitorovat převzetí služeb při selhání instance SAP ASCS/SCS a replikaci disku SIOS pomocí Správce clusteru s podporou převzetí služeb při selhání a nástroje pro správu a konfiguraci datového keeperu SIOS.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>Instance SAP ASCS/SCS je spuštěna na uzlu clusteru A

Skupina clusteru SAP PR1 je spuštěna v uzlu Clusteru A. Například na pr1-ascs-0. Přiřaďte uzel A sdílené diskové jednotky S, která je součástí skupiny clusterů SAP PR1. Instance ASCS/SCS také používá diskovou jednotku S. 

![Obrázek 6: Správce clusteru \<s\> podporou převzetí služeb při selhání: Skupina clusterů SAP SID je spuštěna v uzlu Clusteru A][sap-ha-guide-figure-5000]

_**Obrázek 6:** Správce clusteru s \<podporou\> převzetí služeb při selhání: Skupina clusterů SAP SID je spuštěna v uzlu Clusteru A_

V nástroji pro správu a konfiguraci datového keeperu SIOS uvidíte, že data sdíleného disku jsou synchronně replikována ze zdrojové jednotky svazku S v uzlu Clusteru A na cílovou jednotku svazku S v uzlu clusteru B. Například je replikován z pr1-ascs-0 [10.0.0.40] na pr1-ascs-1 [10.0.0.41].

![Obrázek 7: V souboru SIOS DataKeeper replikujte místní svazek z uzlu clusteru A do uzlu Cluster B][sap-ha-guide-figure-5001]

_**Obrázek 7:** V souboru SIOS DataKeeper replikujte místní svazek z uzlu clusteru A do uzlu Clusterb_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>Převzetí služeb při selhání z uzlu A do uzlu B

1. Vyberte jednu z těchto možností, \<chcete-li zahájit převzetí služeb při selhání skupiny clusterů SAP SID\> z uzlu Clusteru A do uzlu Cluster B:
   - Správce clusteru s podporou převzetí služeb při selhání  
   - Prostředí PowerShell clusteru s podporou převzetí služeb při selhání

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Restartujte uzel clusteru A v rámci hostovaného operačního systému Windows. Tím se iniciuje \<automatické\> převzetí služeb při selhání skupiny clusteru SAP SID z uzlu A do uzlu B.  
3. Restartujte uzel clusteru A z portálu Azure. Tím se iniciuje \<automatické\> převzetí služeb při selhání skupiny clusteru SAP SID z uzlu A do uzlu B.  
4. Restartujte uzel clusteru A pomocí Azure PowerShellu. Tím se iniciuje \<automatické\> převzetí služeb při selhání skupiny clusteru SAP SID z uzlu A do uzlu B.

   Po převzetí služeb \<při\> selhání je skupina clusteru SAP SID spuštěna v uzlu clusteru B. Například běží na pr1-ascs-1.

   ![Obrázek 8: Ve Správci \<clusteru\> s podporou převzetí služeb při selhání je skupina clusterů SAP SID spuštěna v uzlu clusteru B][sap-ha-guide-figure-5002]

   _**Obrázek 8:** Ve Správci \<clusterů\> s podporou převzetí služeb při selhání je skupina clusterů SAP SID spuštěna v uzlu clusteru B_

   Sdílený disk je nyní připojen k uzlu clusteru B. SIOS DataKeeper replikuje data ze zdrojové jednotky svazku S v uzlu clusteru B na cílovou jednotku svazku S v uzlu clusteru A. Například je replikace z pr1-ascs-1 [10.0.0.41] na pr1-ascs-0 [10.0.0.40].

   ![Obrázek 9: Sios DataKeeper replikuje místní svazek z uzlu clusteru B do uzlu Clusteru A][sap-ha-guide-figure-5003]

   _**Obrázek 9:** Sios DataKeeper replikuje místní svazek z uzlu clusteru B do uzlu Clusteru A_
