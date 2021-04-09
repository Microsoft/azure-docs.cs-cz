---
title: Návrh a implementace databáze Oracle v Azure | Microsoft Docs
description: Navrhněte a implementujte databázi Oracle v prostředí Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 12/17/2020
ms.author: kegorman
ms.reviewer: tigorman
ms.openlocfilehash: 6e59d0065dfa74979bf3bbc72458bda516e3b641
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669978"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Návrh a implementace databáze Oracle v Azure

## <a name="assumptions"></a>Předpoklady

- Chystáte se migrovat databázi Oracle z místního prostředí do Azure.
- Máte [balíček diagnostiky](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) nebo [Automatické úložiště úloh](https://www.oracle.com/technetwork/database/manageability/info/other-manageability/wp-self-managing-database18c-4412450.pdf) pro Oracle Database, které chcete migrovat.
- Znáte různé metriky v Oracle.
- Máte základní znalosti o výkonu aplikací a využití platforem.

## <a name="goals"></a>Cíle

- Pochopte, jak optimalizovat nasazení Oracle v Azure.
- Prozkoumejte možnosti ladění výkonu pro databázi Oracle v prostředí Azure.
- Měli byste jasné očekávání mezi omezeními fyzického vyladění prostřednictvím architektury a výhod nebo logickým vyladěním kódu databáze (SQL) a celkového návrhu databáze.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Rozdíly mezi místním nasazením a implementací Azure 

Níže jsou uvedeny některé důležité věci, které je potřeba vzít v úvahu při migraci místních aplikací do Azure. 

Jedním z důležitých rozdílů je to, že v implementaci Azure se prostředky, jako jsou virtuální počítače, disky a virtuální sítě, sdílí mezi ostatními klienty. Prostředky je navíc možné omezit na základě požadavků. Místo toho, abyste se zazaměřili na selhání (MTBF), se Azure podrobněji zaměřuje na zbývající část (MTTR).

V následující tabulce jsou uvedeny některé rozdíly mezi místními implementacemi a implementací Azure databáze Oracle.


|  | Místní implementace | Implementace Azure |
| --- | --- | --- |
| **Sítě** |LAN/WAN  |SDN (softwarově definované sítě)|
| **Skupina zabezpečení** |Nástroje pro omezení IP adres nebo portů |[Skupina zabezpečení sítě (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
| **Odolnost** |MTBF (střední doba mezi selháními) |MTTR (Průměrná doba obnovení)|
| **Plánovaná údržba** |Opravy a upgrady|[Skupiny dostupnosti](/previous-versions/azure/virtual-machines/windows/infrastructure-example) (opravy a upgrady spravované přes Azure) |
| **Prostředek** |Vyhrazená  |Sdíleno s ostatními klienty|
| **Oblasti** |Datová centra |[Párování oblastí](../../regions.md#region-pairs)|
| **Storage** |SÍŤ SAN/fyzické disky |[Úložiště spravované v Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
| **Škálování** |Vertikální škálování |Horizontální škálování|


### <a name="requirements"></a>Požadavky

- Určete skutečné využití CPU, protože je Oracle licencováno jádrem a určení velikosti vCPU potřeb může představovat základní cvičení pro úspory nákladů. 
- Určete velikost databáze, úložiště zálohování a míru růstu.
- Určete požadavky na vstupně-výstupní operace, které můžete odhadnout na základě sestav Oracle Statspack a AWR nebo nástrojů pro monitorování úložiště na úrovni operačního systému.

## <a name="configuration-options"></a>Možnosti konfigurace

Existují čtyři možné oblasti, které můžete vyladit, aby se zlepšil výkon v prostředí Azure:

- Velikost virtuálního počítače
- Propustnost sítě
- Typy disků a konfigurace
- Nastavení diskové mezipaměti

### <a name="generate-an-awr-report"></a>Generování sestavy AWR

Pokud máte existující databázi Oracle Enterprise Edition a plánujete migrovat na Azure, máte několik možností. Pokud máte [diagnostické sady](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) pro instance Oracle, můžete spuštěním sestavy Oracle AWR získat metriky (IOPS, MB/s, GiBs a tak dále). U databází bez licence pro integrační balíček nebo pro databázi Standard Edition je možné po ručních snímcích shromáždit stejnou důležitou metriku se sestavou Statspack.  Hlavním rozdílem mezi těmito dvěma způsoby vytváření sestav je, že AWR se automaticky shromáždí a poskytuje další informace o databázi, než je možnost vytváření sestav předchůdce Statspack.

V rámci pravidelných i špičkových úloh můžete zvážit spuštění sestavy AWR, abyste je mohli porovnat. Chcete-li shromáždit přesnější úlohu, vezměte v úvahu sestavu rozšířeného okna o jeden týden, vs. 24 hodinových běhů a vezměte v úvahu, že AWR poskytuje průměry jako součást výpočtů v sestavě.  Pro migraci Datacenter doporučujeme shromažďovat sestavy pro určení velikosti produkčních systémů a odhadovat zbývající kopie databáze používané pro testování uživatelů, testování, vývoj atd. v procentech (UAT se rovnají produkci, testování a vývoji 50% velikosti produkčního prostředí atd.)

Ve výchozím nastavení uchovává úložiště AWR 8 dní dat a pořizuje snímky v hodinových intervalech.  Pokud chcete spustit sestavu AWR z příkazového řádku, můžete provést následující kroky z terminálu:

```bash
$ sqlplus / as sysdba
SQL> @$ORACLE_HOME/rdbms/admin/awrrpt.sql;
```

### <a name="key-metrics"></a>Klíčové metriky

V sestavě se zobrazí výzva k zadání následujících informací:
- Typ sestavy: HTML nebo TEXT (HTML v 12,1 a poskytuje další informace, než je textový formát)
- Počet dní, po které se mají zobrazit snímky (pro časové intervaly, bude sestava o jednom týdnu 168 odlišná v ID snímku)
- Začátek SnapshotID okna sestavy
- Koncový SnapshotId okna sestavy
- Název sestavy, kterou má skript AWR vytvořit.

Pokud se AWR spouští na skutečném aplikačním clusteru, (RAC) je sestava příkazového řádku awrgrpt. SQL namísto awrrpt. SQL.  Sestava "g" vytvoří sestavu pro všechny uzly v databázi RAC v jedné sestavě vs. je nutné ji spustit na každém uzlu RAC.

Níže jsou uvedené metriky, které můžete získat ze sestavy AWR:

- Název databáze, název instance a název hostitele
- Verze databáze (podpora podle Oracle)
- PROCESOR/jádra
- SGA/PGA, (a poradci, kteří vám pomůžou zjistit, jestli nedostatečné)
- Celková paměť v GB
- PROCESOR je zaneprázdněný
- Procesory DB
- IOPs (čtení a zápis)
- MB/s (čtení i zápis)
- Propustnost sítě
- Frekvence latence sítě (nízká/vysoká)
- Hlavní události čekání 
- Nastavení parametru pro databázi
- Je databáze RAC, Exadata s využitím pokročilých funkcí nebo konfigurací

### <a name="virtual-machine-size"></a>Velikost virtuálního počítače

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. odhad velikosti virtuálního počítače na základě využití procesoru, paměti a vstupně-výstupních operací ze sestavy AWR

Jednou z věcí, které byste mohli nahlížet, je prvních pět událostí s vypršenou platností na popředí, které označují, kde jsou systémové body kritické.

Například v následujícím diagramu je synchronizace souborů protokolu v horní části. Označuje počet čekání, které jsou požadovány před tím, než LGWR zapíše vyrovnávací paměť protokolu do souboru protokolu opětovného odeslání. Tyto výsledky naznačují, že je potřeba úložiště nebo disky lépe provádět. Kromě toho diagram zobrazuje také počet PROCESORů (jader) a množství paměti.

![Snímek obrazovky zobrazující synchronizaci souboru protokolu v horní části tabulky](./media/oracle-design/cpu_memory_info.png)

Následující diagram znázorňuje celkový počet vstupně-výstupních operací čtení a zápisu. Během sestavy bylo zapsáno 59 GB a 247,3 GB v době sestavování.

![Snímek obrazovky, který zobrazuje celkový počet vstupně-výstupních operací čtení a zápisu.](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Volba virtuálního počítače

Na základě informací, které jste shromáždili ze sestavy AWR, je dalším krokem výběr virtuálního počítače podobné velikosti, která splňuje vaše požadavky. Seznam dostupných virtuálních počítačů najdete v paměti v článku [optimalizované pro paměť](../../sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. vyladění velikosti virtuálního počítače pomocí podobné série virtuálních počítačů na základě ACU

Po výběru virtuálního počítače věnujte pozornost ACU pro virtuální počítač. Můžete zvolit jiný virtuální počítač na základě hodnoty ACU, která lépe vyhovuje vašim požadavkům. Další informace najdete v tématu [výpočetní jednotka Azure](../../acu.md).

![Snímek obrazovky se stránkou jednotek ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Propustnost sítě

Následující diagram znázorňuje vztah mezi propustností a IOPS:

![Snímek obrazovky s propustností](./media/oracle-design/throughput.png)

Celková propustnost sítě se odhadne na základě těchto informací:
- SQL * síťový provoz
- MB/s × počet serverů (odchozí datový proud, jako je Oracle data Guard)
- Další faktory, jako je například replikace aplikací

![Snímek obrazovky SQL * NET propustnost](./media/oracle-design/sqlnet_info.png)

Na základě požadavků na šířku pásma sítě si můžete vybrat z různých typů bran. Mezi ně patří Basic, VpnGw a Azure ExpressRoute. Další informace najdete na stránce s [cenami služby VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Doporučení**

- V porovnání s místním nasazením je latence sítě vyšší. Snížení zatížení sítě může výrazně zlepšit výkon.
- Pro omezení zpátečních cest Konsolidujte aplikace, které mají vysoké transakce nebo "konverzace" na stejném virtuálním počítači.
- Pro lepší výkon sítě používejte Virtual Machines s [akcelerovanými síťovými](../../../virtual-network/create-vm-accelerated-networking-cli.md) službami.
- U některých distribucí systému Linux zvažte možnost povolit [podporu pro funkci trim a](/previous-versions/azure/virtual-machines/linux/configure-lvm#trimunmap-support)oddálení.
- Nainstalujte [správce Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) do samostatného virtuálního počítače.
- Ve výchozím nastavení nejsou v systému Linux povoleny velké stránky. Zvažte možnost Povolit velké stránky a nastavit `use_large_pages = ONLY` Oracle DB. To může přispět ke zvýšení výkonu. Další informace najdete [tady](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390).

### <a name="disk-types-and-configurations"></a>Typy disků a konfigurace

- *Výchozí disky s operačním systémem*: tyto typy disků nabízejí trvalá data a ukládání do mezipaměti. Jsou optimalizované pro přístup k operačnímu systému při spuštění a nejsou navržené pro transakční data ani pro úlohy datového skladu (analytická data).

- *Spravované disky*: Azure spravuje účty úložiště, které používáte pro disky virtuálních počítačů. Určíte typ disku (nejčastěji pro úlohy úrovně Premium SSD pro Oracle) a velikost disku, který potřebujete. Azure vytvoří a spravuje disk za vás.  SSD úrovně Premium spravovaný disk je k dispozici jenom pro řady virtuálních počítačů optimalizované pro paměť a specificky. Po zvolení konkrétní velikosti virtuálního počítače se v nabídce zobrazí jenom dostupné SKU Premium Storage, které jsou založené na velikosti tohoto virtuálního počítače.

![Snímek stránky spravovaného disku](./media/oracle-design/premium_disk01.png)

Po konfiguraci úložiště na VIRTUÁLNÍm počítači můžete před vytvořením databáze zátěž testů načíst. Znalost vstupně-výstupních přenosů z hlediska latence a propustnosti vám může pomoci určit, jestli virtuální počítače podporují očekávanou propustnost s cíli latence.

K dispozici je řada nástrojů pro zátěžové testování aplikací, jako je například Oracle Orion, Sysbench, SLOB a FIO.

Spusťte test zatížení znovu po nasazení databáze Oracle. Zahajte pravidelné a špičkové úlohy a výsledky vám ukáže základní údaje o vašem prostředí.  Udělejte v testu úloh realistickou úlohu – nedává smysl spustit úlohu, která není stejná jako u virtuálních počítačů, které ve skutečnosti spustíte.

Protože Oracle je databáze náročná na vstupně-výstupní operace pro mnoho, je poměrně důležité velikost úložiště na základě sazby za IOPS místo velikosti úložiště. Pokud je například požadovaný IOPS 5 000, ale potřebujete pouze 200 GB, můžete i nadále získat disk třídy P30 na úrovni Premium, i když obsahuje více než 200 GB úložiště.

Frekvence IOPS se dá získat ze sestavy AWR. Určuje se v protokolu opětovného načtení, fyzických čtení a rychlosti zápisu.  Vždy ověřte, že zvolená řada virtuálních počítačů má možnost zpracovat také požadavky na vstupně-výstupní operace úlohy.  Pokud má virtuální počítač snížený počet vstupně-výstupních operací, než je úložiště, nastaví se maximální limit pro virtuální počítač.

![Snímek obrazovky se stránkou sestavy AWR](./media/oracle-design/awr_report.png)

Například velikost opakování je 12 200 000 bajtů za sekundu, což se rovná 11,63 MB/s.
IOPS je 12 200 000/2 358 = 5 174.

Po jasném přehledu požadavků na vstupně-výstupní operace můžete zvolit kombinaci jednotek, které jsou nejvhodnější pro splnění těchto požadavků.

**Doporučení**

- V případě datového tabulkového úložiště Rozšiřte vstupně-výstupní úlohy napříč několika disky pomocí spravovaného úložiště nebo Oracle ASM.
- Použití rozšířené komprese Oracle k omezení vstupně-výstupních operací (pro data i indexy).
- Oddělte protokoly znovu, dočasná a odvolat je na samostatných datových discích.
- Neumísťujte žádné soubory aplikace na výchozí disky s operačním systémem (/dev/sda). Tyto disky nejsou optimalizované pro rychlé spouštění virtuálních počítačů a nemusí pro vaši aplikaci poskytovat dobrý výkon.
- Při použití virtuálních počítačů řady M-Series na Premium Storage povolte [akcelerátor zápisu](../../how-to-enable-write-accelerator.md) na disku znovu protokoly.
- Zvažte možnost přesunout protokoly znovu s vysokou latencí na Ultra disk.

### <a name="disk-cache-settings"></a>Nastavení diskové mezipaměti

Existují tři možnosti ukládání do mezipaměti hostitele, ale pro databázi Oracle, je pro úlohu databáze doporučeno pouze ukládání do mezipaměti jen pro čtení.  Čtení pro čtení a zápis dat do datového typu může znamenat významné ohrožení zabezpečení, kde cílem zápisu do databáze je záznam v mezipaměti.

Na rozdíl od systému souborů nebo aplikace pro databázi je doporučení pro ukládání do mezipaměti hostitele jen pro *čtení*: všechny požadavky jsou ukládány do mezipaměti pro budoucí čtení. Všechna zápisy budou nadále zapisovány na disk.

**Doporučení**

Pro maximalizaci propustnosti doporučujeme začít s možností **ReadOnly** pro ukládání do mezipaměti hostitele, kdykoli je to možné. V případě Premium Storage Pamatujte na to, že při připojování systému souborů s možnostmi **jen pro čtení** musíte zakázat "překážky". Aktualizujte soubor/etc/fstab s identifikátorem UUID na disky.

![Snímek stránky spravovaného disku, který zobrazuje možnosti ReadOnly a None](./media/oracle-design/premium_disk02.png)

- Pro disky s operačním systémem použijte výchozí mezipaměť **pro čtení a zápis** a použijte Premium SSD pro virtuální počítače pro úlohy Oracle.  Také se ujistěte, že svazek, který se používá pro swap, je také na jednotce SSD služby Premium.
- U všech souborů DataFile použijte **jen pro čtení** pro ukládání do mezipaměti. Mezipaměť jen pro čtení je dostupná jenom pro spravovaný disk úrovně Premium, P30 a vyšší.  Existuje limit 4095GiB svazku, který je možné použít s ukládáním do mezipaměti jen pro čtení.  Větší přidělení bude ve výchozím nastavení zakázat ukládání do mezipaměti hostitele.

Pokud se úlohy značně liší od dne a večeru a vstupně-výstupní operace je může podporovat, SSD úrovně Premium P20 P1 může poskytovat výkon potřebný během dávkového načítání nebo omezeného počtu vstupně-výstupních operací v noci.  

## <a name="security"></a>Zabezpečení

Po nastavení a konfiguraci prostředí Azure je dalším krokem zabezpečení vaší sítě. Tady je několik doporučení:

- *Zásady NSG*: NSG se dají definovat v podsíti nebo síťové kartě. Je jednodušší řídit přístup na úrovni podsítě, a to jak pro zabezpečení, tak pro vynucení směrování pro věci, jako jsou brány firewall pro aplikace.

- *JumpBox*: pro bezpečnější přístup by se správci neměli přímo připojovat ke službě Application Service nebo databázi. JumpBox se používá jako médium mezi počítačem správce a prostředky Azure.
![Snímek obrazovky se stránkou topologie JumpBox](./media/oracle-design/jumpbox.png)

    Počítač správce by měl nabízet jenom přístup s omezením IP adres jenom pro JumpBox. JumpBox by měl mít přístup k aplikaci a databázi.

- *Privátní síť* (podsítě): Doporučujeme, abyste měli aplikační službu a databázi v samostatných podsítích, takže lepší kontrolu můžete nastavit pomocí zásad NSG.


## <a name="additional-reading"></a>Další materiály ke čtení

- [Konfigurace Oracle ASM](configure-oracle-asm.md)
- [Konfigurace Oracle Data Guardu](configure-oracle-dataguard.md)
- [Konfigurace Oracle Zlaté brány](configure-oracle-golden-gate.md)
- [Zálohování a obnovení Oracle](./oracle-overview.md)

## <a name="next-steps"></a>Další kroky

- [Kurz: Vytvoření virtuálních počítačů s vysokou dostupností](../../linux/create-cli-complete.md)
- [Ukázky ukázek Azure CLI pro nasazení virtuálních počítačů](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
