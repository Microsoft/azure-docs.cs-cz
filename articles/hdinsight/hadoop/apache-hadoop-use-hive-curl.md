---
title: Použití Apache Hadoop Hive s Curl v HDInsight - Azure
description: Naučte se vzdáleně odesílat úlohy Apache Pig do Azure HDInsight pomocí Curlu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/06/2020
ms.openlocfilehash: 10a2f413142124db7547e68280a0d5e9abac9b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298746"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Spouštění dotazů Apache Hive s Apache Hadoop v HDInsight pomocí REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Naučte se používat rozhraní WebHCat REST API ke spouštění dotazů Apache Hive pomocí Apache Hadoop v clusteru Azure HDInsight.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Hadoop na HDInsight. Viz [Začínáme s HDInsight na Linuxu](./apache-hadoop-linux-tutorial-get-started.md).

* Klient REST. Tento dokument používá [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) v prostředí Windows PowerShell a [Curl](https://curl.haxx.se/) on [Bash](https://docs.microsoft.com/windows/wsl/install-win10).

* Pokud používáte Bash, budete také potřebovat jq, procesor JSON příkazového řádku.  Viz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

## <a name="base-uri-for-rest-api"></a>Základní identifikátor URI pro rozhraní REST API

Základní identifikátor URI (Uniform Resource Identifier) pro `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`rozhraní `CLUSTERNAME` REST API na HDInsight je , kde je název clusteru.  Názvy clusterů v identifikátorech URI **rozlišují malá a velká písmena**.  Zatímco název clusteru v plně kvalifikované části názvu domény (Plně`CLUSTERNAME.azurehdinsight.net`kvalifikovaný název domény) uri ( ) je malá a velká písmena, ostatní výskyty v URI jsou malá a velká písmena.

## <a name="authentication"></a>Ověřování

Při použití cURL nebo jakékoli jiné komunikace REST s WebHCat, je nutné ověřit požadavky zadáním uživatelské jméno a heslo pro správce clusteru HDInsight. Rozhraní API REST je zabezpečeno pomocí [základního ověřování](https://en.wikipedia.org/wiki/Basic_access_authentication). Chcete-li zajistit, aby vaše přihlašovací údaje byly bezpečně odesílány na server, vždy odesílejte požadavky pomocí zabezpečeného protokolu HTTP (HTTPS).

### <a name="setup-preserve-credentials"></a>Nastavení (zachovat pověření)

Zachovejte pověření, abyste je pro každý příklad nezadali znovu.  Název clusteru bude zachován v samostatném kroku.

**A. Bash**  
Upravte níže uvedený `PASSWORD` skript nahrazením skutečným heslem.  Pak zadejte příkaz.

```bash
export password='PASSWORD'
```  

**B. PowerShell** Spusťte níže uvedený kód a zadejte své přihlašovací údaje v automaticky otevírané okno:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Správně identifikovat název clusteru s velikostmi písmen

Skutečné velikosti písmen názvu clusteru se mohou lišit od očekávání v závislosti na způsobu vytvoření clusteru.  Kroky zde zobrazí skutečné velikostě písmen a pak jej uložte do proměnné pro všechny pozdější příklady.

Upravte níže uvedené `CLUSTERNAME` skripty a nahraďte je názvem clusteru. Pak zadejte příkaz. (Název clusteru pro hlavní název sítě se nerozlišují malá a velká písmena.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

## <a name="run-a-hive-query"></a>Spuštění dotazu Hive

1. Chcete-li ověřit, zda se můžete připojit ke clusteru HDInsight, použijte jeden z následujících příkazů:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Obdržíte odpověď podobnou následujícímu textu:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Parametry použité v tomto příkazu jsou následující:

    * `-u`- Uživatelské jméno a heslo použité k ověření požadavku.
    * `-G`- Označuje, že tento požadavek je operace GET.

1. Začátek adresy URL `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, je stejný pro všechny požadavky. Cesta , `/status`označuje, že požadavek je vrátit stav WebHCat (také známý jako Templeton) pro server. Můžete také požádat o verzi Hive pomocí následujícího příkazu:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Tento požadavek vrátí odpověď podobnou následujícímu textu:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. Pomocí následujícího příkazu vytvořte tabulku s názvem **log4jLogs**:

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Tento požadavek používá metodu POST, která odesílá data jako součást požadavku do rozhraní REST API. S požadavkem jsou odesílány následující hodnoty dat:

     * `user.name`- Uživatel, který je spuštěn příkaz.
     * `execute`- Příkazy HiveQL k provedení.
     * `statusdir`- Adresář, do kterého je zapsán stav této úlohy.

   Tyto příkazy provádět následující akce:

   * `DROP TABLE`- Pokud tabulka již existuje, je odstraněna.
   * `CREATE EXTERNAL TABLE`- Vytvoří novou "externí" tabulku v Úlu. Externí tabulky ukládají pouze definici tabulky v Hive. Data zůstanou v původním umístění.

     > [!NOTE]  
     > Externí tabulky by měly být použity, pokud očekáváte, že podkladová data budou aktualizována externím zdrojem. Například automatizovaný proces odesílání dat nebo jiné operace MapReduce.
     >
     > Uvolněním externí tabulky **neodstraníte** data, pouze definice tabulky.

   * `ROW FORMAT`- Jak jsou data formátována. Pole v každém protokolu jsou oddělena mezerou.
   * `STORED AS TEXTFILE LOCATION`- Kde jsou data uložena (příklad / datový adresář) a že jsou uložena jako text.
   * `SELECT`- Vybere počet všech řádků, kde sloupec **t4** obsahuje hodnotu **[ERROR]**. Tento příkaz vrátí hodnotu **3,** protože existují tři řádky, které obsahují tuto hodnotu.

     > [!NOTE]  
     > Všimněte si, že mezery mezi Příkazy `+` HiveQL jsou nahrazeny znakem při použití s Curl. Kotované hodnoty, které obsahují mezeru, například oddělovač, by neměly být nahrazeny . `+`

      Tento příkaz vrátí ID úlohy, které lze použít ke kontrole stavu úlohy.

1. Chcete-li zkontrolovat stav úlohy, použijte následující příkaz:

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
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

    Pokud je úloha dokončena, je stav **succeeded**.

1. Po změně stavu úlohy na **SUCCEEDED**můžete načíst výsledky úlohy z úložiště objektů blob Azure. Parametr `statusdir` předaný s dotazem obsahuje umístění výstupního souboru; v tomto `/example/rest`případě. Tato adresa ukládá výstup `example/curl` do adresáře ve výchozím úložišti clusterů.

    Tyto soubory můžete vypsat a stáhnout pomocí [rozhraní příkazového příkazu Konzumu Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Další informace o používání azure cli s Azure Storage najdete [v tématu použití Azure CLI s](https://docs.microsoft.com/azure/storage/storage-azure-cli) Azure Storage dokumentu.

## <a name="next-steps"></a>Další kroky

Informace o dalších způsobech práce s Hadoopem na HDInsight:

* [Použití Apache Hive s Apache Hadoop na HDInsight](hdinsight-use-hive.md)
* [Použití MapReduce s Apache Hadoop na HDInsight](hdinsight-use-mapreduce.md)

Další informace o rozhraní REST API použitév tomto dokumentu naleznete v referenčním dokumentu [WebHCat.](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)