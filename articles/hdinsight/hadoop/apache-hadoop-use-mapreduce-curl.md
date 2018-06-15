---
title: Použití MapReduce a Curl s Hadoop v HDInsight - Azure | Microsoft Docs
description: Naučte se vzdáleně spouštět úlohy MapReduce s Hadoop v HDInsight pomocí Curl.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: bc6daf37-fcdc-467a-a8a8-6fb2f0f773d1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: eeecdb6432c4ab13b051c9a9dba1e7f14ce40f91
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400210"
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-rest"></a>Spuštění úloh MapReduce s Hadoop v HDInsight pomocí REST

Naučte se používat rozhraní REST API WebHCat ke spuštění úloh MapReduce systému Hadoop v clusteru HDInsight. Curl se používá k ukazují, jak můžete pracovat s HDInsight pomocí nezpracovaná požadavků HTTP ke spuštění úloh MapReduce.

> [!NOTE]
> Pokud jste již obeznámeni s pomocí serverů se systémem Linux Hadoop, ale jsou pro vás nové do HDInsight, najdete v článku [co potřebujete vědět o systémem Linux Hadoop v HDInsight](../hdinsight-hadoop-linux-information.md) dokumentu.


## <a id="prereq"></a>Požadavky

* Na clusteru HDInsight Hadoop
* Prostředí Windows PowerShell nebo [Curl](http://curl.haxx.se/) a [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Spustit úlohu MapReduce

> [!NOTE]
> Pokud používáte Curl nebo jinou komunikaci REST s WebHCat, je třeba ověřit žádosti zadáním uživatelského jména správce clusteru HDInsight a hesla. Název clusteru musí používat jako součást identifikátoru URI, který se používá k odesílání požadavků na server.
>
> Rozhraní API REST je zabezpečená pomocí [ověřování přístupu k základní](http://en.wikipedia.org/wiki/Basic_access_authentication). Vždy doporučujeme provádět požadavky pomocí protokolu HTTPS k zajištění, že vaše přihlašovací údaje jsou bezpečně odeslat na server.

1. Pokud chcete nastavit přihlášení clusteru, který je používán skripty v tomto dokumentu, použijte jednu z followig příkazy:

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

    Můžete zobrazit odpověď podobná následujícím kódu JSON:

        {"status":"ok","version":"v1"}

    Parametry použité v tomto příkazu jsou následující:

   * **-u**: Určuje uživatelské jméno a heslo použité k ověření žádosti
   * **-G**: označuje, že tato operace je požadavek GET.

   Na začátek identifikátor URI **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, je stejný pro všechny požadavky.

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

    Konec identifikátor URI (/ mapreduce/jar) informuje WebHCat, že tento požadavek spustí úlohu MapReduce z třídy v souboru jar. Parametry použité v tomto příkazu jsou následující:

   * **-d**: `-G` se nepoužívá, takže požadavek výchozí metodu POST. `-d` Určuje datových hodnot, které se odesílají s požadavkem.
    * **User.Name**: uživatel, který spouští příkaz
    * **JAR**: umístění na soubor jar, který obsahuje třídu být spustili
    * **Třída**: třídu, která obsahuje logiku MapReduce
    * **arg**: argumenty, které mají být předána do úlohy MapReduce. V tomto případě, vstupní textového souboru a adresáře, které se používají pro výstup

   Tento příkaz by měla vrátit ID úlohy, který slouží ke kontrole stavu úlohy:

       job_1415651640909_0026

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

    Pokud tato úloha skončí, vrácená stav `SUCCEEDED`.

   > [!NOTE]
   > Tento požadavek Curl vrátí dokumentu JSON s informacemi o úloze. Jq se používá k načtení jenom hodnotu stavu.

6. Pokud se změnila stav úlohy na `SUCCEEDED`, můžete načíst výsledky úlohy z Azure Blob storage. `statusdir` Parametr, který je předán s dotaz obsahuje umístění výstupního souboru. V tomto příkladu je umístění `/example/curl`. Tuto adresu ukládá výstup úlohy do úložiště výchozí clustery na `/example/curl`.

Můžete zobrazit seznam a stáhnout tyto soubory pomocí [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Další informace o práci s objekty BLOB z příkazového řádku Azure najdete v tématu [použití Azure CLI 2.0 s Azure Storage](../../storage/common/storage-azure-cli.md#create-and-manage-blobs) dokumentu.

## <a id="nextsteps"></a>Další kroky

Obecné informace o úloh MapReduce v HDInsight:

* [Používání nástroje MapReduce s Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Informace o jiných způsobech můžete pracovat s Hadoop v HDInsight:

* [Použijte Hive s Hadoop v HDInsight](hdinsight-use-hive.md)
* [Použijte Pig s Hadoop v HDInsight](hdinsight-use-pig.md)

Další informace o rozhraní REST, který se používá v tomto článku najdete v tématu [WebHCat odkaz](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
