---
title: Používání Apache Hive jako ETL nástroj - Azure HDInsight | Microsoft Docs
description: Pomocí Apache Hive extrakce, transformace a načítání (ETL) dat v Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 06e06d87abd66c80deb2c8731f68bb8171da574b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31399582"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Použijte Apache Hive jako nástroj extrakce, transformace a načítání (ETL)

Obvykle potřebujete vyčistit a transformaci příchozích dat před načtením do cílového vhodný pro analýzu. Operace extrakce, transformace a načítání (ETL) se používají k Příprava dat a načtení do cílového data.  Hive v HDInsight můžete číst v nestrukturovaných dat, zpracování dat, podle potřeby a potom načíst data do relační datový sklad pro systémy podporu rozhodnutí. V tomto přístupu jsou data extrahována ze zdroje a uložená v škálovatelné úložiště, jako je například objektů BLOB služby Azure Storage nebo Azure Data Lake Store. Data se pak transformovat pomocí posloupnost dotazů Hive a nakonec se připraví v rámci Hive v rámci přípravy hromadné načtení do cílového úložiště dat.

## <a name="use-case-and-model-overview"></a>Použít případu a modelu – přehled

Následující obrázek ukazuje přehled pro případ použití a model pro automatizaci ETL. Vstupní data transformována ke generování příslušné výstup.  Během transformace můžete změnit data tvar, datový typ a jazyk i.  Procesy ETL může převést Imperial metrika, Změna časových pásem a zlepšit přesnost k správně zarovnané s existujícími daty v cílovém.  Procesy ETL můžete také kombinovat nová data s existujícími daty zachovat reporting aktuální nebo poskytnout další aspekty stávající data.  Aplikace, jako je vytváření sestav nástroje a služby, pak může využít tato data v požadovaném formátu.

![Apache Hive jako ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Hadoop se obvykle používá v procesech ETL, které importovat buď obrovské množství textové soubory (jako sdílené svazky clusteru) nebo menší, ale často Změna počtu textové soubory, nebo obojí.  Hive je skvělý nástroj, který slouží k přípravě data před načtením do cílového data.  Hive můžete vytvořit schéma přes sdílený svazek clusteru a použití jiného jazyka SQL jako ke generování MapReduce programy, které pracují s daty. 

Typické postup použití Hive k provedení ETL jsou následující:

1. Načtení dat do Azure Data Lake Store nebo úložiště objektů Blob Azure.
2. Vytvořte databázi úložiště metadat (s použitím Azure SQL Database) pro použití Hive v ukládání vaší schémat.
3. Vytvoření clusteru HDInsight a připojte úložiště dat.
4. Definujte schéma tak, aby použít během čtení dat v úložišti dat:

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

5. Transformace dat a načtení do cílové.  Použijte Hive během transformace a načítání několika způsoby:

    * Dotaz a připravit data pomocí Hive a uložte ho jako sdílený svazek clusteru Azure Data Lake Store nebo do úložiště objektů blob v Azure.  Pak můžete použijte nástroj jako integrační služby SSIS (SQL Server) k získání těchto sdílených svazků clusteru a načíst data do cílové relační databáze jako SQL Server.
    * Dotaz na data přímo z aplikace Excel nebo C# pomocí ovladače Hive ODBC.
    * Použití [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) číst připravené plochých souborů CSV a načíst je do cílové relační databáze.

## <a name="data-sources"></a>Zdroje dat

Zdroje dat jsou obvykle externích dat, který je možné přiřadit ke stávající data v úložišti dat, například:

* Data sociálních médií, soubory protokolů, senzory a aplikace, které generují datových souborů.
* Datové sady získat od poskytovatelů dat, jako je například počasí statistiky nebo dodavatele prodejní čísla.
* Datový proud zachycení, filtrovat a zpracované pomocí vhodného nástroje nebo framework.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Výstup cíle

Hive můžete výstupní data do různých cíle, včetně:

* Relační databáze, např. SQL Server nebo Azure SQL Database.
* Datový sklad, například Azure SQL Data Warehouse.
* Excel.
* Úložiště Azure table a objektů blob.
* Aplikace nebo služby, které vyžadují data ke zpracování na určité formáty, nebo jako soubory, které obsahují konkrétní typy struktury informace.
* Jako úložiště dokumentů JSON <a href="https://azure.microsoft.com/services/cosmos-db/">CosmosDB</a>.

## <a name="considerations"></a>Požadavky

ETL model se obvykle používá, když chcete:

* Načíst data datového proudu nebo velkých objemů částečně strukturovaných nebo nestrukturovaných dat z externích zdrojů do existující databáze nebo informace o systému.
* Vyčistit, transformace a ověřit data před načtením ji, případně s použitím více než jeden transformace předávat clusteru.
* Generování sestav a vizualizací, které se pravidelně aktualizují.  Například pokud sestavu trvá příliš dlouho k vygenerování během dne, můžete naplánovat spuštění v noci sestavy.  Azure Scheduler a prostředí PowerShell můžete použít pro automatické spouštění dotazů Hive.

Pokud je cílem pro data se nejedná o databázi, můžete vygenerovat soubor v příslušném formátu v dotazu, například do sdíleného svazku clusteru. Tento soubor můžete poté importovat do aplikace Excel nebo produktu Power BI.

Pokud potřebujete provést několik operací na základě dat v rámci procesu ETL, zvažte, jak spravovat. Pokud operace, které jsou řízené vnějšímu programu, místo jako pracovní postup v rámci řešení, musíte se rozhodnout, zda některé operace lze provádět souběžně a zjistit, kdy dokončení každé úlohy. Použití pracovního postupu mechanismus například Oozie v rámci Hadoop může být jednodušší než v pokusu o orchestraci posloupnost operací pomocí externích skriptů nebo vlastní programy. Další informace o Oozie najdete v tématu [pracovního postupu a úlohy orchestration](https://msdn.microsoft.com/library/dn749829.aspx).

## <a name="next-steps"></a>Další postup

* [ETL ve velkém měřítku](apache-hadoop-etl-at-scale.md)
* [Zprovoznit datovém kanálu](../hdinsight-operationalize-data-pipeline.md)
<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md) -->
