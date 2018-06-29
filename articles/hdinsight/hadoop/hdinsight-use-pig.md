---
title: Použijte Hadoop Pig v HDInsight | Microsoft Docs
description: Další informace o použití Pig s Hadoop v HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: acfeb52b-4b81-4a7d-af77-3e9908407404
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: d030bdaf4c68a7e2ec9d11cdd8a2f55eccfbd67d
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081163"
---
# <a name="use-pig-with-hadoop-on-hdinsight"></a>Použijte Pig s Hadoop v HDInsight

Další informace o použití [Apache Pig](http://pig.apache.org/) s HDInsight.

Pig je platforma pro vytváření programů pro Hadoop pomocí procedurální jazyka známé jako *Pig Latin*. Pig je alternativa k Java k vytváření *MapReduce* řešení a je součástí Azure HDInsight. Následující tabulku použijte ke zjištění různých způsobech, kterými vepřových lze použít s HDInsight:

| **Použít** Pokud chcete... | ...an **interaktivní** prostředí | ...**batch** zpracování | ...při to **clusteru operačního systému** | ...from to **klientský operační systém** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X nebo systému Windows |
| [REST API](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux nebo Windows |Linux, Unix, Mac OS X nebo systému Windows |
| [Sada .NET SDK pro Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux nebo Windows |Windows (prozatím) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux nebo Windows |Windows |

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="why"></a>Proč používat Pig

Jedním z problémů zpracování dat pomocí MapReduce v Hadoop je implementace logika zpracování pomocí pouze mapu a snižte funkce. Komplexní zpracování, je často nutné na přerušení zpracování do více operací MapReduce, které jsou zřetězené dohromady a dosáhnout požadovaného výsledku.

Pig umožňuje definovat zpracování jako řadu transformace, které se data prochází k vytvoření požadované výstup.

Jazyka Pig Latin můžete k popisu toku dat z nezpracovaná vstupu prostřednictvím jeden nebo více transformace, k vytvoření požadované výstup. Pig Latin programy postupujte podle tohoto vzoru Obecné:

* **Zatížení**: čtení dat manipulaci s těmito ze systému souborů

* **Transformace**: manipulovat s daty

* **Výpis stavu nebo ukládat**: výstupní data na obrazovku nebo ho uložit pro zpracování

### <a name="user-defined-functions"></a>Uživatelem definované funkce

Pig Latin také podporuje uživatelsky definované funkce (UDF), která umožňuje vyvolání externí součásti, které implementují logiku, která je obtížné model v Pig Latin.

Další informace o Pig Latin najdete v tématu [Pig Latin odkazu ruční 1](http://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) a [Pig Latin odkaz ruční 2](http://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

Příklad použití UDF s Pig najdete v následujících dokumentech:

* [Pig v HDInsight pomocí DataFu](apache-hadoop-use-pig-datafu-udf.md) -DataFu je kolekce užitečné UDF udržované Apache
* [Používat jazyk Python s Pig a Hive v HDInsight](python-udf-hdinsight.md)
* [Použití jazyka C# s Hive a Pig v HDInsight](apache-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>Příklad dat

HDInsight poskytuje různé příklad datových sad, které jsou uloženy v `/example/data` a `/HdiSamples` adresáře. Tyto adresáře jsou ve výchozím nastavení úložiště pro cluster. Pig příklad v tomto dokumentu používá *log4j* souboru z `/example/data/sample.log`.

Každý protokol uvnitř souboru se skládá z řádku pole, která obsahuje `[LOG LEVEL]` pole, které chcete zobrazit typ a závažnost, například:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

V předchozím příkladu je úroveň protokolu chyba.

> [!NOTE]
> Můžete také vygenerovat soubor log4j pomocí [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) protokolování nástroj a pak tento soubor nahrajte do objektu blob služby. V tématu [nahrát Data do HDInsight](../hdinsight-upload-data.md) pokyny. Další informace o použití objektů BLOB v úložišti Azure s HDInsight naleznete v tématu [použití Azure Blob Storage s HDInsight](../hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Příklad úloh

Načte následující úlohy Pig Latin `sample.log` soubor z výchozí úložiště pro váš cluster HDInsight. Pak provede řady transformací, jejichž výsledkem počet kolikrát vstupních dat došlo k chybě každou úroveň protokolu. Výsledky se zapisují do STDOUT.

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

Následující obrázek zobrazuje souhrn každý transformace nemá k datům.

![Grafické znázornění transformací][image-hdi-pig-data-transformation]

## <a id="run"></a>Spustit úlohu Pig Latin

HDInsight můžete spustit úlohy Pig Latin pomocí různých metod. Následující tabulku použijte k rozhodování, jakou metodu je pro vás nejvhodnější a potom klepněte na odkaz návod.

| **Použít** Pokud chcete... | ...an **interaktivní** prostředí | ...**batch** zpracování | ...při to **clusteru operačního systému** | ...from to **klienta** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X nebo systému Windows |
| [curl](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux nebo Windows |Linux, Unix, Mac OS X nebo systému Windows |
| [Sada .NET SDK pro Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux nebo Windows |Windows (prozatím) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux nebo Windows |Windows |

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="pig-and-sql-server-integration-services"></a>Pig a SQL Server Integration Services

Integrace služby SSIS (SQL Server) můžete použít ke spuštění úlohy Pig. Azure Feature Pack pro službu SSIS poskytuje následující součásti, které práce s úlohami Pig v HDInsight.

* [Úloha Azure HDInsight Pig][pigtask]

* [Správce připojení k Azure předplatného][connectionmanager]

Další informace o Azure Feature Pack pro služby SSIS [sem][ssispack].

## <a id="nextsteps"></a>Další kroky
Teď, když jste se naučili používání Pig s HDInsight, pomocí následujících odkazů a prozkoumejte další způsoby, jak pracovat s Azure HDInsight.

* [Nahrání dat do HDInsight](../hdinsight-upload-data.md)
* [Použití Hivu se službou HDInsight][hdinsight-use-hive]
* [Použití nástroje Sqoop s HDInsight](hdinsight-use-sqoop.md)
* [Použijte Oozie s HDInsight](../hdinsight-use-oozie.md)
* [Použijte úlohy MapReduce s HDInsight][hdinsight-use-mapreduce]

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
