---
title: Použití Apache Hivu jako nástroje ETL – Azure HDInsight
description: Pomocí Apache Hive extrakce, transformace a načítání (ETL) dat v Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.openlocfilehash: f8fb036eaca35e41d89b0a9610ebcd68e65f40f9
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630254"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Použití Apache Hivu jako nástroj extrakce, transformace a načítání (ETL)

Je obvykle potřeba čištění a transformace příchozích dat před jejich načtením do cíle vhodný pro účely analýzy. Operace extrakce, transformace a načítání (ETL) slouží k přípravě dat a jejich načtení do cíl dat.  Apache Hive s HDInsight může číst v nestrukturovaných dat, zpracování dat podle potřeby a pak načíst data do relačního datového skladu pro podporu systémů rozhodnutí. V takovém případě jsou data extrahovaná ze zdroje a uložená v škálovatelné úložiště, jako jsou objekty BLOB služby Azure Storage nebo Azure Data Lake Storage. Data se potom transformuje použití posloupnosti dotazů Hive a nakonec zpracováním v rámci Hive v rámci přípravy pro hromadné načítání do cílového úložiště dat.

## <a name="use-case-and-model-overview"></a>Přehled případu a modelů

Následující obrázek znázorňuje přehled o případu použití a model pro službu ETL automation. Generovat odpovídající výstup se transformuje vstupní data.  Při transformaci můžete změnit data obrazce, datový typ a dokonce i jazyk.  Procesy ETL můžete převést Imperial metriku, změnit časová pásma a zvýšit přesnost správně zarovnat s existujícími daty v cílovém umístění.  Procesy ETL můžete také kombinovat nová data s existujícími daty zachovat Reporting aktuální, nebo k poskytování dalších přehledů o existující data.  Aplikace, jako je vytváření sestav nástroje a služby, pak mohou využívat tato data v požadovaném formátu.

![Apache Hivu jako ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Hadoop se obvykle používá v ETL procesy, které importovat buď obrovské množství textové soubory (např. csv) nebo menší, ale často mění číslo textové soubory nebo obojí.  Hive je skvělý nástroj, který slouží k přípravě dat před jejich načtením do cíle dat.  Hive můžete vytvořit schéma přes sdílený svazek clusteru a generovat programů MapReduce, které pracují s daty pomocí jazyka typu SQL. 

Typické postupy použití Hive k provedení ETL jsou následující:

1. Načtení dat do služby Azure Data Lake Storage nebo Azure Blob Storage.
2. Vytvoření databáze metadat Store (využívající Azure SQL Database) pro použití Hive v ukládání vašich schémata.
3. Vytvoření clusteru HDInsight a připojte úložiště.
4. Definice schématu pro použití při čtení dat v úložišti dat:

    ```
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs
    
    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT, 
        system BIGINT, 
        systemage BIGINT, 
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    STORED AS TEXTFILE LOCATION 'wasb://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Transformace dat a jejich načtení do cíle.  Použití Hivu během transformace a načítání několika způsoby:

    * Dotazování a příprava dat pomocí Hivu a uložte ho jako sdíleného svazku clusteru v Azure Data Lake Storage nebo Azure blob storage.  Pak pomocí nástroje jako je integrace služby SSIS (SQL Server) k získání těchto sdílených svazků clusteru a načtení dat do cílové relační databáze jako je SQL Server.
    * Dotaz na data přímo z aplikace Excel nebo C# pomocí ovladače Hive ODBC.
    * Použití [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) číst připravené plochých souborů CSV a načíst je do cílové relační databáze.

## <a name="data-sources"></a>Zdroje dat

Zdroje dat jsou obvykle externí data, která by se shodovala se stávajícími daty v úložišti dat například:

* Data sociálních médií, soubory protokolů, senzory a aplikací, které generují data souborů.
* Datové sady ze zprostředkovatele dat, jako je například statistické údaje o počasí nebo dodavatele získat prodejní čísla.
* Streamovaná data zachytit, filtrovat a zpracovává prostřednictvím vhodného nástroje nebo architektury.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Výstup cíle

Hive můžete použít k výstupní data do různých cílů, včetně:

* Relační databáze, jako je například SQL Server nebo databázi SQL Azure.
* Datový sklad, jako je Azure SQL Data Warehouse.
* Excel.
* Azure table a blob storage.
* Aplikace nebo služby, které vyžadují data ke zpracování do určité formáty, nebo jako soubory, které obsahují konkrétní typy struktury informace.
* Store dokumentů JSON, jako jsou <a href="https://azure.microsoft.com/services/cosmos-db/">CosmosDB</a>.

## <a name="considerations"></a>Požadavky

ETL model se obvykle používá, když chcete:

* Načíst data datového proudu nebo velké objemy nestrukturovaných nebo částečně strukturovaných dat z externích zdrojů do existující databáze nebo informace o systému.
* Vyčistit, transformace a ověřit data před načtením, třeba pomocí více než jedna transformace předávání clusteru.
* Generovat sestavy a vizualizace, které se pravidelně aktualizují.  Například pokud sestavy trvá příliš dlouho k vygenerování během dne, při plánování sestavy ke spuštění v noci.  Azure Scheduler a prostředí PowerShell můžete použít pro automatické spouštění dotazů Hive.

Pokud cíl pro data se nejedná o databázi, můžete generovat soubor v příslušném formátu v rámci dotazu, například sdíleného svazku clusteru. Tento soubor lze poté importovat do Excelu nebo Power BI.

Pokud je potřeba provést několik operací s daty jako součást procesu ETL, zvažte, jak spravovat. Pokud operace jsou řízeny externí program, spíše než jako pracovní postup v rámci řešení, musíte se rozhodnout, zda některé operace lze provádět paralelně a zjistit, kdy každá úloha dokončí. Pomocí mechanismu pracovní postup například Oozie v systému Hadoop může být jednodušší než se pokoušet o orchestraci pořadí operací pomocí externích skriptů nebo vlastních aplikací. Další informace o Oozie najdete v tématu [pracovního postupu a úlohy Orchestrace](https://msdn.microsoft.com/library/dn749829.aspx).

## <a name="next-steps"></a>Další postup

* [ETL ve velkém měřítku](apache-hadoop-etl-at-scale.md)
* [Zprovoznění kanálu dat](../hdinsight-operationalize-data-pipeline.md)

<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md) -->
