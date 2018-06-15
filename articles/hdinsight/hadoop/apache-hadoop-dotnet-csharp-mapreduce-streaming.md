---
title: Použití jazyka C# s MapReduce systému Hadoop v HDInsight - Azure | Microsoft Docs
description: Další informace o použití jazyka C# k vytvoření řešení MapReduce s Hadoop v prostředí Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.custom: hdinsightactive
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 7287972ccf63f33a8cf08065f8d5d30ee1b1afb5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400873"
---
# <a name="use-c-with-mapreduce-streaming-on-hadoop-in-hdinsight"></a>Použití jazyka C# s MapReduce, streamování systému Hadoop v HDInsight

Další informace o použití jazyka C# k vytvoření řešení MapReduce v HDInsight.

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Správa verzí komponenty HDInsight](../hdinsight-component-versioning.md).

Streamování Hadoop je nástroj, který umožňuje spouštění úloh MapReduce pomocí skriptu nebo spustitelného souboru. V tomto příkladu se používá .NET pro implementaci mapper a reduktorem počet řešení aplikace word.

## <a name="net-on-hdinsight"></a>Rozhraní .NET v HDInsight

__HDInsight se systémem Linux__ clusterů použijte [Mono (https://mono-project.com) ](https://mono-project.com) ke spouštění aplikací .NET. Je součástí HDInsight verze 3.6 mono verze 4.2.1. Další informace o verzi Mono zahrnuté do HDInsight naleznete v tématu [HDInsight verze součástí](../hdinsight-component-versioning.md). Použít konkrétní verzi Mono, najdete v článku [instalace nebo aktualizace Mono](../hdinsight-hadoop-install-mono.md) dokumentu.

Další informace o Mono kompatibilitu s verzí rozhraní .NET Framework naleznete v tématu [Mono kompatibility](http://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Jak funguje streamování Hadoop

Základní proces použít pro streamování v tomto dokumentu je následující:

1. Hadoop předává na stdin – data mapper (mapper.exe v tomto příkladu).
2. Mapper zpracovává dat a vysílá páry oddělený tabulátory klíč/hodnota pro STDOUT.
3. Výstup bude číst Hadoop a následně předán do reduktorem (reducer.exe v tomto příkladu) na STDIN.
4. Reduktorem čte páry klíč/hodnota oddělený tabulátory, zpracuje data a potom vydá výsledek jako dvojice klíč/hodnota oddělený tabulátory do datového proudu STDOUT.
5. Výstup bude Hadoop číst a zapisovat do výstupního adresáře.

Další informace o streamování najdete v tématu [streamování Hadoop (https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Požadavky

* Znalost zápis a sestavování kód C#, která je cílena rozhraní .NET Framework 4.5. Kroky v tomto dokumentu používají Visual Studio 2017.

* Způsob, jak nahrát soubory .exe do clusteru. Kroky v tomto dokumentu použít nástroje Data Lake pro Visual Studio k nahrání souborů do primárního úložiště pro cluster.

* Prostředí Azure PowerShell nebo SSH klientem.

* Hadoop v clusteru HDInsight. Další informace týkající se vytvoření clusteru najdete v tématu [vytvoření clusteru HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Vytvořte mapper

V sadě Visual Studio vytvořte novou __Konzolová aplikace__ s názvem __mapper__. Použijte následující kód pro aplikaci:

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

Po vytvoření aplikace, sestavte jej k vytvoření `/bin/Debug/mapper.exe` soubor v adresáři projektu.

## <a name="create-the-reducer"></a>Vytvořte reduktorem

V sadě Visual Studio vytvořte novou __Konzolová aplikace__ s názvem __reduktorem__. Použijte následující kód pro aplikaci:

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

Po vytvoření aplikace, sestavte jej k vytvoření `/bin/Debug/reducer.exe` soubor v adresáři projektu.

## <a name="upload-to-storage"></a>Nahrání do úložiště

1. V sadě Visual Studio otevřete **Průzkumníka serveru**.

2. Rozbalte položku **Azure** a pak rozbalte **HDInsight**.

3. Po zobrazení výzvy zadejte své přihlašovací údaje předplatného Azure a pak klikněte na tlačítko **přihlásit**.

4. Rozbalte položku, kterou chcete nasadit tuto aplikaci do clusteru HDInsight. Položku s textem __(výchozí účet úložiště)__ je uveden.

    ![Průzkumník serveru zobrazující účet úložiště pro cluster](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/storage.png)

    * Pokud tuto položku lze rozšířit, kterou používáte __účet úložiště Azure__ jako výchozí úložiště pro cluster. Chcete-li zobrazit soubory na výchozí úložiště pro cluster, rozbalte položku a dvakrát __(výchozí kontejner)__.

    * Pokud tuto položku nelze rozšířit, používáte __Azure Data Lake Store__ jako výchozí úložiště pro cluster. Chcete-li zobrazit soubory na výchozí úložiště pro cluster, dvakrát klikněte na __(výchozí účet úložiště)__ položku.

5. Pokud chcete nahrát soubory .exe, použijte jednu z následujících metod:

    * Pokud se používá __účet úložiště Azure__, klikněte na ikonu nahrávání a potom vyhledejte **bin\debug** složku pro **mapper** projektu. Nakonec vyberte **mapper.exe** souboru a klikněte na tlačítko **Ok**.

        ![Nahrajte ikonu](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/upload.png)
    
    * Pokud používáte __Azure Data Lake Store__, klikněte pravým tlačítkem myši na prázdnou oblast v seznamu souboru a pak vyberte __nahrát__. Nakonec vyberte **mapper.exe** souboru a klikněte na tlačítko **otevřete**.

    Jednou __mapper.exe__ odesílání skončí, opakujte tento postup nahrání pro __reducer.exe__ souboru.

## <a name="run-a-job-using-an-ssh-session"></a>Spustit úlohu: pomocí relace SSH

1. Použití SSH se připojit ke clusteru HDInsight. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Při spuštění úlohy MapReduce, použijte jednu z následujících příkazů:

    * Pokud používáte __Data Lake Store__ jako výchozí úložiště:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```
    
    * Pokud používáte __Azure Storage__ jako výchozí úložiště:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```

    Následující seznam popisuje, jaké jsou jednotlivé parametry:

    * `hadoop-streaming.jar`: Na soubor jar, který obsahuje funkce datových proudů MapReduce.
    * `-files`: Přidá `mapper.exe` a `reducer.exe` soubory do této úlohy. `adl:///` Nebo `wasb:///` před každý soubor je cesta ke kořenovému adresáři výchozí úložiště pro cluster.
    * `-mapper`: Určuje soubor, který implementuje mapper.
    * `-reducer`: Určuje soubor, který implementuje reduktorem.
    * `-input`: Vstupní data.
    * `-output`: Výstupního adresáře.

3. Po dokončení úlohy MapReduce, použijte následující postupy pro zobrazení výsledků:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Tento text je příklad dat vrácených tento příkaz:

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>Spustit úlohu: pomocí prostředí PowerShell

Pomocí následujícího skriptu prostředí PowerShell a spusťte úlohu MapReduce stažení výsledků.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Tento skript vyzve k zadání názvu clusteru přihlášení účtu a heslo, společně s názvem clusteru HDInsight. Po dokončení úlohy, výstup se stáhne do souboru s názvem `output.txt`. Tento text je příklad dat v `output.txt` souboru:

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

Další informace o používání MapReduce s HDInsight naleznete v tématu [používání MapReduce s HDInsight](hdinsight-use-mapreduce.md).

Informace o použití jazyka C# s Hive a Pig najdete v tématu [C# uživatelem definované funkce pomocí Hive a Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Informace o použití jazyka C# se Storm v HDInsight naleznete v tématu [vývoj C# topologií pro Storm v HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
