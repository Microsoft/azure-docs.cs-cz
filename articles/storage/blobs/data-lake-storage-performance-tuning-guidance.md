---
title: Azure Data Lake Storage Gen2 výkonu pokyny k ladění | Dokumentace Microsoftu
description: Azure Data Lake Storage Gen2 výkonu pokyny k ladění
services: storage
author: swums
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: stewu
ms.openlocfilehash: 43cec400a21671d47d1a6c390833a5e4c6517709
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975239"
---
# <a name="tuning-azure-data-lake-storage-gen2-for-performance"></a>Optimalizace výkonu pro Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 podporuje vysokou propustnost vstupně-výstupní operace náročné na analýze a k údajům pohyb.  V Data Lake Storage Gen2 je použít všechny dostupné propustnosti – množství dat, které může číst nebo zapisovat za sekundu – důležité, abyste co nejlepšího výkonu.  Tím dosáhnete pomocí provádí tolik čtení a zapíše paralelně nejvíce.

![Data Lake Storage Gen2 výkonu](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen2 můžete škálovat pro zajištění nezbytné propustnosti pro všechny scénáře analýzy. Ve výchozím nastavení poskytuje účet Data Lake Storage Gen2 automaticky dostatečnou propustnost pro potřeby hlavních kategorií případy použití. Pro případy, kdy zákazníci spouštět do výchozí limit, může účet Data Lake Storage Gen2 nakonfigurované na poskytování větší propustnost kontaktováním [podpory Azure](https://azure.microsoft.com/support/faq/).

## <a name="data-ingestion"></a>Přijímání dat

Příjem dat ze zdrojového systému na Gen2 úložiště Data Lake, je důležité vzít v úvahu, že zdroj hardwaru, zdroj síťového hardwaru a síťové připojení k Data Lake Storage Gen2 může být problémové místo.  

![Data Lake Storage Gen2 výkonu](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

Je důležité zajistit, že přesun dat nemá vliv tyto faktory.

### <a name="source-hardware"></a>Zdroj hardwaru

Ať používáte místní počítače nebo virtuální počítače v Azure, byste měli pečlivě zvolit vhodný hardware. Pro zdrojový Disk Hardware raději SSD disků HDD a vyberte hardware disku s rychlejší diskových jednotek. Pro zdroj síťového hardwaru použijte nejrychlejší možné síťové adaptéry.  V Azure doporučujeme D14 virtuální počítače Azure, které mají odpovídajícím způsobem výkonné disku a síťový hardware.

### <a name="network-connectivity-to-data-lake-storage-gen2"></a>Připojení k síti na Gen2 úložiště Data Lake

Připojení k síti mezi zdrojem dat a Data Lake Storage Gen2 může být někdy problémové místo. Pokud zdrojová data nejsou v místním, zvažte použití vyhrazené propojení s [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Pokud je zdrojová data v Azure, bude výkon nejlepší při data jsou ve stejné oblasti Azure jako účet Data Lake Storage Gen2.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurace nástroje pro ingestování dat pro maximální paralelizaci

Jakmile vyřešili zdrojového hardwaru a výše kritické body připojení k síti, jste připraveni ke konfiguraci nástroje pro ingestování. Následující tabulka shrnuje nastavení klíče pro nástroje pro několik oblíbených ingestování a poskytuje podrobné výkonů články pro ně.  Další informace o nástroji pro váš scénář, navštivte tuto [článku](data-lake-storage-data-scenarios.md).

| Nástroj               | Nastavení     | Další podrobnosti                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| DistCp            | -m (mapování)   | [Odkaz](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Odkaz](../../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS.Azure.Block.Size -m (mapování)    |   [Odkaz](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Struktury datové sady

Pokud jsou data uložená v Data Lake Storage Gen2, velikost, počet souborů a strukturu složek mít vliv na výkon.  Následující část popisuje osvědčené postupy v těchto oblastech.  

### <a name="file-size"></a>Velikost souboru

Analytics modulů, jako je HDInsight a Azure Data Lake Analytics obvykle mají režijní náklady na soubor. Pokud data ukládáte tolik souborů malých, to negativně ovlivnit výkon. Obecně platí uspořádejte data do větší velikosti souborů pro zajištění lepšího výkonu (256MB až do velikosti 100GB). Některé weby a aplikace můžou mít potíže s efektivní zpracování souborů, které jsou větší než 100GB.

V některých případech datové kanály, máte omezenou kontrolu nad nezpracovanými daty, která obsahuje velké množství malých souborů. Doporučuje se mít "kuchařkám" proces, který generuje větší soubory pro příjem dat aplikací.

### <a name="organizing-time-series-data-in-folders"></a>Uspořádání dat časových řad ve složkách

Pro úlohy Hive může pomoct oddílu vyřazení dat časových řad některé dotazy číst pouze podmnožinu dat, což zvyšuje výkon.    

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

## <a name="general-considerations"></a>Obecné aspekty

Máte úlohu, která čte nebo zapisuje navýší 100MB v rámci jedné operace, ale do vyrovnávací paměti, které může snížit výkon.
K optimalizaci výkonu, akci a chránit tak velikost vstupně-výstupní operace mezi 4MB, 16MB.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Obecné aspekty clusteru služby HDInsight

* **HDInsight verze.** Pro zajištění nejlepšího výkonu použijte nejnovější vydání HDInsight.
* **Oblasti.** Účet Data Lake Storage Gen2 umístěte do stejné oblasti jako HDInsight cluster.  

HDInsight cluster se skládá z dvou hlavních uzlů a některé uzly pracovního procesu. Každý pracovní uzel obsahuje určitý počet jader a paměti, které se určuje podle typu virtuálního počítače.  Při spuštění úlohy, YARN je resource negotiator, který přiděluje dostupné paměti a jader k vytvoření kontejnerů.  Každý kontejner spustí úloh potřebných k dokončení úlohy.  Paralelní zpracování úloh rychle spouštět kontejnery. Proto se výkon spuštěním libovolný počet paralelních kontejnerů nejvíce.

Existují tři vrstvy v rámci clusteru služby HDInsight, který lze ladit o zvýšení počtu kontejnerů a všechny dostupné propustnosti.  

* **Fyzickou vrstvu**
* **Vrstva YARN**
* **Vrstvu pracovního vytížení**

### <a name="physical-layer"></a>Fyzickou vrstvu

**Spuštění clusteru s více uzly a/nebo větší velikosti virtuálních počítačů.**  Větší cluster vám umožní spustit další kontejnery YARN, jak je znázorněno na obrázku níže.

![Data Lake Storage Gen2 výkonu](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**Použití virtuálních počítačů s větší šířku pásma sítě.**  Šířku pásma sítě může být kritickým bodem, pokud je menší šířku pásma sítě než Data Lake Storage Gen2 propustnosti.  Různé virtuální počítače budou mít různé velikosti šířky pásma sítě.  Vyberte virtuální počítač – typ, který má největší šířka pásma sítě je to možné.

### <a name="yarn-layer"></a>Vrstva YARN

**Použijte menší kontejnery YARN.**  Zmenšete velikost každého kontejneru YARN vytvoření více kontejnerů pomocí stejné množství prostředků.

![Data Lake Storage Gen2 výkonu](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

V závislosti na velikosti pracovní zátěže bude vždy minimální velikost kontejneru YARN, které je potřeba. Pokud vyberete příliš malé kontejner, bude vaše úlohy narazíte na problémy na více instancí z důvodu nedostatku paměti. Kontejnery YARN by měl být obvykle nesmí být menší než 1GB. Je běžné zobrazte kontejnery YARN 3GB. Pro některé úlohy můžete potřebovat větší kontejnery YARN.  

**Zvýšit počet jader na kontejneru YARN.**  Zvýšíte počet jader přidělené na každý kontejner zvyšte počet paralelních úloh, které běží v jednotlivých kontejnerech.  Tento postup funguje pro aplikace, jako je Spark, které běží více úkolů na kontejner.  Pro aplikace jako Hive, které běží jedno vlákno v jednotlivých kontejnerech je lepší mít víc kontejnerů než více jader na kontejner.

### <a name="workload-layer"></a>Vrstvu pracovního vytížení

**Použijte všechny dostupné kontejnery.**  Nastavte počet úloh tak, aby všechny prostředky přistupujících bude stejná nebo větší než počet dostupných kontejnerů.

![Data Lake Storage Gen2 výkonu](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**Neúspěšné úlohy je nákladné.** Pokud každý úkol má velké množství dat ke zpracování, selhání úkolu výsledků v nákladné opakování.  Proto je vhodnější vytvořit více úkolů, z nichž každý zpracovává malé množství dat.

Kromě výše uvedených obecné pokyny každá aplikace má různé parametry, které jsou k dispozici pro ladění pro určitou aplikaci. Následující tabulka uvádí některé z parametrů a odkazy pro zahájení práce s pro jednotlivé aplikace pro optimalizaci výkonu.

| Úloha               | Parametr pro nastavení úlohy                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Spark v hdinisight do domény](data-lake-storage-performance-tuning-spark.md)       | <ul><li>Počet prováděcích modulů</li><li>Prováděcí modul paměti</li><li>Prováděcí modul jader</li></ul> |
| [Hive s HDInsight](data-lake-storage-performance-tuning-hive.md)    | <ul><li>Hive.tez.Container.Size</li></ul>         |
| [MapReduce v HDInsight](data-lake-storage-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.Memory</li><li>Mapreduce.job.Maps</li><li>Mapreduce.reduce.Memory</li><li>Mapreduce.job.reduces</li></ul> |
| [Storm v HDInsight](data-lake-storage-performance-tuning-storm.md)|  | <ul><li>Počet pracovních procesů</li><li>Počet instancí spout prováděcího modulu</li><li>Počet instancí bolt prováděcího modulu </li><li>Počet úkolů spout</li><li>Počet úkolů bolt</li></ul>|

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled služby Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
