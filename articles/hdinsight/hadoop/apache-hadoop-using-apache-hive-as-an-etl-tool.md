---
title: Použití Apache Hive jako nástroje ETL – Azure HDInsight
description: Použijte Apache Hive k extrakci, transformaci a načítání dat (ETL) ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: 9b9dcd683915c17ae6909ebb88927d275e9a5896
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505280"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Použití Apache Hive jako nástroje pro extrakci, transformaci a načítání (ETL)

Obvykle je nutné vyčistit a transformovat příchozí data před jejich načtením do cíle vhodného pro analýzu. Operace extrakce, transformace a načítání (ETL) slouží k přípravě dat a jejich načtení do cíle dat.  Apache Hive ve službě HDInsight může číst v nestrukturovaných datech, zpracovávat data podle potřeby a následně data načíst do relačního datového skladu pro systémy podpory rozhodování. V tomto přístupu se data extrahují ze zdroje. Pak se uloží do přizpůsobivého úložiště, například Azure Storage objektů BLOB nebo Azure Data Lake Storage. Data se pak transformují pomocí posloupnosti dotazů na podregistr. Pak se v rámci podregistru připravil pro hromadné načítání do cílového úložiště dat.

## <a name="use-case-and-model-overview"></a>Přehled případu a modelu použití

Následující obrázek ukazuje přehled případu použití a modelu pro automatizaci ETL. Vstupní data jsou transformovaná tak, aby vygenerovala příslušný výstup.  Během této transformace se změní tvar, datový typ a dokonce i jazyk.  Procesy ETL můžete převést na možnost britské na metriky, změnit časová pásma a zlepšit přesnost správného zarovnání se stávajícími daty v cíli. Procesy ETL mohou také kombinovat nová data se stávajícími daty, aby bylo možné sestavy udržovat v aktualizovaném stavu, nebo poskytnout další přehled o stávajících datech. Aplikace, jako jsou nástroje pro vytváření sestav a služby, pak mohou tato data využívat v požadovaném formátu.

![Apache Hive jako architektura ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Hadoop se obvykle používá v procesech ETL, které importují buď obrovské množství textových souborů (například CSV). Nebo menší, ale často měnící se počet textových souborů nebo obojí.  Podregistr je skvělý nástroj, který slouží k přípravě dat před jejich načtením do cíle dat.  Podregistr umožňuje vytvořit schéma přes sdílený svazek clusteru a použít jazyk podobný SQL ke generování MapReduce programů, které pracují s daty.

Mezi obvyklé kroky pro použití podregistru do ETL patří následující:

1. Načte data do Azure Data Lake Storage nebo Azure Blob Storage.
2. Vytvořte databázi úložiště metadat (pomocí Azure SQL Database), kterou použijete v podregistru při ukládání schémat.
3. Vytvořte cluster HDInsight a připojte úložiště dat.
4. Zadejte schéma, které se má použít pro dobu čtení dat v úložišti dat:

    ```hql
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs

    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT,
        system BIGINT,
        systemage BIGINT,
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    STORED AS TEXTFILE LOCATION 'wasbs://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Transformujte data a načtěte je do cíle.  Existuje několik způsobů, jak použít podregistr při transformaci a načítání:

    * Dotazování a Příprava dat pomocí podregistru a jeho uložení ve formátu CSV do Azure Data Lake Storage nebo úložiště objektů BLOB v Azure.  Pak pomocí nástroje jako služba SSIS (SQL Server Integration Services) (SSIS) Získejte tyto CSV a načtěte data do cílové relační databáze, jako je například SQL Server.
    * Dotazujte data přímo z Excelu nebo C# pomocí ovladače podregistr ODBC.
    * Pomocí [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) si můžete přečíst připravené soubory CSV a načíst je do cílové relační databáze.

## <a name="data-sources"></a>Zdroje dat

Zdroje dat jsou obvykle externí data, která se dají spárovat se stávajícími daty v úložišti dat, například:

* Data sociálních médií, soubory protokolů, senzory a aplikace, které generují datové soubory.
* Datové sady získané od zprostředkovatelů dat, jako jsou třeba statistiky počasí nebo prodejní čísla dodavatelů.
* Streamovaná data zachycená, filtrovaná a zpracovaná prostřednictvím vhodného nástroje nebo architektury.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Cíle výstupu

Můžete použít podregistr pro výstup dat do různých druhů cílů, včetně:

* Relační databáze, například SQL Server nebo Azure SQL Database.
* Datový sklad, jako je Azure synapse Analytics.
* Excel.
* Azure Table a BLOB Storage.
* Aplikace nebo služby, které vyžadují zpracování dat do konkrétních formátů nebo jako soubory, které obsahují určité typy informačních struktur.
* Úložiště dokumentů JSON, jako je Azure Cosmos DB.

## <a name="considerations"></a>Požadavky

Model ETL se obvykle používá, pokud chcete:

`*` Načtěte data streamu nebo velké objemy částečně strukturovaných nebo nestrukturovaných dat z externích zdrojů do existující databáze nebo informačního systému.
`*` Vyčistit, transformovat a ověřit data před jejich načtením, například pomocí více než jedné transformace projde clusterem.
`*` Vygenerujte sestavy a vizualizace, které se pravidelně aktualizují. Například pokud sestava trvá příliš dlouho pro vygenerování během dne, můžete naplánovat, aby se sestava spouštěla v noci. Pokud chcete automaticky spustit dotaz na podregistr, můžete použít [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) a PowerShell.

Pokud cíl pro data není databáze, můžete vygenerovat soubor v příslušném formátu v rámci dotazu, například CSV. Tento soubor je pak možné importovat do aplikace Excel nebo Power BI.

Pokud v rámci procesu ETL potřebujete spustit několik operací s daty, zvažte, jak je spravovat. V případě operací řízených externím programem, nikoli jako pracovní postup v rámci řešení, se rozhodněte, zda lze některé operace provádět paralelně. A ke zjištění, kdy se Každá úloha dokončí. Použití mechanismu pracovního postupu, jako je Oozie v rámci Hadoop, může být jednodušší než pokus o orchestraci sekvence operací pomocí externích skriptů nebo vlastních programů.

## <a name="next-steps"></a>Další kroky

* [ETL ve velkém měřítku](apache-hadoop-etl-at-scale.md)
* [`Operationalize a data pipeline`](../hdinsight-operationalize-data-pipeline.md)
