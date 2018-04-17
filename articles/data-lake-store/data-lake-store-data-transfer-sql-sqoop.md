---
title: Kopírovat data mezi Data Lake Store a Azure SQL database pomocí Sqoop | Microsoft Docs
description: Použití nástroje Sqoop ke kopírování dat mezi Azure SQL Database a Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3f914b2a-83cc-4950-b3f7-69c921851683
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 2e68f74976c25a5c2db32867dabca147f5c30e80
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="copy-data-between-data-lake-store-and-azure-sql-database-using-sqoop"></a>Kopírovat data mezi Data Lake Store a Azure SQL database pomocí Sqoop
Další informace o použití Apache Sqoop k importu a exportu dat mezi Azure SQL Database a Data Lake Store.

## <a name="what-is-sqoop"></a>Co je Sqoop?
Velké objemy dat aplikací jsou přirozené volbou pro zpracování nestrukturovaných a částečně strukturovaných dat, jako jsou protokoly a soubory. Však také pravděpodobně potřeba zpracování strukturovaných dat, která je uložená v relačních databází.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) je nástroj sloužící k přenosu dat mezi relační databáze a úložiště velkých objemů dat, například Data Lake Store. Můžete ji použít k importu dat ze systému správy relačních databází (RDBMS), jako je například databáze SQL Azure do Data Lake Store. Můžete pak transformovat a analyzovat data pomocí úloh velkých objemů dat a pak exportovat data zpět do relační. V tomto kurzu použijete k Azure SQL Database jako relační databáze k importu a exportu z.

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto článku měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Store**. Pokyny o tom, jak vytvořit najdete v tématu [Začínáme s Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Azure HDInsight cluster** s přístupem k účtu Data Lake Store. V tématu [vytvoření clusteru HDInsight s Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Tento článek předpokládá, že máte cluster HDInsight Linux s přístupem k Data Lake Store.
* **Azure SQL Database**. Pokyny o tom, jak vytvořit najdete v tématu [vytvoření databáze Azure SQL](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Pomáhají vám při učení videa?
[Přehrát toto video](https://mix.office.com/watch/1butcdjxmu114) o tom, jak kopírovat data mezi objektů BLOB služby Azure Storage a Data Lake Store pomocí DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Vytvoření ukázkové tabulky v databázi SQL Azure
1. Abyste mohli začít vytvořte dva ukázkové tabulky v databázi SQL Azure. Použití [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) nebo Visual Studio k připojení k databázi SQL Azure a pak spusťte následující dotazy.

    **Vytvoření tabulky1**

        CREATE TABLE [dbo].[Table1](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO

    **Vytvoření tabulky2**

        CREATE TABLE [dbo].[Table2](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO
2. V **tabulky1**, přidejte ukázková data. Nechte **tabulky2** prázdný. Budeme importovat data z **tabulky1** do Data Lake Store. Potom bude exportu dat z Data Lake Store do **tabulky2**. Spusťte následující fragment kódu.

        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');


## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-store"></a>Použít Sqoop z clusteru služby HDInsight s přístupem do Data Lake Store
Cluster služby HDInsight je již k dispozici Sqoop balíčky. Pokud jste nakonfigurovali clusteru HDInsight pomocí Data Lake Store jako další úložiště, můžete použít Sqoop (bez změny konfigurace) k importu a exportu dat mezi relační databáze (v tomto příkladu Azure SQL Database) a účet Data Lake Store.

1. V tomto kurzu předpokládáme, že jste vytvořili Linux cluster, měli byste použít SSH připojit ke clusteru. V tématu [připojení ke clusteru HDInsight se systémem Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
2. Ověřte, zda máte přístup účtu Data Lake Store z clusteru. Spusťte následující příkaz z příkazového řádku SSH:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

    To by mělo poskytovat seznam souborů a složek v účtu Data Lake Store.

### <a name="import-data-from-azure-sql-database-into-data-lake-store"></a>Importovat data z databáze SQL Azure do Data Lake Store
1. Přejděte do adresáře, kde jsou Sqoop balíčky k dispozici. Obvykle to bude v `/usr/hdp/<version>/sqoop/bin`.
2. Umožňuje importovat data z **tabulky1** do účtu Data Lake Store. Použijte následující syntaxi:

        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Všimněte si, že **-název databáze sql-server** zástupný symbol představuje název serveru, kde je spuštěna databáze Azure SQL. **Název databáze SQL** zastupuje název skutečné databáze.

    Například:


        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Ověřte, že data byla převedena do účtu Data Lake Store. Spusťte následující příkaz:

        hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Měli byste vidět následující výstup.


        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Každý **část-m -*** odpovídá souboru na řádek, ve zdrojové tabulce **tabulky1**. Můžete zobrazit obsah části - m-* souborů a ověřit.


### <a name="export-data-from-data-lake-store-into-azure-sql-database"></a>Export dat z Data Lake Store do Azure SQL Database
1. Exportovat data z účtu Data Lake Store k prázdná tabulka **tabulky2**, ve službě Azure SQL Database. Použijte následující syntaxi.

        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Například:


        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Ověřte, zda data byl nahrán do tabulky databáze SQL. Použití [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) nebo Visual Studio k připojení k databázi SQL Azure a pak spusťte následující dotaz.

        SELECT * FROM TABLE2

    To by měl mít následující výstup.

         ID  FName   LName
        ------------------
        1    Neal    Kell
        2    Lila    Fulton
        3    Erna    Myers
        4    Annette    Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Faktory ovlivňující výkon při použití Sqoop

Ladění Sqoop úlohu kopírování dat do Data Lake Store výkonu, najdete v části [Sqoop výkonu dokumentu](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="see-also"></a>Další informace najdete v tématech
* [Kopírování dat z úložiště objektů BLOB Azure do Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [Zabezpečení dat ve službě Data Lake Store](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics se službou Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight se službou Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
