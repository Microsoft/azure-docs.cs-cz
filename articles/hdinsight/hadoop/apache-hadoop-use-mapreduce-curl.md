---
title: Použití MapReduce a Curl s Apache Hadoop v HDInsight - Azure
description: Naučte se vzdáleně spouštět úlohy MapReduce s Apache Hadoop na HDInsight pomocí Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: abc3cc8c526e37e18f1e67b109a9a8e15ff8c989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302708"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Spusťte mapreduce úlohy s Apache Hadoop na HDInsight pomocí REST

Přečtěte si, jak pomocí rozhraní APACHE Hive WebHCat REST API spouštět úlohy MapReduce v clusteru Apache Hadoop v clusteru HDInsight. Curl se používá k předvedení, jak můžete pracovat s HDInsight pomocí nezpracovaných požadavků HTTP ke spuštění mapreduce úlohy.

> [!NOTE]  
> Pokud jste již obeznámeni s používáním linuxových serverů Hadoop, ale jste v HDInsightu noví, podívejte se na [dokument Co potřebujete vědět o Apache Hadoop založeném na Linuxu na dokumentu HDInsight.](../hdinsight-hadoop-linux-information.md)

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Hadoop na HDInsight. Viz [Vytvoření clusterů Apache Hadoop pomocí portálu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

Buď:
  * Prostředí Windows PowerShell nebo
  * [Zvlnění](https://curl.haxx.se/) s [jq](https://stedolan.github.io/jq/)

## <a name="run-a-mapreduce-job"></a>Spuštění úlohy MapReduce

> [!NOTE]  
> Při použití Curl nebo jiné komunikace REST s WebHCat, je nutné ověřit požadavky poskytnutím HDInsight správce clusteru uživatelské jméno a heslo. Název clusteru je nutné použít jako součást identifikátoru URI, který slouží k odesílání požadavků na server.
>
> Rozhraní REST API je zabezpečeno pomocí [základního ověřování přístupu](https://en.wikipedia.org/wiki/Basic_access_authentication). Vždy byste měli provádět požadavky pomocí protokolu HTTPS, abyste zajistili, že vaše přihlašovací údaje budou bezpečně odeslány na server.

### <a name="curl"></a>Curl

1. Pro snadné použití nastavte níže uvedené proměnné. Tento příklad je založen na prostředí systému Windows, revidovat podle potřeby pro vaše prostředí.

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. Z příkazového řádku použijte následující příkaz k ověření, zda se můžete připojit ke clusteru HDInsight:

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Parametry použité v tomto příkazu jsou následující:

   * **-u**: Označuje uživatelské jméno a heslo použité k ověření požadavku
   * **-G**: Označuje, že tato operace je požadavek GET

   Začátek identifikátoru `https://CLUSTERNAME.azurehdinsight.net/templeton/v1`URI , je stejný pro všechny požadavky.

    Obdržíte odpověď podobnou následující JSON:

    ```output
    {"version":"v1","status":"ok"}
    ```

1. Chcete-li odeslat úlohu MapReduce, použijte následující příkaz. Podle potřeby upravte cestu k **jq.**

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    Konec IDENTIFIKÁTORU URI (/mapreduce/jar) informuje webhcat, že tento požadavek spustí úlohu MapReduce z třídy v souboru jar. Parametry použité v tomto příkazu jsou následující:

   * **-d** `-G` : se nepoužívá, takže požadavek je výchozí pro metodu POST. `-d`určuje hodnoty dat, které jsou odeslány s požadavkem.
     * **user.name**: Uživatel, který příkaz spouštěl
     * **jar**: Umístění jar souboru, který obsahuje třídu, která má být spuštěna
     * **třída**: Třída, která obsahuje logiku MapReduce
     * **arg**: Argumenty, které mají být předány úlohě MapReduce. V tomto případě vstupní textový soubor a adresář, které se používají pro výstup

    Tento příkaz by měl vrátit ID úlohy, které lze použít ke kontrole stavu úlohy:

       job_1415651640909_0026

1. Chcete-li zkontrolovat stav úlohy, použijte následující příkaz. Nahraďte `JOBID` hodnotu pro **skutečnou** hodnotou vrácenou v předchozím kroku. Podle potřeby revidovat umístění **jq.**

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. Pro snadné použití nastavte níže uvedené proměnné. Nahraďte `CLUSTERNAME` skutečným názvem clusteru. Spusťte příkaz a po zobrazení výzvy zadejte přihlašovací heslo clusteru.

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. Pomocí následujícího příkazu ověřte, zda se můžete připojit ke clusteru HDInsight:

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Obdržíte odpověď podobnou následující JSON:

    ```output
    {"version":"v1","status":"ok"}
    ```

1. Chcete-li odeslat úlohu MapReduce, použijte následující příkaz:

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Konec IDENTIFIKÁTORU URI (/mapreduce/jar) informuje webhcat, že tento požadavek spustí úlohu MapReduce z třídy v souboru jar. Parametry použité v tomto příkazu jsou následující:

    * **user.name**: Uživatel, který příkaz spouštěl
    * **jar**: Umístění jar souboru, který obsahuje třídu, která má být spuštěna
    * **třída**: Třída, která obsahuje logiku MapReduce
    * **arg**: Argumenty, které mají být předány úlohě MapReduce. V tomto případě vstupní textový soubor a adresář, které se používají pro výstup

   Tento příkaz by měl vrátit ID úlohy, které lze použít ke kontrole stavu úlohy:

       job_1415651640909_0026

1. Chcete-li zkontrolovat stav úlohy, použijte následující příkaz:

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

### <a name="both-methods"></a>Obě metody

1. Pokud je úloha dokončena, `SUCCEEDED`je vrácený stav .

1. Když se stav úlohy `SUCCEEDED`změní na , můžete načíst výsledky úlohy z úložiště objektů blob Azure. Parametr, `statusdir` který je předán s dotazem obsahuje umístění výstupního souboru. V tomto příkladu `/example/curl`je umístění . Tato adresa ukládá výstup úlohy ve výchozím `/example/curl`úložišti clusterů na adrese .

Tyto soubory můžete vypsat a stáhnout pomocí [rozhraní příkazového příkazu Konzumu Azure](/cli/azure/install-azure-cli). Další informace o použití rozhraní příkazového příkazového příkazu Azure pro práci s úložištěm objektů Blob Azure najdete [v tématu Úvodní příručka: Vytvoření, stažení a seznam objektů BLOB pomocí rozhraní PŘÍKAZOVÉHO PŘÍKAZOVÉHO NEBO VYPSat pomocí rozhraní PŘÍKAZOVÉHO PŘÍKAZOVÉHO PŘÍKAZU k Azure](../../storage/blobs/storage-quickstart-blobs-cli.md).

## <a name="next-steps"></a>Další kroky

Informace o dalších způsobech práce s Hadoopem na HDInsightu:

* [Použití MapReduce s Apache Hadoop na HDInsight](hdinsight-use-mapreduce.md)
* [Použití Apache Hive s Apache Hadoop na HDInsight](hdinsight-use-hive.md)

Další informace o rozhraní REST, který se používá v tomto článku, naleznete [webHcat odkaz](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
