---
title: Použití C# s MapReduce na Hadoop ve službě HDInsight – Azure
description: Naučte se používat C# k vytváření řešení MapReduce pomocí Apache Hadoop ve službě Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 7f82ad65ecc805d5a45c78e8b190dd0eee4c340c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70882324"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Použití C# se službou MapReduce streaming na Apache Hadoop ve službě HDInsight

Naučte se používat C# k vytvoření řešení MapReduce ve službě HDInsight.

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Správa verzí komponent HDInsight](../hdinsight-component-versioning.md).

Apache Hadoop streaming je nástroj, který umožňuje spouštět úlohy MapReduce pomocí skriptu nebo spustitelného souboru. V tomto příkladu je rozhraní .NET použito k implementaci mapovače a zúžení pro řešení počtu slov.

## <a name="net-on-hdinsight"></a>.NET v HDInsight

Clustery __HDInsight se systémem Linux__ používají [mono https://mono-project.com) (](https://mono-project.com) ke spouštění aplikací .NET). Mono verze 4.2.1 je součástí HDInsight verze 3,6. Další informace o verzi mono, která je součástí služby HDInsight, najdete v tématu [verze komponent HDInsight](../hdinsight-component-versioning.md). 

Další informace o kompatibilitě mono s .NET Framework verzí naleznete v tématu [Kompatibilita mono](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Jak funguje streamování Hadoop

Základní proces používaný pro streamování v tomto dokumentu je následující:

1. Hadoop předá data mapovači (Mapper. exe v tomto příkladu) na STDIN.
2. Mapovač zpracovává data a generuje páry klíč/hodnota, které jsou odděleny tabulátory, do STDOUT.
3. Výstup je přečtený pomocí Hadoop a pak se předává do nástroje pro redukci (v tomto příkladu je to příkaz zpomalení. exe) na STDIN.
4. Snížení načte páry klíč/hodnota, které jsou odděleny tabulátory, zpracuje data a pak výsledek vygeneruje jako páry klíč/hodnota oddělených tabulátorem na STDOUT.
5. Výstup se načte pomocí Hadoop a zapisuje do výstupního adresáře.

Další informace o streamování najdete v tématu [streamování Hadoop](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Požadavky

* Znalost psaní a vytváření C# kódu, který cílí na .NET Framework 4,5. Kroky v tomto dokumentu používají Visual Studio 2017.

* Způsob nahrání souborů. exe do clusteru. Kroky v tomto dokumentu používají nástroje Data Lake pro Visual Studio k nahrání souborů do primárního úložiště pro cluster.

* Azure PowerShell nebo klient SSH.

* Cluster Hadoop v HDInsight. Další informace o vytvoření clusteru najdete v tématu [Vytvoření clusteru HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Vytvoření mapovače

V aplikaci Visual Studio vytvořte novou __konzolovou aplikaci__ s názvem __Mapper__. Pro aplikaci použijte následující kód:

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

Po vytvoření aplikace ji Sestavte, aby se `/bin/Debug/mapper.exe` soubor vytvořil v adresáři projektu.

## <a name="create-the-reducer"></a>Vytvoření redukce

V aplikaci Visual Studio vytvořte novou __konzolovou aplikaci__ s názvem __snižovalo__. Pro aplikaci použijte následující kód:

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

Po vytvoření aplikace ji Sestavte, aby se `/bin/Debug/reducer.exe` soubor vytvořil v adresáři projektu.

## <a name="upload-to-storage"></a>Nahrání do úložiště

1. V aplikaci Visual Studio otevřete **Průzkumník serveru**.

2. Rozbalte položku **Azure** a pak rozbalte **HDInsight**.

3. Pokud se zobrazí výzva, zadejte svoje přihlašovací údaje k předplatnému Azure a pak klikněte na **Přihlásit**se.

4. Rozbalte cluster HDInsight, do kterého chcete nasadit tuto aplikaci. Zobrazí se položka s textem __(výchozí účet úložiště)__ .

    ![Průzkumník serveru zobrazení účtu úložiště pro cluster](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

    * Pokud tuto položku lze rozšířit, používáte __účet Azure Storage__ jako výchozí úložiště pro cluster. Chcete-li zobrazit soubory ve výchozím úložišti pro cluster, rozbalte položku a dvakrát klikněte na položku __(výchozí kontejner)__ .

    * Pokud tuto položku nelze rozšířit, jako výchozí úložiště pro cluster používáte __Azure Data Lake Storage__ . Chcete-li zobrazit soubory ve výchozím úložišti pro cluster, dvakrát klikněte na položku __(výchozí účet úložiště)__ .

5. Chcete-li nahrát soubory. exe, použijte jednu z následujících metod:

   * Pokud používáte __účet Azure Storage__, klikněte na ikonu nahrát a potom přejděte do složky **bin\Debug** pro projekt **Mapper** . Nakonec vyberte soubor **Mapper. exe** a klikněte na tlačítko **OK**.

        ![nahrát ikonu](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)
    
   * Pokud používáte __Azure Data Lake Storage__, klikněte pravým tlačítkem myši na prázdnou oblast v seznamu souborů a vyberte __Odeslat__. Nakonec vyberte soubor **Mapper. exe** a klikněte na **otevřít**.

     Po dokončení nahrávání __mapovače. exe__ opakujte proces nahrávání pro soubor. exe pro __zmenšení__ .

## <a name="run-a-job-using-an-ssh-session"></a>Spustit úlohu: Použití relace SSH

1. Pomocí SSH se připojte ke clusteru HDInsight. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Pomocí jednoho z následujících příkazů spusťte úlohu MapReduce:

   * Pokud se jako výchozí úložiště používá __Data Lake Storage Gen2__ :

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files abfs:///mapper.exe,abfs:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

   * Pokud se jako výchozí úložiště používá __Data Lake Storage Gen1__ :

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```
    
   * Pokud se jako výchozí úložiště používá __Azure Storage__ :

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

     Následující seznam popisuje, co každý parametr dělá:

   * `hadoop-streaming.jar`: Soubor JAR, který obsahuje funkci streamování MapReduce.
   * `-files`: Přidá soubory `reducer.exe` a do této úlohy. `mapper.exe` `abfs:///`, Nebopřed`wasb:///` každým souborem je cesta k kořenovému adresáři výchozího úložiště pro cluster.`adl:///`
   * `-mapper`: Určuje, který soubor implementuje Mapovač.
   * `-reducer`: Určuje, který soubor implementuje zpomalení.
   * `-input`: Vstupní data.
   * `-output`: Výstupní adresář.

3. Po dokončení úlohy MapReduce můžete zobrazit výsledky následujícím způsobem:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Následující text je příkladem dat vrácených tímto příkazem:

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>Spustit úlohu: Pomocí prostředí PowerShell

Pomocí následujícího skriptu PowerShellu spusťte úlohu MapReduce a stáhněte výsledky.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Tento skript vás vyzve k zadání názvu a hesla přihlašovacího účtu clusteru spolu s názvem clusteru HDInsight. Po dokončení úlohy se výstup stáhne do souboru s názvem `output.txt`. Následující text je příkladem dat v `output.txt` souboru:

    you     1128
    young   38
    younger 1
    youngest        1
    your    338
    yours   4
    yourself        34
    yourselves      3
    youth   17

## <a name="next-steps"></a>Další postup

Další informace o používání MapReduce se službou HDInsight najdete v tématu [použití MapReduce se službou HDInsight](hdinsight-use-mapreduce.md).

Informace o použití C# s podregistru a vepřovým nástrojem najdete v tématu [použití C# uživatelsky definované funkce s Apache Hive a Apache prasete](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Informace o použití C# se zaplavou v HDInsight najdete v tématu [vývoj C# topologií pro Apache Storm ve službě HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
