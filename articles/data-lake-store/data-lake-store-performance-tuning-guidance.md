---
title: Ladění pravidla výkonu Azure Data Lake Store | Microsoft Docs
description: Ladění pravidla výkonu Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: cgronlun
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: 29b662aa2f30083b444483554a78d53f0d05cb7f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196980"
---
# <a name="tuning-azure-data-lake-store-for-performance"></a>Ladění výkonu Azure Data Lake Store

Data Lake Store podporuje vysokou propustností k analýze intenzivním vstupně-výstupních operací a přesun dat.  V Azure Data Lake Store pomocí všechny dostupné propustnost – množství dat, která můžou číst nebo zapisovat za sekundu – je důležité získat nejlepší výkon.  To se dosáhne provedením tolik čte a zapisuje paralelně nejdříve.

![Data Lake Store výkonu](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Azure Data Lake Store můžete škálovat zajistit potřebné propustnost pro všechny scénáře analýzy. Ve výchozím nastavení účet Azure Data Lake Store poskytuje automaticky dostatek propustnost, aby vyhovovaly hlavních kategorií případy použití. V případech, kde zákazníci spustit do výchozí limit může být účtu ADLS nakonfigurován pro poskytují další propustnost, že se obrátíte na podporu společnosti Microsoft.

## <a name="data-ingestion"></a>Přijímání dat

Když příjem dat ze zdrojového systému ADLS, je důležité vzít v úvahu, že zdroj hardwaru, zdroj síťového hardwaru a síťové připojení k ADLS mohou být problémové místo.  

![Data Lake Store výkonu](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

Je důležité zajistit, že přesun dat není ovlivněn tyto faktory.

### <a name="source-hardware"></a>Zdroj hardwaru

Ať používáte místní počítače nebo virtuální počítače v Azure, měli byste pečlivě vybrat vhodný hardware. Pro zdroj disku Hardware raději pevné disky SSD a vyberte hardware disku s rychlejší diskových jednotek. Pro zdroj síťový Hardware použijte nejrychlejší možné síťové adaptéry.  V Azure doporučujeme D14 virtuálních počítačích Azure, které mají správně výkonné disku a síťového hardwaru.

### <a name="network-connectivity-to-azure-data-lake-store"></a>Síťové připojení k Azure Data Lake Store

Síťové připojení mezi zdrojem dat a Azure Data Lake store v některých případech může být kritická místa. Pokud je zdroj dat na místě, zvažte použití vyhrazených odkaz s [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Pokud je zdroj dat v Azure, výkon bude nejlepší, když jsou data ve stejné oblasti Azure jako Data Lake Store.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurace nástrojů přijímání dat pro maximální paralelizace

Jakmile vyřešili zdrojového hardwaru a síťové připojení kritická místa výše, jste připraveni ke konfiguraci vaší přijímání nástroje. Následující tabulka shrnuje nastavení klíče pro několik oblíbených přijímání nástrojů a poskytuje podrobné ladění články pro ně výkonu.  Další informace o nástroji pro váš scénář, získáte na tomto [článku](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios).

| Nástroj               | Nastavení     | Další informace                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount ConcurrentFileCount |  [Odkaz](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-powershell#performance-guidance-while-using-powershell) |
| AdlCopy    | Azure Data Lake Analytics jednotky  |   [Odkaz](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (Mapovač)   | [Odkaz](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Odkaz](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS.Azure.Block.Size -m (Mapovač)    |   [Odkaz](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Struktury sadu dat

Pokud data uložená v Data Lake Store, velikost souboru, počet souborů a struktura složek mít vliv na výkon.  Následující část popisuje osvědčené postupy v těchto oblastech.  

### <a name="file-size"></a>Velikost souboru

Analýza stroje například HDInsight a Azure Data Lake Analytics obvykle mají režijní náklady na soubor.  Pokud data ukládáte jako mnoho malých souborů, to může mít negativní vliv na výkon.  

Obecně platí organizování vašich dat ve větší velikosti souborů pro lepší výkon.  Jako existuje pravidlo uspořádání datových sad v souborech 256 MB nebo větší. V některých případech, například bitové kopie a binární data není možné zpracovat souběžně.  V těchto případech doporučujeme ponechat jednotlivých souborů v části 2GB.

V některých případech datových kanálů mají omezenou kontrolu nad nezpracovaná data, která obsahuje velké množství malých souborů.  Doporučuje se mít "vaření" postup, který generuje větší soubory pro příjem dat aplikací.

### <a name="organizing-time-series-data-in-folders"></a>Uspořádání data časové řady ve složkách

Pro úlohy Hive a ADLA oddílu vyřazování data časové řady může pomoct některé dotazy číst pouze podmnožinu dat, což zvyšuje výkon.    

Tyto kanály, které ingestují data časové řady, často umístit své soubory s velmi strukturovaných pojmenovávání souborů a složek. Níže je velmi běžné příklad, který vidíte pro data, která je strukturovaná data:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Všimněte si, že data a času informace se zobrazí jako složky i v názvu souboru.

Datum a čas Toto je běžný vzor

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Znovu volba provedete se složkou a organizace souborů by měl optimalizovat pro větší velikosti souborů a přiměřené počet souborů v každé složky.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optimalizace náročné úlohy vstupně-výstupních operací na úlohy Hadoop a Spark v HDInsight

Úlohy spadat do jednoho z těchto tří kategorií:

* **Náročné na prostředky procesoru.**  Tyto úlohy má dlouhou výpočetní dobu s minimálním časy vstupu a výstupu.  Mezi příklady patří strojového učení a přirozeného jazyka zpracování úloh.  
* **Velké množství paměti.**  Tyto úlohy použijte velké množství paměti.  Mezi příklady patří PageRank a analýzu v reálném čase úlohy.  
* **Vstupně-výstupní operace náročné na prostředky.**  Tyto úlohy tráví většinu jejich doby provádění vstupně-výstupní operace.  Běžným příkladem jsou kopie úlohu, u které pouze operacích čtení a zápisu.  Další příklady zahrnují data přípravy úlohy, které číst velké množství dat, provádí některé transformaci dat a zapisuje data zpět do úložiště.  

Následující pokyny platí pouze pro úlohy náročné vstupně-výstupní operace.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Obecné aspekty pro cluster služby HDInsight

* **HDInsight verze.** Pro nejlepší výkon použijte nejnovější verzi HDInsight.
* **Oblasti.** Místní Data Lake Store ve stejné oblasti jako HDInsight cluster.  

Cluster služby HDInsight se skládá ze dvou hlavních uzlech a některé uzly pracovního procesu. Každý pracovní uzel poskytuje určitý počet jader a paměti, což je dáno typ virtuálního počítače.  Při spuštění úlohy, je YARN vyjednavač prostředku, který přiděluje dostupnou paměť a počet jader na vytvoření kontejnerů.  Každý kontejner spouští úlohy potřebný k dokončení úlohy.  Paralelní zpracování úlohy rychle spuštění kontejnery. Proto spuštěním tolik paralelní kontejnery nejdříve vyšší výkon.

Existují tři vrstvy v rámci clusteru služby HDInsight, který lze ladit zvýšit počet kontejnerů a používat všechny dostupné propustnost.  

* **Fyzickou vrstvu**
* **YARN vrstvy**
* **Zatížení vrstvy**

### <a name="physical-layer"></a>Fyzickou vrstvu

**Spusťte clusteru s více uzly nebo větší velikosti virtuálních počítačů.**  Větší cluster vám umožní spustit víc kontejnerů YARN, jak je znázorněno na obrázku níže.

![Data Lake Store výkonu](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Použijte virtuální počítače s větší šířku pásma sítě.**  Šířku pásma sítě může být kritický bod, pokud je menší šířku pásma sítě než propustnost Data Lake Store.  Různé virtuální počítače budou mít různých velikosti šířky pásma sítě.  Vyberte virtuální počítač typ, který má největší možné šířku pásma sítě.

### <a name="yarn-layer"></a>YARN vrstvy

**Použití menší YARN kontejnerů.**  Snížení velikosti každý kontejner YARN k vytvoření více kontejnerů se stejnou velikostí prostředků.

![Data Lake Store výkonu](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

V závislosti na velikosti pracovní zátěže bude vždy minimální velikost YARN kontejneru, která je potřeba. Pokud vyberete příliš malá kontejner, vaše úlohy se spustí do problémy z důvodu nedostatku paměti. Kontejnery YARN obvykle by měla být menší než 1GB. Je běžné najdete v části kontejnery YARN 3GB. Pro některé úlohy pravděpodobně bude třeba větší kontejnery YARN.  

**Zvýšit jader na kontejner YARN.**  Zvyšte počet jader přidělené pro každý kontejner a zvýšit počet paralelních úloh, které běží v každém kontejneru.  Tento postup funguje pro aplikace jako Spark, které se spustit několik úkolů na kontejneru.  Pro aplikace jako Hive, které se spustit jedno vlákno v jednotlivých kontejnerech je lepší víc kontejnerů než více jader na kontejneru.

### <a name="workload-layer"></a>Zatížení vrstvy

**Použijte všechny dostupné kontejnery.**  Nastavte počet úloh tak, aby všechny prostředky jsou využité být stejná nebo větší než počet dostupných kontejnerů.

![Data Lake Store výkonu](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**Neúspěšné úlohy jsou nákladné.** Pokud má každý úkol velké množství dat ke zpracování, selhání úkolu výsledků v nákladné opakování.  Proto je lepší vytvořit další úlohy, z nichž každý malé množství dat zpracovává.

Kromě výše uvedených obecné pokyny každá aplikace má jiné parametry, které jsou k dispozici pro optimalizaci pro konkrétní aplikace. Následující tabulka uvádí některé parametry a odkazy na začít pracovat s ladění výkonu pro každou aplikaci.

| Úloha               | Parametr k nastavení úlohy                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Spark v HDInisight](data-lake-store-performance-tuning-spark.md)       | <ul><li>Poče vykonavatelů</li><li>Vykonavatele paměti</li><li>Vykonavatele jader</li></ul> |
| [Hive v HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>Hive.tez.Container.Size</li></ul>         |
| [MapReduce v HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>mapreduce.map.Memory</li><li>Mapreduce.job.Maps</li><li>mapreduce.reduce.Memory</li><li>Mapreduce.job.reduces</li></ul> |
| [Storm v HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Počet pracovních procesů</li><li>Počet instancí vykonavatele spout</li><li>Počet instancí vykonavatele bolt </li><li>Počet funkcí spout úlohy</li><li>Počet úloh bolt</li></ul>|

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled Azure Data Lake Storu](data-lake-store-overview.md)
* [Začínáme s Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
