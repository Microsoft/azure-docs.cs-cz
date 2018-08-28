---
title: Použití Pigu Hadoop v HDInsight
description: Další informace o použití Pig se systémem Hadoop v HDInsight.
services: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.openlocfilehash: e97763adbe7998ed93e3ba8b87d89ffe8d8de6aa
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045441"
---
# <a name="use-pig-with-hadoop-on-hdinsight"></a>Použití Pigu se systémem Hadoop v HDInsight

Další informace o použití [Apache Pig](http://pig.apache.org/) s HDInsight.

Pig je platforma pro vytváření aplikací pro Hadoop pomocí procedurální jazyk říká *Pig Latin*. Pig se o alternativu k Javě pro vytvoření *MapReduce* řešení a je součástí Azure HDInsight. V následující tabulce použijte ke zjištění, že lze použít různými způsoby, jakými Pig s HDInsight:

| **Použít** Pokud chcete... | ...an **interaktivní** prostředí | ...**batch** zpracování | ...při to **clusteru operačního systému** | ...from to **klientský operační systém** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X a Windows |
| [REST API](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux nebo Windows |Linux, Unix, Mac OS X a Windows |
| [Sada .NET SDK pro Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux nebo Windows |Windows (prozatím) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux nebo Windows |Windows |

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="why"></a>Proč používat Pig

Jedním z problémů zpracování dat pomocí MapReduce Hadoop s použitím pouze mapy a snižte funkce implementuje logiku zpracování. Pro komplexní zpracování, je často nutné přerušit zpracování více operací MapReduce, které jsou zřetězené společně k dosažení požadovaného výsledku.

Pig umožňuje definovat zpracování jako řadu objektů transformace, které data prochází požadovaný výstup.

Jazyka Pig Latin můžete k popisu toku dat z vstup nezpracovaných dat, prostřednictvím jednoho nebo více transformací, k vytvoření požadovaného výstupu. Pig Latin programy postupovat podle tohoto vzoru Obecné:

* **Zatížení**: čtení dat manipulovat ze systému souborů

* **Transformace**: práce s daty

* **Výpis stavu nebo ukládat**: výstup dat na obrazovku nebo ukládat pro zpracování

### <a name="user-defined-functions"></a>Uživatelem definované funkce

Pig Latin také podporuje uživatelsky definovaných funkcí (UDF), které umožňuje vyvolat externí komponenty, které implementují logiku, která je obtížné model v Pig Latin.

Další informace o Pig Latin, naleznete v tématu [Pig Latin referenční příručce 1](http://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) a [Pig Latin referenční příručce 2](http://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

Příklad použití funkce UDF s Pig najdete v následujících dokumentech:

* [Použití DataFu s Pig v HDInsight](apache-hadoop-use-pig-datafu-udf.md) -DataFu je kolekce užitečné UDF udržuje Apache
* [Použití Pythonu s Pigu a Hivu ve službě HDInsight](python-udf-hdinsight.md)
* [Použití jazyka C# s Hivem a Pig v HDInsight](apache-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>Ukázková data

HDInsight poskytuje různé příklad datové sady, které jsou uloženy v `/example/data` a `/HdiSamples` adresáře. Tyto adresáře jsou ve výchozím nastavení úložiště pro váš cluster. Pig příkladu v tomto dokumentu se používá *log4j* souboru z `/example/data/sample.log`.

Každý protokol uvnitř souboru se skládá z řádku polí, která obsahuje `[LOG LEVEL]` pole, které chcete zobrazit typ a závažnost, například:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

V předchozím příkladu je úroveň protokolu chyba.

> [!NOTE]
> Můžete také vygenerovat soubor log4j pomocí [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) protokolování nástroje a pak nahrajte tento soubor do objektu blob služby. Zobrazit [nahrání dat do HDInsight](../hdinsight-upload-data.md) pokyny. Další informace o používání objektů BLOB v Azure storage s HDInsight naleznete v tématu [použití služby Azure Blob Storage s HDInsight](../hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Příklad projektu

Načte následující úlohy Pig Latin `sample.log` soubor z výchozího úložiště pro váš cluster HDInsight. Potom provede řadou transformace, které počet, kolikrát každou úroveň protokolu došlo k chybě ve vstupních datech. Výsledky se zapisují do STDOUT.

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

Na následujícím obrázku zobrazuje souhrn toho, co dělá každý transformaci na data.

![Grafické znázornění transformací][image-hdi-pig-data-transformation]

## <a id="run"></a>Spuštění úlohy Pig Latin

HDInsight můžete spouštět úlohy Pig Latin pomocí různých metod. Použijte následující tabulku k rozhodnutí, která metoda je pro vás nejvhodnější a potom na odkaz pro návod.

| **Použít** Pokud chcete... | ...an **interaktivní** prostředí | ...**batch** zpracování | ...při to **clusteru operačního systému** | ...from to **klienta** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X a Windows |
| [Curl](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux nebo Windows |Linux, Unix, Mac OS X a Windows |
| [Sada .NET SDK pro Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux nebo Windows |Windows (prozatím) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux nebo Windows |Windows |

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="pig-and-sql-server-integration-services"></a>Pig a SQL Server Integration Services

SQL Server Integration Services (SSIS) můžete použít ke spuštění úlohy Pig. Azure Feature Pack for SSIS obsahuje následující součásti, které pracují s úlohy Pig v HDInsight.

* [Úlohy Azure HDInsight Pig][pigtask]

* [Správce připojení Azure předplatného][connectionmanager]

Další informace o Azure Feature Pack for SSIS [tady][ssispack].

## <a id="nextsteps"></a>Další kroky
Teď, když jste se naučili, jak použití Pigu se službou HDInsight, pomocí následujících odkazů a prozkoumejte další možnosti, jak pracovat s Azure HDInsight.

* [Nahrání dat do služby HDInsight](../hdinsight-upload-data.md)
* [Použití Hivu se službou HDInsight][hdinsight-use-hive]
* [Použití nástroje Sqoop se HDInsight](hdinsight-use-sqoop.md)
* [Použití Oozie s HDInsight](../hdinsight-use-oozie.md)
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
