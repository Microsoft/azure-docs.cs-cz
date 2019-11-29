---
title: Použití C# s MapReduce na Hadoop ve službě HDInsight – Azure
description: Naučte se používat C# k vytváření řešení MapReduce pomocí Apache Hadoop ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 025b5c5c1e3b8543111e112202906ef6f1fdb482
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561805"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Použití C# se službou MapReduce streaming na Apache Hadoop ve službě HDInsight

Naučte se používat C# k vytvoření řešení MapReduce ve službě HDInsight.

Apache Hadoop streaming je nástroj, který umožňuje spouštět úlohy MapReduce pomocí skriptu nebo spustitelného souboru. V tomto příkladu je rozhraní .NET použito k implementaci mapovače a zúžení pro řešení počtu slov.

## <a name="net-on-hdinsight"></a>.NET v HDInsight

Clustery HDInsight používají [Mono (https://mono-project.com)](https://mono-project.com) ke spouštění aplikací .NET. Mono verze 4.2.1 je součástí HDInsight verze 3,6. Další informace o verzi mono, která je součástí služby HDInsight, najdete v tématu [Apache Hadoop komponenty dostupné s různými verzemi služby HDInsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).

Další informace o kompatibilitě mono s .NET Framework verzí naleznete v tématu [Kompatibilita mono](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Jak funguje streamování Hadoop

Základní proces používaný pro streamování v tomto dokumentu je následující:

1. Hadoop předá data mapovači (*Mapper. exe* v tomto příkladu) na stdin.
2. Mapovač zpracovává data a generuje páry klíč/hodnota, které jsou odděleny tabulátory, do STDOUT.
3. Výstup je přečtený pomocí Hadoop a pak se předává do nástroje pro redukci (v tomto příkladu je to příkaz zpomalení *. exe* ) na stdin.
4. Snížení načte páry klíč/hodnota, které jsou odděleny tabulátory, zpracuje data a pak výsledek vygeneruje jako páry klíč/hodnota oddělených tabulátorem na STDOUT.
5. Výstup se načte pomocí Hadoop a zapisuje do výstupního adresáře.

Další informace o streamování najdete v tématu [streamování Hadoop](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Předpoklady

* Visual Studio.

* Znalost psaní a vytváření C# kódu, který cílí na .NET Framework 4,5.

* Způsob nahrání souborů. exe do clusteru. Kroky v tomto dokumentu používají nástroje Data Lake pro Visual Studio k nahrání souborů do primárního úložiště pro cluster.

* Pokud používáte PowerShell, budete potřebovat [AZ Module](https://docs.microsoft.com/powershell/azure/overview).

* Klient SSH (volitelné). Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Cluster Apache Hadoop v HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* [Schéma identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pro primární úložiště clusterů. To `wasb://` pro Azure Storage `abfs://` pro Azure Data Lake Storage Gen2 nebo `adl://` pro Azure Data Lake Storage Gen1. Pokud je pro Azure Storage nebo Data Lake Storage Gen2 povolený zabezpečený přenos, identifikátor URI by byl `wasbs://` nebo `abfss://`[, a to](../../storage/common/storage-require-secure-transfer.md)i v tomto pořadí.


## <a name="create-the-mapper"></a>Vytvoření mapovače

V aplikaci Visual Studio vytvořte novou konzolovou aplikaci .NET Framework s názvem *Mapper*. Pro aplikaci použijte následující kód:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

Po vytvoření aplikace ji Sestavte, abyste vytvořili soubor */bin/Debug/Mapper.exe* v adresáři projektu.

## <a name="create-the-reducer"></a>Vytvoření redukce

V aplikaci Visual Studio vytvořte novou konzolovou aplikaci .NET Framework s názvem *snižovalo*. Pro aplikaci použijte následující kód:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

Po vytvoření aplikace ji Sestavte, abyste vytvořili soubor */bin/Debug/Reducer.exe* v adresáři projektu.

## <a name="upload-to-storage"></a>Nahrání do úložiště

Dál je potřeba nahrát *Mapovač* a aplikace *snižující* kapacitu do úložiště HDInsight.

1. V aplikaci Visual Studio vyberte **zobrazit** > **Průzkumník serveru**.

1. Klikněte pravým tlačítkem myši na **Azure**, vyberte **připojit k Microsoft Azure předplatnému...** a dokončete proces přihlašování.

1. Rozbalte cluster HDInsight, do kterého chcete nasadit tuto aplikaci. Zobrazí se položka s textem **(výchozí účet úložiště)** .

   ![Účet úložiště, cluster HDInsight, Průzkumník serveru, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Pokud je možné rozšířit položku **(výchozí účet úložiště)** , používáte **účet Azure Storage** jako výchozí úložiště pro cluster. Chcete-li zobrazit soubory ve výchozím úložišti pro cluster, rozbalte položku a dvakrát klikněte na položku **(výchozí kontejner)** .

   * Pokud položku **(výchozí účet úložiště)** nejde rozbalit, jako výchozí úložiště pro cluster používáte **Azure Data Lake Storage** . Chcete-li zobrazit soubory ve výchozím úložišti pro cluster, dvakrát klikněte na položku **(výchozí účet úložiště)** .

1. Chcete-li nahrát soubory. exe, použijte jednu z následujících metod:

    * Pokud používáte **účet Azure Storage**, vyberte ikonu **nahrát objekt BLOB** .

        ![Ikona nahrání HDInsight pro Mapper, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        V dialogovém okně **nahrát nový soubor** vyberte v části **název souboru**možnost **Procházet**. V dialogovém okně **nahrát objekt BLOB** přejdete do složky *bin\Debug* pro projekt *Mapper* a pak zvolíte soubor *Mapper. exe* . Nakonec vyberte **otevřít** a pak kliknutím na **OK** dokončete nahrávání.

    * V případě **Azure Data Lake Storage**klikněte pravým tlačítkem myši na prázdnou oblast v seznamu souborů a pak vyberte **Odeslat**. Nakonec vyberte soubor *Mapper. exe* a pak vyberte **otevřít**.

    Po dokončení nahrávání *mapovače. exe* opakujte proces nahrávání pro soubor. exe pro *zmenšení* .

## <a name="run-a-job-using-an-ssh-session"></a>Spustit úlohu: použití relace SSH

Následující postup popisuje, jak spustit úlohu MapReduce pomocí relace SSH:

1. Připojte se ke clusteru pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Pomocí jednoho z následujících příkazů spusťte úlohu MapReduce:

   * Pokud je výchozí úložiště **Azure Storage**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Pokud je výchozí úložiště **Data Lake Storage Gen1**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Pokud je výchozí úložiště **Data Lake Storage Gen2**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   Následující seznam popisuje, co každý parametr a možnost představuje:

   * *Hadoop-streaming. jar*: Určuje soubor JAR, který obsahuje funkci streamování MapReduce.
   * `-files`: Určuje soubory *Mapper. exe* a *snižoval. exe* pro tuto úlohu. Deklarace protokolu `wasbs:///`, `adl:///`nebo `abfs:///` před každým souborem je cestou k kořenovému adresáři výchozího úložiště pro cluster.
   * `-mapper`: Určuje soubor, který implementuje Mapovač.
   * `-reducer`: Určuje soubor, který implementuje redukci.
   * `-input`: Určuje vstupní data.
   * `-output`: Určuje výstupní adresář.

3. Po dokončení úlohy MapReduce použijte následující příkaz k zobrazení výsledků:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   Následující text je příkladem dat vrácených tímto příkazem:

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>Spuštění úlohy: používání PowerShellu

Pomocí následujícího skriptu PowerShellu spusťte úlohu MapReduce a stáhněte výsledky.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Tento skript vás vyzve k zadání názvu a hesla přihlašovacího účtu clusteru spolu s názvem clusteru HDInsight. Po dokončení úlohy se výstup stáhne do souboru s názvem *Output. txt*. Následující text je příkladem dat v souboru `output.txt`:

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>Další kroky

Další informace o používání MapReduce se službou HDInsight najdete v tématu [použití MapReduce v Apache Hadoop ve službě HDInsight](hdinsight-use-mapreduce.md).

Informace o použití C# s podregistru a vepřovým nástrojem najdete v tématu [použití C# uživatelsky definované funkce s Apache Hive a Apache prasete](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Informace o použití C# se zaplavou v HDInsight najdete v tématu [vývoj C# topologií pro Apache Storm ve službě HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
