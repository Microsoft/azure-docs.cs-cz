---
title: Použití Apache Hadoop Hive pomocí Curl v HDInsight – Azure
description: Zjistěte, jak vzdáleně odesílat úlohy Apache Pig do HDInsight, pomocí příkazu Curl.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: hrasheed
ms.openlocfilehash: 334d7b886aa4e2130a12f0c8a7919986fdac55d1
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508127"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Spustit dotazy Apache Hive s Apache Hadoop v HDInsight pomocí rozhraní REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Zjistěte, jak spustit dotazy Apache Hive v clusteru Azure HDInsight s Apache Hadoop pomocí rozhraní REST API WebHCat.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Hadoop v HDInsight. Zobrazit [Začínáme s HDInsight v Linuxu](./apache-hadoop-linux-tutorial-get-started.md).

* Klient REST. Tento dokument používá [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) v prostředí Windows PowerShell a [Curl](https://curl.haxx.se/) na [Bash](https://docs.microsoft.com/windows/wsl/install-win10).

* Pokud používáte prostředí Bash, budete také potřebovat jq příkazového řádku procesoru JSON.  Zobrazit [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/).

## <a name="base-uri-for-rest-api"></a>Základní identifikátor URI pro Rest API

Základní identifikátor URI (Uniform Resource) pro rozhraní REST API na HDInsight je `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, kde `CLUSTERNAME` je název vašeho clusteru.  Názvy clusterů v identifikátorech URI **malá a velká písmena**.  Zatímco název clusteru v části plně kvalifikovaný název (FQDN) identifikátoru URI (`CLUSTERNAME.azurehdinsight.net`) je velká a malá písmena, další výskyty v identifikátoru URI jsou malá a velká písmena.

## <a name="authentication"></a>Authentication

Pokud používáte cURL nebo jinou komunikaci REST s WebHCat, je třeba ověřit žádosti zadáním uživatelského jména a hesla pro správce clusteru HDInsight. Rozhraní API REST je zabezpečeno pomocí [základního ověřování](https://en.wikipedia.org/wiki/Basic_access_authentication). K zajištění, že vaše přihlašovací údaje se bezpečně odesílají na server, vždy proveďte požadavky pomocí Secure HTTP (HTTPS).

### <a name="setup-preserve-credentials"></a>Nastavení (zachovat přihlašovací údaje)
Zachovat svoje přihlašovací údaje, aby se zabránilo pokuste pro každý příklad.  Název clusteru budou zachovány v samostatný krok.

**A. Bash**  
Níže uvedený skript upravte tak, že nahradíte `PASSWORD` s vlastní heslo.  Potom zadejte příkaz.

```bash
export password='PASSWORD'
```  

**B. Prostředí PowerShell** spusťte níže uvedený kód a zadejte svoje přihlašovací údaje v automaticky otevíraném okně:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Zjištění názvu správně malá a velká použita clusteru
Skutečné malých a velkých písmen na název clusteru může být jiný než byste očekávali, v závislosti na způsobu vytvoření clusteru.  Zde uvedené kroky se zobrazí skutečné velká a malá písmena a uložte ho do proměnné pro všechny další příklady.

Upravit skripty níže nahraďte `CLUSTERNAME` názvem vašeho clusteru. Potom zadejte příkaz. (Název clusteru plně kvalifikovaného názvu domény není malá a velká písmena.)

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

## <a id="curl"></a>Spuštění dotazu Hive

1. Pokud chcete ověřit, zda se můžete připojit ke clusteru HDInsight, použijte jednu z následujících příkazů:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
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

1. Počáteční adresa URL, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, je stejný pro všechny požadavky. Cesta, `/status`, signalizuje požadavek vrátit stav WebHCat (také známé jako Templeton) pro server. Můžete také požádat o verzi Hive pomocí následujícího příkazu:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Tento požadavek vrátí odpověď podobná následujícímu textu:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. Pomocí následujícího postupu vytvořte tabulku s názvem **log4jLogs**:

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
   * `SELECT` – Počet všech řádků vybere kde sloupec **t4** obsahuje hodnotu **[Chyba]** . Tento příkaz vrátí hodnotu **3** jsou tři řádky, které obsahují tuto hodnotu.

     > [!NOTE]  
     > Všimněte si, že jsou nahrazené mezery mezi příkazy HiveQL `+` znaků při použití s Curl. Hodnoty v uvozovkách, které obsahují mezeru, jako jsou oddělovač, by neměly být nahrazen `+`.

      Tento příkaz vrátí ID úlohy, který slouží ke kontrole stavu úlohy.

1. Pokud chcete zkontrolovat stav úlohy, použijte následující příkaz:

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

    Pokud úloha dokončí, je stav **SUCCEEDED**.

1. Jakmile se stav úlohy se změnila na **SUCCEEDED**, můžete načíst výsledky úlohy z úložiště objektů Blob v Azure. `statusdir` Parametr předaný s dotazem obsahuje umístění výstupního souboru; v takovém případě `/example/rest`. Tato adresa se ukládá výstup v `example/curl` ve výchozím úložištěm clustery.

    Můžete seznam a stáhnout tyto soubory [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Další informace o použití Azure CLI s Azure Storage, najdete v článku [pomocí Azure CLI s Azure Storage](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) dokumentu.

## <a id="nextsteps"></a>Další kroky

Obecné informace o Hive s HDInsight:

* [Použití Apache Hivu s Apache Hadoop v HDInsight](hdinsight-use-hive.md)

Další informace o dalších způsobech můžete pracovat s Hadoop v HDInsight:

* [Použití Apache Pig s Apache Hadoop v HDInsight](hdinsight-use-pig.md)
* [Použití MapReduce se službou Apache Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Další informace o rozhraní REST API v tomto dokumentu najdete v tématu [WebHCat odkaz](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) dokumentu.