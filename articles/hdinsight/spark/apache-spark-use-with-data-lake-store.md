---
title: Analyzujte Azure Data Lake Storage Gen1 s HDInsight Apache Spark
description: Spusťte úlohy Apache Spark k analýze dat uložených v Azure Data Lake Storage Gen1
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: f7a6ab954aff1bcc2e3dae3fc035db4b136ccbbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73818169"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>Použití clusteru HDInsight Spark k analýze dat v zařízení Data Lake Storage Gen1

V tomto článku použijete [Jupyter Notebook](https://jupyter.org/) k dispozici s clustery HDInsight Spark ke spuštění úlohy, která čte data z účtu úložiště datového jezera.

## <a name="prerequisites"></a>Požadavky

* Účet Azure Data Lake Storage Gen1. Postupujte podle pokynů na [webu Začínáme s Azure Data Lake Storage Gen1 pomocí portálu Azure](../../data-lake-store/data-lake-store-get-started-portal.md).

* Cluster Azure HDInsight Spark s porty Data Lake Storage Gen1 jako úložištěm. Postupujte podle pokynů na [úvodním startu: Nastavení clusterů v HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

## <a name="prepare-the-data"></a>Příprava dat

> [!NOTE]  
> Tento krok není nutné provést, pokud jste vytvořili cluster HDInsight s úložištěm datových jezer jako výchozí úložiště. Proces vytváření clusteru přidá některé ukázková data v účtu úložiště datového jezera, který zadáte při vytváření clusteru. Přeskočte do sekce Použití clusteru HDInsight Spark s úložištěm datových jezer.

Pokud jste vytvořili cluster HDInsight s úložištěm datových jezer jako další úložiště a Azure Storage Blob jako výchozí úložiště, měli byste nejprve zkopírovat přes některé ukázková data do účtu úložiště datového jezera. Můžete použít ukázková data z objektu blob úložiště Azure přidružené k clusteru HDInsight. K tomu můžete použít [nástroj ADLCopy.](https://www.microsoft.com/download/details.aspx?id=50358) Stáhněte a nainstalujte nástroj z odkazu.

1. Otevřete příkazový řádek a přejděte do adresáře, `%HOMEPATH%\Documents\adlcopy`ve kterém je nainstalována aplikace AdlCopy, obvykle .

2. Spusťte následující příkaz pro kopírování konkrétního objektu blob ze zdrojového kontejneru do úložiště datového jezera:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Zkopírujte ukázkový datový soubor **HVAC.csv** na **adrese /HdiSamples/HdiSamples/SensorSampleData/hvac/** do účtu Azure Data Lake Storage. Fragment kódu by měl vypadat takto:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]  
   > Ujistěte se, že názvy souborů a cest používají správná velká písmena.

3. Budete vyzváni k zadání přihlašovacích údajů pro předplatné Azure, pod kterým máte účet úložiště datového jezera. Zobrazí se výstup, který bude podobný následujícímu fragmentu kódu:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Datový soubor (**HVAC.csv**) bude zkopírován do složky **/hvac** v účtu Úložiště datového jezera.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>Použití clusteru HDInsight Spark s datem Jezera Gen1

1. Z [portálu Azure](https://portal.azure.com/), z úvodní desky klikněte na dlaždici pro cluster Apache Spark (pokud jste ji připnuli na startboard). Můžete také přejít do clusteru v části **Procházet všechny** > **clustery HDInsight**.

2. Z okna clusteru Spark klikněte na tlačítko **Rychlé odkazy** a pak z okna **Řídicí panel clusteru** klikněte na tlačítko **Poznámkový blok Jupyter**. Po vyzvání zadejte přihlašovací údaje správce clusteru.

   > [!NOTE]  
   > Může také otevřít poznámkový blok Jupyter pro váš cluster tak, že otevřete následující adresu URL v prohlížeči. Nahraďte **název clusteru názvem clusteru:**
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Vytvořte nový poznámkový blok. Klikněte na tlačítko **Nový** a pak klikněte na tlačítko **PySpark**.

    ![Vytvoření nového poznámkového bloku Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Vytvoření nového poznámkového bloku Jupyter")

4. Vzhledem k tomu, že jste poznámkový blok vytvořili pomocí jádra PySpark, není nutné explicitně tvořit kontexty. Kontexty Spark a Hive se automaticky vytvoří za vás při spuštění první buňky kódu. Můžete začít importem typů nezbytných pro tento scénář. Chcete-li tak učinit, vložte následující fragment kódu do buňky a stiskněte klávesu **SHIFT + ENTER**.

        from pyspark.sql.types import *

    Při každém spuštění úlohy v Jupyter se název okna webového prohlížeče zobrazí jako **(Zaneprázdněn)** společně s názvem poznámkového bloku. Zobrazí se také plný kroužek vedle textu **PySpark** v pravém horním rohu. Po dokončení úlohy se změní na prázdný kruh.

     ![Stav úlohy poznámkového bloku Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Stav úlohy poznámkového bloku Jupyter")

5. Načtěte ukázková data do dočasné tabulky pomocí souboru **HVAC.csv,** který jste zkopírovali do účtu Data Lake Storage Gen1. K datům v účtu Úložiště datového jezera můžete přistupovat pomocí následujícího vzoru adresy URL.

   * Pokud máte data Lake Storage Gen1 jako výchozí úložiště, HVAC.csv bude na cestě podobné následující adrese URL:

           adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

       Nebo můžete také použít zkrácený formát, například následující:

           adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

   * Pokud máte úložiště data lake jako další úložiště, HVAC.csv bude v místě, kde jste ho zkopírovali, například:

           adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     Do prázdné buňky vložte následující příklad kódu, nahraďte **MYDATALAKESTORE** názvem účtu úložiště datového jezera a stiskněte **klávesu SHIFT + ENTER**. Tento ukázkový kód registruje data do dočasné tabulky nazývané **TVK**.

           # Load the data. The path below assumes Data Lake Storage is default storage for the Spark cluster
           hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

           # Create the schema
           hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

           # Parse the data in hvacText
           hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

           # Create a data frame
           hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

           # Register the data fram as a table to run queries against
           hvacdf.registerTempTable("hvac")

6. Vzhledem k tomu, že používáte jádro PySpark, můžete nyní přímo spustit dotaz SQL na dočasnou tabulku **TVK**, kterou jste právě vytvořili pomocí `%%sql` magic. Další informace o `%%sql` magii a dalších kouzlech dostupných v jádře PySpark najdete [v tématu Jádra dostupná na poznámkových blocích Jupyter u clusterů Apache Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. Po úspěšném dokončení úlohy se ve výchozím nastavení zobrazí následující tabulkový výstup.

      ![Tabulkový výstup výsledků dotazu](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Tabulkový výstup výsledků dotazu")

     Výsledky můžete také zobrazit v dalších vizualizacích. Například plošný graf pro stejný výstup bude vypadat následovně.

     ![Plošný graf výsledku dotazu](./media/apache-spark-use-with-data-lake-store/jupyter-area-output1.png "Plošný graf výsledku dotazu")

8. Po dokončení spuštění aplikace byste měli poznámkový blok vypnout a uvolnit tak prostředky. To provedete kliknutím na položku **Zavřít a zastavit** z nabídky **Soubor** v poznámkovém bloku. Dojde k vypnutí a zavření poznámkového bloku.


## <a name="next-steps"></a>Další kroky

* [Vytvoření samostatné aplikace Scala pro spuštění v clusteru Apache Spark](apache-spark-create-standalone-application.md)
* [Použití nástrojů HDInsight v sadě nástrojů Azure pro IntelliJ k vytváření aplikací Apache Spark pro cluster HDInsight Spark Linux](apache-spark-intellij-tool-plugin.md)
* [Použití nástrojů HDInsight v sadě nástrojů Azure pro Eclipse k vytváření aplikací Apache Spark pro cluster HDInsight Spark Linux](apache-spark-eclipse-tool-plugin.md)
* [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
