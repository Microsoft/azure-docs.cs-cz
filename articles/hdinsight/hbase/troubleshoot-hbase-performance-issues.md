---
title: Řešení potíží s výkonem Apache HBase ve službě Azure HDInsight
description: Různé pokyny k ladění výkonu Apache HBA a tipy pro získání optimálního výkonu v Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 466fac524601e2d569bfa0ccf90179fe9419210d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942899"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Řešení potíží s výkonem Apache HBase ve službě Azure HDInsight

Tento článek popisuje různé pokyny k ladění výkonu Apache HBA a tipy pro získání optimálního výkonu v Azure HDInsight. Mnohé z těchto tipů závisí na konkrétním zatížení a vzoru pro čtení a zápis/skenování. Před použitím změn konfigurace v produkčním prostředí je důkladně otestujte.

## <a name="hbase-performance-insights"></a>Přehledy o výkonu HBA

Nejdůležitějším kritickým bodem pro většinu úloh HBA je protokol zápisu předem (WAL). Má vážně vliv na výkon zápisu. Služba HDInsight HBA má oddělený model úložiště – Compute. Data se ukládají vzdáleně na Azure Storage, a to i přesto, že virtuální počítače hostují servery oblastí. Až do poslední doby, WAL byla také zapsána do Azure Storage. V HDInsight toto chování toto kritické místo přináší. Funkce [akcelerované zápisy](./apache-hbase-accelerated-writes.md) je navržena k vyřešení tohoto problému. Zapisuje WAL na disky spravované službou Azure SSD úrovně Premium. To výrazně přináší výkon při zápisu a pomáhá mnoha problémům, na které čelí některé úlohy náročné na zápis.

Chcete-li získat významné vylepšení operací čtení, použijte jako vzdálené úložiště [účet Premium Block BLOB Storage](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) . Tato možnost je dostupná jenom v případě, že je povolená funkce WAL.

## <a name="compaction"></a>Komprimační

Komprimace je další možné kritické body, které se v komunitě schvalují podle zásad. Ve výchozím nastavení jsou hlavní komprimace v clusterech HDInsight HBA zakázané. Komprimace je zakázaná, protože i když se jedná o proces náročný na prostředky, zákazníci mají plnou flexibilitu při plánování podle jejich zatížení. Můžou je například naplánovat v době mimo špičku. Nezáleží taky na tom, že úložiště je vzdálené (Azure Storage) místo místního systému Hadoop systém souborů DFS (Distributed File System) (HDFS).

Zákazníci by měli naplánovat větší komprimaci na pohodlí. Pokud tato údržba neprovede, komprimace negativně nebude mít vliv na výkon při čtení v dlouhodobém běhu.

V případě operací vyhledávání by měla být příčinou obava latence, které jsou mnohem vyšší než 100 milisekund. Zkontroluje, jestli se hlavní komprimace naplánovala přesně.

## <a name="apache-phoenix-workload"></a>Apache Phoenix úlohy

Odpověď na následující otázky vám pomůžou lépe pochopit Apache Phoenix úlohy:

* Jsou všechna "čtení" překládá se na kontroly?
    * Pokud ano, jaké jsou charakteristiky těchto kontrol?
    * Pro tyto kontroly máte optimalizované vaše schéma tabulky v Phoenixu, včetně vhodného indexování?
* Použili jste `EXPLAIN` příkaz k pochopení plánu dotazů, který vygeneruje "čtení"?
* Jsou vaše zápisy "Upsert-vybírá"?
    * V takovém případě by se prováděly i kontroly. Očekávaná latence pro kontroly v průměru přibližně 100 milisekund, v porovnání s 10 milisekundami pro bod v adaptérech HBA.  

## <a name="test-methodology-and-metrics-monitoring"></a>Metodologie testování a monitorování metrik

Pokud používáte srovnávací testy, například Yahoo! Cloud obsluhující srovnávací testy, JMeter nebo Pherf k testování a ladění výkonu, ujistěte se, že:

- Klientské počítače se nestane kritickým bodem. Provedete to tak, že zkontrolujete využití CPU na klientských počítačích.

- Konfigurace na straně klienta, jako je počet vláken, jsou vhodně vyladěny k sytosti šířky pásma klienta.

- Výsledky testů se zaznamenávají přesně a systematicky.

Pokud vaše dotazy náhle začaly mnohem horší než předtím, vyhledejte potenciální chyby v kódu aplikace. Dochází k náhlému generování velkých objemů neplatných dat? V takovém případě může zvýšit latenci čtení.

## <a name="migration-issues"></a>problémy s migrací

Pokud migrujete do Azure HDInsight, ujistěte se, že je migrace prováděna systematicky a přesně, nejlépe prostřednictvím automatizace. Vyhněte se ruční migraci. Ujistěte se, že:

- Atributy tabulky jsou správně migrovány. Atributy mohou být zahrnuty jako komprese, filtry Bloom a tak dále.

- Nastavení odsolení v tabulkách v Phoenixu jsou vhodně namapována na novou velikost clusteru. Například počet kontejnerů Salt by měl být násobkem počtu pracovních uzlů v clusteru. A měli byste použít násobek, který je faktorem množství horké hledání.

## <a name="server-side-configuration-tunings"></a>Ladění konfigurace na straně serveru

Ve službě HDInsight HBA jsou HFiles uložené ve vzdáleném úložišti. V případě neúspěšného ukládání do mezipaměti je cena čtení vyšší než u místních systémů, protože data v místních systémech jsou zajištěna místními HDFS. Pro většinu scénářů je k obcházení tohoto problému navrženo inteligentní použití mezipamětí HBA (bloková mezipaměť a mezipaměť bloků). V případech, kdy se problém nevyřeší, může tento problém pomoci při použití účtu typu blob bloku Premium. Ovladač Windows Azure Storage Blob spoléhá na určité vlastnosti, jako je například `fs.azure.read.request.size` načtení dat v blocích na základě toho, co určuje, zda má být režim čtení (sekvenční a náhodný), takže může i nadále existovat instance vyšší latence s čtením. Díky empirickým experimentům jsme zjistili, že nastavení velikosti bloku požadavků na čtení ( `fs.azure.read.request.size` ) na 512 KB a velikost bloku pro tabulky adaptérů HBA mají stejnou velikost, což vede k dosažení nejlepšího výsledku v praxi.

U většiny clusterových uzlů HDInsight poskytuje služby HDInsight HBA `bucketcache` jako soubor na místní SSD úrovně Premium, která je připojená k virtuálnímu počítači, na kterém běží `regionservers` . Použití mezipaměti mimo haldu místo toho může poskytovat nějaké vylepšení. Toto alternativní řešení má omezení využití dostupné paměti a může být menší než mezipaměť na základě souborů, takže nemusí vždy být nejlepší volbou.

Níže jsou uvedené některé další konkrétní parametry, které jsme provedli a které mi pomohly při různých stupních:

- Zvětšete `memstore` Velikost z výchozích 128 MB na 256 MB. Toto nastavení se obvykle doporučuje pro těžké scénáře zápisu.

- Zvyšte počet podprocesů, které jsou vyhrazeny pro komprimaci, od výchozího nastavení **1** až **4**. Toto nastavení je relevantní, pokud sledujeme časté drobné komprimace.

- Vyhněte se zablokování `memstore` vyprázdnění kvůli limitu úložiště. Pokud chcete tuto vyrovnávací paměť zadat, zvyšte `Hbase.hstore.blockingStoreFiles` nastavení na **100**.

- K řízení vyprázdnění použijte následující nastavení:

    - `Hbase.regionserver.maxlogs`: **140** (nejedná se o vyprázdnění z důvodu omezení Wal)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0,55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0,60**

- Konfigurace specifické pro Phoenix pro optimalizaci fondu vláken:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`: **512**

- Další konfigurace specifické pro Phoenix:

    - `Phoenix.rpc.index.handler.count`: **50** (pokud existuje velké nebo mnoho vyhledávacích indexů)

    - `Phoenix.stats.updateFrequency`: **1 hodina**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 hodina**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- Vypršení časových limitů RPC: **3 minuty**

   - Vypršení časových limitů RPC zahrnuje vypršení časového limitu pro adaptéry služby HBA, časový limit pro klientský skener a časový limit dotazů v Phoenixu. 
   - Ujistěte se, že `hbase.client.scanner.caching` je parametr nastavený na stejnou hodnotu na konci serveru i na konci klienta. Pokud nejsou totožné, toto nastavení vede k chybám na konci klienta, které souvisejí s `OutOfOrderScannerException` . Toto nastavení by mělo být pro velké kontroly nastaveno na nízkou hodnotu. Nastavíme tuto hodnotu na **100**.

## <a name="other-considerations"></a>Další důležité informace

Níže jsou uvedené další parametry pro zvážení ladění:

- `Hbase.rs.cacheblocksonwrite` – ve výchozím nastavení je v HDI toto nastavení nastaveno na **hodnotu true**.

- Nastavení, které umožňuje odložit menší komprimaci na později.

- Experimentální nastavení, jako je například úprava procentuálních hodnot front, které jsou rezervovány pro požadavky na čtení a zápis.

## <a name="next-steps"></a>Další kroky

Pokud váš problém zůstane nevyřešený, podívejte se na jeden z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

- Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) . Jedná se o oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky. Propojuje komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

- Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Vaše předplatné Microsoft Azure zahrnuje přístup k podpoře správy předplatných a fakturaci a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).