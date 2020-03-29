---
title: C#, Apache Hive & Apache Pig na Apache Hadoop - Azure HDInsight
description: Naučte se používat uživatelem definované funkce Jazyka C# (UDF) se streamováním Apache Hive a Apache Pig v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 9ef9eada9b9aec50642a8bf357edab0677868817
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74949385"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Používejte uživatelem definované funkce Jazyka C# s Apache Hive a Apache Pig na Apache Hadoop v HDInsightu

Naučte se používat uživatelem definované funkce Jazyka C# (UDF) s [Apache Hive](https://hive.apache.org) a [Apache Pig](https://pig.apache.org) na HDInsightu.

> [!IMPORTANT]
> Kroky v tomto dokumentu fungují s clustery HDInsight založenými na Linuxu. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace naleznete v tématu [správa verzí komponent HDInsight](../hdinsight-component-versioning.md).

Jak Hive, tak Pig mohou předávat data externím aplikacím pro zpracování. Tento proces se označuje jako _streamování_. Při použití aplikace .NET jsou data předána aplikaci na STDIN a aplikace vrátí výsledky na STDOUT. Chcete-li číst a zapisovat z STDIN a STDOUT, můžete použít `Console.ReadLine()` a `Console.WriteLine()` z konzolové aplikace.

## <a name="prerequisites"></a>Požadavky

* Znalost psaní a vytváření kódu Jazyka C#, který se zaměřuje na rozhraní .NET Framework 4.5.

    Použijte libovolné ide, které chcete. Doporučujeme [Visual Studio](https://www.visualstudio.com/vs) nebo Visual Studio [Kód](https://code.visualstudio.com/). Kroky v tomto dokumentu používají Visual Studio 2019.

* Způsob, jak nahrát soubory EXE do clusteru a spustit úlohy Pig a Hive. Doporučujeme [nástroje data lake pro Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md), Azure [PowerShell](/powershell/azure)a [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Kroky v tomto dokumentu používají nástroje Data Lake Tools pro Visual Studio k nahrání souborů a spuštění ukázkového dotazu Hive.

    Informace o dalších způsobech spouštění dotazů Hive najdete v tématu [Co je Apache Hive a HiveQL v Azure HDInsight?](hdinsight-use-hive.md).

* Hadoop v clusteru HDInsight. Další informace o vytvoření clusteru naleznete v tématu [Vytvoření clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>Rozhraní .NET na HDInsight

*Clustery HDInsight založené na Linuxu* používají [mono (https://mono-project.com) ](https://mono-project.com) ke spuštění aplikací .NET. Mono verze 4.2.1 je součástí HDInsight verze 3.6.

Další informace o kompatibilitě mono s verzemi rozhraní .NET Framework naleznete v [tématu Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/).

Další informace o verzi rozhraní .NET Framework a Mono, které jsou součástí verzí HDInsight, naleznete v [tématu verze komponent HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Vytvoření projektů\# C

Následující části popisují, jak vytvořit projekt C# v sadě Visual Studio pro Apache Hive UDF a Apache Pig UDF.

### <a name="apache-hive-udf"></a>Apache Hive UDF

Vytvoření projektu Jazyka C# pro UDF podregistru Apache:

1. Spusťte Visual Studio.

2. Vyberte **možnost Vytvořit nový projekt**.

3. V okně **Vytvořit nový projekt** zvolte šablonu **Konzolové aplikace (.NET Framework)** (verze jazyka C#). Pak vyberte **Další**.

4. V okně **Konfigurovat nový projekt** zadejte název **projektu** *HiveCSharp*a přejděte nebo vytvořte **umístění** pro uložení nového projektu. Pak vyberte **Vytvořit**.

5. V ide sady Visual Studio nahraďte obsah *Program.cs* následujícím kódem:

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

6. Na řádku nabídek vyberte **sestavení** > **sestavení řešení** k sestavení projektu.

7. Zavřete řešení.

### <a name="apache-pig-udf"></a>Apache Prase UDF

Vytvoření projektu Jazyka C# pro UDF podregistru Apache:

1. Otevřete sadu Visual Studio.

2. V okně **Start** vyberte **Vytvořit nový projekt**.

3. V okně **Vytvořit nový projekt** zvolte šablonu **Konzolové aplikace (.NET Framework)** (verze jazyka C#). Pak vyberte **Další**.

4. V okně **Konfigurovat nový projekt** zadejte název **projektu** *PigUDF*a přejděte do nebo vytvořte **umístění** pro uložení nového projektu. Pak vyberte **Vytvořit**.

5. V ide sady Visual Studio nahraďte obsah *Program.cs* následujícím kódem:

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

    Tento kód analyzuje řádky odeslané z Pig a přeformátuje řádky, které začínají `java.lang.Exception`.

6. Na řádku nabídek zvolte **Sestavení** > **sestavení řešení** k sestavení projektu.

7. Nechte roztok otevřený.

## <a name="upload-to-storage"></a>Nahrání do úložiště

Dále nahrajte aplikace Hive a Pig UDF do úložiště v clusteru HDInsight.

1. V sadě Visual Studio přejděte do **aplikace View** > **Server Explorer**.

1. Z **Průzkumníka serveru**klikněte pravým tlačítkem na **Azure**, vyberte Připojit **k předplatnému Microsoft Azure**a dokončete proces přihlášení.

1. Rozbalte cluster HDInsight, do kterého chcete tuto aplikaci nasadit. Položka s textem **(Výchozí účet úložiště)** je uvedena.

    ![Výchozí účet úložiště, cluster HDInsight, Průzkumník serveru](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Pokud tuto položku lze rozbalit, používáte **účet úložiště Azure** jako výchozí úložiště pro cluster. Chcete-li zobrazit soubory ve výchozím úložišti pro cluster, rozbalte položku a poklepejte na **položku (Výchozí kontejner).**

    * Pokud tuto položku nejde rozbalit, používáte **Azure Data Lake Storage** jako výchozí úložiště pro cluster. Chcete-li zobrazit soubory ve výchozím úložišti pro cluster, poklepejte na položku **(Výchozí účet úložiště).**

1. Chcete-li nahrát soubory EXE, použijte jednu z následujících metod:

    * Pokud používáte **účet úložiště Azure**, vyberte ikonu **Nahrát objekt blob.**

        ![HDInsight ikona nahrávání pro nový projekt](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        V dialogovém okně **Nahrát nový soubor** vyberte v části **Název souboru** **položku Procházet**. V dialogovém okně **Nahrát objekt blob** přejděte do složky *bin\debug* pro projekt *HiveCSharp* a pak zvolte soubor *HiveCSharp.exe.* Nakonec vyberte **Otevřít** a pak **OK** pro dokončení nahrávání.

    * Pokud používáte **Azure Data Lake Storage**, klikněte pravým tlačítkem myši na prázdnou oblast v výpisu souborů a pak vyberte **Nahrát**. Nakonec zvolte soubor *HiveCSharp.exe* a vyberte **Otevřít**.

    Po dokončení nahrávání *hivecsharp.exe* opakujte proces nahrávání pro soubor *PigUDF.exe.*

## <a name="run-an-apache-hive-query"></a>Spuštění dotazu Apache Hive

Nyní můžete spustit dotaz Hive, který používá vaši aplikaci Hive UDF.

1. V sadě Visual Studio přejděte do **aplikace View** > **Server Explorer**.

2. Rozbalte položku **Azure** a pak rozbalte **HDInsight**.

3. Klikněte pravým tlačítkem myši na cluster, do kterého jste nasadili aplikaci *HiveCSharp,* a pak vyberte **Příkaz hive query**.

4. Pro dotaz Hive použijte následující text:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
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
    > Odkomentujte `add file` příkaz, který odpovídá typu výchozího úložiště používaného pro váš cluster.

    Tento dotaz vybere `clientid` `devicemake`, `devicemodel` a `hivesampletable`pole z , a pak předá pole do aplikace *HiveCSharp.exe.* Dotaz očekává, že aplikace vrátí tři pole, `clientid` `phoneLabel`která `phoneHash`jsou uložena jako , a . Dotaz také očekává, že najde *HiveCSharp.exe* v kořenovém adresáři výchozího kontejneru úložiště.

5. Přepněte výchozí **interaktivní** na **batch**a pak vyberte **Odeslat,** chcete-li úlohu odeslat do clusteru HDInsight. Otevře se okno **Souhrn úloh úlu.**

6. Výběrem **možnosti Aktualizovat** aktualizujete souhrn, dokud se **stav úlohy** nezmění na **Dokončeno**. Chcete-li zobrazit výstup úlohy, vyberte **možnost Výstup úlohy**.

## <a name="run-an-apache-pig-job"></a>Spuštění úlohy Apache Pig

Můžete také spustit úlohu Pig, která používá vaši aplikaci Pig UDF.

1. Pomocí SSH se připojte ke clusteru HDInsight. (Spusťte například `ssh sshuser@<clustername>-ssh.azurehdinsight.net`příkaz .) Další informace naleznete [v tématu Použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. K spuštění příkazového řádku Prase použijte následující příkaz:

    ```shell
    pig
    ```

    Zobrazí `grunt>` se výzva.

3. Chcete-li spustit úlohu Pig, která používá aplikaci rozhraní .NET Framework, zadejte následující:

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    Příkaz `DEFINE` vytvoří alias `streamer` aplikace *PigUDF.exe* a `CACHE` načte jej z výchozího úložiště pro cluster. Později `streamer` se používá `STREAM` s operátorem ke zpracování `LOG` jednotlivých řádků obsažených v a vrátit data jako řadu sloupců.

    > [!NOTE]
    > Název aplikace, který se používá pro streamování, musí být při aliasu obklopen znakem \` (backtick) `SHIP`a znakem ' (jedna uvozovka) při použití s .

4. Po zadání posledního řádku by měla být úloha zahájena. Vrátí výstup podobný následujícímu textu:

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. Používá `exit` se k odchodu prasete.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se naučili používat aplikaci .NET Framework od Hive a Pig na HDInsight. Pokud se chcete dozvědět, jak používat Python s Hive a Pig, přečtěte si [informace o použití Pythonu s Apache Hive a Apache Pig v HDInsightu](python-udf-hdinsight.md).

Další způsoby použití Hive a informace o používání MapReduce najdete v následujících článcích:

* [Použití Apache Hive s HDInsight](hdinsight-use-hive.md)
* [Použití mapreduce s HDInsight](hdinsight-use-mapreduce.md)
* [Prasečí latinské základy](https://pig.apache.org/docs/latest/basic.html)