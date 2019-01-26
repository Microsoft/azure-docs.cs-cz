---
title: Použití Apache Sparku pro analýzu dat ve službě Azure Data Lake Storage
description: Spouštění Sparkových úloh pro analýzu dat uložených ve službě Azure Data Lake Storage
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.openlocfilehash: 29fef9177e40bd89dd0f179b028d2f044af8addb
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913085"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage"></a>Použití clusteru Spark v HDInsight k analýze dat v Data Lake Storage

V tomto kurzu použijete [Poznámkový blok Jupyter](https://jupyter.org/) s clustery HDInsight Spark ke spuštění úlohy, která čte data z účtu Data Lake Storage k dispozici.

## <a name="prerequisites"></a>Požadavky

* Účet úložiště Azure Data Lake. Postupujte podle pokynů na adrese [Začínáme s Azure Data Lake Storage pomocí webu Azure portal](../../data-lake-store/data-lake-store-get-started-portal.md).

* Cluster Azure HDInsight Spark s Data Lake Storage jako úložiště. Postupujte podle pokynů na adrese [rychlý start: Nastavení clusterů v HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    
## <a name="prepare-the-data"></a>Příprava dat

> [!NOTE]  
> Není potřeba tento krok proveďte, pokud jste vytvořili HDInsight cluster s Data Lake Storage jako výchozí úložiště. V procesu vytváření clusteru přidá nějaká ukázková data v účtu Data Lake Storage, který zadáte při vytváření clusteru. Přeskočit k části [clusteru používejte HDInsight Spark s Data Lake Storage](#use-an-hdinsight-spark-cluster-with-data-lake-store).

Pokud jste vytvořili cluster služby HDInsight s Data Lake Storage jako další úložiště a Azure Storage Blob jako výchozím úložištěm, měli byste nejprve zkopírovat přes nějaká ukázková data do účtu Data Lake Storage. Můžete tak ukázku, kterou data z Azure Storage Blob přidružené ke clusteru HDInsight. Můžete použít [ADLCopy nástroj](https://aka.ms/downloadadlcopy) Uděláte to tak. Stáhněte a nainstalujte nástroj z odkazu.

1. Otevřete příkazový řádek a přejděte do adresáře, kde AdlCopy je nainstalovaný, obvykle `%HOMEPATH%\Documents\adlcopy`.

2. Spusťte následující příkaz pro kopírování jen konkrétní objekt blob z zdrojového kontejneru do Data Lake Storage:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Kopírovat **HVAC.csv** ukázková data souboru **/HdiSamples/HdiSamples/SensorSampleData/hvac/** do účtu úložiště Azure Data Lake. Fragment kódu by měl vypadat:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]  
   > Ujistěte se, že jste se v případě správné názvy souborů a cestu.

3. Zobrazí se výzva k zadání přihlašovacích údajů předplatného Azure, ve kterém máte účtu Data Lake Storage. Zobrazí se výstup, který bude podobný následujícímu fragmentu kódu:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Datový soubor (**HVAC.csv**) v rámci složky budou zkopírovány **/hvac** v účtu Data Lake Storage.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage"></a>Cluster HDInsight Spark pomocí Data Lake Storage

1. Z [webu Azure Portal](https://portal.azure.com/), z úvodního panelu klikněte na dlaždici pro váš cluster Apache Spark (Pokud je připnutý na úvodní panel). Můžete také přejít na cluster pod položkou **Procházet vše** > **Clustery HDInsight**.

2. Z okna clusteru Spark klikněte na tlačítko **Rychlé odkazy** a pak z okna **Řídicí panel clusteru** klikněte na tlačítko **Poznámkový blok Jupyter**. Po vyzvání zadejte přihlašovací údaje správce clusteru.

   > [!NOTE]  
   > Může také otevřít poznámkový blok Jupyter pro váš cluster tak, že otevřete následující adresu URL v prohlížeči. Nahraďte **CLUSTERNAME** názvem clusteru:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Vytvořte nový poznámkový blok. Klikněte na tlačítko **Nový** a pak klikněte na tlačítko **PySpark**.

    ![Vytvoření nového poznámkového bloku Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Vytvoření nového poznámkového bloku Jupyter")

4. Vzhledem k tomu, že jste poznámkový blok vytvořili pomocí jádra PySpark, není nutné explicitně tvořit kontexty. Kontexty Spark a Hive se automaticky vytvoří za vás při spuštění první buňky kódu. Můžete začít importem typů nezbytných pro tento scénář. Chcete-li tak učinit, vložte následující fragment kódu do buňky a stiskněte klávesu **SHIFT + ENTER**.

        from pyspark.sql.types import *

    Při každém spuštění úlohy v Jupyter se název okna webového prohlížeče zobrazí jako **(Zaneprázdněn)** společně s názvem poznámkového bloku. Zobrazí se také plný kroužek vedle textu **PySpark** v pravém horním rohu. Po dokončení úlohy se změní na prázdný kruh.

     ![Stav úlohy poznámkového bloku Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Stav úlohy poznámkového bloku Jupyter")

5. Načtení ukázkových dat do dočasné tabulky pomocí **HVAC.csv** soubor zkopírován do účtu Data Lake Storage. Můžete přistupovat k datům v účtu Data Lake Storage pomocí následující vzor adresy URL.

    * Pokud máte Data Lake Storage jako výchozím úložištěm, HVAC.csv bude v cestě podobně jako na následující adresu URL:

            adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

        Nebo můžete také použít zkrácenou formát jako je následující:

            adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

    * Pokud máte Data Lake Storage jako další úložiště, HVAC.csv bude v umístění, kam jste zkopírovali, jako například:

            adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     Do prázdné buňky vložte následující příklad kódu, nahraďte **MYDATALAKESTORE** se název účtu Data Lake Storage a stisknutím klávesy **SHIFT + ENTER**. Tento ukázkový kód registruje data do dočasné tabulky nazývané **TVK**.

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

6. Vzhledem k tomu, že používáte jádro PySpark, můžete nyní přímo spustit dotaz SQL na dočasnou tabulku **TVK**, kterou jste právě vytvořili pomocí `%%sql` magic. Další informace o `%%sql` magic a také dalších Magic, které jsou k dispozici s jádrem pyspark, naleznete v tématu [jádra dostupná v poznámkových blocích Jupyter s clustery Apache Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. Po úspěšném dokončení úlohy se ve výchozím nastavení zobrazí následující tabulkový výstup.

      ![Tabulkový výstup výsledků dotazu](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Tabulkový výstup výsledků dotazu")

     Výsledky můžete také zobrazit v dalších vizualizacích. Například plošný graf pro stejný výstup bude vypadat následovně.

     ![Plošný graf výsledku dotazu](./media/apache-spark-use-with-data-lake-store/jupyter-area-output.png "Plošný graf výsledku dotazu")

8. Po dokončení spuštění aplikace byste měli poznámkový blok vypnout a uvolnit tak prostředky. To provedete kliknutím na položku **Zavřít a zastavit** z nabídky **Soubor** v poznámkovém bloku. Dojde k vypnutí a zavření poznámkového bloku.


## <a name="next-steps"></a>Další postup

* [Vytvoření samostatného Scala aplikaci pro spuštění v clusteru Apache Spark](apache-spark-create-standalone-application.md)
* [Vytváření aplikací Apache Spark pro cluster HDInsight Spark Linux pomocí nástrojů HDInsight v sadě Azure Toolkit pro IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Vytváření aplikací Apache Spark pro cluster HDInsight Spark Linux pomocí nástrojů HDInsight v sadě Azure Toolkit pro Eclipse](apache-spark-eclipse-tool-plugin.md)
* [Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
