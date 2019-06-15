---
title: Apache Sqoop s Apache Hadoopu – Azure HDInsight
description: Další informace o použití Apache Sqoop k importu a exportu mezi Apache Hadoop v HDInsight a Azure SQL Database.
keywords: hadoop, sqoop, sqoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 6dcb6853daf34fede590011d165c0ba9001cbac6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721632"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Použití Apache Sqoop k importu a exportu dat mezi Apache Hadoop v HDInsight a databází SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Další informace o použití Apache Sqoop k importu a exportu mezi cluster Apache Hadoop v Azure HDInsight a databází Azure SQL Database nebo Microsoft SQL Server. Kroky v tomto dokumentu pomocí `sqoop` přímo z hlavního uzlu clusteru Hadoop. Použití SSH pro připojení k hlavnímu uzlu a spusťte příkazy v tomto dokumentu. V tomto článku je pokračování [použití Apache Sqoop se systémem Hadoop v HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Požadavky

* Dokončení [nastavit testovací prostředí](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) z [použití Apache Sqoop se systémem Hadoop v HDInsight](./hdinsight-use-sqoop.md).

* Klient k dotazování Azure SQL database. Zvažte použití [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) nebo [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* Klient SSH. Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sqoop-export"></a>Export Sqoopu

Z Hive k systému SQL Server.

1. Pomocí SSH se připojte ke clusteru HDInsight. Nahraďte `CLUSTERNAME` s názvem vašeho clusteru, zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Nahraďte `MYSQLSERVER` s názvem serveru SQL Server. Chcete-li ověřit, že Sqoop uvidí vaše databáze SQL, zadejte následující příkaz v otevření připojení SSH. Zadejte heslo pro přihlašovací jméno SQL serveru po zobrazení výzvy. Tento příkaz vrátí seznam databází.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. Nahraďte `MYSQLSERVER` s názvem serveru SQL Server a `MYDATABASE` s názvem vaší databáze SQL. Export dat z Hive `hivesampletable` tabulky `mobiledata` tabulky ve službě SQL Database, zadejte následující příkaz v otevření připojení SSH. Zadejte heslo pro přihlašovací jméno SQL serveru po zobrazení výzvy

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Pokud chcete ověřit, že se data vyexportovala, použijte následující dotazy z vašeho klienta SQL exportovaná data zobrazíte:

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Sqoop import

Z SQL serveru do úložiště Azure.

1. Nahraďte `MYSQLSERVER` s názvem serveru SQL Server a `MYDATABASE` s názvem vaší databáze SQL. Zadejte následující příkaz Otevřít připojení SSH k importu dat z `mobiledata` do tabulky ve službě SQL Database `wasb:///tutorials/usesqoop/importeddata` adresáře v HDInsight. Zadejte heslo pro přihlašovací jméno SQL serveru po zobrazení výzvy. Pole v datech jsou oddělené tabulátorem a řádky jsou ukončeny znak nového řádku.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. Po dokončení importu, zadejte následující příkaz v otevření připojení SSH do seznamu dat v novém adresáři:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>Omezení

* Hromadné export - s Linuxovým systémem HDInsight, Sqoop konektor používaný k exportu dat Microsoft SQL Server nebo Azure SQL Database nepodporuje operace hromadného vložení.

* Dávkování – s Linuxovým systémem HDInsight při použití `-batch` přepnout při provádění operace vložení, Sqoop díky více vloží místo dávkování operace vložení.

## <a name="important-considerations"></a>Důležité informace

* HDInsight a SQL Server musí být ve stejné virtuální síti Azure.

    Příklad najdete v tématu [HDInsight připojit k místní síti](./../connect-on-premises-network.md) dokumentu.

    Další informace o používání HDInsight se službou Azure Virtual Network, najdete v článku [rozšířit HDInsight s Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md) dokumentu. Další informace o Azure Virtual Network, najdete v článku [Přehled služby Virtual Network](../../virtual-network/virtual-networks-overview.md) dokumentu.

* SQL Server musí být nakonfigurované pro umožnění ověřování SQL. Další informace najdete v tématu [volba režimu ověřování](https://msdn.microsoft.com/ms144284.aspx) dokumentu.

* Bude pravděpodobně nutné nakonfigurovat SQL Server tak, aby přijímal vzdálená připojení. Další informace najdete v tématu [postupy řešení potíží s připojením k databázovému stroji SQL serveru](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) dokumentu.

## <a name="next-steps"></a>Další postup

Nyní jste se naučili, jak použít Sqoop. Další informace naleznete v tématu:

* [Použití Apache Oozie s HDInsight](../hdinsight-use-oozie-linux-mac.md): Pomocí Sqoop akce v pracovním postupu Oozie.
* [Analýza zpoždění letů pomocí HDInsight](../hdinsight-analyze-flight-delay-data-linux.md): Analýza zpoždění letů pomocí Apache Hive a potom použít Sqoop k exportování dat do Azure SQL database.
* [Nahrání dat do HDInsight](../hdinsight-upload-data.md): Najdete další metody pro nahrávání dat do HDInsight nebo Azure Blob storage.
