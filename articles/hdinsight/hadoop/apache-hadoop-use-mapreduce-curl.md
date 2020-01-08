---
title: Použití MapReduce a kudrlinkou s Apache Hadoop ve službě HDInsight – Azure
description: Naučte se vzdáleně spouštět úlohy MapReduce pomocí Apache Hadoop ve službě HDInsight pomocí funkce kudrlinkou.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/27/2018
ms.openlocfilehash: 274d8dc80d9318aa3ddf4a904a5b623319ea01f4
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645000"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Spouštění úloh MapReduce s využitím Apache Hadoop ve službě HDInsight pomocí REST

Naučte se používat REST API Apache Hive WebHCat ke spouštění úloh MapReduce na Apache Hadoop clusteru HDInsight. Kudrlinkou se používá k předvedení, jak můžete s HDInsight pracovat pomocí nezpracovaných požadavků HTTP ke spouštění úloh MapReduce.

> [!NOTE]  
> Pokud jste již obeznámeni s používáním serverů Hadoop se systémem Linux, ale jste novinkou ke službě HDInsight, Projděte si informace [o tom, co potřebujete vědět o Apache Hadoop pro Linux v dokumentu HDInsight](../hdinsight-hadoop-linux-information.md) .


## <a id="prereq"></a>Požadavky

* Cluster Hadoop v HDInsight
* Prostředí Windows PowerShell nebo [oblé](https://curl.haxx.se/) a [JQ](https://stedolan.github.io/jq/)

## <a id="curl"></a>Spuštění úlohy MapReduce

> [!NOTE]  
> Při použití kudrlinkou nebo jakékoli jiné komunikace REST s WebHCat je nutné ověřit požadavky zadáním uživatelského jména a hesla správce clusteru HDInsight. Název clusteru musíte použít jako součást identifikátoru URI, který se používá k odesílání požadavků na server.
>
> REST API je zabezpečený pomocí [základního ověřování přístupu](https://en.wikipedia.org/wiki/Basic_access_authentication). Vždy byste měli vytvářet žádosti pomocí protokolu HTTPS, aby bylo zajištěno, že vaše přihlašovací údaje budou bezpečně odesílány na server.

1. Chcete-li nastavit přihlášení clusteru používané skripty v tomto dokumentu, použijte jeden z následujících příkazů:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Chcete-li nastavit název clusteru, použijte jeden z následujících příkazů:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Z příkazového řádku použijte následující příkaz k ověření, zda se můžete připojit ke clusteru HDInsight:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Dostanete odpověď podobnou následujícímu formátu JSON:

        {"status":"ok","version":"v1"}

    Parametry použité v tomto příkazu jsou následující:

   * **-u**: označuje uživatelské jméno a heslo použité k ověření žádosti.
   * **-G**: označuje, že tato operace je žádostí o získání.

   Začátek identifikátoru URI, `https://CLUSTERNAME.azurehdinsight.net/templeton/v1`, je stejný pro všechny požadavky.

4. Chcete-li odeslat úlohu MapReduce, použijte následující příkaz:

    ```bash
    JOBID=`curl -u $LOGIN -d user.name=$LOGIN -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar | jq .id`
    echo $JOBID
    ```

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

   * **-d**: `-G` se nepoužívá, proto je požadavek nastaven na výchozí metodu post. `-d` určuje hodnoty dat, které se odesílají spolu s požadavkem.
     * **User.Name**: uživatel, který spouští příkaz
     * **jar**: umístění souboru jar, který obsahuje třídu, která má být spuštěna.
     * **Třída**: třída, která obsahuje logiku MapReduce
     * **arg**: argumenty, které mají být předány do úlohy MapReduce. V tomto případě vstupní textový soubor a adresář, které se používají pro výstup

   Tento příkaz by měl vrátit ID úlohy, která se dá použít ke kontrole stavu úlohy:

       job_1415651640909_0026

5. Chcete-li zjistit stav úlohy, použijte následující příkaz:

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

    Pokud je úloha dokončena, vrátí se stav `SUCCEEDED`.

   > [!NOTE]  
   > Tato žádost o kudrlinkou vrátí dokument JSON s informacemi o úloze. JQ se používá k načtení pouze hodnoty stavu.

6. Až se stav úlohy změní na `SUCCEEDED`, můžete načíst výsledky úlohy z úložiště objektů BLOB v Azure. Parametr `statusdir`, který je předán s dotazem, obsahuje umístění výstupního souboru. V tomto příkladu je umístění `/example/curl`. Tato adresa ukládá výstup úlohy do výchozího úložiště v clusterech na `/example/curl`.

Tyto soubory můžete zobrazit a stáhnout pomocí rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Další informace o práci s objekty BLOB z Azure CLI najdete v článku [použití Azure CLI s Azure Storage](../../storage/common/storage-azure-cli.md#create-and-manage-blobs) dokumentem.

## <a id="nextsteps"></a>Další kroky

Obecné informace o úlohách MapReduce v HDInsight:

* [Použití MapReduce s Apache Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Informace o dalších způsobech, jak můžete pracovat se systémem Hadoop ve službě HDInsight:

* [Použití Apache Hive s Apache Hadoop v HDInsight](hdinsight-use-hive.md)
* [Použití systému Apache prasete s Apache Hadoop v HDInsight](hdinsight-use-pig.md)

Další informace o rozhraní REST, které se používá v tomto článku, najdete v [referenčních](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)informacích k WebHCat.
