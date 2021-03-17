---
title: Kopírování dat mezi Data Lake Storage Gen1 a Azure SQL-Sqoop | Microsoft Docs
description: Kopírování dat mezi Azure SQL Database pomocí Sqoop a Azure Data Lake Storage Gen1
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: 9bb787138267fd8a9fab4dea233c1c828b457d67
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "92109183"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Kopírování dat mezi Data Lake Storage Gen1 a Azure SQL Database pomocí Sqoop

Naučte se používat Apache Sqoop k importu a exportu dat mezi Azure SQL Database a Azure Data Lake Storage Gen1.

## <a name="what-is-sqoop"></a>Co je Sqoop?

Aplikace pro velké objemy dat jsou přirozenou volbou pro zpracování nestrukturovaných a částečně strukturovaných dat, jako jsou protokoly a soubory. Je ale možné, že budete potřebovat zpracovat strukturovaná data uložená v relačních databázích.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) je nástroj určený k přenosu dat mezi relačními databázemi a úložištěm s velkými objemy dat, jako je například Data Lake Storage Gen1. Můžete ji použít k importu dat ze systému pro správu relačních databází (RDBMS), jako je například Azure SQL Database do Data Lake Storage Gen1. Data pak můžete transformovat a analyzovat pomocí úloh s velkými objemy dat a pak je exportovat zpátky do RDBMS. V tomto článku použijete pro import/export do relační databáze databázi v Azure SQL Database.

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte mít následující:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen1**. Pokyny k vytvoření účtu najdete v tématu Začínáme [s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md) .
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Storage Gen1. Další informace najdete v tématu [Vytvoření clusteru HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). V tomto článku se předpokládá, že máte cluster HDInsight Linux s přístupem Data Lake Storage Gen1.
* **Azure SQL Database**. Pokyny k vytvoření databáze v Azure SQL Database najdete v tématu [Vytvoření databáze v Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md)

## <a name="create-sample-tables-in-the-database"></a>Vytvoření ukázkových tabulek v databázi

1. Začněte vytvořením dvou vzorových tabulek v databázi. Pomocí [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) nebo sady Visual Studio se připojte k databázi a spusťte následující dotazy.

    **Vytvořit Tabulka1**

    ```tsql
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
    ```

    **Vytvořit Tabulka2**

    ```tsql
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
    ```

1. Spusťte následující příkaz, který přidá ukázková data do **Tabulka1**. Nechejte **Tabulka2** prázdné. Později naimportujete data ze **Tabulka1** do data Lake Storage Gen1. Potom budete exportovat data z Data Lake Storage Gen1 do **Tabulka2**.

    ```tsql
    INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');
    ```

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Použití Sqoop z clusteru HDInsight s přístupem k Data Lake Storage Gen1

Pro An HDInsight cluster již jsou k dispozici balíčky Sqoop. Pokud jste nakonfigurovali cluster HDInsight, aby používal Data Lake Storage Gen1 jako další úložiště, můžete k importu a exportu dat mezi relační databází, jako je například Azure SQL Database, použít Sqoop (bez jakýchkoli změn konfigurace) a účet Data Lake Storage Gen1.

1. Pro účely tohoto článku předpokládáme, že jste vytvořili cluster pro Linux, abyste se mohli připojit ke clusteru pomocí SSH. Viz [připojení ke clusteru HDInsight se systémem Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Ověřte, zda máte přístup k účtu Data Lake Storage Gen1 z clusteru. Z příkazového řádku SSH spusťte následující příkaz:

    ```console
    hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/
    ```

   Tento příkaz zobrazí seznam souborů nebo složek v účtu Data Lake Storage Gen1.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Import dat z Azure SQL Database do Data Lake Storage Gen1

1. Přejděte do adresáře, kde jsou k dispozici balíčky Sqoop. Obvykle toto umístění je `/usr/hdp/<version>/sqoop/bin` .

1. Importujte data ze služby **Tabulka1** do účtu Data Lake Storage Gen1. Použijte následující syntaxi:

    ```console
    sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

   Zástupný symbol **SQL-Database-Server-Name** představuje název serveru, na kterém je databáze spuštěná. zástupný symbol **SQL-Database-Name** představuje skutečný název databáze.

   Příklad:

    ```console
    sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

1. Ověřte, zda byla data přenesena na účet Data Lake Storage Gen1. Spusťte následující příkaz:

    ```console
    hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/
    ```

   Měli byste vidět následující výstup.

    ```console
    -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
    -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
    -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
    -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
    -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003
    ```

   Každý soubor **část-m-** _ odpovídá řádku ve zdrojové tabulce, _ *Tabulka1 * *. Můžete zobrazit obsah souboru části m* , který chcete ověřit.

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Export dat z Data Lake Storage Gen1 do Azure SQL Database

1. Exportujte data z účtu Data Lake Storage Gen1 do prázdné tabulky **Tabulka2** v Azure SQL Database. Použijte následující syntaxi.

    ```console
    sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

   Příklad:

    ```console
    sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

1. Ověřte, že se data nahrála do SQL Database tabulky. Pomocí [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) nebo sady Visual Studio se připojte k Azure SQL Database a pak spusťte následující dotaz.

    ```tsql
    SELECT * FROM TABLE2
    ```

   Tento příkaz by měl mít následující výstup.

    ```output
     ID  FName    LName
    -------------------
    1    Neal     Kell
    2    Lila     Fulton
    3    Erna     Myers
    4    Annette  Simpson
    ```

## <a name="performance-considerations-while-using-sqoop"></a>Požadavky na výkon při použití Sqoop

Informace o optimalizaci výkonu úlohy Sqoop pro kopírování dat do Data Lake Storage Gen1 najdete v [příspěvku na blogu o výkonu Sqoop](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs).

## <a name="next-steps"></a>Další kroky

* [Kopírování dat z objektů blob Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)