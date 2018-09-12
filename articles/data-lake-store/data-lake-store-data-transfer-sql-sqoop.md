---
title: Kopírovat data mezi Azure Data Lake Storage Gen1 a Azure SQL database pomocí Sqoop | Dokumentace Microsoftu
description: Použití Sqoopu ke kopírování dat mezi Azure SQL Database a Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3f914b2a-83cc-4950-b3f7-69c921851683
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 958171a8d1091254588aef250406b968009eb968
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391738"
---
# <a name="copy-data-between-azure-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Kopírovat data mezi Azure Data Lake Storage Gen1 a Azure SQL database pomocí Sqoop
Další informace o použití Apache Sqoop k importu a exportu dat mezi Azure SQL Database a Azure Data Lake Storage Gen1.

## <a name="what-is-sqoop"></a>Co je Sqoop?
Aplikace velkých objemů dat jsou přirozenou volbou pro zpracování nestrukturovaných a částečně strukturovaných dat, jako jsou protokoly a soubory. Ale mohou také existovat věcí a potřebovali si zpracování strukturovaných dat, která je uložená v relačních databázích.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) je nástroj určený pro přenos dat mezi relačních databází a úložiště velké objemy dat, jako je třeba Gen1 úložiště Data Lake. Můžete ho použít k importu dat ze systému správy relačních databází (RDBMS), jako je Azure SQL Database do Data Lake Storage Gen1. Pak můžete transformovat a analyzovat data s využitím úloh s velkými objemy dat a poté exportovat data zpět do relační databázový systém. V tomto kurzu použijete Azure SQL Database jako relační databáze pro import/export z.

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto článku měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen1**. Pokyny k jeho vytvoření najdete v tématu [Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Azure HDInsight cluster** s přístupem k účtu Data Lake Storage Gen1. Zobrazit [vytvoření clusteru HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Tento článek předpokládá, že máte cluster Linuxu HDInsight s Data Lake Storage Gen1 přístup.
* **Azure SQL Database**. Pokyny k jeho vytvoření najdete v tématu [vytvořit databázi Azure SQL](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Pomáhají vám při učení videa?
[V tomto videu](https://mix.office.com/watch/1butcdjxmu114) o tom, jak kopírovat data mezi objekty BLOB Azure Storage a Data Lake Storage Gen1 pomocí DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Vytvoření ukázkové tabulky ve službě Azure SQL Database
1. Začněte tím vytvořte dva ukázkové tabulky ve službě Azure SQL Database. Použití [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) nebo Visual Studio pro připojení k databázi SQL Azure a pak spusťte následující dotazy.

    **Vytvoření Tabulka1**

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

    **Vytvoření tabulka2**

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
2. V **Tabulka1**, přidat nějaká ukázková data. Ponechte **tabulka2** prázdný. Importujeme data z **Tabulka1** do Data Lake Storage Gen1. Potom jsme se exportovat data z Data Lake Storage Gen1 do **tabulka2**. Spusťte následující fragment kódu.

        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');


## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Pomocí Sqoop z clusteru služby HDInsight s přístupem Data Lake Storage Gen1
HDInsight cluster už má k dispozici Sqoop balíčky. Pokud jste nakonfigurovali clusteru HDInsight pro použití služby Data Lake Storage Gen1 jako další úložiště, můžete použít Sqoop (bez změny konfigurace) k importu a exportu dat mezi relační databáze (v tomto příkladu Azure SQL Database) a Data Lake Gen1 účtu úložiště.

1. Pro účely tohoto kurzu předpokládáme, že jste vytvořili cluster s Linuxem, proto byste měli použít SSH pro připojení ke clusteru. Zobrazit [připojení ke clusteru HDInsight se systémem Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
2. Ověřte, jestli je přístup k účtu Data Lake Storage Gen1, z clusteru. Spusťte následující příkaz z příkazového řádku SSH:

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

    To by měla poskytnout seznam souborů a složek v účtu Data Lake Storage Gen1.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Import dat ze služby Azure SQL Database do Data Lake Storage Gen1
1. Přejděte do adresáře, kde jsou k dispozici balíčky Sqoop. Obvykle to bude na `/usr/hdp/<version>/sqoop/bin`.
2. Umožňuje importovat data z **Tabulka1** do účtu Data Lake Storage Gen1. Použijte následující syntaxi:

        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Všimněte si, že **sql-database-server-name** zastupuje název serveru se spuštěným systémem Azure SQL database. **Název databáze SQL** zastupuje název skutečné databázi.

    Například:


        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Ověřte, že data byla převedena do účtu Data Lake Storage Gen1. Spusťte následující příkaz:

        hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Zobrazí se následující výstup.


        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Každý **část-m -*** odpovídá souboru na řádek v tabulce zdroje **Tabulka1**. Můžete zobrazit obsah části - m-* soubory k ověření.


### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Export dat z Data Lake Storage Gen1 do Azure SQL Database
1. Exportovat data z účtu Data Lake Storage Gen1 prázdné tabulky **tabulka2**, ve službě Azure SQL Database. Použijte následující syntaxi.

        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Například:


        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Ověřte, že data byla nahrána do tabulky databáze SQL. Použití [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) nebo Visual Studio pro připojení k Azure SQL Database a potom spusťte následující dotaz.

        SELECT * FROM TABLE2

    To by měl mít následující výstup.

         ID  FName   LName
        ------------------
        1    Neal    Kell
        2    Lila    Fulton
        3    Erna    Myers
        4    Annette    Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Důležité informace o výkonu pomocí Sqoop

Ladění úlohy Sqoop ke zkopírování dat do Data Lake Storage Gen1 výkonu, naleznete v tématu [Sqoop výkonu dokumentu](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="see-also"></a>Další informace najdete v tématech
* [Kopírování dat z Azure Storage BLOB do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
