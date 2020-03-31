---
title: Řešení potíží s výkonem Apache HBase ve službě Azure HDInsight
description: Různé pokyny pro ladění výkonu Apache HBase a tipy pro dosažení optimálního výkonu v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 93698fadcecf190dd8bbc24a9d03978899d3c5e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887151"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Řešení potíží s výkonem Apache HBase ve službě Azure HDInsight

Tento článek popisuje různé apache hbase pokyny pro ladění výkonu a tipy pro získání optimálního výkonu na Azure HDInsight. Mnoho z těchto tipů závisí na konkrétní pracovní zátěža čtení / zápisu / skenování vzor. Před použitím změn konfigurace v provozním prostředí je důkladně otestujte.

## <a name="hbase-performance-insights"></a>Přehledy výkonu HBase

Hlavní kritické místo ve většině úloh HBase je protokol dopředného zápisu (WAL). To vážně ovlivňuje výkon zápisu. HDInsight HBase má oddělený výpočetní model úložiště. Data se ukládají vzdáleně ve službě Azure Storage, i když virtuální počítače hostují servery oblasti. Až donedávna wal byl také zapsán do služby Azure Storage. V HDInsight toto chování zesílilo toto kritické místo. Funkce [Accelerated Writes](./apache-hbase-accelerated-writes.md) je určena k vyřešení tohoto problému. Zapíše WAL na disky spravované ssd Azure Premium. To nesmírně prospívá výkonu zápisu a pomáhá mnoha problémům, kterým čelí některé úlohy náročné na zápis.

Chcete-li získat významné zlepšení v operacích čtení, použijte [premium blok blob storage účet](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) jako vzdálené úložiště. Tato možnost je možná pouze v případě, že je povolena funkce WAL.

## <a name="compaction"></a>Komprese

Zhutnění je dalším potenciálním úzkým hrdlem, který je zásadně dohodnuto v komunitě. Ve výchozím nastavení je v clusterech HDInsight HBase zakázáno hlavní zhutnění. Zhutnění je zakázáno, protože i když se jedná o proces náročný na prostředky, zákazníci mají plnou flexibilitu při plánování podle svých úloh. Mohou ji například naplánovat mimo špičku. Lokalita dat také není problém, protože naše úložiště je vzdálené (podporované službou Azure Storage) namísto místního distribuovaného souborového systému Hadoop (HDFS).

Zákazníci by měli naplánovat velké zhutnění podle jejich pohodlí. Pokud tuto údržbu neudělají, zhutnění bude mít z dlouhodobého hlediska nepříznivý vliv na výkon čtení.

Pro operace skenování střední latence, které jsou mnohem vyšší než 100 milisekund by měla být důvodem k obavám. Zkontrolujte, zda bylo naplánováno velké zhutnění přesně.

## <a name="apache-phoenix-workload"></a>Apache Phoenix pracovní zátěž

Zodpovězení následujících otázek vám pomůže lépe porozumět vaší pracovní zátěži Apache Phoenix:

* Překládají všechna vaše "čtení" na skeny?
    * Pokud ano, jaké jsou vlastnosti těchto skenů?
    * Optimalizovali jste schéma tabulky Phoenix pro tyto skeny včetně příslušného indexování?
* Použili `EXPLAIN` jste příkaz k pochopení plánů dotazu, které generujete "čte"?
* Jsou vaše zápisy "upsert-výběr"?
    * Pokud ano, budou také dělat skeny. Očekávaná latence pro prohledává průměry přibližně 100 milisekund, ve srovnání s 10 milisekund pro bod získá v HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Zkušební metodika a monitorování metrik

Pokud používáte měřítka, jako je Yahoo! Cloud Serving Benchmark, JMeter nebo Pherf pro testování a ladění výkonu, ujistěte se, že:

- Klientské počítače se nestanou kritickým bodem. Chcete-li to provést, zkontrolujte využití procesoru v klientských počítačích.

- Konfigurace na straně klienta, stejně jako počet podprocesů, jsou vhodně vyladěny tak, aby nasytily šířku pásma klienta.

- Výsledky zkoušek jsou zaznamenávány přesně a systematicky.

Pokud vaše dotazy náhle začal dělat mnohem horší než dříve, zkontrolujte potenciální chyby v kódu aplikace. Generuje náhle velké množství neplatných dat? Pokud ano, může zvýšit latence čtení.

## <a name="migration-issues"></a>Problémy s migrací

Pokud migrujete do Azure HDInsight, ujistěte se, že vaše migrace se provádí systematicky a přesně, nejlépe prostřednictvím automatizace. Vyhněte se ruční migraci. Ujistěte se, že:

- Atributy tabulky jsou migrovány přesně. Atributy mohou zahrnovat jako kompresi, filtry květu a tak dále.

- Nastavení soli v tabulkách Phoenix jsou mapovány odpovídajícím způsobem na novou velikost clusteru. Například počet bloků soli by měl být násobkem počtu pracovních uzlů v clusteru. A měli byste použít násobek, který je faktorem množství horké špinění.

## <a name="server-side-configuration-tunings"></a>Ladění konfigurace na straně serveru

V HDInsight HBase jsou HFiles uloženy ve vzdáleném úložišti. Pokud dojde k chybě mezipaměti, náklady na čtení je vyšší než místní systémy, protože data v místních systémech je zálohována místní HDFS. Pro většinu scénářů je inteligentní použití mezipamětí HBase (blokové mezipaměti a mezipaměti bloku) navrženo k obcházení tohoto problému. V případech, kdy problém není obcházen, může tento problém pomoci pomocí účtu objektů blob premium bloku. Ovladač objektu blob úložiště Windows Azure `fs.azure.read.request.size` závisí na určitých vlastnostech, jako je například načtení dat v blocích na základě toho, co určuje jako režim čtení (sekvenční versus náhodné), takže může nadále existovat instance vyšší latence s čtení. Prostřednictvím empirických experimentů jsme zjistili, že`fs.azure.read.request.size`nastavení velikosti bloku požadavku na čtení ( ) na 512 KB a odpovídající velikosti bloku tabulek HBase na stejnou velikost vytváří nejlepší výsledek v praxi.

Pro většinu velkých uzlů clusterů HDInsight HBase poskytuje `bucketcache` jako soubor na místní Premium SSD, který je `regionservers`připojen k virtuálnímu počítači, který běží . Použití mezipaměti off-haldy místo toho může poskytnout určité zlepšení. Toto řešení má omezení použití dostupné paměti a potenciálně menší než mezipaměti založené na souborech, takže nemusí být vždy nejlepší volbou.

Níže jsou uvedeny některé z dalších specifických parametrů, které jsme vyladili, a zdálo se, že pomáhají v různé míře:

- Zvětšete `memstore` velikost z výchozích 128 MB na 256 MB. Toto nastavení se obvykle doporučuje pro scénáře náročného zápisu.

- Zvyšte počet vláken, které jsou vyhrazeny pro zhutnění, z výchozího nastavení **1** na **4**. Toto nastavení je důležité, pokud pozorujeme časté menší zhutnění.

- Vyhněte se blokování `memstore` vyprázdnění z důvodu limitu úložiště. Chcete-li tuto vyrovnávací `Hbase.hstore.blockingStoreFiles` paměť poskytnout, zvyšte nastavení na **100**.

- Chcete-li řídit vyprázdnění, použijte následující nastavení:

    - `Hbase.regionserver.maxlogs`: **140** (zabraňuje proplachování kvůli limitům WAL)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0,55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0,60**

- Phoenix specifické konfigurace pro ladění fondu vláken:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`**512.**

- Další konfigurace specifické pro Phoenix:

    - `Phoenix.rpc.index.handler.count`: **50** (pokud existují velké nebo mnoho vyhledávání indexu)

    - `Phoenix.stats.updateFrequency`: **1 hodina**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 hodina**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- Časové výčasové osádky RPC: **3 minuty**

   - Mezi časové limity vzdáleného volání procedur patří časový limit HBase RPC, časový limit skeneru klienta HBase a časový limit dotazu phoenix. 
   - Ujistěte se, že `hbase.client.scanner.caching` parametr je nastavena na stejnou hodnotu na konci serveru i na konci klienta. Pokud nejsou stejné, toto nastavení vede k chybám na `OutOfOrderScannerException`konci klienta, které souvisejí s . Toto nastavení by mělo být nastaveno na nízkou hodnotu pro velké prohledávací skeny. Tuto hodnotu nastavíme na **100**.

## <a name="other-considerations"></a>Další aspekty

Následují další parametry, které je třeba zvážit ladění:

- `Hbase.rs.cacheblocksonwrite`– ve výchozím nastavení na HDI je toto nastavení nastaveno na **hodnotu true**.

- Nastavení, která umožňují odložit menší zhutnění na později.

- Experimentální nastavení, jako je například úprava procent front, které jsou vyhrazeny pro čtení a zápis požadavků.

## <a name="next-steps"></a>Další kroky

Pokud váš problém zůstává nevyřešený, navštivte jeden z následujících kanálů pro další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

- Spojte [@AzureSupport](https://twitter.com/azuresupport)se s uživatelem . Toto je oficiální účet Microsoft Azure pro zlepšení zákaznického prostředí. Propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

- Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Vaše předplatné Microsoft Azure zahrnuje přístup ke správě předplatného a fakturační podpoře a technická podpora je poskytována prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
