---
title: Použijte Hadoop Pig s REST v HDInsight - Azure | Microsoft Docs
description: Zjistěte, jak se použije ke spuštění úlohy Pig Latin na cluster Hadoop v prostředí Azure HDInsight REST.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: ed5e10d1-4f47-459c-a0d6-7ff967b468c4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: 4883794261116abf4925e7e4e9a8df14626c7a71
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-rest"></a>Spuštění úlohy Pig s Hadoop v HDInsight pomocí REST

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Zjistěte, jak spouštět úlohy Pig Latin tím, že požadavky REST do clusteru Azure HDInsight. Curl se používá k ukazují, jak mohou komunikovat s HDInsight pomocí rozhraní REST API WebHCat.

> [!NOTE]
> Pokud jste obeznámeni s pomocí serverů se systémem Linux Hadoop, ale jsou nové do HDInsight, projděte si téma [systémem Linux HDInsight tipy](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Požadavky

* Clusteru Azure HDInsight (Hadoop v HDInsight) (systémem Linux nebo systému Windows)

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Spuštění úlohy Pig pomocí Curl

> [!NOTE]
> Rozhraní API REST je zabezpečeno pomocí [ověřování přístupu k základní](http://en.wikipedia.org/wiki/Basic_access_authentication). Vždy proveďte požadavky pomocí Secure HTTP (HTTPS) k zajištění, že vaše přihlašovací údaje jsou bezpečně odeslat na server.
>
> Když pomocí příkazů v této části, vyměňte `USERNAME` uživatelem pro ověření clusteru a nahraďte `PASSWORD` s heslem pro uživatelský účet. Nahraďte `CLUSTERNAME` názvem svého clusteru.
>


1. Z příkazového řádku použijte následující příkaz k ověření, zda se můžete připojit ke clusteru HDInsight:

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Mělo by se zobrazit následující odpověď JSON:

        {"status":"ok","version":"v1"}

    Parametry použité v tomto příkazu jsou následující:

    * **-u**: uživatelské jméno a heslo použité k ověření žádosti
    * **-G**: označuje, že tento požadavek je požadavek GET.

     Na začátek adresu URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, je stejný pro všechny požadavky. Cesta **/status**, označuje, že požadavek je vrátit stav WebHCat (také známé jako Templeton) pro server.

2. Použijte následující kód se odeslat úlohu Pig Latin do clusteru:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    Parametry použité v tomto příkazu jsou následující:

    * **-d**: protože `-G` se nepoužívá, použije se výchozí hodnota žádost na metodu POST. `-d` Určuje datových hodnot, které se odesílají s požadavkem.

    * **User.Name**: uživatel, který spouští příkaz
    * **spuštění**: příkazy Pig Latin provést
    * **statusdir**: adresář, který stav pro tuto úlohu je zapsán do

    > [!NOTE]
    > Všimněte si, že jsou nahrazovány prostory v příkazech Pig Latin `+` znak při použití s Curl.

    Tento příkaz by měla vrátit ID úlohy, které je možné zkontrolovat stav úlohy, například:

        {"id":"job_1415651640909_0026"}

3. Pokud chcete zkontrolovat stav úlohy, použijte následující příkaz

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     Nahraďte `JOBID` se hodnota vrácená v předchozím kroku. Například, pokud byl návratovou hodnotu `{"id":"job_1415651640909_0026"}`, pak `JOBID` je `job_1415651640909_0026`.

    Pokud se úloha dokončí, je stav **úspěšné**.

    > [!NOTE]
    > Tento požadavek Curl vrátí dokument JavaScript Object Notation (JSON) s informace o úloze a jq se používá k načtení jenom hodnotu stavu.

## <a id="results"></a>Zobrazení výsledků

Pokud se změnila stav úlohy na **úspěšné**, můžete načíst výsledky úlohy. `statusdir` Parametr předaný s dotaz obsahuje umístění výstupního souboru; v tomto případě `/example/pigcurl`.

HDInsight, můžete použít jako výchozího datového úložiště Azure Storage nebo Azure Data Lake Store. Existují různé způsoby, jak získat na data, podle toho, který používáte. Další informace najdete v části úložiště [HDInsight se systémem Linux informace](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-store) dokumentu.

## <a id="summary"></a>Shrnutí

Jak je ukázáno v tomto dokumentu, můžete spouštět, monitorovat a zobrazit výsledky úlohy Pig v clusteru HDInsight nezpracovaná požadavek HTTP.

Další informace o rozhraní REST používané v tomto článku najdete v tématu [WebHCat odkaz](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Další kroky

Obecné informace o Pig v HDInsight:

* [Použijte Pig s Hadoop v HDInsight](hdinsight-use-pig.md)

Informace o jiných způsobech můžete pracovat s Hadoop v HDInsight:

* [Použijte Hive s Hadoop v HDInsight](hdinsight-use-hive.md)
* [Používání nástroje MapReduce s Hadoop v HDInsight](hdinsight-use-mapreduce.md)
