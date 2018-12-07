---
title: Použití Apache Hadoop Hive pomocí Curl v HDInsight – Azure
description: Zjistěte, jak vzdáleně odesílat úlohy Apache Pig do HDInsight, pomocí příkazu Curl.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 637ce81f2e2ef4c7271e26bd4e9dfe36da524e27
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012738"
---
# <a name="run-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Spouštění dotazů Hive pomocí Apache Hadoop v HDInsight pomocí rozhraní REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Další informace o použití rozhraní REST API WebHCat ke spouštění dotazů Hive v clusteru Azure HDInsight se Apache Hadoop.

## <a name="prerequisites"></a>Požadavky

* Hadoop založených na Linuxu v clusteru HDInsight verze 3.4 nebo vyšší.

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Klient REST. Tento dokument používá prostředí Windows PowerShell a [Curl](https://curl.haxx.se/) příklady.

    > [!NOTE]
    > Prostředí Azure PowerShell obsahuje jednoúčelové rutiny určené pro práci s Hive v HDInsight. Další informace najdete v tématu [použití Hivu se službou Azure Powershellu](apache-hadoop-use-hive-powershell.md) dokumentu.

Tento dokument taky využívá prostředí Windows PowerShell a [Jq](https://stedolan.github.io/jq/) ke zpracování dat JSON vrácených z požadavky REST.

## <a id="curl"></a>Spuštění dotazu Hive

> [!NOTE]
> Pokud používáte cURL nebo jinou komunikaci REST s WebHCat, je třeba ověřit žádosti zadáním uživatelského jména a hesla pro správce clusteru HDInsight.
>
> Rozhraní API REST je zabezpečeno pomocí [základního ověřování](https://en.wikipedia.org/wiki/Basic_access_authentication). K zajištění, že vaše přihlašovací údaje se bezpečně odesílají na server, vždy proveďte požadavky pomocí Secure HTTP (HTTPS).

1. Nastavení přihlášení ke clusteru, který se používá ve skriptech v tomto dokumentu, použijte jednu z následujících příkazů:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Pokud chcete nastavit název clusteru, použijte jednu z následujících příkazů:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Pokud chcete ověřit, zda se můžete připojit ke clusteru HDInsight, použijte jednu z následujících příkazů:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status)
    ```
    
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Můžete zobrazit odpověď podobná následujícímu textu:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Parametry použité v tomto příkazu jsou následující:

    * `-u` -Uživatelské jméno a heslo použité pro ověření žádosti.
    * `-G` – Znamená, že tuto žádost o operaci GET.

   Počáteční adresa URL, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, je stejný pro všechny požadavky. Cesta, `/status`, signalizuje požadavek vrátit stav WebHCat (také známé jako Templeton) pro server. Můžete také požádat o verzi Hive pomocí následujícího příkazu:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Tento požadavek vrátí odpověď podobná následujícímu textu:

    ```json
        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}
    ```

4. Pomocí následujícího postupu vytvořte tabulku s názvem **log4jLogs**:

    ```bash
    JOBID=`curl -s -u $LOGIN -d user.name=$LOGIN -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/hive | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="set hive.execution.engine=tez;DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Tento požadavek používá metodu POST, který odesílá data jako součást požadavku na rozhraní REST API. Následující hodnoty data se odesílají v požadavku:

     * `user.name` -Uživatel, který spouští příkaz.
     * `execute` K provedení – příkazy HiveQL.
     * `statusdir` -Adresáře, který stavu pro tuto úlohu je zapsán do.

   Tyto příkazy provádět následující akce:
   
   * `DROP TABLE` – Pokud je tabulka již existuje, je odstranit.
   * `CREATE EXTERNAL TABLE` -Vytvoří novou tabulku "externí" v podregistru. Externí tabulky uložte definici tabulky Hive. Data zůstane v původním umístění.

     > [!NOTE]
     > Pokud očekáváte, že podkladová data aktualizovat externího zdroje je třeba použít externí tabulky. Například automatizovaných datových odesílat další operaci MapReduce nebo procesu.
     >
     > Vyřazení externí tabulky neodpovídá **není** odstranit data, pouze definici tabulky.

   * `ROW FORMAT` -Způsob formátování data. Pole v každém protokolu jsou oddělené mezerou.
   * `STORED AS TEXTFILE LOCATION` -Data se mají ukládat (do adresáře příkladu/dat) a, která je uložená jako text.
   * `SELECT` – Počet všech řádků vybere kde sloupec **t4** obsahuje hodnotu **[Chyba]**. Tento příkaz vrátí hodnotu **3** jsou tři řádky, které obsahují tuto hodnotu.

     > [!NOTE]
     > Všimněte si, že jsou nahrazené mezery mezi příkazy HiveQL `+` znaků při použití s Curl. Hodnoty v uvozovkách, které obsahují mezeru, jako jsou oddělovač, by neměly být nahrazen `+`.

      Tento příkaz vrátí ID úlohy, který slouží ke kontrole stavu úlohy.

5. Pokud chcete zkontrolovat stav úlohy, použijte následující příkaz:

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Pokud úloha dokončí, je stav **SUCCEEDED**.

6. Jakmile se stav úlohy se změnila na **SUCCEEDED**, můžete načíst výsledky úlohy z úložiště objektů Blob v Azure. `statusdir` Parametr předaný s dotazem obsahuje umístění výstupního souboru; v takovém případě `/example/rest`. Tato adresa se ukládá výstup v `example/curl` ve výchozím úložištěm clustery.

    Můžete seznam a stáhnout tyto soubory [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Další informace o použití Azure CLI s Azure Storage, najdete v článku [pomocí Azure CLI s Azure Storage](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) dokumentu.

## <a id="nextsteps"></a>Další kroky

Obecné informace o Hive s HDInsight:

* [Použití Hivu s Hadoopem v HDInsight](hdinsight-use-hive.md)

Další informace o dalších způsobech můžete pracovat s Hadoop v HDInsight:

* [Použití Pigu se systémem Hadoop v HDInsight](hdinsight-use-pig.md)
* [Použití MapReduce se systémem Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Pokud používáte pomocí Hive Tez, naleznete v následujících dokumentech pro informace o ladění:

* [Použití zobrazení Ambari Tez na HDInsight založených na Linuxu](../hdinsight-debug-ambari-tez-view.md)

Další informace o rozhraní REST API v tomto dokumentu najdete v tématu [WebHCat odkaz](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) dokumentu.

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx


