---
title: IBM DB2 pureScale v Azure
description: V tomto článku ukážeme architekturu pro spuštění prostředí IBM DB2 pureScale v Azure.
author: njray
manager: edprice
editor: edprice
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: d8309a69c9c38610fa7bea3fee202a60d836980c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945058"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale v Azure

Prostředí IBM DB2 pureScale poskytuje databázový cluster pro Azure s vysokou dostupností a škálovatelností v operačních systémech Linux. Tento článek ukazuje architekturu pro spuštění DB2 pureScale v Azure.

## <a name="overview"></a>Přehled

Podniky již dlouho používají tradiční platformy systému správy relačních databází (RDBMS), aby uspokojily své potřeby online zpracování transakcí (OLTP). V těchto dnech mnozí migrují svá databázová prostředí založená na sálových počítačích do Azure jako způsob, jak rozšířit kapacitu, snížit náklady a udržovat stabilní strukturu provozních nákladů. Migrace je často prvním krokem při modernizaci starší platformy. 

Nedávno podnikový zákazník rehosted jeho IBM DB2 prostředí běží na z/OS na IBM DB2 pureScale v Azure. Databázové clusterové řešení Db2 pureScale poskytuje vysokou dostupnost a škálovatelnost v operačních systémech Linux. Zákazník úspěšně spustil Db2 jako samostatnou instanci navertiveně na škálování na jednom virtuálním počítači (VM) ve velkém škálovacím systému v Azure před instalací Db2 pureScale. 

Ibm DB2 pureScale na Linuxu sice není shodná s původním prostředím, ale nabízí podobné funkce s vysokou dostupností a škálovatelností jako IBM DB2 for z/OS běžící v konfiguraci Parallel Sysplex na sálovém počítači. V tomto scénáři je cluster připojen přes iSCSI ke sdílenému clusteru úložiště. Použili jsme souborový systém GlusterFS, bezplatný, škálovatelný, open source distribuovaný souborový systém speciálně optimalizovaný pro cloudové úložiště. IBM však toto řešení již nepodporuje. Chcete-li zachovat podporu ibm, musíte použít podporovaný systém souborů kompatibilní s iSCSI. Microsoft nabízí storage spaces direct (S2D) jako volitelné příslušenství

Tento článek popisuje architekturu používanou pro tuto migraci Azure. Zákazník použil Red Hat Linux 7.4 k testování konfigurace. Tato verze je dostupná na Azure Marketplace. Než zvolíte linuxovou distribuci, ověřte aktuálně podporované verze. Podrobnosti naleznete v dokumentaci k [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) a [GlusterFS](https://docs.gluster.org/en/latest/).

Tento článek je výchozím bodem pro váš plán implementace DB2. Vaše obchodní požadavky se budou lišit, ale platí stejný základní vzorec. Tento architektonický vzor můžete také použít pro online analytické zpracování (OLAP) aplikace v Azure.

Tento článek nezahrnuje rozdíly a možné úlohy migrace pro přesunutí databáze IBM DB2 for z/OS do IBM DB2 pureScale běžící na Linuxu. A neposkytuje odhady velikosti a analýzy pracovního vytížení pro přechod z DB2 z/OS na DB2 pureScale. 

Chcete-li vám pomoci rozhodnout o nejlepší DB2 pureScale architektura pro vaše prostředí, doporučujeme plně odhadnout velikost i hypotézu. Ve zdrojovém systému zvažte db2 z/OS Parallel Sysplex s architekturou sdílení dat, konfigurací spojovacího zařízení a statistikou využití distribuovaného datového zařízení (DDF).

> [!NOTE]
> Tento článek popisuje jeden přístup k migraci DB2, ale existují další. Například DB2 pureScale můžete také spustit ve virtualizovaných místních prostředích. IBM podporuje DB2 na Microsoft Hyper-V v různých konfiguracích. Další informace naleznete v [architektuře virtualizace DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) v CENTRU ZNALOSTNÍHO PROSTŘEDÍ IBM Knowledge Center.

## <a name="architecture"></a>Architektura

Chcete-li podporovat vysokou dostupnost a škálovatelnost v Azure, můžete použít architekturu sdílených dat pro DB2 pureScale. Migrace zákazníka používá následující příklad architektury.

![DB2 pureScale na virtuálních počítačích Azure zobrazující úložiště a sítě](media/db2-purescale-on-azure/pureScaleArchitecture.png "DB2 pureScale na virtuálních počítačích Azure zobrazující úložiště a sítě")


Diagram znázorňuje logické vrstvy potřebné pro cluster DB2 pureScale. Patří mezi ně virtuální počítače pro klienta, pro správu, pro ukládání do mezipaměti, pro databázový stroj a pro sdílené úložiště. 

Kromě uzlů databázového stroje obsahuje diagram dva uzly používané pro zařízení mezipaměti clusteru (CF). Minimálně dva uzly se používají pro samotný databázový stroj. Server DB2, který patří do clusteru pureScale, se nazývá člen. 

Cluster je připojen přes iSCSI ke clusteru sdíleného úložiště se třemi uzly, aby poskytoval horizontální navýšení kapacity úložiště a vysokou dostupnost. DB2 pureScale se nainstalovaný na virtuálních počítačích Azure se systémem Linux.

Tento přístup je šablona, kterou můžete upravit pro velikost a měřítko vaší organizace. Je založen na následující:

-   Dva nebo více členů databáze jsou kombinovány s alespoň dvěma uzly CF. Uzly spravovat globální fond vyrovnávací paměti (GBP) pro sdílené paměti a globální správce zámků (GLM) služby pro řízení sdíleného přístupu a uzamčení tvrzení od aktivních členů. Jeden uzel CF funguje jako primární a druhý jako sekundární uzel CF s podporou převzetí služeb při selhání. Aby se zabránilo jeden bod selhání v prostředí, DB2 pureScale clusteru vyžaduje alespoň čtyři uzly.

-   Vysoce výkonné sdílené úložiště (znázorněno ve velikosti P30 v diagramu). Každý uzel používá toto úložiště.

-   Vysoce výkonné sítě pro datové členy a sdílené úložiště.

### <a name="compute-considerations"></a>Důležité informace o výpočtu

Tato architektura spouští aplikační, úložné a datové vrstvy na virtuálních počítačích Azure. [Skripty pro nastavení nasazení](https://aka.ms/db2onazure) vytvářejí následující:

-   Cluster DB2 pureScale. Typ výpočetních prostředků, které potřebujete v Azure, závisí na vašem nastavení. Obecně lze použít dva přístupy:

    -   Použijte síť ve stylu více uzlů a vysoce výkonné výpočetní techniky (HPC), kde malé až středně velké instance přistupují ke sdílenému úložišti. Pro tento typ konfigurace typu HPC poskytují virtuální počítače řady E optimalizované pro paměť Azure nebo [virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) optimalizované pro úložiště l potřebný výpočetní výkon.

    -   Pro datové moduly použijte méně velkých instancí virtuálních strojů. Pro velké instance jsou největší virtuální počítače [m-series](https://azure.microsoft.com/pricing/details/virtual-machines/series/) optimalizované pro paměť ideální pro náročné úlohy v paměti. Může být nutné vyhrazené instance, v závislosti na velikosti logického oddílu (LPAR), který se používá ke spuštění DB2.

-   DB2 CF používá virtuální počítače optimalizované pro paměť, jako je například řada E nebo řada L.

-   Cluster sdíleného úložiště,\_který\_používá standardní virtuální počítače DS4 v2 se systémem Linux.

-   Management jumpbox je\_standardní DS2\_v2 virtuální stroj se systémem Linux.  Alternativou je Azure Bastion, služba, která poskytuje zabezpečené prostředí RDP/SSH pro všechny virtuální počítače ve vaší virtuální síti.

-   Klient je standardní\_virtuální\_počítač DS3 v2 se systémem Windows (který se používá k testování).

-   *Nepovinné*. Server svědků. To je potřeba pouze s některými staršími verzemi Db2 pureScale. Tento příklad používá\_virtuální\_počítač Standard DS3 v2 se systémem Linux (používá se pro DB2 pureScale).

> [!NOTE]
> Cluster DB2 pureScale vyžaduje alespoň dvě instance DB2. Vyžaduje také instanci mezipaměti a instanci správce zámků.

### <a name="storage-considerations"></a>Aspekty úložišť

Stejně jako Oracle RAC, DB2 pureScale je vysoce výkonný blok V/V, horizontální navýšení kapacity databáze. Doporučujeme použít největší možnost [Azure premium SSD,](disks-types.md) která vyhovuje vašim potřebám. Menší možnosti úložiště mohou být vhodné pro vývojová a testovací prostředí, zatímco produkční prostředí často potřebují větší úložnou kapacitu. Ukázková architektura používá [P30](https://azure.microsoft.com/pricing/details/managed-disks/) z důvodu jeho poměr viops velikost a cenu. Bez ohledu na velikost použijte úložiště Premium pro nejlepší výkon.

DB2 pureScale používá architekturu shared-everything, kde jsou všechna data přístupná ze všech uzlů clusteru. Úložiště Premium musí být sdíleno mezi více instancemi, ať už na vyžádání nebo na vyhrazených instancích.

Velký cluster DB2 pureScale může vyžadovat 200 terabajtů (TB) nebo více sdíleného úložiště s vstupně-operacemi VOPS 100 000. DB2 pureScale podporuje rozhraní bloku iSCSI, které můžete použít v Azure. Rozhraní iSCSI vyžaduje cluster sdíleného úložiště, který můžete implementovat pomocí s2D nebo jiného nástroje. Tento typ řešení vytvoří zařízení sítě virtuálního úložiště (vSAN) v Azure. DB2 pureScale používá vSAN k instalaci clusterovaného systému souborů, který se používá ke sdílení dat mezi virtuálními počítači.

### <a name="networking-considerations"></a>Aspekty sítí

IBM doporučuje síť InfiniBand pro všechny členy v clusteru DB2 pureScale. DB2 pureScale také používá vzdálený přímý přístup do paměti (RDMA), pokud je k dispozici, pro CFs.

Během instalace vytvoříte [skupinu prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) Azure, která bude obsahovat všechny virtuální počítače. Obecně platí, že seskupujete prostředky na základě jejich životnosti a kdo je bude spravovat. Virtuální počítače v této architektuře vyžadují [zrychlené sítě](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Jedná se o funkci Azure, která poskytuje konzistentní, ultra nízkou latenci sítě prostřednictvím virtualizace vstupně-výstupních vstupně-výstupních videa (SR-IOV) do virtuálního počítače.

Každý virtuální počítač Azure se nasadí do virtuální sítě, která má podsítě: hlavní, Gluster FS front-end (gfsfe), Gluster FS back-end (bfsbe), DB2 pureScale (db2be) a DB2 pureScale front-end (db2fe). Instalační skript také vytvoří primární [síťové karty](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) na virtuálních počítačích v hlavní podsíti.

Pomocí [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) můžete omezit provoz v síti ve virtuální síti a izolovat podsítě.

V Azure db2 pureScale potřebuje použít TCP/IP jako síťové připojení pro úložiště.

## <a name="next-steps"></a>Další kroky

-   [Nasazení této architektury v Azure](deploy-ibm-db2-purescale-azure.md)
