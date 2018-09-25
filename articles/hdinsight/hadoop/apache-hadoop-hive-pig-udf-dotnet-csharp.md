---
title: Použití jazyka C# s Hivem a Pig platformy hadoop v HDInsight – Azure
description: Další informace o použití jazyka C# uživatelem definované funkce (UDF) s Hivem a Pig, streamování v Azure HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: jasonh
ms.openlocfilehash: ebc4c918745e273700a0efb54db4408bb702c75e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991364"
---
# <a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>Použití uživatelem definovaných funkcí jazyka C# s Hivem a Pig streamování platformy hadoop v HDInsight

Další informace o použití jazyka C# uživatelem definované funkce (UDF) s Apache Hive a Pig v HDInsight.

> [!IMPORTANT]
> Kroky v tomto dokumentu fungují s clustery HDInsight založené na Linuxu i založené na Windows. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Správa verzí komponenty HDInsight](../hdinsight-component-versioning.md).

Obě Hive a Pig můžete předat data do externí aplikace pro zpracování. Tento proces se označuje jako _streamování_. Pokud používáte aplikaci .NET, data jsou předána do aplikace na STDIN a aplikace vrátí výsledky StdOut. Čtení a zápis ze standardního vstupu a výstupu STDOUT, můžete použít `Console.ReadLine()` a `Console.WriteLine()` z konzolové aplikace.

## <a name="prerequisites"></a>Požadavky

* Důvěrně při psaní a vytváření kódu jazyka C#, který cílí na rozhraní .NET Framework 4.5.

    * Pomocí libovolné integrované vývojové prostředí má. Doporučujeme [sady Visual Studio](https://www.visualstudio.com/vs) 2015, 2017, nebo [Visual Studio Code](https://code.visualstudio.com/). Kroky v tomto dokumentu pomocí sady Visual Studio 2017.

* Způsob, jak nahrát soubory .exe do clusteru a spouštět úlohy Pigu a Hivu. Doporučujeme vám nástroje Data Lake pro Visual Studio, Azure Powershellu a rozhraní příkazového řádku Azure Classic. Kroky v tomto dokumentu využívají Data Lake Tools pro Visual Studio pro nahrávání souborů a spuštění příkladu dotaz Hive.

    Informace o dalších způsobech spuštění Hive dotazy a úlohy Pig, najdete v následujících dokumentech:

    * [Použití Apache Hivu se službou HDInsight](hdinsight-use-hive.md)

    * [Použití Apache Pig s HDInsight](hdinsight-use-pig.md)

* Hadoop v clusteru HDInsight. Další informace týkající se vytvoření clusteru najdete v tématu [vytvořit HDInsight cluster](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET pro HDInsight

* __HDInsight se systémem Linux__ clusterů pomocí [Mono (https://mono-project.com) ](https://mono-project.com) ke spouštění aplikací .NET. Mono verze 4.2.1 je součástí HDInsight verze 3.6.

    Další informace o Mono kompatibilitu s verzí rozhraní .NET Framework najdete v tématu [Mono compatibility](http://www.mono-project.com/docs/about-mono/compatibility/).

    Pokud chcete použít konkrétní verzi nástroje Mono, najdete v článku [instalace nebo aktualizace Mono](../hdinsight-hadoop-install-mono.md) dokumentu.

* __HDInsight se systémem Windows__ clusterů pomocí rozhraní Microsoft .NET CLR ke spouštění aplikací .NET.

Další informace o verzi rozhraní .NET framework a Mono je součástí verze HDInsight najdete v tématu [verzí komponenty HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Vytvoření jazyka C\# projekty

### <a name="hive-udf"></a>Hive UDF

1. Otevřít Visual Studio a vytvořte řešení. Pro typ projektu vyberte **Konzolová aplikace (.NET Framework)** a pojmenujte nový projekt **HiveCSharp**.

    > [!IMPORTANT]
    > Vyberte __rozhraní .NET Framework 4.5__ Pokud používáte cluster HDInsight se systémem Linux. Další informace o Mono kompatibilitu s verzí rozhraní .NET Framework najdete v tématu [Mono compatibility](http://www.mono-project.com/docs/about-mono/compatibility/).

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

1. Otevřít Visual Studio a vytvořte řešení. Pro typ projektu vyberte **konzolovou aplikaci**a pojmenujte nový projekt **PigUDF**.

2. Nahraďte obsah **Program.cs** souboru následujícím kódem:

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

    Tento kód odeslané z Pig řádky Parsuje a přeformátuje řádky začínající `java.lang.Exception`.

3. Uložit **Program.cs**a pak sestavte projekt.

## <a name="upload-to-storage"></a>Nahrání do úložiště

1. V sadě Visual Studio, otevřete **Průzkumníka serveru**.

2. Rozbalte položku **Azure** a pak rozbalte **HDInsight**.

3. Pokud se zobrazí výzva, zadejte svoje přihlašovací údaje předplatného Azure a pak klikněte na tlačítko **Sign In**.

4. Rozbalte položku, kterou chcete nasadit tuto aplikaci do clusteru HDInsight. Položka s textem __(výchozí účet úložiště)__ je uvedena.

    ![Průzkumník serveru zobrazující účet úložiště pro cluster](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Pokud tuto položku lze rozšířit, používáte __účet služby Azure Storage__ jako výchozí úložiště pro cluster. Chcete-li zobrazit soubory na výchozí úložiště pro cluster, rozbalte položku a potom dvakrát klikněte __(výchozí kontejner)__.

    * Pokud tuto položku nelze rozšířit, používáte __Azure Data Lake Store__ jako výchozího úložiště pro cluster. Chcete-li zobrazit soubory na výchozí úložiště pro cluster, dvakrát klikněte __(výchozí účet úložiště)__ položka.

6. Pokud chcete nahrát soubory .exe, použijte jednu z následujících metod:

    * Pokud používáte __účet služby Azure Storage__, klikněte na tlačítko Nahrát ikonu a potom vyhledejte **bin\debug** složku pro **HiveCSharp** projektu. Nakonec vyberte **HiveCSharp.exe** souboru a klikněte na tlačítko **Ok**.

        ![Nahrát ikonu](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
    * Pokud používáte __Azure Data Lake Store__, klikněte pravým tlačítkem na prázdnou oblast v seznamu souboru a pak vyberte __nahrát__. Nakonec vyberte **HiveCSharp.exe** souboru a klikněte na tlačítko **otevřít**.

    Jednou __HiveCSharp.exe__ nahrávání dokončí, opakujte proces nahrávání __PigUDF.exe__ souboru.

## <a name="run-a-hive-query"></a>Spuštění dotazu Hive

1. V sadě Visual Studio, otevřete **Průzkumníka serveru**.

2. Rozbalte položku **Azure** a pak rozbalte **HDInsight**.

3. Klikněte pravým tlačítkem na cluster, který jste nasadili **HiveCSharp** do aplikace a pak vyberte **napsat dotaz Hive**.

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
    > Zrušením komentáře u `add file` příkaz, který odpovídá typu použitého výchozí úložiště pro váš cluster.

    Tento dotaz vybere `clientid`, `devicemake`, a `devicemodel` pole z `hivesampletable`a předá pole HiveCSharp.exe aplikaci. Dotaz očekává, že aplikace má být vrácena tři pole, které jsou uloženy jako `clientid`, `phoneLabel`, a `phoneHash`. Dotaz také očekává HiveCSharp.exe v kořenovém adresáři výchozího kontejneru úložiště.

5. Klikněte na tlačítko **odeslat** se odeslat úlohu do clusteru HDInsight. **Souhrn úlohy Hive** otevře se okno.

6. Klikněte na tlačítko **aktualizovat** aktualizovat souhrn až do **stav úlohy** změny **dokončeno**. Pokud chcete zobrazit výstup úlohy, klikněte na tlačítko **výstup úlohy**.

## <a name="run-a-pig-job"></a>Spuštění úlohy Pig

1. Použijte jednu z následujících metod pro připojení ke clusteru HDInsight:

    * Pokud používáte __založených na Linuxu__ HDInsight clusteru, pomocí SSH. Například, `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Další informace najdete v tématu [withHDInsight použití SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * Pokud používáte __založené na Windows__ clusteru HDInsight, [připojit ke clusteru pomocí vzdálené plochy](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)

2. Pomocí jedné následujícího příkazu spusťte Pig příkazového řádku:

        pig

    > [!IMPORTANT]
    > Pokud používáte cluster se systémem Windows, použijte následující příkazy:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    A `grunt>` se zobrazí výzva.

3. Zadejte následující příkaz pro spuštění úlohy Pig, který používá aplikace rozhraní .NET Framework:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    `DEFINE` Příkaz vytvoří alias `streamer` pigudf.exe aplikacích a `CACHE` načte z výchozí úložiště pro cluster. Později `streamer` se používá s `STREAM` operátor ke zpracování jedné řádků obsažená v protokolu a vrátit data jako řada sloupců.

    > [!NOTE]
    > Název aplikace, který se používá pro streamování musejí být uzavřeny do \` (prvními) znaků při alias, a ' (jednoduchá uvozovka) při použití s `SHIP`.

4. Po zadání na posledním řádku, se má úloha spustit. Vrátí výstup podobný následujícímu textu:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Další postup

V tomto dokumentu jste zjistili, jak používat aplikace rozhraní .NET Framework v Hivu a Pigu v HDInsight. Pokud chcete další informace o použití Pythonu s Hivem a Pig, přečtěte si téma [použití Pythonu s Hivem a Pigem ve HDInsight](python-udf-hdinsight.md).

Další způsoby použití Pigu a Hivu a další informace o použití prostředí MapReduce naleznete v následujících dokumentech:

* [Použití Hivu se službou HDInsight](hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](hdinsight-use-pig.md)
* [Použití MapReduce se službou HDInsight](hdinsight-use-mapreduce.md)
