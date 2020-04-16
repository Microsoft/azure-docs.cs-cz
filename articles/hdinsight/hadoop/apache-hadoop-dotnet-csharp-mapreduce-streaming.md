---
title: Použití Jazyka C# s MapReduce na Hadoopu v HDInsightu – Azure
description: Naučte se používat C# k vytváření řešení MapReduce pomocí Apache Hadoop v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 7a299ce16f6e9c7292cebf198c9c3077f8e05fcb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417614"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Použití Jazyka C# s mapovým streamováním MapReduce na Apache Hadoop v HDInsightu

Naučte se, jak pomocí jazyka C# vytvořit řešení MapReduce na webu HDInsight.

Apache Hadoop streaming umožňuje spustit MapReduce úlohy pomocí skriptu nebo spustitelný soubor. Zde .NET se používá k implementaci mapper a reduktor pro řešení počtu slov.

## <a name="net-on-hdinsight"></a>Rozhraní .NET na HDInsight

Clustery HDInsight používají [mono (https://mono-project.com) ](https://mono-project.com) ke spuštění aplikací .NET. Mono verze 4.2.1 je součástí HDInsight verze 3.6. Další informace o verzi Mono, která je součástí HDInsightu, najdete v [tématu Komponenty Apache Hadoop dostupné v různých verzích HDInsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).

Další informace o kompatibilitě mono s verzemi rozhraní .NET Framework naleznete v [tématu Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Jak funguje streamování Hadoopu

Základní proces používaný pro streamování v tomto dokumentu je následující:

1. Hadoop předává data mapper *(mapper.exe* v tomto příkladu) na STDIN.
2. Mapovač zpracuje data a vyzařuje dvojice klíč/hodnota oddělené tabulátorem do služby STDOUT.
3. Výstup je přečten Hadoopa a poté předán reduktoru (*reducer.exe* v tomto příkladu) na STDIN.
4. Reduktor čte dvojice klíč/hodnota oddělené tabulátorem, zpracovává data a potom vydává výsledek jako dvojice klíč/hodnota oddělené tabulátorem na STDOUT.
5. Výstup je přečten Hadoopem a zapsán do výstupního adresáře.

Další informace o streamování naleznete v tématu [Hadoop Streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Požadavky

* Visual Studio.

* Znalost psaní a vytváření kódu Jazyka C#, který se zaměřuje na rozhraní .NET Framework 4.5.

* Způsob, jak nahrát soubory EXE do clusteru. Kroky v tomto dokumentu používají nástroje Data Lake Tools pro Visual Studio k nahrání souborů do primárního úložiště pro cluster.

* Pokud používáte Prostředí PowerShell, budete potřebovat [modul Az](https://docs.microsoft.com/powershell/azure/overview).

* Cluster Apache Hadoop na HDInsight. Viz [Začínáme s HDInsight na Linuxu](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* [Schéma URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pro primární úložiště clusterů. Toto schéma `wasb://` by bylo `abfs://` pro Azure Storage, `adl://` pro Azure Data Lake Storage Gen2 nebo pro Azure Data Lake Storage Gen1. Pokud je zabezpečený přenos povolen pro Azure Storage nebo `wasbs://` `abfss://`Data Lake Storage Gen2, identifikátor URI by byl nebo , respektive Viz také [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md).

## <a name="create-the-mapper"></a>Vytvoření mapovače

V sadě Visual Studio vytvořte novou konzolovou aplikaci rozhraní .NET Framework s názvem *mapper*. Pro aplikaci použijte následující kód:

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

Po vytvoření aplikace ji sestavte tak, aby byla v adresáři projektu k výrobě souboru */bin/Debug/mapper.exe.*

## <a name="create-the-reducer"></a>Vytvoření reduktoru

V sadě Visual Studio vytvořte novou aplikaci konzoly rozhraní .NET Framework s názvem *Reducer*. Pro aplikaci použijte následující kód:

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

Po vytvoření aplikace ji sestavte tak, aby byla v adresáři projektu k výrobě souboru */bin/Debug/reducer.exe.*

## <a name="upload-to-storage"></a>Nahrání do úložiště

Dále musíte nahrát *mapovač* a *redukční* aplikace do úložiště HDInsight.

1. V sadě Visual Studio vyberte **Zobrazit** > **Průzkumníka serveru**.

1. Klikněte pravým tlačítkem na **Azure**, vyberte **Připojit k předplatnému Microsoft Azure...** a dokončete proces přihlášení.

1. Rozbalte cluster HDInsight, do kterého chcete tuto aplikaci nasadit. Položka s textem **(Výchozí účet úložiště)** je uvedena.

   ![Účet úložiště, cluster HDInsight, Průzkumník serveru, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Pokud lze rozbalit položku **(výchozí účet úložiště),** používáte **účet úložiště Azure** jako výchozí úložiště pro cluster. Chcete-li zobrazit soubory ve výchozím úložišti pro cluster, rozbalte položku a poklepejte na **položku (Výchozí kontejner).**

   * Pokud položku **(výchozí účet úložiště)** nelze rozbalit, používáte **Azure Data Lake Storage** jako výchozí úložiště pro cluster. Chcete-li zobrazit soubory ve výchozím úložišti pro cluster, poklepejte na položku **(Výchozí účet úložiště).**

1. Chcete-li nahrát soubory EXE, použijte jednu z následujících metod:

    * Pokud používáte **účet úložiště Azure**, vyberte ikonu **Nahrát objekt blob.**

        ![HdInsight ikona nahrávání mapper, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        V dialogovém okně **Nahrát nový soubor** vyberte v části **Název souboru** **položku Procházet**. V dialogovém okně **Nahrát objekt Blob** přejděte do složky *bin\debug* pro projekt *mapovače* a zvolte soubor *mapper.exe.* Nakonec vyberte **Otevřít** a pak **OK** pro dokončení nahrávání.

    * V **zařízení Azure Data Lake Storage**klikněte pravým tlačítkem myši na prázdnou oblast v seznamu souborů a potom vyberte **Nahrát**. Nakonec vyberte soubor *mapper.exe* a pak vyberte **Otevřít**.

    Po dokončení nahrávání *mapper.exe* opakujte proces nahrávání pro soubor *reducer.exe.*

## <a name="run-a-job-using-an-ssh-session"></a>Spuštění úlohy: Použití relace SSH

Následující postup popisuje, jak spustit úlohu MapReduce pomocí relace SSH:

1. Pomocí [příkazu ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru. Upravte níže uvedený příkaz nahrazením názvu clusteru názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Ke spuštění úlohy MapReduce použijte jeden z následujících příkazů:

   * Pokud je výchozí úložiště **Azure Storage**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Pokud je výchozím úložištěm **Data Lake Storage Gen1**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Pokud je výchozím úložištěm **Data Lake Storage Gen2**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   Následující seznam popisuje, co jednotlivé parametry a možnosti představují:

   |Parametr | Popis |
   |---|---|
   |hadoop-streaming.jar|Určuje soubor jar, který obsahuje funkci streamování MapReduce.|
   |-soubory|Určuje soubory *mapper.exe* a *reducer.exe* pro tuto úlohu. `wasbs:///`Deklarace `adl:///`, `abfs:///` nebo protokol před každým souborem je cesta ke kořenovému adresáři výchozího úložiště pro cluster.|
   |-mapovač|Určuje soubor, který implementuje mapovač.|
   |-reduktor|Určuje soubor, který implementuje reduktor.|
   |-vstup|Určuje vstupní data.|
   |-výstup|Určuje výstupní adresář.|

1. Po dokončení úlohy MapReduce zobrazte výsledky pomocí následujícího příkazu:

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

## <a name="run-a-job-using-powershell"></a>Spuštění úlohy: Použití PowerShellu

Pomocí následujícího skriptu prostředí PowerShell spusťte úlohu MapReduce a stáhněte výsledky.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Tento skript vás vyzve k zadání názvu a hesla přihlašovacího účtu clusteru spolu s názvem clusteru HDInsight. Po dokončení úlohy se výstup stáhne do souboru s názvem *output.txt*. Následující text je příkladem dat v `output.txt` souboru:

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

Další informace o používání MapReduce s HDInsight najdete [v tématu Použití MapReduce v Apache Hadoop na HDInsight](hdinsight-use-mapreduce.md).

Informace o použití jazyka C# s hive a prase, najdete v článku [Použití C# uživatelem definované funkce s Apache Hive a Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Informace o používání jazyka C# s stormem na HDInsight najdete v [tématu Vývoj topologií Jazyka C# pro Apache Storm na HDInsightu](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
