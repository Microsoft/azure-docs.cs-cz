---
title: Použití oblé k exportu dat s Apache Sqoop ve službě Azure HDInsight
description: Naučte se vzdáleně odesílat úlohy Apache Sqoop do Azure HDInsight pomocí funkce kudrlinkou.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/06/2020
ms.openlocfilehash: 4de42bf30824fd71228aa27cc478a54ec3741da9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98928360"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Spouštění úloh Apache Sqoop v HDInsight pomocí kudrlinkou

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Naučte se, jak pomocí technologie kudrlinkou spouštět úlohy Apache Sqoop v clusteru Apache Hadoop v HDInsight. Tento článek ukazuje, jak exportovat data z Azure Storage a importovat je do databáze SQL Server pomocí oblé. Tento článek je pokračováním [v použití Apache Sqoop se systémem Hadoop ve službě HDInsight](./hdinsight-use-sqoop.md).

Kudrlinkou se používá k předvedení, jak můžete s HDInsight pracovat pomocí nezpracovaných požadavků HTTP ke spouštění, monitorování a načítání výsledků úloh Sqoop. To funguje pomocí REST API WebHCat (dříve označované jako Templeton) poskytované clusterem HDInsight.

## <a name="prerequisites"></a>Předpoklady

* Dokončení [Nastavení testovacího prostředí](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) [pro použití Apache Sqoop se systémem Hadoop ve službě HDInsight](./hdinsight-use-sqoop.md).

* Klient pro dotaz na Azure SQL Database. Zvažte použití [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) nebo [Visual Studio Code](../../azure-sql/database/connect-query-vscode.md).

* [Kudrlinkou](https://curl.haxx.se/). Kudrlinkou je nástroj pro přenos dat z clusteru HDInsight nebo do něj.

* [JQ](https://stedolan.github.io/jq/). Nástroj JQ se používá ke zpracování dat JSON vrácených z požadavků REST.

* Seznamte se se znalostí pro Sqoop. Další informace najdete v tématu [uživatelská příručka pro Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Odesílání úloh Apache Sqoop pomocí kudrlinkou

Pomocí kudrlinkou můžete exportovat data pomocí úloh Apache Sqoop z Azure Storage do SQL Server.

> [!NOTE]  
> Pokud používáte Curl nebo jinou komunikaci REST s WebHCat, je třeba ověřit žádosti zadáním uživatelského jména a hesla pro správce clusteru HDInsight. Název clusteru také musíte použít jako součást identifikátoru URI (Uniform Resource Identifier) sloužícímu k odesílání požadavků na server.

Pro příkazy v této části nahraďte `USERNAME` uživatele, který se má ověřit v clusteru, a nahraďte `PASSWORD` heslem pro uživatelský účet. Nahraďte `CLUSTERNAME` názvem svého clusteru.

Rozhraní API REST je zabezpečeno pomocí [základního ověřování](https://en.wikipedia.org/wiki/Basic_access_authentication). Vždy doporučujeme provádět požadavky pomocí protokolu HTTPS (Secure HTTP) a pomoci tak zajistit, že přihlašovací údaje budou na server odeslány bezpečně.

1. Pro snadné použití nastavte následující proměnné. Tento příklad je založený na prostředí Windows, podle potřeby si můžete prohlédnout v prostředí.

    ```cmd
    set CLUSTERNAME=
    set USERNAME=admin
    set PASSWORD=
    set SQLDATABASESERVERNAME=
    set SQLDATABASENAME=
    set SQLPASSWORD=
    set SQLUSER=sqluser
    ```

1. Z příkazového řádku použijte následující příkaz k ověření, zda se můžete připojit ke clusteru HDInsight:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Měla by se zobrazit odpověď podobná následujícímu:

    ```json
    {"status":"ok","version":"v1"}
    ```

1. K odeslání úlohy Sqoop použijte následující postup:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% -d command="export --connect jdbc:sqlserver://%SQLDATABASESERVERNAME%.database.windows.net;user=%SQLUSER%@%SQLDATABASESERVERNAME%;password=%PASSWORD%;database=%SQLDATABASENAME% --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/sqoop
    ```

    Parametry použité v tomto příkazu jsou následující:

   * **-d** – vzhledem k tomu `-G` , že se nepoužívá, je požadavek nastaven na výchozí metodu post. `-d` Určuje hodnoty dat, které se odesílají spolu s požadavkem.

       * **User.Name** – uživatel, který spouští příkaz.

       * **příkaz** – příkaz Sqoop, který se má provést.

       * **statusdir** – adresář, do kterého se bude zapisovat stav této úlohy.

     Tento příkaz vrátí ID úlohy, která se dá použít ke kontrole stavu úlohy.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

1. Chcete-li zjistit stav úlohy, použijte následující příkaz. Nahraďte `JOBID` hodnotou vrácenou v předchozím kroku. Například Pokud vrácená hodnota byla `{"id":"job_1415651640909_0026"}` , pak `JOBID` by byla `job_1415651640909_0026` . Podle potřeby upravte umístění `jq` .

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | C:\HDI\jq-win64.exe .status.state
    ```

    Pokud byla úloha dokončena, stav bude **úspěšný**.

   > [!NOTE]  
   > Tato žádost o kudrlinkou vrátí dokument JavaScript Object Notation (JSON) s informacemi o úloze. JQ se používá k načtení pouze hodnoty stavu.

1. Jakmile se stav úlohy změní na **úspěch**, můžete načíst výsledky úlohy z úložiště objektů BLOB v Azure. `statusdir`Parametr předaný dotazu obsahuje umístění výstupního souboru, v tomto případě `wasb:///example/data/sqoop/curl` . Tato adresa ukládá výstup úlohy do `example/data/sqoop/curl` adresáře ve výchozím kontejneru úložiště, který používá cluster HDInsight.

    Azure Portal můžete použít pro přístup k objektům blob stderr a STDOUT.

1. Chcete-li ověřit, zda byla data exportována, použijte následující dotazy z klienta SQL k zobrazení exportovaných dat:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Omezení

* Hromadný export – pomocí HDInsight se systémem Linux, konektor Sqoop používaný k exportu dat do Microsoft SQL Server nebo Azure SQL Database v současné době nepodporuje hromadné vložení.
* Dávkování – se systémem Linux HDInsight při použití `-batch` přepínače při provádění operací INSERT Sqoop provede vícenásobné vkládání místo dávkování operací vložení.

## <a name="summary"></a>Souhrn

Jak je znázorněno v tomto dokumentu, můžete použít nezpracovaný požadavek HTTP ke spuštění, monitorování a zobrazení výsledků úloh Sqoop v clusteru HDInsight.

Další informace o rozhraní REST, které se používá v tomto článku, najdete v tématu <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">REST API příručka pro Apache Sqoop</a>.

## <a name="next-steps"></a>Další kroky

[Použití Apache Sqoop s Apache Hadoop v HDInsight](hdinsight-use-sqoop.md)

Další články HDInsight zahrnující oblé:

* [Vytváření clusterů Apache Hadoop pomocí Azure REST API](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Spouštění dotazů Apache Hive pomocí Apache Hadoop ve službě HDInsight pomocí REST](apache-hadoop-use-hive-curl.md)
* [Spouštění úloh MapReduce s využitím Apache Hadoop ve službě HDInsight pomocí REST](apache-hadoop-use-mapreduce-curl.md)
