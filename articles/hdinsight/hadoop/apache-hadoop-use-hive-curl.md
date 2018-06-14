---
title: Používání Hadoop Hive s Curl v HDInsight - Azure | Microsoft Docs
description: Naučte se vzdáleně odesílání úloh Pig do HDInsight pomocí Curl.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6ce18163-63b5-4df6-9bb6-8fcbd4db05d8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: f602cf45165625ec252f2e29cb9b0e5ed878f3a8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32170250"
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>Spouštění dotazů Hive se systémem Hadoop v HDInsight pomocí REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Naučte se používat rozhraní REST API WebHCat ke spouštění dotazů Hive se systémem Hadoop v clusteru Azure HDInsight.

## <a name="prerequisites"></a>Požadavky

* Systémem Linux Hadoop na verzi clusteru HDInsight 3.4 nebo vyšší.

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Klient REST. Tento dokument využívá prostředí Windows PowerShell a [Curl](http://curl.haxx.se/) příklady.

    > [!NOTE]
    > Prostředí Azure PowerShell poskytuje vyhrazený rutiny pro práci s Hive v HDInsight. Další informace najdete v tématu [použití Hive v prostředí Azure PowerShell](apache-hadoop-use-hive-powershell.md) dokumentu.

Tento dokument taky využívá prostředí Windows PowerShell a [Jq](http://stedolan.github.io/jq/) ke zpracování dat JSON vrácených z požadavků REST.

## <a id="curl"></a>Spouštění dotazů Hive

> [!NOTE]
> Pokud používáte cURL nebo jinou komunikaci REST s WebHCat, je třeba ověřit žádosti zadáním uživatelského jména a hesla pro správce clusteru HDInsight.
>
> Rozhraní API REST je zabezpečeno pomocí [základního ověřování](http://en.wikipedia.org/wiki/Basic_access_authentication). K zajištění, že vaše přihlašovací údaje jsou bezpečně odeslat na server, vždy proveďte požadavky pomocí Secure HTTP (HTTPS).

1. Pokud chcete nastavit přihlášení clusteru, který je používán skripty v tomto dokumentu, použijte jednu z následujících příkazů:

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

3. Ověřte, že se můžete připojit ke svému clusteru HDInsight, použijte jednu z následujících příkazů:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status)
    ```
    
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Můžete zobrazit odpověď podobná následující text:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Parametry použité v tomto příkazu jsou následující:

    * `-u` -Uživatelské jméno a heslo použité k ověření žádosti.
    * `-G` -Určuje, zda je tato žádost o operaci GET.

   Na začátek adresu URL, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, je stejný pro všechny požadavky. Cesty, `/status`, označuje, že požadavek je vrátit stav WebHCat (také známé jako Templeton) pro server. Můžete také vyžadovat verzi Hive pomocí následujícího příkazu:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Tento požadavek vrátí odpověď podobná následující text:

    ```json
        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}
    ```

4. Následující informace vám pomůžou vytvořit tabulku s názvem **log4jLogs**:

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

    Tento požadavek používá metodu POST, který odesílá data jako součást požadavku REST API. Následující hodnoty data se odesílají s žádostí:

     * `user.name` -Uživatel, který spouští příkaz.
     * `execute` -Příkazy HiveQL k provedení.
     * `statusdir` -Adresář, který stav pro tuto úlohu je zapsán do.

   Tyto příkazy provádět následující akce:
   
   * `DROP TABLE` – Pokud tabulka již existuje, je odstraněn.
   * `CREATE EXTERNAL TABLE` -Vytvoří novou tabulku "externí" v Hive. Externí tabulky uložit pouze definici tabulky Hive. Data je ponechán v původním umístění.

     > [!NOTE]
     > Externí tabulky by měl být použit při očekáváte, že v základních datech aktualizovat externího zdroje. Například automatizované data nahrát procesu nebo jiná operace MapReduce.
     >
     > Vyřazení externí tabulku nemá **není** odstranit data, jenom definici tabulky.

   * `ROW FORMAT` -Způsob formátování data. Pole v každém protokolu jsou oddělené mezerou.
   * `STORED AS TEXTFILE LOCATION` -Data se uloží (adresář, příklad nebo data) a která je uložena jako text.
   * `SELECT` – Počet všech řádků vybere kde sloupec **t4** obsahuje hodnotu **[Chyba]**. Tento příkaz vrátí hodnotu **3** jsou tři řádky, které obsahují tuto hodnotu.

     > [!NOTE]
     > Všimněte si, že jsou nahrazovány mezery mezi příkazy HiveQL `+` znak při použití s Curl. Hodnoty v uvozovkách, které obsahují mezery, jako je například oddělovač, by neměl být nahrazen `+`.

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

    Pokud se úloha dokončí, je stav **úspěšné**.

6. Jakmile se stav úlohy se změnila na **úspěšné**, můžete načíst výsledky úlohy z Azure Blob storage. `statusdir` Parametr předaný s dotaz obsahuje umístění výstupního souboru; v tomto případě `/example/rest`. Tato adresa ukládá výstup v `example/curl` adresáře v úložišti výchozí clustery.

    Můžete zobrazit seznam a stáhnout tyto soubory pomocí [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Další informace o používání rozhraní příkazového řádku Azure s Azure Storage najdete v tématu [použití Azure CLI 2.0 s Azure Storage](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) dokumentu.

## <a id="nextsteps"></a>Další kroky

Obecné informace o Hive s HDInsight:

* [Použijte Hive s Hadoop v HDInsight](hdinsight-use-hive.md)

Informace o jiných způsobech můžete pracovat s Hadoop v HDInsight:

* [Použijte Pig s Hadoop v HDInsight](hdinsight-use-pig.md)
* [Používání nástroje MapReduce s Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Pokud používáte s Hive Tez, najdete v následujících dokumentech pro ladění informace:

* [Použití zobrazení Ambari Tez na HDInsight se systémem Linux](../hdinsight-debug-ambari-tez-view.md)

Další informace o rozhraní API REST v tomto dokumentu najdete v tématu [WebHCat odkaz](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) dokumentu.

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


