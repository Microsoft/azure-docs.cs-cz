---
title: Použití C# s Apache Hive a Apache prasetem v Apache Hadoop ve službě HDInsight – Azure
description: Naučte se používat C# uživatelsky definované funkce (UDF) s Apache Hive a Apache vepřovým streamem v Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: c05e72cd28c78b26b7c23a123b133d10147a4421
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810785"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Použití C# uživatelsky definovaných funkcí s Apache Hive a Apache prasetem v Apache Hadoop ve službě HDInsight

Naučte se používat C# uživatelsky definované funkce (UDF) s Apache Hive a Apache prasetem v HDInsight.

> [!IMPORTANT]
> Kroky v tomto dokumentu fungují s clustery HDInsight se systémem Linux i Windows. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Správa verzí komponent HDInsight](../hdinsight-component-versioning.md).

Podregistr i prase mohou předat data externím aplikacím ke zpracování. Tento proces se označuje jako _streamování_. Při použití aplikace .NET se data předávají do aplikace na vstupu a aplikace vrátí výsledky na STDOUT. Chcete-li číst a zapisovat ze standardního vstupu a STDOUT, `Console.ReadLine()` můžete `Console.WriteLine()` použít a z konzolové aplikace.

## <a name="prerequisites"></a>Požadavky

* Znalost psaní a vytváření C# kódu, který cílí na .NET Framework 4,5.

    * Použijte libovolné prostředí IDE, které chcete. Doporučujeme [Visual Studio](https://www.visualstudio.com/vs) 2015, 2017 nebo [Visual Studio Code](https://code.visualstudio.com/). Kroky v tomto dokumentu používají Visual Studio 2017.

* Způsob nahrajte soubory. exe do clusteru a spusťte úlohy z vepřového a podregistru. Doporučujeme Data Lake nástroje pro Visual Studio, Azure PowerShell a Azure Classic CLI. Kroky v tomto dokumentu používají nástroje Data Lake pro Visual Studio k nahrání souborů a spuštění ukázkového dotazu na podregistr.

    Další informace o dalších způsobech spuštění dotazů na podregistrů a úloh pro vepřové zobrazení najdete v následujících dokumentech:

    * [Použití Apache Hive se službou HDInsight](hdinsight-use-hive.md)

    * [Použití Apache prasete se službou HDInsight](hdinsight-use-pig.md)

* Cluster Hadoop v HDInsight. Další informace o vytvoření clusteru najdete v tématu [Vytvoření clusteru HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET v HDInsight

* Clustery __HDInsight se systémem Linux__ využívající [mono https://mono-project.com) (](https://mono-project.com) pro spouštění aplikací .NET. Mono verze 4.2.1 je součástí HDInsight verze 3,6.

    Další informace o kompatibilitě mono s .NET Framework verzí naleznete v tématu [Kompatibilita mono](https://www.mono-project.com/docs/about-mono/compatibility/).

* Clustery __HDInsight založené na Windows__ používají ke spouštění aplikací .NET modul Microsoft .NET CLR.

Další informace o verzi rozhraní .NET Framework a mono, které jsou součástí verze služby HDInsight, najdete v tématu [verze komponent HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Vytvoření projektů v\# jazyce C

### <a name="apache-hive-udf"></a>Apache Hive UDF

1. Otevřete Visual Studio a vytvořte řešení. Jako typ projektu vyberte **Konzolová aplikace (.NET Framework)** a pojmenujte nový projekt **HiveCSharp**.

    > [!IMPORTANT]
    > Pokud používáte cluster HDInsight se systémem Linux, vyberte __.NET Framework 4,5__ . Další informace o kompatibilitě mono s .NET Framework verzí naleznete v tématu [Kompatibilita mono](https://www.mono-project.com/docs/about-mono/compatibility/).

2. Obsah **program.cs** nahraďte následujícím kódem:

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

### <a name="apache-pig-udf"></a>Apache prasete UDF

1. Otevřete Visual Studio a vytvořte řešení. Jako typ projektu vyberte **Konzolová aplikace**a pojmenujte nový projekt **PigUDF**.

2. Obsah souboru **program.cs** nahraďte následujícím kódem:

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

    Tento kód analyzuje řádky odesílané z prasete a znovu formátuje řádky, které začínají `java.lang.Exception`na.

3. Uložte **program.cs**a pak Sestavte projekt.

## <a name="upload-to-storage"></a>Nahrání do úložiště

1. V aplikaci Visual Studio otevřete **Průzkumník serveru**.

2. Rozbalte položku **Azure** a pak rozbalte **HDInsight**.

3. Pokud se zobrazí výzva, zadejte svoje přihlašovací údaje k předplatnému Azure a pak klikněte na **Přihlásit**se.

4. Rozbalte cluster HDInsight, do kterého chcete nasadit tuto aplikaci. Zobrazí se položka s textem __(výchozí účet úložiště)__ .

    ![Průzkumník serveru zobrazení účtu úložiště pro cluster](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Pokud tuto položku lze rozšířit, používáte __účet Azure Storage__ jako výchozí úložiště pro cluster. Chcete-li zobrazit soubory ve výchozím úložišti pro cluster, rozbalte položku a dvakrát klikněte na položku __(výchozí kontejner)__ .

    * Pokud tuto položku nelze rozšířit, jako výchozí úložiště pro cluster používáte __Azure Data Lake Storage__ . Chcete-li zobrazit soubory ve výchozím úložišti pro cluster, dvakrát klikněte na položku __(výchozí účet úložiště)__ .

6. Chcete-li nahrát soubory. exe, použijte jednu z následujících metod:

   * Pokud používáte __účet Azure Storage__, klikněte na ikonu nahrát a potom přejděte do složky **bin\Debug** projektu **HiveCSharp** . Nakonec vyberte soubor **HiveCSharp. exe** a klikněte na tlačítko **OK**.

       ![nahrát ikonu](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
   * Pokud používáte __Azure Data Lake Storage__, klikněte pravým tlačítkem myši na prázdnou oblast v seznamu souborů a vyberte __Odeslat__. Nakonec vyberte soubor **HiveCSharp. exe** a klikněte na **otevřít**.

     Po dokončení nahrávání __HiveCSharp. exe__ opakujte proces nahrávání pro soubor __PigUDF. exe__ .

## <a name="run-an-apache-hive-query"></a>Spuštění dotazu Apache Hive

1. V aplikaci Visual Studio otevřete **Průzkumník serveru**.

2. Rozbalte položku **Azure** a pak rozbalte **HDInsight**.

3. Klikněte pravým tlačítkem na cluster, na který jste nasadili aplikaci **HiveCSharp** , a pak vyberte **zapsat dotaz na podregistr**.

4. Pro dotaz na podregistr použijte následující text:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Odkomentujte `add file` příkaz, který odpovídá typu výchozího úložiště, který se používá pro váš cluster.

    Tento dotaz vybere `clientid` `devicemodel` pole, `devicemake`a z `hivesampletable`a předá pole aplikaci HiveCSharp. exe. Dotaz očekává, že aplikace vrátí tři pole, která jsou uložena jako `clientid`, `phoneLabel`a `phoneHash`. Dotaz také očekává, že se HiveCSharp. exe najde v kořenovém adresáři výchozího kontejneru úložiště.

5. Kliknutím na **Odeslat** odešlete úlohu do clusteru HDInsight. Otevře se okno **Souhrn úlohy podregistru** .

6. Kliknutím na tlačítko **aktualizovat** aktualizujete souhrn, dokud se **stav úlohy** nezmění na **dokončeno**. Výstup úlohy zobrazíte kliknutím na **výstup úlohy**.

## <a name="run-an-apache-pig-job"></a>Spuštění úlohy Apache prasete

1. Pomocí SSH se připojte ke clusteru HDInsight. Například, `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Další informace najdete v tématu [Použití SSH withHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) .

2. Pomocí jednoho z následujících příkazů spusťte příkazový řádek prasete:

        pig

    > [!IMPORTANT]
    > Pokud používáte cluster se systémem Windows, použijte místo toho následující příkazy:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    Zobrazí `grunt>` se výzva.

3. Chcete-li spustit úlohu prasete, která používá aplikaci .NET Framework, zadejte následující:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    Příkaz vytvoří alias pro aplikace pigudf. exe a `CACHE` načte ho z výchozího úložiště pro cluster. `streamer` `DEFINE` `streamer` Později se `STREAM` s operátorem používá ke zpracování jednoduchých řádků obsažených v protokolu a vrátí data jako řadu sloupců.

    > [!NOTE]
    > Název aplikace, který se používá pro streamování, musí být při použití \` aliasu ohraničen znakem (znakem zaškrtnutí) a ' (jednoduchá uvozovka), `SHIP`Pokud se používá s.

4. Po zadání posledního řádku by se měla úloha spustit. Vrátí výstup podobný následujícímu textu:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Další postup

V tomto dokumentu jste se seznámili s postupem použití .NET Framework aplikace z podregistru a vepřového sádla v HDInsight. Pokud se chcete dozvědět, jak používat Python s podregistru a vepřovým nástrojem, přečtěte si téma [použití Pythonu s Apache Hive a Apache prasete v HDInsight](python-udf-hdinsight.md).

Další způsoby použití prasete a podregistru a další informace o použití MapReduce najdete v následujících dokumentech:

* [Použití Apache Hive se službou HDInsight](hdinsight-use-hive.md)
* [Použití Apache prasete se službou HDInsight](hdinsight-use-pig.md)
* [Použití MapReduce se službou HDInsight](hdinsight-use-mapreduce.md)
