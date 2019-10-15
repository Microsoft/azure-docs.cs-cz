---
title: Řešení potíží s výkonem pro Apache HBA v Azure HDInsight
description: Různé pokyny k ladění výkonu Apache HBA a tipy pro získání optimálního výkonu v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: c67f21a6ed8a7697977bb7737f0e46348efb2530
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "71266650"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Řešení potíží s výkonem pro Apache HBA v Azure HDInsight

Tento dokument popisuje různé pokyny k ladění výkonu Apache HBA a tipy pro získání optimálního výkonu v Azure HDInsight. Mnohé z těchto tipů závisí na konkrétním zatížení a vzoru pro čtení a zápis/skenování. Před použitím v produkčním prostředí se důkladně testují změny konfigurace.

## <a name="hdinsight-hbase-performance-insights"></a>Přehledy výkonu HDInsight HBA

Nejdůležitějším kritickým bodem pro většinu úloh HBA je protokol zápisu předem (WAL). Má vážně vliv na výkon zápisu. Služba HDInsight HBA má oddělený výpočetní model úložiště – to znamená, že data se ukládají vzdáleně na Azure Storage ale oblasti servery jsou hostovány na virtuálních počítačích. Až do poslední doby byl protokol pro zápis do protokolu zapsaný i Azure Storage tím, že se tento problém bude rozšířit i v případě HDInsight. Funkce [akcelerované zápisy](./apache-hbase-accelerated-writes.md) je navržená tak, aby tento problém vyřešila zápisem protokolu pro zápis do služby Azure Premium SSD na disky. Tyto výhody zapisují obrovský výkon a pomáhají mnoha problémům, na které čelí některé úlohy náročné na zápis.

Jako vzdálené úložiště použijte [účet Premium Block BLOB Storage](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) , abyste získali výrazné zlepšení operací čtení. Tato možnost je dostupná jenom v případě, že je povolená funkce zápisy do protokolu předem.

## <a name="compaction"></a>Komprimační

Komprimace je dalším možným kritickým bodem, který je v komunitě dohodnutý.  Ve výchozím nastavení je hlavní komprimace na clusterech HDInsight HBA zakázaná. Je to proto, že vzhledem k tomu, že se jedná o proces náročný na prostředky, chceme zákazníkům poskytnout plnou flexibilitu, aby ji naplánovala podle jejich charakteristik zatížení – to znamená, že v době mimo špičku. I když je naše úložiště vzdálené (zajištěné službou Azure Storage) na rozdíl od místního HDFS, což je pro většinu Prem instancí běžné, nejedná se o problém, který je jedním z primárních cílů hlavní komprimace.

Předpokladem je, že zákazník bude pečlivě plánovat hlavní komprimaci podle jejich pohodlí. Pokud tato údržba není hotová, bude komprimace významně ovlivňovat výkon při čtení v dlouhodobém běhu.

Pro konkrétní operace kontroly by se měla jednat o příčinu potíží mnohem vyšší než 100 ms. Zkontroluje, jestli se hlavní komprimace naplánovala přesně.

## <a name="know-your-apache-phoenix-workload"></a>Informace o Apache Phoenix úlohách

Odpověď na následující otázky vám pomůžou lépe pochopit Apache Phoenix úlohy:

* Jsou všechna "čtení" překládá se na kontroly?
    * Pokud ano, jaké jsou charakteristiky těchto kontrol?
    * Pro tyto kontroly máte optimalizované vaše schéma tabulky v Phoenixu, včetně vhodného indexování?
* Použili jste příkaz `EXPLAIN` pro pochopení plánu dotazů, který vygeneruje "čtení".
* Jsou vaše zápisy "Upsert-vybírá"?
    * V takovém případě by se prováděly i kontroly. Očekávaná latence pro kontroly je v průměru 100 ms, na rozdíl od 10 MS pro bod v adaptérech HBA.  

## <a name="test-methodology-and-metrics-monitoring"></a>Metodologie testování a monitorování metrik

Pokud k testování a optimalizaci výkonu používáte srovnávací testy, jako je YCSB, JMeter nebo Pherf, zajistěte následující:

1. Klientské počítače se nestane kritickým bodem (kontrolu využití procesoru u klientských počítačů).

1. Konfigurace na straně klienta – například počet vláken a tak dále, jsou vhodně vyladěny k sytosti šířky pásma klienta.

1. Výsledky testů se zaznamenávají přesně a systematicky.

Pokud vaše dotazy náhle začaly mnohem horší než předtím – kontrolovat potenciální chyby v kódu aplikace – dojde k náhlému generování velkých objemů neplatných dat, takže přirozeně roste latence čtení?

## <a name="migration-issues"></a>Problémy s migrací

Pokud migrujete do Azure HDInsight, ujistěte se, že se migrace provádí systematicky a přesně, nejlépe prostřednictvím automatizace. Vyhněte se ruční migraci. Zajistěte následující:

1. Atributy tabulky – například komprese, filtry Bloom a tak dále, by měly být migrovány přesně.

1. Pro tabulky v Phoenixu by se nastavení odsolení mělo namapovat odpovídajícím způsobem na novou velikost clusteru. Například počet kontejnerů Salt se doporučuje používat jako násobek počtu pracovních uzlů v clusteru – konkrétní násobek množství horké hledáníy pozorovány.  

## <a name="server-side-config-tunings"></a>Optimalizace konfigurace na straně serveru

Ve službě HDInsight HBA jsou HFiles uložené ve vzdáleném úložišti, takže pokud dojde k neúspěšnému uložení v mezipaměti, budou náklady na čtení omezeny na Prem systémy, které mají data zajištěná místními HDFS, a to díky tomu, že se to týká latence sítě. Pro většinu scénářů je k obcházení tohoto problému navrženo inteligentní použití mezipamětí HBA (bloková mezipaměť a mezipaměť bloků). Existují však i občasné případy, kdy se může jednat o problém zákazníka. To trochu pomohlo pomocí účtu bloku blob bloku Premium. Avšak s WASB (ovladačem Windows Azure Storage) se spoléhá na některé vlastnosti, jako je `fs.azure.read.request.size`, aby se načetla data v blocích na základě toho, co určuje režim čtení (sekvenční vs náhodná), můžeme dál zobrazovat instance vyšších latencí s čtením. Zjistili jsme, že na základě empirických experimentů, které nastavují velikost bloku požadavků na čtení (`fs.azure.read.request.size`) až 512 KB a odpovídají velikosti bloku v tabulkách, které mají být stejné, je výsledkem nejlepší praxe.

Služby HDInsight HBA pro většinu uzlů s velkými velikostmi uzlů poskytuje `bucketcache` jako soubor na místní disk SSD připojený k virtuálnímu počítači, který spouští `regionservers`. V době, kdy se místo toho dá použít mezipaměť haldy, může to mít nějaké vylepšení. To má omezení využití dostupné paměti a potenciálně menší velikosti než mezipaměť založené na souborech, takže to nemusí být vždy zjevně nejlepší volbou.

Některé další konkrétní parametry, které jsme se zdáli, že se jim pomohla v různých stupních, jak je uvedeno níže:

1. Zvětšete velikost `memstore` z výchozí 128 MB na 256 MB – toto nastavení se obvykle doporučuje pro silný scénář zápisu.

1. Zvýšení počtu vláken vyhrazených pro komprimaci – od výchozí hodnoty 1 až 4. Toto nastavení je relevantní, pokud sledujeme časté drobné komprimace.

1. Vyhněte se blokování `memstore` vyprázdnění kvůli limitu úložiště. `Hbase.hstore.blockingStoreFiles` se dá zvýšit na 100, aby se poskytla tato vyrovnávací paměť.

1. Pro řízení vyprázdnění se výchozí hodnoty dají vyřešit následujícím způsobem:

    1. `Hbase.regionserver.maxlogs` může být upped až 140 z 32 (zamezení vyprázdnění z důvodu omezení WAL).

    1. `Hbase.regionserver.global.memstore.lowerLimit` = 0,55.

    1. `Hbase.regionserver.global.memstore.upperLimit` = 0,60.

1. Konfigurace specifické pro Phoenix pro optimalizaci fondu vláken:

    1. `Phoenix.query.queuesize` se dá zvýšit na 10000.

    1. `Phoenix.query.threadpoolsize` se dá zvýšit na 512.

1. Další konfigurace specifické pro Phoenix:

    1. `Phoenix.rpc.index.handler.count` můžete nastavit na 50, pokud máme velké nebo mnoho vyhledávacích indexů.

    1. `Phoenix.stats.updateFrequency` – může být upped na 1 hodinu od výchozí hodnoty 15 minut.

    1. `Phoenix.coprocessor.maxmetadatacachetimetolivems` – může být upped na 1 hodinu od 30 minut.

    1. `Phoenix.coprocessor.maxmetadatacachesize` – může být upped až 50 MB z 20 MB.

1. Vypršení časových limitů RPC – časový limit vzdáleného volání procedur (HBA), časový limit klientského skeneru a časový limit dotazu v Phoenixu se dá prodloužit na 3 minuty. Je důležité si uvědomit, že parametr `hbase.client.scanner.caching` je nastaven na hodnotu odpovídající hodnotě na konci serveru a na konci klienta. Jinak toto nastavení vede k chybám souvisejícím s `OutOfOrderScannerException` na konci klienta. Toto nastavení by mělo být pro velké kontroly nastaveno na nízkou hodnotu. Nastavíme tuto hodnotu na 100.

## <a name="other-considerations"></a>Další aspekty

Některé další parametry, které se mají vzít v úvahu pro ladění:

1. `Hbase.rs.cacheblocksonwrite` – Toto nastavení je ve výchozím nastavení nastaveno na hodnotu true v HDI.

1. Nastavení, které umožňuje odložit menší komprimaci na později.

1. Experimentální nastavení, jako je úprava procentuálních hodnot front rezervovaných pro požadavky na čtení a zápis.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

- Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) -oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

- Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
