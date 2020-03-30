---
title: Používání Apache Hive jako nástroje ETL – Azure HDInsight
description: Apache Hive slouží k extrahování, transformaci a načtení dat (ETL) v Azure HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: be331f36a6305b05ce83a2b2d5fdfb73a154ce3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623122"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Použití Apache Hive jako nástroje pro extrakci, transformaci a načtení (ETL)

Obvykle je potřeba vyčistit a transformovat příchozí data před načtením do cíle vhodného pro analýzu. Extrahovat, transformovat a načíst (ETL) operace se používají k přípravě dat a načíst do cíle dat.  Apache Hive na HDInsight umějí číst v nestrukturovaných datech, zpracovávat data podle potřeby a pak načíst data do relačního datového skladu pro systémy podpory rozhodování. V tomto přístupu se data extrahují ze zdroje a ukládají se ve škálovatelném úložišti, jako jsou objekty BLOB azure storage nebo Azure Data Lake Storage. Data je pak transformována pomocí posloupnosti dotazů Hive a je nakonec připravena v rámci Hive v rámci přípravy na hromadné načítání do cílového úložiště dat.

## <a name="use-case-and-model-overview"></a>Přehled případu použití a modelu

Následující obrázek znázorňuje přehled případu použití a modelu pro automatizaci ETL. Vstupní data jsou transformována tak, aby generovala příslušný výstup.  Během této transformace mohou data měnit tvar, datový typ a dokonce i jazyk.  ETL procesy můžete převést Imperial na metrické, změnit časová pásma a zlepšit přesnost správně zarovnat s existujícími daty v cíli.  Procesy ETL mohou také kombinovat nová data se stávajícími daty, aby bylo možné vykazovat aktuální sestavy nebo poskytnout další přehled o existujících datech.  Aplikace, jako jsou nástroje pro vytváření sestav a služby, pak mohou tato data využívat v požadovaném formátu.

![Apache Hive jako architektura ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Hadoop se obvykle používá v procesech ETL, které importují buď velký počet textových souborů (jako jsou soubory CSV), nebo menší, ale často se měnící počet textových souborů, nebo obojí.  Hive je skvělý nástroj pro přípravu dat před načtením do cíle dat.  Hive umožňuje vytvořit schéma přes CSV a používat jazyk podobný SQL ke generování MapReduce programy, které interagují s daty.

Typické kroky k provedení ETL pomocí Hive jsou následující:

1. Načtěte data do Azure Data Lake Storage nebo Azure Blob Storage.
2. Vytvořte databázi úložiště metadat (pomocí Azure SQL Database) pro použití Hive při ukládání schémat.
3. Vytvořte cluster HDInsight a připojte úložiště dat.
4. Definujte schéma, které se má použít v době čtení přes data v úložišti dat:

    ```
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

5. Transformujte data a načtěte je do cíle.  Existuje několik způsobů, jak použít Hive během transformace a načítání:

    * Zadejte dotaz a připravte data pomocí Hive a uložte je jako CSV v Azure Data Lake Storage nebo Azure blob storage.  Pak použijte nástroj, jako je SQL Server Integration Services (SSIS) získat tyto CSV a načíst data do cílové relační databáze, jako je například SQL Server.
    * Dotaz na data přímo z aplikace Excel nebo C# pomocí ovladače HIVE ODBC.
    * Pomocí [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) si můžete přečíst připravené ploché soubory CSV a načíst je do cílové relační databáze.

## <a name="data-sources"></a>Zdroje dat

Zdroje dat jsou obvykle externí data, která lze spárovat s existujícími daty v úložišti dat, například:

* Data sociálních médií, soubory protokolu, senzory a aplikace, které generují datové soubory.
* Datové sady získané od poskytovatelů dat, jako jsou statistiky počasí nebo prodejní čísla dodavatelů.
* Streamování dat zachycených, filtrovaných a zpracovávaných prostřednictvím vhodného nástroje nebo architektury.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Výstupní cíle

Hive můžete použít k výstupu dat pro různé cíle, včetně:

* Relační databáze, jako je například SQL Server nebo Azure SQL Database.
* Datový sklad, jako je například Azure SQL Data Warehouse.
* Aplikace excel.
* Azure tabulka a úložiště objektů blob.
* Aplikace nebo služby, které vyžadují zpracování dat do určitých formátů nebo jako soubory obsahující určité typy informační struktury.
* Úložiště dokumentů JSON, jako je [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

## <a name="considerations"></a>Požadavky

Model ETL se obvykle používá, když chcete:

* Načtěte data datového proudu nebo velké objemy částečně strukturovaných nebo nestrukturovaných dat z externích zdrojů do existující databáze nebo informačního systému.
* Vyčistěte, transformujte a ověřte data před načtením, například pomocí více než jedné transformace projít clusteru.
* Vytvářejte sestavy a vizualizace, které jsou pravidelně aktualizovány. Pokud například generování sestavy během dne trvá příliš dlouho, můžete naplánovat spuštění sestavy v noci. Chcete-li automaticky spustit dotaz Hive, můžete použít [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) a PowerShell.

Pokud cíl pro data není databáze, můžete vygenerovat soubor v příslušném formátu v rámci dotazu, například CSV. Tento soubor pak můžete importovat do Excelu nebo Power BI.

Pokud potřebujete provést několik operací s daty jako součást procesu ETL, zvažte, jak je spravujete. Pokud jsou operace řízeny externím programem, nikoli jako pracovní postup v rámci řešení, musíte se rozhodnout, zda některé operace mohou být provedeny paralelně, a zjistit, kdy je každá úloha dokončena. Použití mechanismu pracovního postupu, jako je Oozie v Hadoopu, může být jednodušší než se snažit zorganizovat sekvenci operací pomocí externích skriptů nebo vlastních programů. Další informace o Oozie, naleznete [v tématu Workflow a job orchestraation](https://msdn.microsoft.com/library/dn749829.aspx).

## <a name="next-steps"></a>Další kroky

* [ETL ve velkém měřítku](apache-hadoop-etl-at-scale.md)
* [Zprovoznění datového kanálu](../hdinsight-operationalize-data-pipeline.md)

<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md) -->
