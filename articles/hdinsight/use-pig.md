---
title: Použití Apache Pigu
titleSuffix: Azure HDInsight
description: Naučte se používat prase s Apache Hadoop v HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 7b74a41f7d6b636dddce0388d5ee0e0a12658d52
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944609"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Použití systému Apache prasete s Apache Hadoop v HDInsight

Naučte se používat [Apache prasete](https://pig.apache.org/) s HDInsight.

Apache prasete je platforma pro vytváření programů pro Apache Hadoop pomocí jazyka procedurálního, který se označuje jako *prasečí Latin*. Prasete je alternativou k Java pro vytváření řešení *MapReduce* a je součástí Azure HDInsight. Pomocí následující tabulky můžete zjistit různé způsoby, kterými se dá pomocí HDInsight použít.

## <a name="why-use-apache-pig"></a><a id="why"></a>Proč používat Apache prasete

Jednou z výzev ke zpracování dat pomocí MapReduce v Hadoop je implementace logiky zpracování pomocí pouze mapy a funkce zmenšení. Pro složité zpracování často musíte přerušit zpracování na více operací MapReduce, které jsou zřetězeny, abyste dosáhli požadovaného výsledku.

Prase umožňuje definovat zpracování jako řadu transformací, které data proudí, aby vytvořila požadovaný výstup.

Jazyk latince pro vepřové písmo umožňuje popsat tok dat z nezpracovaného vstupu přes jednu nebo více transformací, aby vznikl požadovaný výstup. Programy v latince pro vepřové písmo dodržují tento obecný vzor:

* **Načíst**: číst data, která se mají manipulovat ze systému souborů.

* **Transformace**: manipulace s daty.

* **Vypsat nebo uložit**: výstupní data na obrazovku nebo je uložit ke zpracování.

### <a name="user-defined-functions"></a>Uživatelsky definované funkce

Prasečí Latin podporuje také uživatelsky definované funkce (UDF), které vám umožní vyvolat externí komponenty, které implementují logiku, která je obtížné modelovat v prasečí latince.

Další informace o standardu prasečí latinku najdete v tématu Ruční použití [referenčních](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) informací pro prasečí latinku 1 a [vepřového odkazu na latinku](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

## <a name="example-data"></a><a id="data"></a>Ukázková data

HDInsight poskytuje různé příklady datových sad, které jsou uložené v `/example/data` `/HdiSamples` adresářích a. Tyto adresáře jsou ve výchozím úložišti pro váš cluster. Příklad prasete v tomto dokumentu používá soubor *log4j* z `/example/data/sample.log` .

Každý protokol uvnitř souboru se skládá z řádku polí obsahujícího `[LOG LEVEL]` pole pro zobrazení typu a závažnosti, například:

```output
2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937
```

V předchozím příkladu je úroveň protokolu CHYBná.

> [!NOTE]  
> Soubor log4j můžete také vygenerovat pomocí nástroje pro protokolování [Apache log4j](https://en.wikipedia.org/wiki/Log4j) a pak tento soubor nahrát do objektu BLOB. Pokyny najdete v tématu [nahrání dat do služby HDInsight](hdinsight-upload-data.md) . Další informace o tom, jak se v HDInsight používají objekty blob ve službě Azure Storage, najdete v tématu [použití Azure Blob Storage se službou HDInsight](hdinsight-hadoop-use-blob-storage.md).

## <a name="example-job"></a><a id="job"></a>Příklad úlohy

Následující úloha v latince prasete načte `sample.log` soubor z výchozího úložiště pro cluster HDInsight. Pak provede řadu transformací, jejichž výsledkem je počet, kolikrát se jednotlivé úrovně protokolu objevily ve vstupních datech. Výsledky jsou zapsány do STDOUT.

```output
LOGS = LOAD 'wasb:///example/data/sample.log';
LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
RESULT = order FREQUENCIES by COUNT desc;
DUMP RESULT;
```

Následující obrázek ukazuje souhrn toho, co každá transformace provádí s daty.

![Grafické znázornění transformací][image-hdi-pig-data-transformation]

## <a name="run-the-pig-latin-job"></a><a id="run"></a>Spuštění úlohy v latince prasete

HDInsight může spouštět úlohy s latinkou pro prase pomocí nejrůznějších metod. Pomocí následující tabulky se rozhodněte, která metoda je pro vás nejvhodnější, a pak použijte odkaz na návod.

## <a name="pig-and-sql-server-integration-services"></a>Prase a služba SSIS (SQL Server Integration Services)

Pomocí služba SSIS (SQL Server Integration Services) (SSIS) můžete spustit úlohu prasete. Sada Azure Feature Pack pro SSIS poskytuje následující komponenty, které pracují s úlohami v rámci služby HDInsight.

* [Úloha Azure HDInsight pro výkrm][pigtask]

* [Správce připojení předplatného Azure][connectionmanager]

Další informace o sadě Azure Feature Pack pro SSIS [najdete tady][ssispack].

## <a name="next-steps"></a><a id="nextsteps"></a>Další kroky

Teď, když jste se naučili, jak používat prase se službou HDInsight, můžete pomocí následujících odkazů prozkoumat další způsoby práce s Azure HDInsight.

* [Nahrání dat do služby HDInsight](hdinsight-upload-data.md)
* [Použití Apache Hive se službou HDInsight](./hadoop/hdinsight-use-hive.md)
* [Použití Apache Sqoop se službou HDInsight](./hadoop/hdinsight-use-sqoop.md)
* [Použijte úlohy MapReduce s HDInsight](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: /sql/integration-services/control-flow/azure-hdinsight-pig-task?viewFallbackFrom=sql-server-2014
[connectionmanager]: /sql/integration-services/connection-manager/azure-subscription-connection-manager?viewFallbackFrom=sql-server-2014
[ssispack]: /sql/integration-services/azure-feature-pack-for-integration-services-ssis?viewFallbackFrom=sql-server-2014
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azure/

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/use-pig/hdi-data-transformation.gif