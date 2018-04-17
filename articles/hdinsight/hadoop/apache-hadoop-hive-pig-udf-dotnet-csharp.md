---
title: Použití jazyka C# s Hive a Pig systému Hadoop v HDInsight - Azure | Microsoft Docs
description: Další informace o použití jazyka C# uživatelsky definované funkce (UDF) s Hive a Pig streamování v Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: b776b214b9d46293a3ab97ad226dffd3107f3430
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>Uživatelem definované funkce jazyka C# pomocí Hive a Pig streamování systému Hadoop v HDInsight

Další informace o použití jazyka C# uživatelsky definované funkce (UDF) s Apache Hive a Pig v HDInsight.

> [!IMPORTANT]
> Kroky v tomto dokumentu pracovat s clustery HDInsight se systémem Linux i systému Windows. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Správa verzí komponenty HDInsight](../hdinsight-component-versioning.md).

Obě Hive a Pig můžete předat data do externí aplikace pro zpracování. Tento proces se označuje jako _streamování_. Při použití aplikace .NET, data, je předaná do aplikace na stdin – a aplikace vrací výsledky do datového proudu STDOUT. Číst a zapisovat ze standardního vstupu a výstupu STDOUT, můžete použít `Console.ReadLine()` a `Console.WriteLine()` z konzolové aplikace.

## <a name="prerequisites"></a>Požadavky

* Znalost zápis a sestavování kód C#, která je cílena rozhraní .NET Framework 4.5.

    * Ať IDE, které chcete použijte. Doporučujeme, abyste [Visual Studio](https://www.visualstudio.com/vs) 2015, 2017, nebo [Visual Studio Code](https://code.visualstudio.com/). Kroky v tomto dokumentu používají Visual Studio 2017.

* Způsob, jak nahrát soubory .exe do clusteru a spuštění úlohy Pig a Hive. Doporučujeme, abyste nástrojů Data Lake pro Visual Studio, prostředí Azure PowerShell a rozhraní příkazového řádku Azure. Kroky v tomto dokumentu nástroje Data Lake pro Visual Studio a odesílat soubory, spusťte v příkladu Hive dotaz.

    Informace o jiných způsobech spouštění Hive dotazy a úlohy Pig, najdete v následujících dokumentech:

    * [Použijte Apache Hive s HDInsight](hdinsight-use-hive.md)

    * [Použijte Apache Pig s HDInsight](hdinsight-use-pig.md)

* Hadoop v clusteru HDInsight. Další informace týkající se vytvoření clusteru najdete v tématu [vytvoření clusteru HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>Rozhraní .NET v HDInsight

* __HDInsight se systémem Linux__ clusterů pomocí [Mono (https://mono-project.com) ](https://mono-project.com) ke spouštění aplikací .NET. Je součástí HDInsight verze 3.6 mono verze 4.2.1.

    Další informace o Mono kompatibilitu s verzí rozhraní .NET Framework naleznete v tématu [Mono kompatibility](http://www.mono-project.com/docs/about-mono/compatibility/).

    Použít konkrétní verzi Mono, najdete v článku [instalace nebo aktualizace Mono](../hdinsight-hadoop-install-mono.md) dokumentu.

* __HDInsight se systémem Windows__ clustery pomocí rozhraní Microsoft .NET CLR ke spouštění aplikací .NET.

Další informace o verzi rozhraní .NET framework a Mono součástí verzích HDInsight naleznete v tématu [HDInsight verze součástí](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Vytvoření C\# projekty

### <a name="hive-udf"></a>Hive UDF

1. Otevřete Visual Studio a vytvořte řešení. Typ projektu vyberte **konzolovou aplikaci (rozhraní .NET Framework)**a název nového projektu **HiveCSharp**.

    > [!IMPORTANT]
    > Vyberte __rozhraní .NET Framework 4.5__ Pokud používáte cluster HDInsight se systémem Linux. Další informace o Mono kompatibilitu s verzí rozhraní .NET Framework naleznete v tématu [Mono kompatibility](http://www.mono-project.com/docs/about-mono/compatibility/).

2. Nahraďte obsah **Program.cs** následujícím kódem:

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

3. Sestavte projekt.

### <a name="pig-udf"></a>Pig UDF

1. Otevřete Visual Studio a vytvořte řešení. Typ projektu vyberte **konzolové aplikace**a název nového projektu **PigUDF**.

2. Nahraďte obsah **Program.cs** soubor s následujícím kódem:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Tento kód analyzuje odeslané z Pig řádky a řádky začínající přeformátuje `java.lang.Exception`.

3. Uložit **Program.cs**a potom sestavte projekt.

## <a name="upload-to-storage"></a>Nahrání do úložiště

1. V sadě Visual Studio otevřete **Průzkumníka serveru**.

2. Rozbalte položku **Azure** a pak rozbalte **HDInsight**.

3. Po zobrazení výzvy zadejte své přihlašovací údaje předplatného Azure a pak klikněte na tlačítko **přihlásit**.

4. Rozbalte položku, kterou chcete nasadit tuto aplikaci do clusteru HDInsight. Položku s textem __(výchozí účet úložiště)__ je uveden.

    ![Průzkumník serveru zobrazující účet úložiště pro cluster](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Pokud tuto položku lze rozšířit, kterou používáte __účet úložiště Azure__ jako výchozí úložiště pro cluster. Chcete-li zobrazit soubory na výchozí úložiště pro cluster, rozbalte položku a dvakrát __(výchozí kontejner)__.

    * Pokud tuto položku nelze rozšířit, používáte __Azure Data Lake Store__ jako výchozí úložiště pro cluster. Chcete-li zobrazit soubory na výchozí úložiště pro cluster, dvakrát klikněte na __(výchozí účet úložiště)__ položku.

6. Pokud chcete nahrát soubory .exe, použijte jednu z následujících metod:

    * Pokud se používá __účet úložiště Azure__, klikněte na ikonu nahrávání a potom vyhledejte **bin\debug** složku pro **HiveCSharp** projektu. Nakonec vyberte **HiveCSharp.exe** souboru a klikněte na tlačítko **Ok**.

        ![Nahrajte ikonu](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
    * Pokud používáte __Azure Data Lake Store__, klikněte pravým tlačítkem myši na prázdnou oblast v seznamu souboru a pak vyberte __nahrát__. Nakonec vyberte **HiveCSharp.exe** souboru a klikněte na tlačítko **otevřete**.

    Jednou __HiveCSharp.exe__ odesílání skončí, opakujte tento postup nahrání pro __PigUDF.exe__ souboru.

## <a name="run-a-hive-query"></a>Spuštění dotazu Hive

1. V sadě Visual Studio otevřete **Průzkumníka serveru**.

2. Rozbalte položku **Azure** a pak rozbalte **HDInsight**.

3. Pravým tlačítkem na cluster, který jste nasadili **HiveCSharp** do aplikace a pak vyberte **napsat dotaz Hive**.

4. Použijte následující text pro dotaz Hive:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Store
    -- add file adl:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Zrušením komentáře u `add file` příkaz, který odpovídá typu použitého výchozí úložiště pro cluster.

    Tento dotaz vybere `clientid`, `devicemake`, a `devicemodel` pole z `hivesampletable`a předá pole HiveCSharp.exe aplikaci. Dotaz očekává aplikaci, aby vracela tři pole, které jsou uloženy jako `clientid`, `phoneLabel`, a `phoneHash`. Dotaz se také očekává, že HiveCSharp.exe najít v kořenovém kontejneru výchozí úložiště.

5. Klikněte na tlačítko **odeslání** se odeslat úlohu do clusteru HDInsight. **Souhrn úlohy Hive** otevře se okno.

6. Klikněte na tlačítko **aktualizovat** aktualizovat souhrn dokud **stav úlohy** změny **dokončeno**. Chcete-li zobrazit výstup úlohy, klikněte na tlačítko **výstup úlohy**.

## <a name="run-a-pig-job"></a>Spustit úlohu Pig

1. Pro připojení ke svému clusteru HDInsight, použijte jednu z následujících metod:

    * Pokud používáte __systémem Linux__ HDInsight clusteru, použijte SSH. Například, `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Další informace najdete v tématu [withHDInsight použití SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * Pokud používáte __založené na Windows__ clusteru HDInsight, [připojit ke clusteru pomocí vzdálené plochy](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)

2. Pomocí jedné následujícího příkazu spusťte Pig příkazového řádku:

        pig

    > [!IMPORTANT]
    > Pokud používáte cluster systému Windows, použijte následující příkazy:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    A `grunt>` se zobrazí výzva.

3. Zadejte následující příkaz pro spuštění úlohy Pig, která používá aplikace rozhraní .NET Framework:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    `DEFINE` Příkaz vytvoří zástupce `streamer` pro aplikace pigudf.exe a `CACHE` načte z výchozí úložiště pro cluster. Později `streamer` se používá s `STREAM` operátor ke zpracování jedné řádků obsažená v protokolu a vrátí data jako řadu sloupců.

    > [!NOTE]
    > Název aplikace, který se používá pro streamování, musí být uzavřena do \` (backtick) při znak alias, a "(jednoduchou uvozovku) při použití s `SHIP`.

4. Po zadání poslední řádek, by měla spustit úlohu. Vrátí výstup podobný následujícímu:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Další postup

V tomto dokumentu jste se naučili, jak používat rozhraní .NET Framework aplikace z Hive a Pig v HDInsight. Pokud chcete další informace o použití Python s Hive a Pig, přečtěte si téma [použít Python s Hive a Pig v HDInsight](python-udf-hdinsight.md).

Pro jiné způsoby používání Pig a Hive a další informace o použití prostředí MapReduce najdete v následujících dokumentech:

* [Použití Hivu se službou HDInsight](hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](hdinsight-use-pig.md)
* [Používání nástroje MapReduce s HDInsight](hdinsight-use-mapreduce.md)
