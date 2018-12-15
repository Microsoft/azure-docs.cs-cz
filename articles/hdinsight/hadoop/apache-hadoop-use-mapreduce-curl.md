---
title: Použití MapReduce a Curl s Apache Hadoop v HDInsight – Azure
description: Zjistěte, jak pro vzdáleně spouštět úlohy MapReduce s Apache Hadoop v HDInsight pomocí příkazu Curl.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: ff905f34ab63027e9708082c4690e4275220854f
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53406789"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Spuštění úlohy mapreduce je možné s Apache Hadoop v HDInsight pomocí rozhraní REST

Zjistěte, jak použít rozhraní Apache Hive WebHCat REST API ke spuštění úlohy mapreduce je možné na Apache Hadoopu v clusteru HDInsight. Curl slouží k předvedení toho, jak můžete pracovat s HDInsight pomocí nezpracované požadavků HTTP pro spouštění úloh MapReduce.

> [!NOTE]  
> Pokud jste už obeznámení s pomocí serverů se systémem Linux Hadoop, ale teprve začínáte HDInsight, najdete v článku [co potřebujete vědět o založených na Linuxu Apache Hadoop v HDInsight](../hdinsight-hadoop-linux-information.md) dokumentu.


## <a id="prereq"></a>Požadavky

* V clusteru HDInsight Hadoop
* Prostředí Windows PowerShell nebo [Curl](https://curl.haxx.se/) a [jq](https://stedolan.github.io/jq/)

## <a id="curl"></a>Spustit úlohu MapReduce

> [!NOTE]  
> Pokud používáte Curl nebo jinou komunikaci REST s WebHCat, je třeba ověřit žádosti zadáním uživatelského jména správce clusteru HDInsight a heslo. Název clusteru musí používat jako součást identifikátoru URI, který se používá k odesílání požadavků na server.
>
> Rozhraní REST API se šifrují pomocí [ověřování přístupu basic](https://en.wikipedia.org/wiki/Basic_access_authentication). Vždy doporučujeme provádět požadavky pomocí protokolu HTTPS k zajištění, že vaše přihlašovací údaje se bezpečně odesílají na server.

1. Nastavení přihlášení ke clusteru, který se používá ve skriptech v tomto dokumentu, použijte jeden z příkazů followig:

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

    Můžete zobrazit odpověď podobná následující JSON:

        {"status":"ok","version":"v1"}

    Parametry použité v tomto příkazu jsou následující:

   * **-u**: Určuje uživatelské jméno a heslo použité pro ověření žádosti
   * **-G**: Označuje, že tato operace je požadavek GET

   Počáteční identifikátor URI **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, je stejný pro všechny požadavky.

4. Odeslat úlohu MapReduce, použijte následující příkaz:

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

    Konec identifikátoru URI (/ mapreduce/jar) říká WebHCat, že tento požadavek spustí úlohu MapReduce z třídy v souboru jar. Parametry použité v tomto příkazu jsou následující:

   * **-d**: `-G` nepoužívá, tak požadavek výchozí metodu POST. `-d` Určuje hodnoty dat, které se odesílají s požadavkem.
    * **User.Name**: Uživatel, který spouští příkaz
    * **soubor JAR**: Umístění souboru jar, který obsahuje třídu spustit
    * **Třída**: Třídy, která obsahuje logiku MapReduce
    * **arg**: Argumenty, které mají být předány úlohu MapReduce. V tomto případě, vstupního textového souboru a adresáře, který se používá pro výstup

   Tento příkaz by měl vrátit ID úlohy, který slouží ke kontrole stavu úlohy:

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

    Pokud je úloha dokončena, stav vrácený je `SUCCEEDED`.

   > [!NOTE]  
   > Tento požadavek Curl vrátí dokument JSON s informacemi o úloze. Jq slouží k načtení jenom hodnoty stavu.

6. Když má stav úlohy změní na `SUCCEEDED`, můžete načíst výsledky úlohy z úložiště objektů Blob v Azure. `statusdir` Parametr, který je předán s dotazem obsahuje umístění výstupního souboru. V tomto příkladu je umístění `/example/curl`. Tuto adresu ukládá výstup úlohy do clusterů výchozí úložiště na `/example/curl`.

Můžete seznam a stáhnout tyto soubory [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Další informace o práci s objekty BLOB z příkazového řádku Azure, najdete v článku [pomocí Azure CLI s Azure Storage](../../storage/common/storage-azure-cli.md#create-and-manage-blobs) dokumentu.

## <a id="nextsteps"></a>Další kroky

Obecné informace o úlohy mapreduce je možné v HDInsight:

* [Použití MapReduce se službou Apache Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Informace o jiných způsobech, jakými můžete pracovat s Hadoop v HDInsight:

* [Použití Apache Hivu s Apache Hadoop v HDInsight](hdinsight-use-hive.md)
* [Použití Apache Pig s Apache Hadoop v HDInsight](hdinsight-use-pig.md)

Další informace o rozhraní REST, který se používá v tomto článku najdete v tématu [WebHCat odkaz](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
