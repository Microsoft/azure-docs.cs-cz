---
title: Použití jazyka C# s MapReduce na Hadoop ve službě HDInsight – Azure
description: Naučte se používat C# k vytváření řešení MapReduce pomocí Apache Hadoop ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, seoapr2020, devx-track-csharp
ms.date: 04/28/2020
ms.openlocfilehash: 4153b612b4b15883f28517d806acf6b340a94e49
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543045"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Použití jazyka C# s MapReduce streamingmi na Apache Hadoop ve službě HDInsight

Naučte se používat C# k vytvoření řešení MapReduce ve službě HDInsight.

Streamování Apache Hadoop umožňuje spouštět úlohy MapReduce pomocí skriptu nebo spustitelného souboru. V tomto případě se k implementaci mapovače a zúžení pro řešení počtu slov používá rozhraní .NET.

## <a name="net-on-hdinsight"></a>.NET v HDInsight

Clustery HDInsight využívají [mono https://mono-project.com) (](https://mono-project.com) ke spouštění aplikací .NET). Mono verze 4.2.1 je součástí HDInsight verze 3,6. Další informace o verzi mono, která je součástí služby HDInsight, najdete v tématu [Apache Hadoop komponenty dostupné s různými verzemi služby HDInsight](../hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions).

Další informace o kompatibilitě mono s .NET Framework verzí naleznete v tématu [Kompatibilita mono](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Jak funguje streamování Hadoop

Základní proces používaný pro streamování v tomto dokumentu je následující:

1. Hadoop předá data do mapovače ( *mapper.exe* v tomto příkladu) na stdin.
2. Mapovač zpracovává data a generuje páry klíč/hodnota, které jsou odděleny tabulátory, do STDOUT.
3. Výstup se načte pomocí Hadoop a pak se předává do nástroje pro redukci ( *reducer.exe* v tomto příkladu) na stdin.
4. Snížení načte páry klíč/hodnota, které jsou odděleny tabulátory, zpracuje data a pak výsledek vygeneruje jako páry klíč/hodnota oddělených tabulátorem na STDOUT.
5. Výstup se načte pomocí Hadoop a zapisuje do výstupního adresáře.

Další informace o streamování najdete v tématu [streamování Hadoop](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Předpoklady

* Visual Studio

* Znalost psaní a sestavování kódu v jazyce C#, který cílí na .NET Framework 4,5.

* Způsob nahrání souborů. exe do clusteru. Kroky v tomto dokumentu používají nástroje Data Lake pro Visual Studio k nahrání souborů do primárního úložiště pro cluster.

* Pokud používáte PowerShell, budete potřebovat [AZ Module](/powershell/azure/).

* Cluster Apache Hadoop v HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Schéma identifikátoru URI pro primární úložiště clusterů. Toto schéma bude `wasb://` Azure Storage pro `abfs://` Azure Data Lake Storage Gen2 nebo `adl://` pro Azure Data Lake Storage Gen1. Pokud je pro Azure Storage nebo Data Lake Storage Gen2 povolený zabezpečený přenos, identifikátor URI by byl `wasbs://` nebo v `abfss://` uvedeném pořadí.

## <a name="create-the-mapper"></a>Vytvoření mapovače

V aplikaci Visual Studio vytvořte novou konzolovou aplikaci .NET Framework s názvem *Mapper* . Pro aplikaci použijte následující kód:

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

Po vytvoření aplikace ji Sestavte, abyste vytvořili soubor */bin/Debug/mapper.exe* v adresáři projektu.

## <a name="create-the-reducer"></a>Vytvoření redukce

V aplikaci Visual Studio vytvořte novou konzolovou aplikaci .NET Framework s názvem *snižovalo* . Pro aplikaci použijte následující kód:

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

Po vytvoření aplikace ji Sestavte, abyste vytvořili soubor */bin/Debug/reducer.exe* v adresáři projektu.

## <a name="upload-to-storage"></a>Nahrání do úložiště

Dál je potřeba nahrát *Mapovač* a aplikace *snižující* kapacitu do úložiště HDInsight.

1. V aplikaci Visual Studio vyberte **Zobrazit**  >  **Průzkumník serveru** .

1. Klikněte pravým tlačítkem myši na **Azure** , vyberte **připojit k Microsoft Azure předplatnému...** a dokončete proces přihlašování.

1. Rozbalte cluster HDInsight, do kterého chcete nasadit tuto aplikaci. Zobrazí se položka s textem **(výchozí účet úložiště)** .

   ![Účet úložiště, cluster HDInsight, Průzkumník serveru, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Pokud je možné rozšířit položku **(výchozí účet úložiště)** , používáte **účet Azure Storage** jako výchozí úložiště pro cluster. Chcete-li zobrazit soubory ve výchozím úložišti pro cluster, rozbalte položku a dvakrát klikněte na položku **(výchozí kontejner)** .

   * Pokud položku **(výchozí účet úložiště)** nejde rozbalit, jako výchozí úložiště pro cluster používáte **Azure Data Lake Storage** . Chcete-li zobrazit soubory ve výchozím úložišti pro cluster, dvakrát klikněte na položku **(výchozí účet úložiště)** .

1. Chcete-li nahrát soubory. exe, použijte jednu z následujících metod:

    * Pokud používáte **účet Azure Storage** , vyberte ikonu **nahrát objekt BLOB** .

        ![Ikona nahrání HDInsight pro Mapper, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        V dialogovém okně **nahrát nový soubor** vyberte v části **název souboru** možnost **Procházet** . V dialogovém okně **nahrát objekt BLOB** přejdete do složky *bin\Debug* pro projekt *mapper* a pak zvolte soubor *mapper.exe* . Nakonec vyberte **otevřít** a pak kliknutím na **OK** dokončete nahrávání.

    * V případě **Azure Data Lake Storage** klikněte pravým tlačítkem myši na prázdnou oblast v seznamu souborů a pak vyberte **Odeslat** . Nakonec vyberte soubor *mapper.exe* a pak vyberte **otevřít** .

    Po dokončení nahrávání *mapper.exe* opakujte proces nahrávání pro *reducer.exe* soubor.

## <a name="run-a-job-using-an-ssh-session"></a>Spustit úlohu: použití relace SSH

Následující postup popisuje, jak spustit úlohu MapReduce pomocí relace SSH:

1. Připojte se ke clusteru pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Pomocí jednoho z následujících příkazů spusťte úlohu MapReduce:

   * Pokud je výchozí úložiště **Azure Storage** :

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Pokud je výchozí úložiště **Data Lake Storage Gen1** :

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Pokud je výchozí úložiště **Data Lake Storage Gen2** :

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   Následující seznam popisuje, co každý parametr a možnost představuje:

   |Parametr | Popis |
   |---|---|
   |Hadoop-streaming. jar|Určuje soubor JAR, který obsahuje funkci streamování MapReduce.|
   |– soubory|Určuje *mapper.exe* a *reducer.exe* soubory pro tuto úlohu. `wasbs:///` `adl:///` Deklarace protokolu, nebo `abfs:///` předtím, než je každý soubor cestou k kořenovému adresáři výchozího úložiště pro cluster.|
   |– Mapovač|Určuje soubor, který implementuje Mapovač.|
   |– redukce|Určuje soubor, který implementuje redukci.|
   |-vstup|Určuje vstupní data.|
   |– výstup|Určuje výstupní adresář.|

1. Po dokončení úlohy MapReduce použijte následující příkaz k zobrazení výsledků:

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

Tento skript vás vyzve k zadání názvu a hesla přihlašovacího účtu clusteru spolu s názvem clusteru HDInsight. Po dokončení úlohy se výstup stáhne do souboru s názvem *output.txt* . Následující text je příkladem dat v `output.txt` souboru:

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

* [Použijte MapReduce v Apache Hadoop ve službě HDInsight](hdinsight-use-mapreduce.md).
* [Použijte uživatelsky definovanou funkci jazyka C# s Apache Hive a Apache prasete](apache-hadoop-hive-pig-udf-dotnet-csharp.md).
* [Vývoj programů MapReduce v Javě](apache-hadoop-develop-deploy-java-mapreduce-linux.md)