---
title: Použití MapReduce a kudrlinkou s Apache Hadoop ve službě HDInsight – Azure
description: Naučte se vzdáleně spouštět úlohy MapReduce pomocí Apache Hadoop ve službě HDInsight pomocí funkce kudrlinkou.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: 407db727f521ea7731f0cbdbdd05c4338c9f452e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86207725"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Spouštění úloh MapReduce s využitím Apache Hadoop ve službě HDInsight pomocí REST

Naučte se používat REST API Apache Hive WebHCat ke spouštění úloh MapReduce na Apache Hadoop clusteru HDInsight. Kudrlinkou se používá k předvedení, jak můžete s HDInsight pracovat pomocí nezpracovaných požadavků HTTP ke spouštění úloh MapReduce.

> [!NOTE]  
> Pokud jste již obeznámeni s používáním serverů Hadoop se systémem Linux, ale jste novinkou ke službě HDInsight, Projděte si informace [o tom, co potřebujete vědět o Apache Hadoop pro Linux v dokumentu HDInsight](../hdinsight-hadoop-linux-information.md) .

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Hadoop v HDInsight. Další informace najdete v tématu [Vytvoření clusterů Apache Hadoop pomocí Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

Buď:
  * Prostředí Windows PowerShell nebo
  * [Otočení](https://curl.haxx.se/) pomocí [JQ](https://stedolan.github.io/jq/)

## <a name="run-a-mapreduce-job"></a>Spuštění úlohy MapReduce

> [!NOTE]  
> Při použití kudrlinkou nebo jakékoli jiné komunikace REST s WebHCat je nutné ověřit požadavky zadáním uživatelského jména a hesla správce clusteru HDInsight. Název clusteru musíte použít jako součást identifikátoru URI, který se používá k odesílání požadavků na server.
>
> REST API je zabezpečený pomocí [základního ověřování přístupu](https://en.wikipedia.org/wiki/Basic_access_authentication). Vždy byste měli vytvářet žádosti pomocí protokolu HTTPS, aby bylo zajištěno, že vaše přihlašovací údaje budou bezpečně odesílány na server.

### <a name="curl"></a>Curl

1. Pro snadné použití nastavte následující proměnné. Tento příklad je založený na prostředí Windows, podle potřeby si můžete prohlédnout v prostředí.

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. Z příkazového řádku použijte následující příkaz k ověření, zda se můžete připojit ke clusteru HDInsight:

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Parametry použité v tomto příkazu jsou následující:

   * **-u**: označuje uživatelské jméno a heslo použité k ověření žádosti.
   * **-G**: označuje, že tato operace je žádostí o získání.

   Začátek identifikátoru URI `https://CLUSTERNAME.azurehdinsight.net/templeton/v1` je stejný pro všechny požadavky.

    Dostanete odpověď podobnou následujícímu formátu JSON:

    ```json
    {"version":"v1","status":"ok"}
    ```

1. K odeslání úlohy MapReduce použijte následující příkaz. Podle potřeby upravte cestu k **JQ** .

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    Konec identifikátoru URI (/MapReduce/jar) oznamuje WebHCat, že tento požadavek spustí úlohu MapReduce ze třídy v souboru jar. Parametry použité v tomto příkazu jsou následující:

   * **-d**: `-G` nepoužívá se, takže požadavek využije výchozí metoda post. `-d` Určuje hodnoty dat, které se odesílají spolu s požadavkem.
     * **User.Name**: uživatel, který spouští příkaz
     * **jar**: umístění souboru jar, který obsahuje třídu, která má být spuštěna.
     * **Třída**: třída, která obsahuje logiku MapReduce
     * **arg**: argumenty, které mají být předány do úlohy MapReduce. V tomto případě vstupní textový soubor a adresář, které se používají pro výstup

    Tento příkaz by měl vracet ID úlohy, která se dá použít ke kontrole stavu úlohy: `job_1415651640909_0026` .

1. Chcete-li zjistit stav úlohy, použijte následující příkaz. Nahraďte hodnotu pro `JOBID` **skutečnou** hodnotou vrácenou v předchozím kroku. Podle potřeby upravte umístění **JQ** .

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. Pro snadné použití nastavte následující proměnné. Nahraďte `CLUSTERNAME` skutečným názvem clusteru. Spusťte příkaz a po zobrazení výzvy zadejte heslo pro přihlášení ke clusteru.

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. Pomocí následujícího příkazu ověřte, že se můžete připojit ke clusteru HDInsight:

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Dostanete odpověď podobnou následujícímu formátu JSON:

    ```json
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

    Konec identifikátoru URI (/MapReduce/jar) oznamuje WebHCat, že tento požadavek spustí úlohu MapReduce ze třídy v souboru jar. Parametry použité v tomto příkazu jsou následující:

    * **User.Name**: uživatel, který spouští příkaz
    * **jar**: umístění souboru jar, který obsahuje třídu, která má být spuštěna.
    * **Třída**: třída, která obsahuje logiku MapReduce
    * **arg**: argumenty, které mají být předány do úlohy MapReduce. V tomto případě vstupní textový soubor a adresář, které se používají pro výstup

   Tento příkaz by měl vracet ID úlohy, která se dá použít ke kontrole stavu úlohy: `job_1415651640909_0026` .

1. Chcete-li zjistit stav úlohy, použijte následující příkaz:

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

1. Pokud je úloha dokončena, je vrácen stav `SUCCEEDED` .

1. Když se stav úlohy změní na `SUCCEEDED` , můžete načíst výsledky úlohy z úložiště objektů BLOB v Azure. `statusdir`Parametr, který je předán s dotazem, obsahuje umístění výstupního souboru. V tomto příkladu je umístění `/example/curl` . Tato adresa ukládá výstup úlohy do výchozího úložiště clusterů na adrese `/example/curl` .

Tyto soubory můžete zobrazit a stáhnout pomocí rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli). Další informace o použití rozhraní příkazového řádku Azure pro práci s úložištěm objektů BLOB v Azure najdete v tématu [rychlý Start: vytvoření, stažení a výpis objektů BLOB pomocí Azure CLI](../../storage/blobs/storage-quickstart-blobs-cli.md).

## <a name="next-steps"></a>Další kroky

Informace o dalších způsobech, jak můžete pracovat se systémem Hadoop ve službě HDInsight:

* [Použití MapReduce s Apache Hadoop v HDInsight](hdinsight-use-mapreduce.md)
* [Použití Apache Hive s Apache Hadoop v HDInsight](hdinsight-use-hive.md)

Další informace o rozhraní REST, které se používá v tomto článku, najdete v [referenčních](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)informacích k WebHCat.
