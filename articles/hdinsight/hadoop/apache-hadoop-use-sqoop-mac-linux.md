---
title: Apache Sqoop s Apache Hadoop – Azure HDInsight
description: Naučte se používat Apache Sqoop k importu a exportu mezi Apache Hadoop v HDInsight a Azure SQL Database.
keywords: Hadoop Sqoop, Sqoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: c839aeae77d7e75fb30d82c410c331d21f5868ae
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406040"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Použití Apache Sqoop k importu a exportu dat mezi Apache Hadoop ve službě HDInsight a SQL Database

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Naučte se používat Apache Sqoop k importu a exportu mezi Apache Hadoopm clusterem v Azure HDInsight a v databázi Azure SQL Database nebo Microsoft SQL Server. Kroky v tomto dokumentu používají `sqoop` příkaz přímo z hlavnímu uzlu clusteru Hadoop. Pomocí SSH se připojíte k hlavnímu uzlu a spustíte příkazy v tomto dokumentu. Tento článek je pokračováním [v použití Apache Sqoop se systémem Hadoop ve službě HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Požadavky

* Dokončení [Nastavení testovacího prostředí](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) [pro použití Apache Sqoop se systémem Hadoop ve službě HDInsight](./hdinsight-use-sqoop.md).

* Klient pro dotazování databáze SQL Azure. Zvažte použití [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) nebo [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* Klient SSH. Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sqoop-export"></a>Export Sqoop

Z podregistru do SQL Server.

1. Pomocí SSH se připojte ke clusteru HDInsight. Nahraďte `CLUSTERNAME` názvem vašeho clusteru a pak zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Nahraďte `MYSQLSERVER` názvem vašeho SQL Server. Pokud chcete ověřit, že Sqoop uvidí vaše SQL Database, zadejte níže uvedený příkaz v otevřeném připojení SSH. Po zobrazení výzvy zadejte heslo pro SQL Server přihlašovací jméno. Tento příkaz vrátí seznam databází.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. Nahraďte `MYSQLSERVER` názvem vaší SQL Server a `MYDATABASE` názvem vaší databáze SQL. Pokud chcete exportovat data z tabulky `hivesampletable` podregistru `mobiledata` do tabulky v SQL Database, zadejte níže uvedený příkaz v otevřeném připojení SSH. Po zobrazení výzvy zadejte heslo pro SQL Server přihlášení.

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Chcete-li ověřit, zda byla data exportována, použijte následující dotazy z klienta SQL k zobrazení exportovaných dat:

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Import Sqoop

Z SQL Server do úložiště Azure.

1. Nahraďte `MYSQLSERVER` názvem vaší SQL Server a `MYDATABASE` názvem vaší databáze SQL. Zadejte níže uvedený příkaz v otevřeném připojení SSH, abyste importovali data `mobiledata` z tabulky v SQL Database `wasb:///tutorials/usesqoop/importeddata` do adresáře v HDInsight. Po zobrazení výzvy zadejte heslo pro SQL Server přihlašovací jméno. Pole v datech jsou oddělena znakem tabulátoru a řádky jsou zakončeny znakem nového řádku.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. Po dokončení importu zadejte následující příkaz v otevřeném připojení SSH, abyste mohli zobrazit data v novém adresáři:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>Omezení

* Hromadný export – pomocí HDInsight se systémem Linux, konektor Sqoop, který slouží k exportu dat do Microsoft SQL Server nebo Azure SQL Database nepodporuje hromadné vložení.

* Dávkování – pomocí HDInsight se systémem Linux při použití `-batch` přepínače při vkládání provede Sqoop vícenásobné vkládání místo dávkování operací vložení.

## <a name="important-considerations"></a>Důležité informace

* HDInsight i SQL Server musí být na stejném Virtual Network Azure.

    Příklad najdete v tématu [připojení HDInsight k](./../connect-on-premises-network.md) místní síťovému dokumentu.

    Další informace o používání služby HDInsight s Virtual Network Azure najdete v dokumentu věnovaném [rozšiřování HDInsight s azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md) . Další informace o Azure Virtual Network najdete v dokumentu [přehled Virtual Network](../../virtual-network/virtual-networks-overview.md) .

* SQL Server musí být nakonfigurovaná tak, aby povolovala ověřování SQL. Další informace naleznete v dokumentu [zvolit režim ověřování](https://msdn.microsoft.com/ms144284.aspx) .

* Možná budete muset nakonfigurovat SQL Server, aby přijímala vzdálená připojení. Další informace najdete v tématu [řešení potíží s připojením k dokumentu SQL Server databázového stroje](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) .

## <a name="next-steps"></a>Další postup

Nyní jste se naučili, jak používat Sqoop. Další informace naleznete v tématu:

* [Použití Apache Oozie se službou HDInsight](../hdinsight-use-oozie-linux-mac.md): Použijte akci Sqoop v pracovním postupu Oozie.
* [Analýza dat zpoždění letu pomocí HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): Pomocí interaktivního dotazu můžete analyzovat data zpoždění letu a pak pomocí Sqoop exportovat data do databáze SQL Azure.
* [Nahrát data do HDInsight](../hdinsight-upload-data.md): Najděte další metody pro nahrávání dat do služby HDInsight/Azure Blob Storage.
