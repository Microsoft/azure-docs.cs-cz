---
title: IBM DB2 pureScale v Azure
description: V tomto článku se zobrazuje architektura pro spuštění prostředí IBM DB2 pureScale v Azure.
author: njray
manager: edprice
editor: edprice
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 4ab0dfabc5fc4773413e7009bb8a16e6b77d5c09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950191"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale v Azure

Prostředí IBM DB2 pureScale poskytuje databázový cluster pro Azure s vysokou dostupností a škálovatelností operačních systémů Linux. Tento článek ukazuje architekturu pro spouštění DB2 pureScale v Azure.

## <a name="overview"></a>Přehled

Podniky se dlouho používaly tradiční platformy RDBMS (relační databáze pro správu relačních databází) do služby stravování až po jejich potřeby pro online zpracování transakcí (OLTP). Tyto dny, mnoho z nich migruje svá Sálová databázová prostředí do Azure jako způsob, jak rozšířit kapacitu, snížit náklady a udržovat konstantní provozní nákladovou strukturu. Migrace je často prvním krokem v modernizaci starší verze platformy. 

V poslední době podnikový zákazník znovu hostovat své prostředí IBM DB2 běžící na z/OS do IBM DB2 pureScale v Azure. Řešení clusteru databáze Db2 pureScale poskytuje vysokou dostupnost a škálovatelnost operačních systémů Linux. Zákazník spustil Db2 úspěšně jako samostatnou instanci, která je na jednom virtuálním počítači (VM) v systému Azure ve velkém měřítku na Azure před instalací Db2 pureScale. 

I když se neshoduje s původním prostředím, IBM DB2 pureScale on Linux přináší podobné funkce s vysokou dostupností a škálovatelností jako IBM DB2 pro z/OS běžící v konfiguraci paralelního Sysplex na sálovém počítači. V tomto scénáři je cluster připojený přes iSCSI ke sdílenému clusteru úložiště. Používali jsme systém souborů GlusterFS, bezplatný, škálovatelný a otevřený zdroj distribuovaného systému souborů, který je speciálně optimalizovaný pro cloudové úložiště. Nicméně společnost IBM již toto řešení nepodporuje. Aby bylo možné zachovat podporu od společnosti IBM, je nutné použít podporovaný systém souborů kompatibilní s technologií iSCSI. Microsoft nabízí Prostory úložiště s přímým přístupem (S2D) jako možnost.

Tento článek popisuje architekturu, která se používá pro tuto migraci Azure. Zákazník použil pro otestování konfigurace systém Red Hat Linux 7,4. Tato verze je k dispozici z Azure Marketplace. Před výběrem distribuce systému Linux ověřte, zda jsou aktuálně podporované verze ověřeny. Podrobnosti najdete v dokumentaci k [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) a [GlusterFS](https://docs.gluster.org/en/latest/).

Tento článek je výchozím bodem pro váš plán implementace DB2. Vaše obchodní požadavky se budou lišit, ale platí stejný základní vzor. Tento model architektury můžete použít také pro aplikace OLAP (Online Analytical Processing) v Azure.

Tento článek nepokrývá rozdíly a možné úlohy migrace pro přesun databáze IBM DB2 pro databázi z/OS do IBM DB2 pureScale běžící na systému Linux. A neposkytuje odhady velikosti a analýzy úloh pro přechod z DB2 z/OS na DB2 pureScale. 

Abychom vám pomohli při rozhodování o nejlepší architektuře pureScale pro vaše prostředí, doporučujeme vám plně odhadnout velikost a vytvořit hypotézu. Ve zdrojovém systému nezapomeňte zvážit možnost DB2 z/OS Parallel Sysplex s architekturou sdílení dat, konfigurací spojovacího zařízení a statistikou využití distribuovaného datového zařízení (DDF).

> [!NOTE]
> Tento článek popisuje jeden přístup k migraci DB2, ale existují další. Například DB2 pureScale lze také spustit v virtualizovaných místních prostředích. IBM podporuje DB2 na Microsoft Hyper-V v různých konfiguracích. Další informace najdete v tématu [Architektura virtualizace DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) ve znalostní bázi IBM Knowledge Center.

## <a name="architecture"></a>Architektura

Pro zajištění vysoké dostupnosti a škálovatelnosti v Azure můžete použít architekturu sdílené dat pro škálování na více instancí pro DB2 pureScale. Migrace zákazníka používala následující příklad architektury.

![DB2 pureScale na virtuálních počítačích Azure, které zobrazují úložiště a sítě](media/pureScaleArchitecture.png "DB2 pureScale na virtuálních počítačích Azure, které zobrazují úložiště a sítě")


Diagram znázorňuje logické vrstvy potřebné pro cluster DB2 pureScale. Mezi ně patří virtuální počítače pro klienta nástroje pro správu, ukládání do mezipaměti, pro databázový stroj a pro sdílené úložiště. 

Kromě uzlů databázového stroje obsahuje diagram dva uzly, které se používají pro funkce ukládání do mezipaměti clusteru (CFs). Pro samotný databázový stroj se používá minimálně dva uzly. Server DB2, který patří do clusteru pureScale, se nazývá člen. 

Cluster je připojený přes iSCSI k clusteru sdíleného úložiště se třemi uzly, který poskytuje úložiště se škálováním na více instancí a vysokou dostupnost. Na virtuálních počítačích Azure se systémem Linux je nainstalovaná pureScale DB2.

Tento přístup je šablona, kterou můžete upravit pro velikost a škálování vaší organizace. Vychází z následujících možností:

-   Dva nebo více databázových členů je sloučeno s alespoň dvěma uzly CF. Uzly spravují Globální fond vyrovnávacích pamětí (GBP) pro sdílenou paměť a služby globálního správce zámků (GLM) pro řízení sdíleného přístupu a zamykání kolizí od aktivních členů. Jeden uzel CF funguje jako primární a druhý jako sekundární uzel převzetí služeb při selhání CF. Aby nedocházelo k jedinému bodu selhání v prostředí, cluster DB2 pureScale vyžaduje aspoň čtyři uzly.

-   Vysoce výkonné sdílené úložiště (zobrazené v P30 velikosti v diagramu). Každý uzel používá toto úložiště.

-   Vysoce výkonné sítě pro datové členy a sdílené úložiště.

### <a name="compute-considerations"></a>Požadavky na výpočetní prostředky

Tato architektura spouští aplikaci, úložiště a datovou vrstvu na virtuálních počítačích Azure. [Skripty pro instalaci nasazení](https://aka.ms/db2onazure) vytvoří následující:

-   Cluster DB2 pureScale Typ výpočetních prostředků, které v Azure potřebujete, závisí na nastavení. Obecně platí, že můžete použít dva přístupy:

    -   Použijte síť s více uzly a vysoce výkonným výpočetním prostředím (HPC), ve které se v malých až středně velkých instancích přistupuje ke sdílenému úložišti. Pro tento typ HPC konfigurace Azure paměťově optimalizované E-series nebo úložiště s podporou [úložišť řady L](../../../sizes.md) -Series poskytují potřebný výpočetní výkon.

    -   Pro datové moduly použijte méně velkých instancí virtuálních počítačů. U velkých instancí jsou největší virtuální počítače optimalizované pro paměť [řady M-Series](https://azure.microsoft.com/pricing/details/virtual-machines/series/) ideální pro náročné úlohy v paměti. V závislosti na velikosti logického oddílu (LPAR), který se používá ke spuštění DB2, budete možná potřebovat vyhrazenou instanci.

-   DB2 CF používá paměťově optimalizované virtuální počítače, například E-series nebo L-series.

-   Sdílený cluster úložiště, který používá standardní \_ \_ virtuální počítače DS4 v2 se systémem Linux.

-   JumpBox pro správu je standardní \_ \_ virtuální počítač DS2 v2 se systémem Linux.  Alternativou je Azure bastionu, což je služba, která poskytuje zabezpečené prostředí RDP/SSH pro všechny virtuální počítače ve vaší virtuální síti.

-   Klient je standardní \_ \_ virtuální počítač DS3 v2 s Windows (používaný pro testování).

-   *Volitelné*. Server s kopií clusteru. To je potřeba jenom pro některé starší verze Db2 pureScale. V tomto příkladu se používá \_ standardní \_ virtuální počítač DS3 v2 se systémem Linux (používá se pro DB2 pureScale).

> [!NOTE]
> Cluster DB2 pureScale vyžaduje aspoň dvě instance DB2. Vyžaduje také instanci mezipaměti a instanci Správce zámků.

### <a name="storage-considerations"></a>Aspekty úložišť

Podobně jako Oracle RAC je DB2 pureScale a vysoce výkonná databáze s horizontálním škálováním I/O. Doporučujeme použít největší možnost [Azure Premium SSD](../../../disks-types.md) , která vyhovuje vašim potřebám. Menší možnosti úložiště můžou být vhodné pro vývojová a testovací prostředí, zatímco produkční prostředí často potřebují větší kapacitu úložiště. Ukázková architektura používá [P30](https://azure.microsoft.com/pricing/details/managed-disks/) kvůli jejich poměru IOPS k velikosti a ceně. Bez ohledu na velikost použijte Premium Storage pro nejlepší výkon.

DB2 pureScale využívá architekturu Shared-All, kde jsou všechna data dostupná ze všech uzlů clusteru. Služba Premium Storage musí být sdílená mezi několika instancemi, ať už na vyžádání, nebo na vyhrazených instancích.

Velký cluster pureScale DB2 může vyžadovat 200 terabajtů (TB) nebo více sdílených úložišť úrovně Premium a s IOPS 100 000. DB2 pureScale podporuje rozhraní blokování iSCSI, které můžete používat v Azure. Rozhraní iSCSI vyžaduje cluster sdíleného úložiště, který můžete implementovat pomocí S2D nebo jiného nástroje. Tento typ řešení vytvoří virtuální zařízení sítě SAN (síti vSAN) v Azure. DB2 pureScale používá síti vSAN k instalaci clusterovaného souborového systému, který se používá ke sdílení dat mezi virtuálními počítači.

### <a name="networking-considerations"></a>Aspekty sítí

Společnost IBM doporučuje InfiniBand sítě pro všechny členy v clusteru DB2 pureScale. DB2 pureScale také používá přímý přístup do paměti vzdáleného počítače (RDMA), pokud je k dispozici pro CFs.

Během instalace vytvoříte [skupinu prostředků](../../../../azure-resource-manager/management/overview.md) Azure, která bude obsahovat všechny virtuální počítače. Obecně platí, že se prostředky seskupují na základě jejich životnosti a kteří je spravují. Virtuální počítače v této architektuře vyžadují [urychlené síťové služby](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Jedná se o funkci Azure, která poskytuje konzistentní a extrémně nízkou latenci sítě prostřednictvím rozhraní SR-IOV (single-root I/O Virtualization) k virtuálnímu počítači.

Každý virtuální počítač Azure je nasazený do virtuální sítě, která má podsítě: Main, front-end Gluster FS (gfsfe), Gluster FS back-end (bfsbe), DB2 pureScale (db2be) a DB2 pureScale front-end (db2fe). Instalační skript také vytvoří primární [síťové adaptéry](../../../windows/multiple-nics.md) na virtuálních počítačích v hlavní podsíti.

[Skupiny zabezpečení sítě](../../../../virtual-network/virtual-network-vnet-plan-design-arm.md) použijte k omezení síťového provozu v rámci virtuální sítě a k izolaci podsítí.

V Azure pureScale potřebuje jako síťové připojení pro úložiště používat protokol TCP/IP.

## <a name="next-steps"></a>Další kroky

-   [Nasadit tuto architekturu v Azure](deploy-ibm-db2-purescale-azure.md)