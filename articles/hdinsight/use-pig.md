---
title: Použití Apache Pigu
titleSuffix: Azure HDInsight
description: Přečtěte si, jak používat Pig s Apache Hadoop na HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: ea960a92aee1c9447bb12d27cffdc42de9fd907a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672119"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Použijte Apache Pig s Apache Hadoop na HDInsight

Přečtěte si, jak používat [Apache Pig](https://pig.apache.org/) s HDInsight.

Apache Pig je platforma pro vytváření programů pro Apache Hadoop pomocí procedurálního jazyka známého jako *Pig Latin*. Pig je alternativou k Javě pro vytváření řešení *MapReduce* a je součástí Azure HDInsight. V následující tabulce můžete zjistit různé způsoby, jak lze pig použít s HDInsight:

## <a name="why-use-apache-pig"></a><a id="why"></a>Proč používat Apache Pig

Jednou z výzev zpracování dat pomocí MapReduce v Hadoopu je implementace logiky zpracování pomocí pouze mapy a funkce reduce. Pro komplexní zpracování je často potřeba rozdělit zpracování na více operací MapReduce, které jsou zřetězené dohromady, aby bylo dosaženo požadovaného výsledku.

Pig umožňuje definovat zpracování jako řadu transformací, které data protéká k vytvoření požadovaného výstupu.

Jazyk pig latinka umožňuje popsat tok dat z nezpracovaného vstupu, přes jednu nebo více transformací, k vytvoření požadovaného výstupu. Pig Latinské programy následovat tento obecný vzor:

* **Načíst**: Čtení dat, se kterými se má manipulovat ze systému souborů.

* **Transformace**: Manipulujte s daty.

* **Výpis nebo uložení**: Výstup dat na obrazovku nebo uložit pro zpracování.

### <a name="user-defined-functions"></a>Uživatelsky definované funkce

Pig Latin také podporuje uživatelem definované funkce (UDF), které umožňují vyvolat externí komponenty, které implementují logiku, kterou je obtížné modelovat v prasečí latince.

Další informace o prasečí latince viz [Pig Latin Skáčná příručka 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) a Pig [Latinská referenční příručka 2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

## <a name="example-data"></a><a id="data"></a>Příklad dat

HDInsight poskytuje různé ukázkové datové sady, které jsou uloženy v adresářích `/example/data` a. `/HdiSamples` Tyto adresáře jsou ve výchozím úložišti pro váš cluster. Příklad Prase v tomto dokumentu používá soubor `/example/data/sample.log` *log4j* od společnosti .

Každý protokol uvnitř souboru se skládá z `[LOG LEVEL]` řádku polí, které obsahuje pole pro zobrazení typu a závažnosti, například:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

V předchozím příkladu je úroveň protokolu CHYBA.

> [!NOTE]  
> Můžete také vygenerovat soubor log4j pomocí nástroje pro protokolování [Apache Log4j](https://en.wikipedia.org/wiki/Log4j) a pak tento soubor nahrát do objektu blob. Pokyny [najdete v tématu Upload Data to HDInsight.](hdinsight-upload-data.md) Další informace o tom, jak se objekty BLOB ve službě Azure používají s HDInsightem, najdete v [tématu Použití azure blob storage s HDInsight](hdinsight-hadoop-use-blob-storage.md).

## <a name="example-job"></a><a id="job"></a>Příklad úlohy

Následující úloha Pig `sample.log` Latin načte soubor z výchozího úložiště pro váš cluster HDInsight. Poté provede řadu transformací, které vedou k počtu, kolikrát došlo k úrovni protokolu ve vstupních datech. Výsledky jsou zapsány do STDOUT.

    ```
    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;
    ```

Následující obrázek znázorňuje souhrn toho, co každá transformace provádí s daty.

![Grafické znázornění transformací][image-hdi-pig-data-transformation]

## <a name="run-the-pig-latin-job"></a><a id="run"></a>Spuštění úlohy Pig Latin

HDInsight můžete spustit pig latinky pomocí různých metod. V následující tabulce se můžete rozhodnout, která metoda je pro vás vhodná, a potom postupujte podle odkazu na návod.

## <a name="pig-and-sql-server-integration-services"></a>Integrační služby pig a SQL Server

Ke spuštění úlohy Pig můžete použít službu SQL Server Integration Services (SSIS). Sada Azure Feature Pack pro SSIS poskytuje následující komponenty, které fungují s úlohami Pig na HDInsightu.

* [Úloha prasátka Azure HDInsight][pigtask]

* [Správce připojení předplatného Azure][connectionmanager]

Další informace o azure feature pack pro SSIS [zde][ssispack].

## <a name="next-steps"></a><a id="nextsteps"></a>Další kroky

Teď, když jste se naučili používat Pig s HDInsight, použijte následující odkazy k prozkoumání dalších způsobů práce s Azure HDInsight.

* [Nahrání dat do služby HDInsight](hdinsight-upload-data.md)
* [Použití Apache Hive s HDInsight](./hadoop/hdinsight-use-hive.md)
* [Použití Apache Sqoop s HDInsight](hdinsight-use-sqoop.md)
* [Použijte úlohy MapReduce s HDInsight](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: https://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: https://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: https://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/use-pig/hdi-data-transformation.gif
