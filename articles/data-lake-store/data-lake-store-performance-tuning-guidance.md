---
title: Azure Data Lake Storage Gen1 – ladění výkonu
description: Přečtěte si, jak využít veškerou dostupnou propustnost v Azure Data Lake Storage Gen1 je důležité pro dosažení optimálního výkonu díky souběžnému provádění tolika operací čtení a zápisu.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: twooley
ms.openlocfilehash: c7f16dd9ea450185893164e10928c7022d6ab5a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97724676"
---
# <a name="tune-azure-data-lake-storage-gen1-for-performance"></a>Vyladění Azure Data Lake Storage Gen1 pro výkon

Data Lake Storage Gen1 podporuje vysokou propustnost pro náročné I výstupní analýzy dat a přesun dat. V Data Lake Storage Gen1 s využitím veškeré dostupné propustnosti – množství dat, která je možné číst nebo zapsat za sekundu – je důležité k dosažení nejlepšího výkonu. Toho dosáhnete tak, že provedete souběžně tolik operací čtení a zápisu.

![Data Lake Storage Gen1 výkon](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen1 se může škálovat, aby poskytovala potřebnou propustnost pro všechny scénáře analýzy. Ve výchozím nastavení poskytuje účet Data Lake Storage Gen1 automaticky dostatečnou propustnost pro splnění potřeb široké kategorie případů použití. V případech, kdy se zákazníci spouštějí do výchozího limitu, je možné účet Data Lake Storage Gen1 nakonfigurovat tak, aby poskytoval větší propustnost, a to kontaktováním podpory Microsoftu.

## <a name="data-ingestion"></a>Přijímání dat

Při příjmu dat ze zdrojového systému do Data Lake Storage Gen1 je důležité vzít v úvahu, že zdrojový hardware, zdrojový síťový hardware a síťové připojení k Data Lake Storage Gen1 může být kritickým bodem.

![Diagram znázorňující, že zdrojový hardware, zdrojový síťový hardware a síťové připojení k Data Lake Storage Gen1 může být kritickým bodem.](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

Je důležité zajistit, aby přesun dat neovlivnily tyto faktory.

### <a name="source-hardware"></a>Zdrojový hardware

Bez ohledu na to, jestli používáte místní počítače nebo virtuální počítače v Azure, byste měli pečlivě vybrat příslušný hardware. V případě hardwaru zdrojového disku preferovat SSD HDD a vyberte diskový hardware s rychlejšími disky. Pro zdrojový síťový hardware použijte nejrychlejší možné síťové karty. V Azure doporučujeme virtuální počítače Azure s D14, které mají vhodně výkonný disk a síťový hardware.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>Připojení k síti Data Lake Storage Gen1

Síťové připojení mezi zdrojovými daty a Data Lake Storage Gen1 může někdy být kritickým bodem. Pokud jsou vaše zdrojová data místní, zvažte použití vyhrazeného propojení s [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Pokud jsou zdrojová data v Azure, výkon bude nejlepší, pokud jsou data ve stejné oblasti Azure jako účet Data Lake Storage Gen1.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurace nástrojů pro přijímání dat pro maximální paralelismus

Po vyřešení kritických bodů hardwaru zdroje a síťového připojení jste připraveni ke konfiguraci nástrojů pro přijímání. Následující tabulka shrnuje nastavení klíče pro několik oblíbených nástrojů pro přijímání a poskytuje podrobné články pro ladění výkonu. Další informace o tom, který nástroj použít pro váš scénář, najdete v tomto [článku](./data-lake-store-data-scenarios.md).

| Nástroj          | Nastavení | Další podrobnosti                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | Hodnoty perfilethreadcount, hodnota concurrentfilecount | [Odkaz](./data-lake-store-get-started-powershell.md) |
| AdlCopy    | Jednotky Azure Data Lake Analytics | [Odkaz](./data-lake-store-copy-data-azure-storage-blob.md#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (mapovač) | [Odkaz](./data-lake-store-copy-data-wasb-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies | [Odkaz](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS. Azure. Block. Size,-m (Mapper) | [Odkaz](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)        |

## <a name="structure-your-data-set"></a>Struktura sady dat

Když jsou data uložená v Data Lake Storage Gen1, velikost souboru, počet souborů a struktura složek mají vliv na výkon. V následující části jsou popsány osvědčené postupy v těchto oblastech.

### <a name="file-size"></a>Velikost souboru

Analytické stroje, jako je HDInsight a Azure Data Lake Analytics, obvykle mají režii vázané na soubor. Pokud uložíte data jako mnoho malých souborů, může to negativně ovlivnit výkon.

Obecně je vhodné uspořádat data do souborů s větší velikostí, aby se zajistil vyšší výkon. Jako pravidlo pro palec uspořádejte datové sady v souborech o velikosti 256 MB nebo větší. V některých případech, jako jsou obrázky a binární data, není možné je zpracovávat paralelně. V těchto případech se doporučuje uchovávat jednotlivé soubory za 2 GB.

Někdy mají datové kanály omezenou kontrolu nad nezpracovanými daty, která mají velký počet malých souborů. Doporučuje se mít "vaření" proces, který generuje větší soubory pro použití pro podřízené aplikace.

### <a name="organize-time-series-data-in-folders"></a>Uspořádání dat časových řad do složek

V případě úloh podregistrů a ADLA může vytváření oddílů při vyřazování dat časových řad v některých dotazech pomáhat při čtení pouze podmnožiny dat, což zvyšuje výkon.

Tyto kanály, které ingestují data časových řad, často umísťují své soubory se strukturovaným pojmenování pro soubory a složky. Níže je uveden běžný příklad pro data, která jsou strukturovaná podle data: *\dataset\yyyy\mm\dd\ datafile_YYYY_MM_DD. TSV*.

Všimněte si, že informace o typu DateTime se zobrazí jak jako složky, tak i v názvu souboru.

Pro datum a čas je zde běžný vzor: *\dataset\yyyy\mm\dd\hh\mm\ datafile_YYYY_MM_DD_HH_mm. TSV*.

Výběr, který provedete se složkou a organizací souborů, by se měl optimalizovat pro větší velikosti souborů a přiměřený počet souborů v každé složce.

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optimalizace úloh náročných na vstupně-výstupní operace pro úlohy Hadoop a Spark v HDInsight

Úlohy spadají do jedné z následujících tří kategorií:

* **Náročné na procesor.** Tyto úlohy mají dlouhé časy výpočtu s minimálním počtem vstupně-výstupních operací. Mezi příklady patří strojové učení a úlohy zpracování v přirozeném jazyce.
* **Náročné na paměť.** Tyto úlohy využívají spoustu paměti. Mezi příklady patří PageRank úlohy a analýzy v reálném čase.
* **I/O náročné.** Tyto úlohy tráví většinu času při provádění vstupně-výstupních operací. Běžným příkladem je úloha kopírování, která provádí pouze operace čtení a zápisu. Mezi další příklady patří úlohy přípravy dat, které čtou množství dat, provádí transformaci dat a pak data zapisuje zpátky do úložiště.

Následující pokyny platí pouze pro úlohy náročné na vstupně-výstupní operace.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Obecné požadavky na cluster HDInsight

* **Verze HDInsight.** Nejlepšího výkonu dosáhnete, když použijete nejnovější verzi HDInsight.
* **Regionu.** Data Lake Storage Gen1 účet umístěte do stejné oblasti jako cluster HDInsight.

Cluster An HDInsight se skládá ze dvou hlavních uzlů a některých pracovních uzlů. Každý pracovní uzel poskytuje určitý počet jader a paměti, které určuje typ virtuálního počítače. Při spuštění úlohy je PŘÍZe prostředkem projednávání prostředků, který přiděluje dostupnou paměť a jádra k vytváření kontejnerů. Každý kontejner spouští úlohy potřebné k dokončení této úlohy. Kontejnery běží paralelně a rychle zpracovávají úlohy. Proto se zvyšuje výkon tím, že spustí tolik paralelních kontejnerů, kolik jich je možné.

V rámci clusteru HDInsight existují tři vrstvy, které je možné vyladit, aby se zvýšil počet kontejnerů a používala se veškerá dostupná propustnost.

* **Fyzická vrstva**
* **PŘÍZ – vrstva**
* **Vrstva úlohy**

### <a name="physical-layer"></a>Fyzická vrstva

**Spusťte cluster s více uzly a/nebo s většími velikostmi virtuálních počítačů.** Větší cluster vám umožní spustit více kontejnerů PŘÍZ, jak je znázorněno na obrázku níže.

![Diagram, který ukazuje použití více kontejnerů PŘÍZe.](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Používejte virtuální počítače s větší šířkou pásma sítě.** Velikost šířky pásma sítě může být kritickým bodem, pokud je menší šířka pásma sítě, než Data Lake Storage Gen1 propustnost. Různé virtuální počítače budou mít proměnlivé velikosti šířky pásma sítě. Vyberte typ virtuálního počítače, který má největší možnou šířku pásma sítě.

### <a name="yarn-layer"></a>PŘÍZ – vrstva

**Použijte menší kontejnery PŘÍZe.** Zmenšete velikost každého kontejneru PŘÍZe, aby bylo možné vytvořit více kontejnerů se stejným množstvím prostředků.

![Diagram, který ukazuje použití menších kontejnerů PŘÍZ.](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

V závislosti na zatížení bude vždy potřeba minimální velikost kontejneru PŘÍZe. Pokud vybíráte příliš malý kontejner, úlohy budou fungovat při potížích s nedostatkem paměti. Obvykle kontejnery PŘÍZe by neměly být menší než 1 GB. Je běžné Zobrazit 3 GB PŘÍZ kontejnery. Pro některé úlohy budete možná potřebovat větší kontejnery PŘÍZe.

**Zvětšete jádra na kontejner PŘÍZe.** Zvyšte počet jader přidělených každému kontejneru a zvyšte počet paralelních úloh, které jsou spuštěny v každém kontejneru. To funguje u aplikací, jako je Spark, které spouštějí více úloh na kontejner. U aplikací, jako je například podregistr, který spouští jedno vlákno v každém kontejneru, je lepší mít více kontejnerů místo více jader na kontejner.

### <a name="workload-layer"></a>Vrstva úlohy

**Použijte všechny dostupné kontejnery.** Nastavte počet úkolů, které mají být stejné nebo větší než počet dostupných kontejnerů, aby byly použity všechny prostředky.

![Diagram, který zobrazuje použití všech dostupných kontejnerů.](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**Neúspěšné úlohy jsou nákladné.** Pokud má každý úkol zpracovat velké množství dat, pak při selhání úlohy dojde k nenáročnému opakování. Proto je lepší vytvořit další úkoly, z nichž každý zpracovává malé množství dat.

Kromě obecných pokynů uvedených v každé aplikaci jsou k dispozici různé parametry pro ladění pro danou konkrétní aplikaci. V následující tabulce jsou uvedené některé parametry a odkazy na začátek ladění výkonu pro jednotlivé aplikace.

| Úloha               | Parametr pro nastavení úloh                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Spark ve službě HDInsight](data-lake-store-performance-tuning-spark.md)  | <ul><li>Počet – vykonavatelé</li><li>Prováděcí modul – paměť</li><li>Prováděcí modul – jádra</li></ul> |
| [Podregistr v HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>podregistr. TEZ. Container. Size</li></ul>         |
| [MapReduce ve službě HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>MapReduce. map. Memory</li><li>MapReduce. job. Maps</li><li>MapReduce. zmenšení paměti</li><li>MapReduce. job. redukuje</li></ul> |
| [Storm v HDInsightu](data-lake-store-performance-tuning-storm.md)| <ul><li>Počet pracovních procesů</li><li>Počet instancí prováděcího modulu Spout</li><li>Počet instancí vykonavatele šroubů </li><li>Počet úloh Spout</li><li>Počet úloh šroubů</li></ul>|

## <a name="see-also"></a>Viz také

* [Přehled Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Začínáme s Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)