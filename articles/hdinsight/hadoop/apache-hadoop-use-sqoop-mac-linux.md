---
title: Apache Sqoop se systémem Hadoop - Azure HDInsight | Microsoft Docs
description: Další informace o použití Apache Sqoop k importu a exportu mezi systémem Hadoop v HDInsight a Azure SQL Database.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
author: Blackmist
tags: azure-portal
keywords: hadoop sqoop, sqoop
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: larryfr
ms.openlocfilehash: 38b1c57b9ac666bc908df69b29f72fbd5aea0495
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Použití Apache Sqoop k importu a exportu dat mezi systémem Hadoop v HDInsight a databáze SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Další informace o použití Apache Sqoop k importu a exportu mezi clusteru Hadoop v prostředí Azure HDInsight a databáze Azure SQL Database nebo Microsoft SQL Server. Kroky v tomto dokumentu pomocí `sqoop` příkaz přímo z headnode clusteru Hadoop. Použití SSH k připojení k hlavnímu uzlu a spusťte příkazy v tomto dokumentu.

> [!IMPORTANT]
> Kroky v tomto dokumentu fungovat jenom s clustery HDInsight, které používají systém Linux. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!WARNING]
> Kroky v tomto dokumentu předpokládají, že jste již vytvořili databázi SQL Azure s názvem `sqooptest`.
>
> Tento dokument obsahuje příkazů T-SQL, které se používají k vytvoření a dotazování tabulky v databázi SQL. Existuje mnoho klientů můžete použít tyto příkazy se databáze SQL. Doporučujeme, abyste na následující klienty:
>
> * [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)
> * [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)
> * [Sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility) nástroj

## <a name="create-the-table-in-sql-database"></a>Vytvořit v tabulce databáze SQL

> [!IMPORTANT]
> Pokud používáte HDInsight cluster a databázi SQL vytvořit v [vytvoření clusteru a databáze SQL](hdinsight-use-sqoop.md), přeskočte postup v této části. Databáze a tabulky, které byly vytvořeny jako součást kroky v [vytvoření clusteru a databáze SQL](hdinsight-use-sqoop.md) dokumentu.

Použít pro připojení k SQL klienta `sqooptest` databáze v databázi SQL. Pak použijte následující T-SQL a vytvořte tabulku s názvem `mobiledata`:

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

## <a name="sqoop-export"></a>Sqoop exportu

1. Použití SSH se připojit ke clusteru HDInsight. Například následující příkaz připojí k primární headnode clusteru s názvem `mycluster`:

    ```bash
    ssh mycluster-ssh.azurehdinsight.net
    ```

    Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Pokud chcete ověřit, že Sqoop vidí vaší databázi SQL, použijte následující příkaz:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    Po zobrazení výzvy zadejte heslo pro přihlášení k databázi SQL.

    Tento příkaz vrátí seznam databází, včetně **sqooptest** použít starší databázi.

3. Export dat z podregistru **hivesampletable** a **mobiledata** tabulky v databázi SQL, použijte následující příkaz:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Pokud chcete ověřit, že se data vyexportovala, použijte tento dotaz z vašeho klienta SQL zobrazíte exportovaná data:

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata;
    ```

    Tento příkaz vypíše 50 řádky, které byly importovány do tabulky.

## <a name="sqoop-import"></a>Sqoop import

1. Použijte následující příkaz pro import dat z **mobiledata** do tabulky v databázi SQL, **wasb: / / / kurzy/usesqoop/importeddata** v HDInsight:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    Pole v datech jsou oddělených tabulátorem a řádky se ukončila příkazem znak nového řádku.

    > [!IMPORTANT]
    > `wasb:///` Cesta funguje s clustery, které používají Azure Storage jako výchozí úložiště clusteru. Pro clustery, které používají Azure Data Lake Store, použijte `adl:///` místo.

2. Po dokončení importu použijte následující příkaz, který seznamu se data v adresáři nové:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Pomocí SQL serveru

Můžete taky Sqoop k importu a exportu dat z SQL serveru. Rozdíly mezi použitím SQL Database a SQL Server jsou:

* HDInsight a SQL Server musí být ve stejné virtuální síti Azure.

    Příklad, naleznete v části [HDInsight připojit k místní síti](./../connect-on-premises-network.md) dokumentu.

    Další informace o používání HDInsight s virtuální síť Azure, najdete v článku [rozšíření prostředí HDInsight pomocí Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md) dokumentu. Další informace o Azure Virtual Network, najdete v článku [Přehled virtuálních sítí](../../virtual-network/virtual-networks-overview.md) dokumentu.

* SQL Server musí nakonfigurovat pro povolení ověřování SQL. Další informace najdete v tématu [volba režimu ověřování](https://msdn.microsoft.com/ms144284.aspx) dokumentu.

* Budete muset nakonfigurovat SQL Server tak, aby přijímal vzdálená připojení. Další informace najdete v tématu [řešení potíží s připojením k databázovému stroji systému SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) dokumentu.

* Pomocí následujících příkazů Transact-SQL můžete vytvořit **mobiledata** tabulky:

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

* Při připojování k systému SQL Server z prostředí HDInsight, budete muset použít IP adresu serveru SQL Server. Příklad:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Omezení

* Hromadné export - s Linuxovým systémem HDInsight, Sqoop konektor umožňuje exportovat data do systému Microsoft SQL Server nebo Azure SQL Database nepodporuje hromadné vložení.

* Dávkování - s HDInsight se systémem Linux, při použití `-batch` přepnout při vložení, Sqoop umožňuje více vloží místo dávkování operace insert.

## <a name="next-steps"></a>Další postup

Nyní jste se naučili postup použití nástroje Sqoop. Další informace naleznete v tématu:

* [Použijte Oozie s HDInsight](../hdinsight-use-oozie.md): použití Sqoop akce v pracovním postupu Oozie.
* [Analýza dat zpoždění letu pomocí HDInsight](../hdinsight-analyze-flight-delay-data.md): použití Hive k analýze letu zpoždění dat a pak pomocí Sqoop exportovat data do Azure SQL database.
* [Nahrání dat do HDInsight](../hdinsight-upload-data.md): Najít další metody pro odesílání dat do HDInsight nebo Azure Blob storage.

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
