---
title: Použití Pigu Apache Hadoop s využitím REST v HDInsight – Azure
description: Zjistěte, jak spouštět úlohy Pig Latin na cluster Apache Hadoop v Azure HDInsight pomocí REST.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: cb96933569c18b8c70d8991c9ba19f9f3e1243e6
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633695"
---
# <a name="run-pig-jobs-with-apache-hadoop-on-hdinsight-by-using-rest"></a>Spuštění úlohy Pig s Apache Hadoop v HDInsight pomocí REST

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Zjistěte, jak spouštět úlohy Apache Pig Latin tím, že požadavky REST do clusteru Azure HDInsight. Curl slouží k předvedení jak mohou komunikovat s HDInsight pomocí rozhraní REST API WebHCat.

> [!NOTE]
> Pokud jste obeznámeni s pomocí serverů se systémem Linux Apache Hadoop, ale teprve začínáte HDInsight, naleznete v tématu [tipy k Linuxovým systémem HDInsight](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Požadavky

* Cluster Azure HDInsight (Hadoop v HDInsight) (založené na Linuxu nebo na základě Windows)

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Spuštění úlohy Pig pomocí Curl

> [!NOTE]
> Rozhraní REST API je zabezpečeno pomocí [ověřování přístupu basic](http://en.wikipedia.org/wiki/Basic_access_authentication). Vždy proveďte požadavky pomocí Secure HTTP (HTTPS) k zajištění, že vaše přihlašovací údaje se bezpečně odesílají na server.
>
> Pokud používáte příkazy v této části, nahraďte `USERNAME` uživatelem pro ověření clusteru a nahraďte `PASSWORD` heslem pro uživatelský účet. Nahraďte `CLUSTERNAME` názvem svého clusteru.
>


1. Z příkazového řádku použijte následující příkaz k ověření, zda se můžete připojit ke clusteru HDInsight:

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Mělo by se zobrazit následující odpověď JSON:

        {"status":"ok","version":"v1"}

    Parametry použité v tomto příkazu jsou následující:

    * **-u**: uživatelské jméno a heslo použité pro ověření žádosti
    * **-G**: Určuje, že tento požadavek je požadavek GET

     Počáteční adresa URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, je stejný pro všechny požadavky. Cesta, **/status**, označuje, že žádost je vrátit stav WebHCat (také známé jako Templeton) pro server.

2. Odeslání úlohy Pig Latin ke clusteru pomocí následujícího kódu:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    Parametry použité v tomto příkazu jsou následující:

    * **-d**: protože `-G` se nepoužívá výchozí nastavení požadavku pro metodu POST. `-d` Určuje hodnoty dat, které se odesílají s požadavkem.

    * **User.Name**: uživatel, který spouští příkaz
    * **Spustit**: The Pig Latin příkazy ke spuštění
    * **statusdir**: adresáře, který stavu pro tuto úlohu je zapsán do

    > [!NOTE]
    > Všimněte si, že jsou nahrazené mezery v příkazech Pig Latin `+` znaků při použití s Curl.

    Tento příkaz by měl vrátit, který slouží ke kontrole stavu úlohy, například ID úlohy:

        {"id":"job_1415651640909_0026"}

3. Pokud chcete zkontrolovat stav úlohy, použijte následující příkaz

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     Nahraďte `JOBID` pomocí hodnoty vrácené v předchozím kroku. Například, pokud se návratová hodnota `{"id":"job_1415651640909_0026"}`, pak `JOBID` je `job_1415651640909_0026`.

    Pokud úloha dokončí, je stav **SUCCEEDED**.

    > [!NOTE]
    > Tento požadavek Curl vrátí dokument JavaScript Object Notation (JSON) se informace o úloze a jq slouží k načtení jenom hodnoty stavu.

## <a id="results"></a>Zobrazení výsledků

Když má stav úlohy změní na **SUCCEEDED**, můžete načíst výsledky úlohy. `statusdir` Parametr předaný s dotazem obsahuje umístění výstupního souboru; v takovém případě `/example/pigcurl`.

HDInsight můžete použít Azure Storage nebo Azure Data Lake Store jako výchozího datového úložiště. Existují různé způsoby, jak získat data v závislosti na tom, co používáte. Další informace najdete v části úložiště [Linuxovým systémem HDInsight informace](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-store) dokumentu.

## <a id="summary"></a>Shrnutí

Jak je ukázáno v tomto dokumentu, můžete spouštět, monitorovat a zobrazit výsledky úlohy Pig ve vašem clusteru HDInsight nezpracovaná požadavku HTTP.

Další informace o rozhraní REST použité v tomto článku najdete v tématu [WebHCat odkaz](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Další kroky

Obecné informace o Pig v HDInsight:

* [Použití Pigu se systémem Hadoop v HDInsight](hdinsight-use-pig.md)

Informace o jiných způsobech, jakými můžete pracovat s Hadoop v HDInsight:

* [Použití Hivu s Hadoopem v HDInsight](hdinsight-use-hive.md)
* [Použití MapReduce se systémem Hadoop v HDInsight](hdinsight-use-mapreduce.md)
