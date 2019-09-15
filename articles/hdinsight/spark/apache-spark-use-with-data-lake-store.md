---
title: Použití Apache Spark k analýze dat v Azure Data Lake Storage Gen1
description: Spuštění úloh Apache Spark pro analýzu dat uložených v Azure Data Lake Storage Gen1
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: b8e830cb187b375e17b3dc33c582126adfa32f3e
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002494"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>Použití clusteru HDInsight Spark k analýze dat v Data Lake Storage Gen1

V tomto článku použijete [Jupyter notebook](https://jupyter.org/) dostupné s clustery HDInsight Spark ke spuštění úlohy, která čte data z účtu Data Lake Storage.

## <a name="prerequisites"></a>Požadavky

* Účet Azure Data Lake Storage Gen1. Postupujte podle pokynů v tématu [Začínáme s Azure Data Lake Storage Gen1 pomocí Azure Portal](../../data-lake-store/data-lake-store-get-started-portal.md).

* Azure HDInsight Spark cluster s Data Lake Storage Gen1 jako úložiště. Postupujte podle pokynů v [části rychlý Start: Nastavte clustery v HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

## <a name="prepare-the-data"></a>Příprava dat

> [!NOTE]  
> Pokud jste vytvořili cluster HDInsight s Data Lake Storage jako výchozí úložiště, nemusíte tento krok provádět. Proces vytvoření clusteru přidá ukázková data do účtu Data Lake Storage, který zadáte při vytváření clusteru. Přejděte k části použití clusteru HDInsight Spark s Data Lake Storage.

Pokud jste vytvořili cluster HDInsight s Data Lake Storage jako další úložiště a Azure Storage Blob jako výchozí úložiště, měli byste nejdřív zkopírovat ukázková data do účtu Data Lake Storage. Můžete použít ukázková data z Azure Storage Blob přidružená ke clusteru HDInsight. K tomu můžete použít [Nástroj ADLCopy](https://aka.ms/downloadadlcopy) . Stáhněte a nainstalujte si nástroj z odkazu.

1. Otevřete příkazový řádek a přejděte do adresáře, kde je nainstalovaný AdlCopy, obvykle `%HOMEPATH%\Documents\adlcopy`.

2. Spusťte následující příkaz ke zkopírování konkrétního objektu BLOB ze zdrojového kontejneru do Data Lake Storage:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Zkopírujte Ukázkový datový soubor **TVK. csv** na **/HdiSamples/HdiSamples/SensorSampleData/hvac/** do účtu Azure Data Lake Storage. Fragment kódu by měl vypadat takto:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]  
   > Ujistěte se, že názvy souborů a cest používají správnou velikost písmen.

3. Zobrazí se výzva k zadání přihlašovacích údajů k předplatnému Azure, pod kterým máte účet Data Lake Storage. Zobrazí se výstup, který bude podobný následujícímu fragmentu kódu:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Datový soubor (**TVK. csv**) se zkopíruje do složky **/hvac** v účtu Data Lake Storage.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>Použití clusteru HDInsight Spark s Data Lake Storage Gen1

1. Z [Azure Portal](https://portal.azure.com/)klikněte na dlaždici pro cluster Apache Spark (Pokud jste ho připnuli k úvodní panel). Můžete také přejít na cluster pod položkou **Procházet vše** > **Clustery HDInsight**.

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

5. Načtěte ukázková data do dočasné tabulky pomocí souboru **TVK. csv** , který jste zkopírovali do účtu Data Lake Storage Gen1. K datům v účtu Data Lake Storage můžete přistupovat pomocí následujícího vzoru adresy URL.

   * Pokud máte Data Lake Storage Gen1 jako výchozí úložiště, TVK. csv bude na cestě podobnou této adrese URL:

           adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

       Nebo můžete použít také zkrácený formát, například následující:

           adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

   * Pokud máte Data Lake Storage jako další úložiště, bude v umístění, kde jste ho zkopírovali, na místo, kam jste ho zkopírovali, například:

           adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     Do prázdné buňky vložte následující příklad kódu, nahraďte **MYDATALAKESTORE** názvem účtu Data Lake Storage a stiskněte **SHIFT + ENTER**. Tento ukázkový kód registruje data do dočasné tabulky nazývané **TVK**.

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

6. Vzhledem k tomu, že používáte jádro PySpark, můžete nyní přímo spustit dotaz SQL na dočasnou tabulku **TVK**, kterou jste právě vytvořili pomocí `%%sql` magic. Další informace o `%%sql` Magic a dalších přístupnosti, které jsou k dispozici v jádru PySpark, najdete v tématu [jádra dostupná na poznámkových blocích Jupyter s Apache Spark clustery HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. Po úspěšném dokončení úlohy se ve výchozím nastavení zobrazí následující tabulkový výstup.

      ![Tabulkový výstup výsledků dotazu](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Tabulkový výstup výsledků dotazu")

     Výsledky můžete také zobrazit v dalších vizualizacích. Například plošný graf pro stejný výstup bude vypadat následovně.

     ![Plošný graf výsledku dotazu](./media/apache-spark-use-with-data-lake-store/jupyter-area-output1.png "Plošný graf výsledku dotazu")

8. Po dokončení spuštění aplikace byste měli poznámkový blok vypnout a uvolnit tak prostředky. To provedete kliknutím na položku **Zavřít a zastavit** z nabídky **Soubor** v poznámkovém bloku. Dojde k vypnutí a zavření poznámkového bloku.


## <a name="next-steps"></a>Další postup

* [Vytvoření samostatné aplikace v Scala ke spuštění v clusteru Apache Spark](apache-spark-create-standalone-application.md)
* [Použití nástrojů HDInsight v Azure Toolkit for IntelliJ k vytvoření Apache Sparkch aplikací pro cluster HDInsight Spark Linux](apache-spark-intellij-tool-plugin.md)
* [Použití nástrojů HDInsight v Azure Toolkit for Eclipse k vytvoření Apache Sparkch aplikací pro cluster HDInsight Spark Linux](apache-spark-eclipse-tool-plugin.md)
* [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
