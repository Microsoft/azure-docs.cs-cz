---
title: Apache Sqoop se systémem Hadoop – Azure HDInsight
description: Další informace o použití Apache Sqoop k importu a exportu mezi platformou Hadoop v HDInsight a Azure SQL Database.
keywords: hadoop, sqoop, sqoop
services: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: e9ee4ceb51b2de58010f3e6cf7feba9df64b9bad
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43050165"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Použití Apache Sqoop k importu a exportu dat mezi platformou Hadoop v HDInsight a SQL Database

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Další informace o použití Apache Sqoop k importu a exportu mezi cluster Hadoop v Azure HDInsight a databází Azure SQL Database nebo Microsoft SQL Server. Kroky v tomto dokumentu pomocí `sqoop` přímo z hlavního uzlu clusteru Hadoop. Použití SSH pro připojení k hlavnímu uzlu a spusťte příkazy v tomto dokumentu.

> [!IMPORTANT]
> Kroky v tomto dokumentu fungovat jenom s clustery HDInsight, které používají systém Linux. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!WARNING]
> Kroky v tomto dokumentu předpokládají, že jste již vytvořili databázi SQL Azure s názvem `sqooptest`.
>
> Tento dokument obsahuje příkazy jazyka T-SQL, které slouží k vytvoření a dotazování na tabulku v databázi SQL. Existuje mnoho klientů, které můžete použít tyto příkazy s využitím SQL Database. Doporučujeme následující klienty:
>
> * [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)
> * [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)
> * [Sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility) nástroje

## <a name="create-the-table-in-sql-database"></a>Vytvoření tabulky ve službě SQL Database

> [!IMPORTANT]
> Pokud používáte HDInsight cluster a databází SQL v [vytvoření clusteru a SQL database](hdinsight-use-sqoop.md), přeskočte kroky v této části. Databáze a tabulky byly vytvořeny jako součást kroky [vytvoření clusteru a SQL database](hdinsight-use-sqoop.md) dokumentu.

Použít pro připojení k SQL client `sqooptest` databáze ve službě SQL Database. Potom použijte následující příkaz T-SQL vytvořte tabulku s názvem `mobiledata`:

```sql
CREATE TABLE [dbo].[mobiledata](
[clientid] [nvarchar](50),
[querytime] [nvarchar](50),
[market] [nvarchar](50),
[deviceplatform] [nvarchar](50),
[devicemake] [nvarchar](50),
[devicemodel] [nvarchar](50),
[state] [nvarchar](50),
[country] [nvarchar](50),
[querydwelltime] [float],
[sessionid] [bigint],
[sessionpagevieworder] [bigint])
GO
CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
GO
```

## <a name="sqoop-export"></a>Export Sqoopu

1. Pomocí SSH se připojte ke clusteru HDInsight. Například následující příkaz se připojí k primárnímu hlavnímu uzlu clusteru s názvem `mycluster`:

    ```bash
    ssh mycluster-ssh.azurehdinsight.net
    ```

    Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Pokud chcete ověřit, že Sqoop uvidí vaši službu SQL Database, použijte následující příkaz:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    Po zobrazení výzvy zadejte heslo pro přihlášení k databázi SQL.

    Tento příkaz vrátí seznam databází, včetně **sqooptest** databázi použili dříve.

3. Export dat z Hive **hivesampletable** tabulky **mobiledata** tabulky ve službě SQL Database, použijte následující příkaz:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Pokud chcete ověřit, že se data vyexportovala, použijte následující dotaz z klienta SQL exportovaná data zobrazíte:

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata;
    ```

    Tento příkaz vypíše 50 řádky, které byly naimportované do tabulky.

## <a name="sqoop-import"></a>Sqoop import

1. Použijte následující příkaz pro import dat z **mobiledata** do tabulky ve službě SQL Database **wasb: / / / kurzy/usesqoop/importeddata** adresáře v HDInsight:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    Pole v datech jsou oddělené tabulátorem a řádky jsou ukončeny znak nového řádku.

    > [!IMPORTANT]
    > `wasb:///` Cesta pracuje s clustery, které používají jako výchozí úložiště clusteru služby Azure Storage. Pro clustery, které používají Azure Data Lake Store, použijte `adl:///` místo.

2. Po dokončení importu použijte následující příkaz, který seznam dat v novém adresáři:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Použití SQL serveru

Můžete také použít Sqoop k importu a exportu dat z SQL serveru. Rozdíly mezi službu SQL Database a SQL Server jsou:

* HDInsight a SQL Server musí být ve stejné virtuální síti Azure.

    Příklad najdete v tématu [HDInsight připojit k místní síti](./../connect-on-premises-network.md) dokumentu.

    Další informace o používání HDInsight se službou Azure Virtual Network, najdete v článku [rozšířit HDInsight s Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md) dokumentu. Další informace o Azure Virtual Network, najdete v článku [Přehled služby Virtual Network](../../virtual-network/virtual-networks-overview.md) dokumentu.

* SQL Server musí být nakonfigurované pro umožnění ověřování SQL. Další informace najdete v tématu [volba režimu ověřování](https://msdn.microsoft.com/ms144284.aspx) dokumentu.

* Bude pravděpodobně nutné nakonfigurovat SQL Server tak, aby přijímal vzdálená připojení. Další informace najdete v tématu [postupy řešení potíží s připojením k databázovému stroji SQL serveru](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) dokumentu.

* Pomocí následujících příkazů jazyka Transact-SQL k vytvoření **mobiledata** tabulky:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    ```

* Při připojování k serveru SQL Server v HDInsight, bude pravděpodobně nutné použít IP adresu serveru SQL Server. Příklad:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Omezení

* Hromadné export - s Linuxovým systémem HDInsight, Sqoop konektor používaný k exportu dat Microsoft SQL Server nebo Azure SQL Database nepodporuje operace hromadného vložení.

* Dávkování – s Linuxovým systémem HDInsight při použití `-batch` přepnout při provádění operace vložení, Sqoop díky více vloží místo dávkování operace vložení.

## <a name="next-steps"></a>Další postup

Nyní jste se naučili, jak použít Sqoop. Další informace naleznete v tématu:

* [Použití Oozie s HDInsight](../hdinsight-use-oozie.md): použití Sqoopu akce v pracovním postupu Oozie.
* [Analýza zpoždění letů pomocí HDInsight](../hdinsight-analyze-flight-delay-data.md): použití Hive k analýze letu zpoždění dat a potom použít Sqoop k exportování dat do Azure SQL database.
* [Nahrání dat do HDInsight](../hdinsight-upload-data.md): Najít další metody pro nahrávání dat do HDInsight nebo Azure Blob storage.

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
