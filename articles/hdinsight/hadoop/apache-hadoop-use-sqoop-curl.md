---
title: Použití curlu k exportu dat pomocí Apache Sqoop v Azure HDInsight
description: Naučte se vzdáleně odesílat úlohy Apache Sqoop do Azure HDInsight pomocí Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: da29785547d1b6eb4b38d07f020ba885dc5137ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767582"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Spouštění úloh Apache Sqoop v HDInsightu s curlem

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Naučte se používat Curl ke spouštění úloh Apache Sqoop v clusteru Apache Hadoop v HDInsightu. Tento článek ukazuje, jak exportovat data z Azure Storage a importovat do databáze SQL Serveru pomocí Curl. Tento článek je pokračováním [použití Apache Sqoop s Hadoop v HDInsight](./hdinsight-use-sqoop.md).

Curl se používá k předvedení, jak můžete pracovat s HDInsight pomocí nezpracovaných požadavků HTTP ke spuštění, monitorování a načtení výsledků úloh Sqoop. To funguje pomocí rozhraní WebHCat REST API (dříve známé jako Templeton) poskytované clusteru HDInsight.

## <a name="prerequisites"></a>Požadavky

* Dokončení [Nastavení testovacího prostředí](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) z [use Apache Sqoop s Hadoopem v HDInsightu](./hdinsight-use-sqoop.md).

* Klient pro dotaz na Azure SQL Database. Zvažte použití [aplikace SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) nebo Visual Studio [Code](../../sql-database/sql-database-connect-query-vscode.md).

* [Zvlnění](https://curl.haxx.se/). Curl je nástroj pro přenos dat z clusteru HDInsight nebo do clusteru HDInsight.

* [jq](https://stedolan.github.io/jq/). Nástroj jq se používá ke zpracování dat JSON vrácených z požadavků REST.

* Obeznámenost s Sqoop. Další informace naleznete v [uživatelské příručce společnosti Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Odeslat úlohy Apache Sqoop pomocí funkce Curl

Curl slouží k exportu dat pomocí úloh Apache Sqoop z Azure Storage na SQL Server.

> [!NOTE]  
> Pokud používáte Curl nebo jinou komunikaci REST s WebHCat, je třeba ověřit žádosti zadáním uživatelského jména a hesla pro správce clusteru HDInsight. Název clusteru také musíte použít jako součást identifikátoru URI (Uniform Resource Identifier) sloužícímu k odesílání požadavků na server.

Pro příkazy v této `USERNAME` části nahraďte uživatelem k `PASSWORD` ověření clusteru a nahraďte heslem pro uživatelský účet. Nahraďte `CLUSTERNAME` názvem svého clusteru.

Rozhraní API REST je zabezpečeno pomocí [základního ověřování](https://en.wikipedia.org/wiki/Basic_access_authentication). Vždy doporučujeme provádět požadavky pomocí protokolu HTTPS (Secure HTTP) a pomoci tak zajistit, že přihlašovací údaje budou na server odeslány bezpečně.

1. Pro snadné použití nastavte níže uvedené proměnné. Tento příklad je založen na prostředí systému Windows, revidovat podle potřeby pro vaše prostředí.

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

1. K odeslání úlohy sqoop použijte následující:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% -d command="export --connect jdbc:sqlserver://%SQLDATABASESERVERNAME%.database.windows.net;user=%SQLUSER%@%SQLDATABASESERVERNAME%;password=%PASSWORD%;database=%SQLDATABASENAME% --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/sqoop
    ```

    Parametry použité v tomto příkazu jsou následující:

   * **-d** - `-G` Vzhledem k tomu, že se nepoužívá, požadavek výchozí post metody. `-d`určuje hodnoty dat, které jsou odeslány s požadavkem.

       * **user.name** - Uživatel, který je spuštěn příkaz.

       * **command** - Příkaz Sqoop k provedení.

       * **statusdir** - Adresář, do kterého bude zapsán stav této úlohy.

     Tento příkaz vrátí ID úlohy, které lze použít ke kontrole stavu úlohy.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

1. Chcete-li zkontrolovat stav úlohy, použijte následující příkaz. Nahradit `JOBID` hodnotou vrácenou v předchozím kroku. Například pokud byla `{"id":"job_1415651640909_0026"}`vrácená `JOBID` hodnota `job_1415651640909_0026`, pak by být . Podle `jq` potřeby zkontrolujte umístění.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | C:\HDI\jq-win64.exe .status.state
    ```

    Pokud je úloha dokončena, bude stav **succeeded**.

   > [!NOTE]  
   > Tento požadavek curl vrátí dokument zápisu objektu JavaScript (JSON) s informacemi o úloze; jq se používá k načtení pouze hodnoty stavu.

1. Po změně stavu úlohy na **SUCCEEDED**můžete načíst výsledky úlohy z úložiště objektů blob Azure. Parametr `statusdir` předaný s dotazem obsahuje umístění výstupního souboru; v tomto `wasb:///example/data/sqoop/curl`případě. Tato adresa ukládá výstup úlohy `example/data/sqoop/curl` v adresáři ve výchozím kontejneru úložiště používaném clusterem HDInsight.

    K přístupu k objektům BLOB stderr a stdout můžete použít portál Azure.

1. Chcete-li ověřit, zda byla data exportována, zobrazte exportovaná data pomocí následujících dotazů z klienta SQL:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Omezení

* Hromadný export – S Linuxem založené HDInsight konektor Sqoop slouží k exportu dat na Microsoft SQL Server nebo Azure SQL Database aktuálně nepodporuje hromadné vložení.
* Dávkování - S Linux-založené HDInsight, `-batch` Při použití přepínače při provádění vložení, Sqoop provede více vložek namísto dávkování operace vložení.

## <a name="summary"></a>Souhrn

Jak je znázorněno v tomto dokumentu, můžete použít nezpracovaný požadavek HTTP ke spuštění, monitorování a zobrazení výsledků úloh Sqoop v clusteru HDInsight.

Další informace o rozhraní REST použité v tomto článku naleznete v <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">průvodci apache sqoop REST API</a>.

## <a name="next-steps"></a>Další kroky

[Použijte Apache Sqoop s Apache Hadoop na HDInsight](hdinsight-use-sqoop.md)

Pro další hdinsight články zahrnující curl:

* [Vytvoření clusterů Apache Hadoop pomocí rozhraní Azure REST API](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Spouštění dotazů Apache Hive s Apache Hadoop v HDInsight pomocí REST](apache-hadoop-use-hive-curl.md)
* [Spusťte mapreduce úlohy s Apache Hadoop na HDInsight pomocí REST](apache-hadoop-use-mapreduce-curl.md)