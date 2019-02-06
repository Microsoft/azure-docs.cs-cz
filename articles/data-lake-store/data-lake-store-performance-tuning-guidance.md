---
title: Azure Data Lake Storage Gen1 výkonu pokyny k ladění | Dokumentace Microsoftu
description: Azure Data Lake Storage Gen1 výkonu pokyny k ladění
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
ms.openlocfilehash: a8a50db5ece242bc00a28e66e21c863388950d6f
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756831"
---
# <a name="tuning-azure-data-lake-storage-gen1-for-performance"></a>Optimalizace výkonu pro Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 podporuje vysokou propustnost vstupně-výstupní operace náročné na analýze a k údajům pohyb.  V Data Lake Storage Gen1 je použít všechny dostupné propustnosti – množství dat, které může číst nebo zapisovat za sekundu – důležité, abyste co nejlepšího výkonu.  Tím dosáhnete pomocí provádí tolik čtení a zapíše paralelně nejvíce.

![Data Lake Storage Gen1 výkonu](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen1 můžete škálovat pro zajištění nezbytné propustnosti pro všechny scénáře analýzy. Ve výchozím nastavení poskytuje účet Data Lake Storage Gen1 automaticky dostatečnou propustnost pro potřeby hlavních kategorií případy použití. Pro případy, kdy zákazníci spouštět do výchozí omezení účet Data Lake Storage Gen1 lze nakonfigurovat k poskytnutí větší propustnost tak, že kontaktujete podporu Microsoftu.

## <a name="data-ingestion"></a>Přijímání dat

Příjem dat ze zdrojového systému k Data Lake Storage Gen1, je důležité vzít v úvahu, že zdroj hardwaru, zdroj síťového hardwaru a síťové připojení k Data Lake Storage Gen1 může být problémové místo.  

![Data Lake Storage Gen1 výkonu](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

Je důležité zajistit, že přesun dat nemá vliv tyto faktory.

### <a name="source-hardware"></a>Zdroj hardwaru

Ať používáte místní počítače nebo virtuální počítače v Azure, byste měli pečlivě zvolit vhodný hardware. Pro zdrojový Disk Hardware raději SSD disků HDD a vyberte hardware disku s rychlejší diskových jednotek. Pro zdroj síťového hardwaru použijte nejrychlejší možné síťové adaptéry.  V Azure doporučujeme D14 virtuální počítače Azure, které mají odpovídajícím způsobem výkonné disku a síťový hardware.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>Síťové připojení k Data Lake Storage Gen1

Připojení k síti mezi zdrojem dat a Data Lake Storage Gen1 může být někdy problémové místo. Pokud zdrojová data nejsou v místním, zvažte použití vyhrazené propojení s [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Pokud je zdrojová data v Azure, bude výkon nejlepší při data jsou ve stejné oblasti Azure jako účet Data Lake Storage Gen1.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurace nástroje pro příjem dat pro maximální paralelizaci

Jakmile vyřešili zdrojového hardwaru a výše kritické body připojení k síti, jste připraveni ke konfiguraci nástroje pro ingestování. Následující tabulka shrnuje nastavení klíče pro nástroje pro několik oblíbených ingestování a poskytuje podrobné výkonů články pro ně.  Další informace o nástroji pro váš scénář, navštivte tuto [článku](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios).

| Nástroj               | Nastavení     | Další podrobnosti                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, ConcurrentFileCount |  [Odkaz](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-powershell) |
| AdlCopy    | Jednotky Azure Data Lake Analytics  |   [Odkaz](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (mapování)   | [Odkaz](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Odkaz](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS.Azure.Block.Size -m (mapování)    |   [Odkaz](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Struktury datové sady

Pokud jsou data uložená v Data Lake Storage Gen1, velikost, počet souborů a strukturu složek mít vliv na výkon.  Následující část popisuje osvědčené postupy v těchto oblastech.  

### <a name="file-size"></a>Velikost souboru

Analytics modulů, jako je HDInsight a Azure Data Lake Analytics obvykle mají režijní náklady na soubor.  Pokud data ukládáte tolik souborů malých, to negativně ovlivnit výkon.  

Obecně platí uspořádejte data do větší velikosti souborů pro zajištění lepšího výkonu.  Jako říci uspořádání datových sad v souborech o 256MB nebo větší. V některých případech, jako jsou obrázky a binárních dat není možné zpracovat souběžně.  V těchto případech doporučujeme ponechat jednotlivé soubory v části 2GB.

V některých případech datové kanály, máte omezenou kontrolu nad nezpracovanými daty, která obsahuje velké množství malých souborů.  Doporučuje se mít "kuchařkám" proces, který generuje větší soubory pro příjem dat aplikací.

### <a name="organizing-time-series-data-in-folders"></a>Uspořádání dat Time Series ve složkách

Pro úlohy Hive a ADLA může pomoct oddílu vyřazení dat časových řad některé dotazy číst pouze podmnožinu dat, což zvyšuje výkon.    

Tyto kanály, které ingestovat data časových řad, často umístit své soubory s velmi strukturovaných pojmenování pro soubory a složky. Níže je velmi Běžným příkladem, který se zobrazí pro data, která strukturovaná data:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Všimněte si, že se zobrazí informace data a času jako složky a název souboru.

Pro datum a čas tady je běžný vzor

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Opět volba, kterou provádíte ve složce a organizace souboru by měla optimalizovat pro větší velikosti souboru a přiměřené počet souborů v každé složky.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optimalizace úlohy náročné na vstupně-výstupních operací úloh Hadoop a Spark v HDInsight

Úlohy spadají do jedné z následujících tří kategorií:

* **Náročné na prostředky procesoru.**  Tyto úlohy mají dlouhé výpočet časů s minimální časy vstupu a výstupu.  Mezi příklady patří machine learning a úloh zpracování přirozeného jazyka.  
* **Náročné na paměť.**  Tyto úlohy používají velké množství paměti.  Mezi příklady patří PageRank a úlohy analýzy v reálném čase.  
* **Vstupně-výstupní operace náročné na prostředky.**  Tyto úlohy tráví většinu svého času provádění vstupně-výstupních operací.  Běžným příkladem jsou úlohu kopírování, která jenom operace čtení a zápisu.  Další příklady úloh přípravy dat, které čtou velké množství dat, provádí některé transformace dat a pak zapíše data do úložiště.  

Následující pokyny platí jenom pro úlohy náročné na vstupně-výstupních operací.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Obecné aspekty clusteru služby HDInsight

* **HDInsight verze.** Pro zajištění nejlepšího výkonu použijte nejnovější vydání HDInsight.
* **Oblasti.** Účet Data Lake Storage Gen1 umístěte do stejné oblasti jako HDInsight cluster.  

HDInsight cluster se skládá z dvou hlavních uzlů a některé uzly pracovního procesu. Každý pracovní uzel obsahuje určitý počet jader a paměti, které se určuje podle typu virtuálního počítače.  Při spuštění úlohy, YARN je resource negotiator, který přiděluje dostupné paměti a jader k vytvoření kontejnerů.  Každý kontejner spustí úloh potřebných k dokončení úlohy.  Paralelní zpracování úloh rychle spouštět kontejnery. Proto se výkon spuštěním libovolný počet paralelních kontejnerů nejvíce.

Existují tři vrstvy v rámci clusteru služby HDInsight, který lze ladit o zvýšení počtu kontejnerů a všechny dostupné propustnosti.  

* **Fyzickou vrstvu**
* **Vrstva YARN**
* **Vrstvu pracovního vytížení**

### <a name="physical-layer"></a>Fyzickou vrstvu

**Spuštění clusteru s více uzly a/nebo větší velikosti virtuálních počítačů.**  Větší cluster vám umožní spustit další kontejnery YARN, jak je znázorněno na obrázku níže.

![Data Lake Storage Gen1 výkonu](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Použití virtuálních počítačů s větší šířku pásma sítě.**  Šířku pásma sítě může být kritickým bodem, pokud je menší šířku pásma sítě než Data Lake Storage Gen1 propustnosti.  Různé virtuální počítače budou mít různé velikosti šířky pásma sítě.  Vyberte virtuální počítač – typ, který má největší šířka pásma sítě je to možné.

### <a name="yarn-layer"></a>Vrstva YARN

**Použijte menší kontejnery YARN.**  Zmenšete velikost každého kontejneru YARN vytvoření více kontejnerů pomocí stejné množství prostředků.

![Data Lake Storage Gen1 výkonu](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

V závislosti na velikosti pracovní zátěže bude vždy minimální velikost kontejneru YARN, které je potřeba. Pokud vyberete příliš malé kontejner, bude vaše úlohy narazíte na problémy na více instancí z důvodu nedostatku paměti. Kontejnery YARN by měl být obvykle nesmí být menší než 1GB. Je běžné zobrazte kontejnery YARN 3GB. Pro některé úlohy můžete potřebovat větší kontejnery YARN.  

**Zvýšit počet jader na kontejneru YARN.**  Zvýšíte počet jader přidělené na každý kontejner zvyšte počet paralelních úloh, které běží v jednotlivých kontejnerech.  Tento postup funguje pro aplikace, jako je Spark, které běží více úkolů na kontejner.  Pro aplikace jako Hive, které běží jedno vlákno v jednotlivých kontejnerech je lepší mít víc kontejnerů než více jader na kontejner.

### <a name="workload-layer"></a>Vrstvu pracovního vytížení

**Použijte všechny dostupné kontejnery.**  Nastavte počet úloh tak, aby všechny prostředky přistupujících bude stejná nebo větší než počet dostupných kontejnerů.

![Data Lake Storage Gen1 výkonu](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**Neúspěšné úlohy je nákladné.** Pokud každý úkol má velké množství dat ke zpracování, selhání úkolu výsledků v nákladné opakování.  Proto je vhodnější vytvořit více úkolů, z nichž každý zpracovává malé množství dat.

Kromě výše uvedených obecné pokyny každá aplikace má různé parametry, které jsou k dispozici pro ladění pro určitou aplikaci. Následující tabulka uvádí některé z parametrů a odkazy pro zahájení práce s pro jednotlivé aplikace pro optimalizaci výkonu.

| Úloha               | Parametr pro nastavení úlohy                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Spark v HDInsight](data-lake-store-performance-tuning-spark.md)       | <ul><li>Počet prováděcích modulů</li><li>Prováděcí modul paměti</li><li>Prováděcí modul jader</li></ul> |
| [Hive s HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>hive.tez.container.size</li></ul>         |
| [MapReduce v HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.Memory</li><li>Mapreduce.job.reduces</li></ul> |
| [Storm v HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Počet pracovních procesů</li><li>Počet instancí spout prováděcího modulu</li><li>Počet instancí bolt prováděcího modulu </li><li>Počet úkolů spout</li><li>Počet úkolů bolt</li></ul>|

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled služby Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Začínáme s Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
