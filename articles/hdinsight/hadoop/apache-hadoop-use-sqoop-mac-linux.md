---
title: Apache Sqoop s Apache Hadoop - Azure HDInsight
description: Přečtěte si, jak pomocí Apache Sqoop importovat a exportovat mezi Apache Hadoop na HDInsight a Azure SQL Database.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 21bc903349876a76576fb742840e9899f9d94bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769383"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Import a export dat mezi Apache Hadoopem ve službě HDInsight a SQL Database pomocí Apache Sqoopu

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Přečtěte si, jak pomocí Apache Sqoop importovat a exportovat mezi clusterem Apache Hadoop v Azure HDInsight a Azure SQL Database nebo databázi Microsoft SQL Serveru. Kroky v tomto dokumentu `sqoop` používají příkaz přímo z čela uzlu clusteru Hadoop. Pomocí SSH se připojíte k hlavnímu uzlu a spustíte příkazy v tomto dokumentu. Tento článek je pokračováním [použití Apache Sqoop s Hadoop v HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Požadavky

* Dokončení [Nastavení testovacího prostředí](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) z [use Apache Sqoop s Hadoopem v HDInsightu](./hdinsight-use-sqoop.md).

* Klient SSH. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Obeznámenost s Sqoop. Další informace naleznete v [uživatelské příručce společnosti Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="set-up"></a>Nastavit

1. Pomocí [příkazu ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru. Upravte níže uvedený příkaz nahrazením názvu clusteru názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Pro snadné použití nastavte proměnné. Nahraďte `PASSWORD`, `MYSQLSERVER`a `MYDATABASE` s příslušnými hodnotami a zadejte následující příkazy:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Vývoz Sqoop

Z Hive na SQL Server.

1. Chcete-li ověřit, zda Sqoop může zobrazit vaši databázi SQL, zadejte níže uvedený příkaz v otevřeném připojení SSH. Tento příkaz vrátí seznam databází.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. Zadáním následujícího příkazu zobrazíte seznam tabulek pro zadanou databázi:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Chcete-li exportovat `hivesampletable` data z `mobiledata` tabulky Hive do tabulky v databázi SQL, zadejte níže uvedený příkaz v otevřeném připojení SSH:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Chcete-li ověřit, zda byla data exportována, zobrazte exportovaná data pomocí následujících dotazů z připojení SSH:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Import Sqoop

Z SQL Serveru do úložiště Azure.

1. Do otevřeného připojení SSH zadejte níže `mobiledata` uvedený příkaz pro import `wasbs:///tutorials/usesqoop/importeddata` dat z tabulky v databázi SQL do adresáře na webu HDInsight. Pole v datech jsou oddělena znakem tabulátoru a řádky jsou zakončeny znakem nového řádku.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. Případně můžete také zadat tabulku Hive:

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. Po dokončení importu zadejte do otevřeného připojení SSH následující příkaz, abyste data v novém adresáři vypsat:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. Pomocí [čáry](./apache-hadoop-use-hive-beeline.md) ověřte, zda byla tabulka vytvořena v Úlu.

    1. Připojení

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Proveďte každý dotaz pod jeden po druhém a zkontrolujte výstup:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Exit beeline `!exit`s .

## <a name="limitations"></a>Omezení

* Hromadný export – S Linuxem založené HDInsight konektor Sqoop slouží k exportu dat na Microsoft SQL Server nebo Azure SQL Database nepodporuje hromadné vložení.

* Dávkování - S Linux-založené HDInsight, `-batch` Při použití přepínače při provádění vložení, Sqoop provede více vložek namísto dávkování operace vložení.

## <a name="important-considerations"></a>Důležité informace

* HDInsight i SQL Server musí být ve stejné virtuální síti Azure.

    Například najdete v tématu [Připojení HDInsight u místního síťového](./../connect-on-premises-network.md) dokumentu.

    Další informace o používání HDInsightu s virtuální sítí Azure najdete v dokumentu [Rozšíření HDInsightu pomocí virtuální sítě Azure.](../hdinsight-plan-virtual-network-deployment.md) Další informace o virtuální síti Azure najdete v dokumentu [Přehled virtuální sítě.](../../virtual-network/virtual-networks-overview.md)

* Sql Server musí být nakonfigurován tak, aby umožňoval ověřování SQL. Další informace naleznete v dokumentu [Zvolit režim ověřování.](https://msdn.microsoft.com/ms144284.aspx)

* Bude pravděpodobně nutné nakonfigurovat sql server pro přijímání vzdálených připojení. Další informace naleznete v tématu Jak řešit potíže s připojením k dokumentu [databázového stroje serveru SQL Server.](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)

## <a name="next-steps"></a>Další kroky

Teď jste se naučili používat Sqoop. Další informace naleznete v tématu:

* [Použijte Apache Oozie s HDInsight:](../hdinsight-use-oozie-linux-mac.md)Použijte akci Sqoop v pracovním postupu Oozie.
* [Analyzujte data zpoždění letu pomocí HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): Pomocí interaktivního dotazu analyzujte data zpoždění letu a pak pomocí Sqoopu exportujte data do databáze Azure SQL.
* [Nahrávání dat do HDInsightu](../hdinsight-upload-data.md): Najděte další metody nahrávání dat do úložiště objektů blob HDInsight/Azure.
