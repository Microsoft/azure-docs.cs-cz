---
title: C#, Apache Hive & Apache prasete v Apache Hadoop – Azure HDInsight
description: Naučte se používat uživatelsky definované funkce jazyka C# s Apache Hive a Apache vepřovým streamem v Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/06/2019
ms.openlocfilehash: 97b23a2b9e3b95a5ea0efcd27d0ec185b1c456f1
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946553"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Použití uživatelem definovaných funkcí jazyka C# s Apache Hive a Apache prasetem v Apache Hadoop ve službě HDInsight

Naučte se používat uživatelsky definované funkce jazyka C# s [Apache Hive](https://hive.apache.org) a [Apache prasetem](https://pig.apache.org) v HDInsight.

> [!IMPORTANT]
> Kroky v tomto dokumentu fungují s clustery HDInsight se systémem Linux. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Správa verzí komponent HDInsight](../hdinsight-component-versioning.md).

Podregistr i prase mohou předat data externím aplikacím ke zpracování. Tento proces se označuje jako _streamování_. Při použití aplikace .NET se data předávají do aplikace na vstupu a aplikace vrátí výsledky na STDOUT. Chcete-li číst a zapisovat ze standardního vstupu a STDOUT, můžete použít `Console.ReadLine()` a `Console.WriteLine()` z konzolové aplikace.

## <a name="prerequisites"></a>Požadavky

* Znalost psaní a sestavování kódu v jazyce C#, který cílí na .NET Framework 4,5.

    Použijte libovolné prostředí IDE, které chcete. Doporučujeme [aplikaci Visual Studio](https://www.visualstudio.com/vs) nebo [Visual Studio Code](https://code.visualstudio.com/). Kroky v tomto dokumentu používají Visual Studio 2019.

* Způsob nahrajte soubory. exe do clusteru a spusťte úlohy z vepřového a podregistru. Doporučujeme [Data Lake nástroje pro Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md), [Azure POWERSHELL](/powershell/azure)a [Azure CLI](/cli/azure/install-azure-cli). Kroky v tomto dokumentu používají nástroje Data Lake pro Visual Studio k nahrání souborů a spuštění ukázkového dotazu na podregistr.

    Další informace o dalších způsobech spuštění dotazů na podregistry najdete v tématu [co je Apache Hive a HiveQL v Azure HDInsight](hdinsight-use-hive.md).

* Cluster Hadoop v HDInsight. Další informace o vytvoření clusteru najdete v tématu [Vytvoření clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET v HDInsight

Clustery *HDInsight se systémem Linux* používají [mono https://mono-project.com) (](https://mono-project.com) ke spouštění aplikací .NET). Mono verze 4.2.1 je součástí HDInsight verze 3,6.

Další informace o kompatibilitě mono s .NET Framework verzí naleznete v tématu [Kompatibilita mono](https://www.mono-project.com/docs/about-mono/compatibility/).

Další informace o verzi .NET Framework a mono obsažených ve verzích HDInsight najdete v tématu [verze komponent HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Vytvoření projektů v jazyce C \#

Následující části popisují, jak vytvořit projekt C# v aplikaci Visual Studio pro Apache Hive systému souborů UDF a Apache prasete UDF.

### <a name="apache-hive-udf"></a>Apache Hive UDF

Vytvoření projektu v jazyce C# pro Apache Hive systému souborů UDF:

1. Spusťte Visual Studio.

2. Vyberte **vytvořit nový projekt**.

3. V okně **vytvořit nový projekt** vyberte šablonu **Konzolová aplikace (.NET Framework)** (verze C#). Pak vyberte **Další**.

4. V okně **Konfigurovat nový projekt** zadejte **název projektu** *HiveCSharp* a přejděte do **umístění** , do kterého chcete nový projekt uložit, nebo ho vytvořte. Potom vyberte **Vytvořit**.

5. V integrovaném vývojovém prostředí sady Visual Studio nahraďte obsah *program.cs* následujícím kódem:

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

6. V řádku nabídek vyberte **sestavit**  >  **sestavení řešení** a sestavte projekt.

7. Zavřete řešení.

### <a name="apache-pig-udf"></a>Apache prasete UDF

Vytvoření projektu v jazyce C# pro Apache Hive systému souborů UDF:

1. Otevřete sadu Visual Studio.

2. V okně **Start** vyberte **vytvořit nový projekt**.

3. V okně **vytvořit nový projekt** vyberte šablonu **Konzolová aplikace (.NET Framework)** (verze C#). Pak vyberte **Další**.

4. V okně **Konfigurovat nový projekt** zadejte **název projektu** *PigUDF* a přejděte na nebo vytvořte **umístění** , do kterého chcete nový projekt uložit. Potom vyberte **Vytvořit**.

5. V integrovaném vývojovém prostředí sady Visual Studio nahraďte obsah *program.cs* následujícím kódem:

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

    Tento kód analyzuje řádky odesílané z prasete a znovu formátuje řádky, které začínají na `java.lang.Exception` .

6. V řádku nabídek vyberte sestavení sestavit   >  **řešení** a sestavte projekt.

7. Řešení nechte otevřené.

## <a name="upload-to-storage"></a>Nahrání do úložiště

V dalším kroku nahrajte do úložiště v clusteru HDInsight aplikace v podregistru a v systému vepřového systému pro systém souborů.

1. V aplikaci Visual Studio přejděte k **zobrazení**  >  **Průzkumník serveru**.

1. V **Průzkumník serveru** klikněte pravým tlačítkem myši na **Azure**, vyberte **připojit se k Microsoft Azure předplatného** a dokončete proces přihlašování.

1. Rozbalte cluster HDInsight, do kterého chcete nasadit tuto aplikaci. Zobrazí se položka s textem **(výchozí účet úložiště)** .

    ![Výchozí účet úložiště, cluster HDInsight, Průzkumník serveru](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Pokud tuto položku lze rozšířit, jako výchozí úložiště pro cluster používáte **účet Azure Storage** . Chcete-li zobrazit soubory ve výchozím úložišti pro cluster, rozbalte položku a dvakrát klikněte na položku **(výchozí kontejner)**.

    * Pokud tuto položku nelze rozšířit, jako výchozí úložiště pro cluster používáte **Azure Data Lake Storage** . Chcete-li zobrazit soubory ve výchozím úložišti pro cluster, dvakrát klikněte na položku **(výchozí účet úložiště)** .

1. Chcete-li nahrát soubory. exe, použijte jednu z následujících metod:

    * Pokud používáte **účet Azure Storage**, vyberte ikonu **nahrát objekt BLOB** .

        ![Ikona nahrání HDInsight pro nový projekt](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        V dialogovém okně **nahrát nový soubor** vyberte v části **název souboru** možnost **Procházet**. V dialogovém okně **nahrát objekt BLOB** otevřete složku *bin\Debug* projektu *HiveCSharp* a vyberte soubor *HiveCSharp.exe* . Nakonec vyberte **otevřít** a pak kliknutím na **OK** dokončete nahrávání.

    * Pokud používáte **Azure Data Lake Storage**, klikněte pravým tlačítkem myši na prázdnou oblast v seznamu souborů a vyberte **Odeslat**. Nakonec zvolte soubor *HiveCSharp.exe* a vyberte **otevřít**.

    Po dokončení nahrávání *HiveCSharp.exe* opakujte proces nahrávání pro *PigUDF.exe* soubor.

## <a name="run-an-apache-hive-query"></a>Spuštění dotazu Apache Hive

Nyní můžete spustit dotaz na podregistr, který používá vaši aplikaci pro podregistr UDF.

1. V aplikaci Visual Studio přejděte k **zobrazení**  >  **Průzkumník serveru**.

2. Rozbalte položku **Azure** a pak rozbalte **HDInsight**.

3. Klikněte pravým tlačítkem na cluster, na který jste nasadili aplikaci *HiveCSharp* , a pak vyberte **zapsat dotaz na podregistr**.

4. Pro dotaz na podregistr použijte následující text:

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
    > Odkomentujte `add file` příkaz, který odpovídá typu výchozího úložiště, který se používá pro váš cluster.

    Tento dotaz vybírá `clientid` `devicemake` pole, a `devicemodel` z `hivesampletable` a poté předává pole do aplikace *HiveCSharp.exe* . Dotaz očekává, že aplikace vrátí tři pole, která jsou uložena jako `clientid` , `phoneLabel` a `phoneHash` . Dotaz také očekává, že *HiveCSharp.exe* v kořenovém adresáři výchozího kontejneru úložiště.

5. Přepněte výchozí **interaktivní** na **Batch** a pak vyberte **Odeslat** a odešlete úlohu do clusteru HDInsight. Otevře se okno **Souhrn úlohy podregistru** .

6. Výběrem **aktualizovat** aktualizujte souhrn, dokud se **stav úlohy** nezmění na **dokončeno**. Chcete-li zobrazit výstup úlohy, vyberte **výstup úlohy**.

## <a name="run-an-apache-pig-job"></a>Spuštění úlohy Apache prasete

Můžete také spustit úlohu vepřového sádla, která používá vaši aplikaci systému souborů prasete UDF.

1. Pomocí SSH se připojte ke clusteru HDInsight. (Například spusťte příkaz `ssh sshuser@<clustername>-ssh.azurehdinsight.net` .) Další informace najdete v tématu [Použití SSH withHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Pomocí následujícího příkazu spusťte příkazový řádek prasete:

    ```shell
    pig
    ```

    `grunt>`Zobrazí se výzva.

3. Chcete-li spustit úlohu prasete, která používá aplikaci .NET Framework, zadejte následující:

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    `DEFINE`Příkaz vytvoří alias `streamer` pro aplikaci *PigUDF.exe* a `CACHE` načte ho z výchozího úložiště pro cluster. Později se `streamer` s `STREAM` operátorem používá ke zpracování jednoduchých řádků obsažených v `LOG` a vrátí data jako řadu sloupců.

    > [!NOTE]
    > Název aplikace, který se používá pro streamování, musí být \` při použití aliasu ohraničen znakem (znakem zaškrtnutí) a znakem "(jednoduché uvozovky)" `SHIP` .

4. Po zadání posledního řádku by se měla úloha spustit. Vrátí výstup podobný následujícímu textu:

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. Použijte `exit` k ukončení prasete.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se seznámili s postupem použití .NET Framework aplikace z podregistru a vepřového sádla v HDInsight. Pokud se chcete dozvědět, jak používat Python s podregistru a vepřovým nástrojem, přečtěte si téma [použití Pythonu s Apache Hive a Apache prasete v HDInsight](python-udf-hdinsight.md).

Další způsoby použití podregistru a další informace o použití MapReduce najdete v následujících článcích:

* [Použití Apache Hive se službou HDInsight](hdinsight-use-hive.md)
* [Použití MapReduce se službou HDInsight](hdinsight-use-mapreduce.md)
* [Základní informace o prasečí latince](https://pig.apache.org/docs/latest/basic.html)
