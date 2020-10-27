---
title: Apache Sqoop s Apache Hadoop – Azure HDInsight
description: Naučte se používat Apache Sqoop k importu a exportu mezi Apache Hadoop ve službě HDInsight a Azure SQL Database.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 6cd522192c498d628490430c46c18c8ae08ad2e2
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547006"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-azure-sql-database"></a>Použití Apache Sqoop k importu a exportu dat mezi Apache Hadoop ve službě HDInsight a Azure SQL Database

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Naučte se používat Apache Sqoop k importu a exportu mezi Apache Hadoopm clusterem v Azure HDInsight a Azure SQL Database nebo Microsoft SQL Server. Kroky v tomto dokumentu používají `sqoop` příkaz přímo z hlavnímu uzlu clusteru Hadoop. Pomocí SSH se připojíte k hlavnímu uzlu a spustíte příkazy v tomto dokumentu. Tento článek je pokračováním [v použití Apache Sqoop se systémem Hadoop ve službě HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Předpoklady

* Dokončení [Nastavení testovacího prostředí](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) [pro použití Apache Sqoop se systémem Hadoop ve službě HDInsight](./hdinsight-use-sqoop.md).

* Klient SSH. Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Seznamte se se znalostí pro Sqoop. Další informace najdete v tématu [uživatelská příručka pro Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="set-up"></a>Nastavení

1. Připojte se ke clusteru pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Pro snadné použití nastavte proměnné. `PASSWORD`Hodnoty, `MYSQLSERVER` a nahraďte `MYDATABASE` příslušnými hodnotami a potom zadejte následující příkazy:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Export Sqoop

Z podregistru do SQL.

1. Pokud chcete ověřit, že Sqoop uvidí vaši databázi, zadejte níže uvedený příkaz v otevřeném připojení SSH. Tento příkaz vrátí seznam databází.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. Zadáním následujícího příkazu zobrazíte seznam tabulek pro zadanou databázi:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Chcete-li exportovat data z `hivesampletable` tabulky podregistru do `mobiledata` tabulky v databázi, zadejte následující příkaz v otevřeném připojení SSH:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Chcete-li ověřit, zda byla data exportována, použijte následující dotazy z připojení SSH k zobrazení exportovaných dat:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Import Sqoop

Z SQL do Azure Storage.

1. Zadejte níže uvedený příkaz v otevřeném připojení SSH, abyste importovali data z `mobiledata` tabulky v SQL do `wasbs:///tutorials/usesqoop/importeddata` adresáře v HDInsight. Pole v datech jsou oddělena znakem tabulátoru a řádky jsou zakončeny znakem nového řádku.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. Případně můžete také zadat tabulku podregistru:

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

1. Po dokončení importu zadejte následující příkaz v otevřeném připojení SSH, abyste mohli zobrazit data v novém adresáři:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. Pomocí [Beeline](./apache-hadoop-use-hive-beeline.md) ověřte, že je tabulka vytvořená v podregistru.

    1. Připojit

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Spusťte každý dotaz v jednom okamžiku a zkontrolujte výstup:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Ukončete Beeline pomocí `!exit` .

## <a name="limitations"></a>Omezení

* Hromadný export – pomocí HDInsight se systémem Linux konektor Sqoop, který slouží k exportu dat do SQL, nepodporuje hromadné vložení.

* Dávkování – pomocí HDInsight se systémem Linux při použití `-batch` přepínače při vkládání provede Sqoop vícenásobné vkládání místo dávkování operací vložení.

## <a name="important-considerations"></a>Důležité informace

* HDInsight i SQL Server musí být na stejném Virtual Network Azure.

    Příklad najdete v tématu [připojení HDInsight k místní síťovému](./../connect-on-premises-network.md) dokumentu.

    Další informace o používání služby HDInsight s Virtual Network Azure najdete v dokumentu věnovaném [rozšiřování HDInsight s azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md) . Další informace o Azure Virtual Network najdete v dokumentu [přehled Virtual Network](../../virtual-network/virtual-networks-overview.md) .

* SQL Server musí být nakonfigurovaná tak, aby povolovala ověřování SQL. Další informace naleznete v dokumentu [zvolit režim ověřování](/sql/relational-databases/security/choose-an-authentication-mode) .

* Možná budete muset nakonfigurovat SQL Server, aby přijímala vzdálená připojení. Další informace najdete v tématu [řešení potíží s připojením k dokumentu SQL Server databázového stroje](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) .

## <a name="next-steps"></a>Další kroky

Nyní jste se naučili, jak používat Sqoop. Další informace najdete v následujících tématech:

* [Použití Apache Oozie se službou HDInsight](../hdinsight-use-oozie-linux-mac.md): použijte akci Sqoop v pracovním postupu Oozie.
* [Analýza dat zpoždění letu pomocí HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): pomocí interaktivního dotazu Analyzujte data zpoždění letu a pak pomocí Sqoop exportujte data do databáze v Azure.
* [Nahrávání dat do HDInsight](../hdinsight-upload-data.md): Najděte další metody pro nahrávání dat do služby HDInsight/Azure Blob Storage.