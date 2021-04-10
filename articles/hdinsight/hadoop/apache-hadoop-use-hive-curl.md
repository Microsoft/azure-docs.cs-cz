---
title: Použití Apache Hadoopho podregistru s kudrlinkou v HDInsight – Azure
description: Naučte se vzdáleně odesílat úlohy Apache prasete do Azure HDInsight pomocí funkce kudrlinkou.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/06/2020
ms.openlocfilehash: 124661c57f779b9f8a639debcc093ed15e717694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946469"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Spouštění dotazů Apache Hive pomocí Apache Hadoop ve službě HDInsight pomocí REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Naučte se používat REST API WebHCat ke spouštění dotazů Apache Hive s Apache Hadoop v clusteru Azure HDInsight.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Hadoop v HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Klient REST. Tento dokument používá rutinu [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) ve Windows PowerShellu a [oblé](https://curl.haxx.se/) v [bash](/windows/wsl/install-win10).

* Pokud používáte bash, budete také potřebovat JQ, procesor JSON příkazového řádku.  Viz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .

## <a name="base-uri-for-rest-api"></a>Základní identifikátor URI pro rozhraní REST API

Základní identifikátor URI (Uniform Resource Identifier) pro REST API v HDInsight je `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME` , kde `CLUSTERNAME` je název vašeho clusteru.  Názvy clusterů v identifikátorech URI rozlišují **velká a malá písmena**.  I když název clusteru v části plně kvalifikovaného názvu domény (FQDN) v identifikátoru URI () rozlišuje velká a malá písmena `CLUSTERNAME.azurehdinsight.net` , jiné výskyty v identifikátoru URI rozlišují velká a malá písmena.

## <a name="authentication"></a>Authentication

Při použití kudrlinkou nebo jakékoli jiné komunikace REST s WebHCat je nutné ověřit požadavky zadáním uživatelského jména a hesla pro správce clusteru HDInsight. Rozhraní API REST je zabezpečeno pomocí [základního ověřování](https://en.wikipedia.org/wiki/Basic_access_authentication). Aby se zajistilo, že se přihlašovací údaje odesílají na server bezpečně, vždy proveďte požadavky pomocí protokolu HTTPS (Secure HTTP).

### <a name="setup-preserve-credentials"></a>Nastavení (zachovat přihlašovací údaje)

Zachovejte přihlašovací údaje, abyste je nemuseli znovu zadávat pro každý příklad.  Název clusteru se zachová v samostatném kroku.

**A. bash**  
Následující skript upravte tak, že nahradíte `PASSWORD` vlastní heslo.  Pak zadejte příkaz.

```bash
export password='PASSWORD'
```  

**B. PowerShell** Spusťte následující kód a v automaticky otevíraném okně zadejte své přihlašovací údaje:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identifikace správného názvu clusteru použita

V závislosti na tom, jak byl cluster vytvořen, může být skutečná velikost názvu clusteru odlišná, než očekáváte.  V těchto krocích se zobrazí skutečná velikost písmen a pak se uloží do proměnné pro všechny další příklady.

Úpravou následujících skriptů nahraďte `CLUSTERNAME` název vašeho clusteru. Pak zadejte příkaz. (Název clusteru pro plně kvalifikovaný název domény nerozlišuje velká a malá písmena.)

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

    Dostanete odpověď podobnou následujícímu textu:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Parametry použité v tomto příkazu jsou následující:

    * `-u` – Uživatelské jméno a heslo použité k ověření žádosti.
    * `-G` – Označuje, že je tento požadavek operace GET.

1. Začátek adresy URL `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1` je stejný pro všechny požadavky. Cesta, `/status` označuje, že požadavek má vrátit stav WebHCat (označovaný také jako Templeton) pro server. Verzi podregistru můžete požádat také pomocí následujícího příkazu:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Tato žádost vrátí odpověď podobnou následujícímu textu:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. K vytvoření tabulky s názvem **log4jLogs** použijte následující:

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

    Tato žádost používá metodu POST, která odesílá data jako součást požadavku na REST API. S požadavkem se odesílají následující hodnoty dat:

     * `user.name` – Uživatel, který spouští příkaz.
     * `execute` – Příkazy HiveQL ke spuštění.
     * `statusdir` – Adresář, do kterého se zapisuje stav této úlohy.

   Tyto příkazy provádějí následující akce:

   * `DROP TABLE` – Pokud tabulka již existuje, je odstraněna.
   * `CREATE EXTERNAL TABLE` – Vytvoří novou tabulku External v podregistru. Externí tabulky ukládají pouze definici tabulky v podregistru. Data zůstanou v původním umístění.

     > [!NOTE]  
     > Externí tabulky by měly být použity, pokud očekáváte, že budou zdrojová data aktualizována externím zdrojem. Například automatizovaný proces odesílání dat nebo jiná operace MapReduce.
     >
     > Vyřazení externí tabulky **neodstraní data** , pouze definici tabulky.

   * `ROW FORMAT` – Způsob formátování dat Pole v každém protokolu jsou oddělená mezerou.
   * `STORED AS TEXTFILE LOCATION` – Kde jsou data uložená (příklad/adresář dat) a že se ukládají jako text.
   * `SELECT` – Vybere počet všech řádků, ve kterých sloupec **T4** obsahuje hodnotu **[Chyba]**. Tento příkaz vrátí hodnotu **3** , protože jsou tři řádky, které obsahují tuto hodnotu.

     > [!NOTE]  
     > Všimněte si, že mezery mezi příkazy HiveQL jsou nahrazeny `+` znakem při použití s kudrlinkou. Hodnoty v uvozovkách, které obsahují mezeru, jako je například oddělovač, by neměly být nahrazeny hodnotou `+` .

      Tento příkaz vrátí ID úlohy, která se dá použít ke kontrole stavu úlohy.

1. Chcete-li zjistit stav úlohy, použijte následující příkaz:

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

    Pokud se úloha dokončí, stav se **podařilo**.

1. Jakmile se stav úlohy změní na **úspěch**, můžete načíst výsledky úlohy z úložiště objektů BLOB v Azure. `statusdir`Parametr předaný dotazu obsahuje umístění výstupního souboru, v tomto případě `/example/rest` . Tato adresa uchovává výstup v `example/curl` adresáři ve výchozím úložišti clusterů.

    Tyto soubory můžete zobrazit a stáhnout pomocí rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli). Další informace o použití rozhraní příkazového řádku Azure s Azure Storage najdete v dokumentu [použití Azure CLI s Azure Storage](../../storage/blobs/storage-quickstart-blobs-cli.md) .

## <a name="next-steps"></a>Další kroky

Další informace o dalších způsobech práce se systémem Hadoop ve službě HDInsight:

* [Použití Apache Hive s Apache Hadoop v HDInsight](hdinsight-use-hive.md)
* [Použití MapReduce s Apache Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Další informace o REST API používaných v tomto dokumentu najdete v [referenčním dokumentu WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) .