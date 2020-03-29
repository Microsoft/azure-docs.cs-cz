---
title: Kopírování dat mezi data mezi Data Lake Storage Gen1 a Azure SQL - Sqoop | Dokumenty společnosti Microsoft
description: Kopírování dat mezi Azure SQL Database a Azure Data Lake Storage Gen1 pomocí Sqoop
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: cf3893706afcb4c4cc5b90dd3d2431ecedc71d0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839063"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Kopírování dat mezi data mezi Data Lake Storage Gen1 a Azure SQL Database pomocí Sqoop

Zjistěte, jak pomocí Apache Sqoop importovat a exportovat data mezi Azure SQL Database a Azure Data Lake Storage Gen1.

## <a name="what-is-sqoop"></a>Co je Sqoop?

Aplikace pro velké objemy dat jsou přirozenou volbou pro zpracování nestrukturovaných a částečně strukturovaných dat, jako jsou protokoly a soubory. Však může mít také potřeba zpracovat strukturovaná data, která jsou uložena v relačních databázích.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) je nástroj určený k přenosu dat mezi relačními databázemi a úložištěm velkých objemů dat, jako je Například Data Lake Storage Gen1. Můžete ji použít k importu dat ze systému správy relační databáze (RDBMS), jako je Azure SQL Database do Data Lake Storage Gen1. Potom můžete transformovat a analyzovat data pomocí úloh velkých objemů dat a potom exportovat data zpět do RDBMS. V tomto článku použijete databázi Azure SQL jako relační databázi k importu nebo exportu.

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte mít následující:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen1**. Pokyny k vytvoření účtu najdete v tématu [Začínáme s Azure Data Lake Storage Gen1.](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Storage Gen1. Viz [Vytvoření clusteru HDInsight s gen1 úložiště datového jezera](data-lake-store-hdinsight-hadoop-use-portal.md). Tento článek předpokládá, že máte cluster HDInsight Linux s přístupem Data Lake Storage Gen1.
* **Azure SQL Database**. Pokyny k vytvoření najdete v [tématu Vytvoření databáze Azure SQL.](../sql-database/sql-database-get-started.md)

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Vytvoření ukázkových tabulek v databázi Azure SQL

1. Chcete-li začít, vytvořte dvě ukázkové tabulky v databázi Azure SQL. Pomocí [aplikace SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) nebo Visual Studio se připojte k databázi a spusťte následující dotazy.

    **Vytvořit tabulku1**

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

    **Vytvořit tabulku2**

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

1. Spuštěním následujícího příkazu přidejte do **tabulky1**ukázková data . Ponechejte **tabulku2** prázdnou. Později importujete data z **tabulky 1** do data Lake Storage Gen1. Potom budete exportovat data z Data Lake Storage Gen1 do **tabulky2**.

       INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Použití Sqoop z clusteru HDInsight s přístupem k úložišti Data Lake Storage Gen1

Cluster HDInsight již má balíčky Sqoop k dispozici. Pokud jste nakonfigurovali cluster HDInsight tak, aby používal data Lake Storage Gen1 jako další úložiště, můžete použít Sqoop (bez jakýchkoli změn konfigurace) k importu nebo exportu dat mezi relační databází, jako je Azure SQL Database, a účtem Data Lake Storage Gen1.

1. Pro tento článek předpokládáme, že jste vytvořili cluster Linux, takže byste měli použít SSH pro připojení ke clusteru. Viz [Připojení k linuxovému clusteru HDInsight](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Ověřte, zda máte přístup k účtu Data Lake Storage Gen1 z clusteru. Z výzvy SSH spusťte následující příkaz:

       hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

   Tento příkaz poskytuje seznam souborů nebo složek v účtu Data Lake Storage Gen1.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Import dat z Azure SQL Database do modulu Gen1 úložiště datového jezera

1. Přejděte do adresáře, kde jsou k dispozici balíčky Sqoop. Toto umístění je `/usr/hdp/<version>/sqoop/bin`obvykle .

1. Importujte data z **tabulky 1** do účtu Data Lake Storage Gen1. Použijte následující syntaxi:

       sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

   Zástupný symbol **název SQL-database-server** představuje název serveru, na kterém je spuštěna databáze Azure SQL. zástupný symbol **název sql-database** představuje skutečný název databáze.

   Například:

       sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Ověřte, zda byla data přenesena do účtu Data Lake Storage Gen1. Spusťte následující příkaz:

       hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/

   Měl by se zobrazit následující výstup.

       -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
       -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
       -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
       -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
       -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

   Každý soubor **part-m-*** odpovídá řádku ve zdrojové tabulce **Table1**. Můžete zobrazit obsah souborů part-m-* k ověření.

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Export dat z data Lake Storage Gen1 do Azure SQL Database

1. Exportujte data z účtu Data Lake Storage Gen1 do prázdné tabulky **Tabulka2**v azure sql databázi. Použijte následující syntaxi.

       sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

   Například:

       sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Ověřte, zda byla data odeslána do tabulky Databáze SQL. Pomocí [sql server management studio](../sql-database/sql-database-connect-query-ssms.md) nebo Visual Studio se připojit k azure sql database a pak spustit následující dotaz.

       SELECT * FROM TABLE2

   Tento příkaz by měl mít následující výstup.

        ID  FName    LName
       -------------------
       1    Neal     Kell
       2    Lila     Fulton
       3    Erna     Myers
       4    Annette  Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Aspekty výkonu při používání sqoop

Informace o optimalizaci výkonu úlohy Sqoop pro kopírování dat do data Lake Storage Gen1 najdete v [příspěvku na blogu výkonu Sqoop](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="next-steps"></a>Další kroky

* [Kopírování dat z objektů BLOB úložiště Azure do data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s datelake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s úložištěm datových jezer Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
