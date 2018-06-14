---
title: Použití Apache Spark k analýze dat v Azure Data Lake Store | Microsoft Docs
description: Spuštění úloh Spark k analýze dat uložených v Azure Data Lake Store
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1f174323-c17b-428c-903d-04f0e272784c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: fa6f93231cba46e29206ec312fb82ad120ed45f6
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31517620"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-store"></a>Použití clusteru HDInsight Spark k analýze dat v Data Lake Store

V tomto kurzu použijete k dispozici poznámkového bloku Jupyter s clustery HDInsight Spark k spustit úlohu, která čte data z účtu Data Lake Store.

## <a name="prerequisites"></a>Požadavky

* Účet Azure Data Lake Store. Postupujte podle pokynů v tématu [Začínáme s Azure Data Lake Store s použitím webu Azure Portal](../../data-lake-store/data-lake-store-get-started-portal.md).

* Cluster Azure HDInsight Spark s Data Lake Store jako úložiště. Postupujte podle pokynů v [vytvoření clusteru HDInsight s Data Lake Store pomocí portálu Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

    
## <a name="prepare-the-data"></a>Příprava dat

> [!NOTE]
> Není nutné k provedení tohoto kroku, pokud jste vytvořili clusteru HDInsight s Data Lake Store jako výchozí úložiště. Procesu vytváření clusteru přidá ukázková data v účtu Data Lake Store, který zadáte při vytváření clusteru. Přeskočit k části [clusteru používejte HDInsight Spark s Data Lake Store](#use-an-hdinsight-spark-cluster-with-data-lake-store).
>
>

Pokud jste vytvořili clusteru HDInsight s Data Lake Store jako další úložiště a Azure Blob Storage jako výchozí úložiště, měli byste nejprve zkopírovat přes ukázková data do účtu Data Lake Store. Můžete použít ukázkových dat z Azure Storage Blob přidruženého ke clusteru HDInsight. Můžete použít [ADLCopy nástroj](http://aka.ms/downloadadlcopy) Uděláte to tak. Stáhněte a nainstalujte nástroj z odkazu.

1. Otevřete příkazový řádek a přejděte do adresáře, kde AdlCopy je nainstalován, obvykle `%HOMEPATH%\Documents\adlcopy`.

2. Spusťte následující příkaz pro kopírování konkrétní objekt blob z kontejneru zdroje do Data Lake Store:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Kopírování **HVAC.csv** ukázková data souborů **/HdiSamples/HdiSamples/SensorSampleData/TVK/** k účtu Azure Data Lake Store. Fragment kódu by měla vypadat podobně jako:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]
   > Nezapomeňte, které jsou názvy souborů a cestu v případě, že správné.
   >
   >
3. Zobrazí se výzva k zadání přihlašovacích údajů pro předplatné Azure, ve kterém máte účtu Data Lake Store. Zobrazí se výstup, který bude podobný následujícímu fragmentu kódu:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Datový soubor (**HVAC.csv**) se zkopírují složce **/hvac** v účtu Data Lake Store.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-store"></a>Používání clusteru HDInsight Spark s Data Lake Store

1. Z [Portálu Azure](https://portal.azure.com/) z úvodního panelu klikněte na dlaždici pro váš cluster Spark (pokud je připnutý na úvodní panel). Můžete také přejít na cluster pod položkou **Procházet vše** > **Clustery HDInsight**.

2. Z okna clusteru Spark klikněte na tlačítko **Rychlé odkazy** a pak z okna **Řídicí panel clusteru** klikněte na tlačítko **Poznámkový blok Jupyter**. Po vyzvání zadejte přihlašovací údaje správce clusteru.

   > [!NOTE]
   > Může také otevřít poznámkový blok Jupyter pro váš cluster tak, že otevřete následující adresu URL v prohlížeči. Nahraďte **CLUSTERNAME** názvem clusteru:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. Vytvořte nový poznámkový blok. Klikněte na tlačítko **Nový** a pak klikněte na tlačítko **PySpark**.

    ![Vytvoření nového poznámkového bloku Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Vytvoření nového poznámkového bloku Jupyter")

4. Vzhledem k tomu, že jste poznámkový blok vytvořili pomocí jádra PySpark, není nutné explicitně tvořit kontexty. Kontexty Spark a Hive se automaticky vytvoří za vás při spuštění první buňky kódu. Můžete začít importem typů nezbytných pro tento scénář. Chcete-li tak učinit, vložte následující fragment kódu do buňky a stiskněte klávesu **SHIFT + ENTER**.

        from pyspark.sql.types import *

    Při každém spuštění úlohy v Jupyter se název okna webového prohlížeče zobrazí jako **(Zaneprázdněn)** společně s názvem poznámkového bloku. Zobrazí se také plný kroužek vedle textu **PySpark** v pravém horním rohu. Po dokončení úlohy se změní na prázdný kruh.

     ![Stav úlohy poznámkového bloku Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Stav úlohy poznámkového bloku Jupyter")

5. Načíst ukázková data do dočasné tabulky pomocí **HVAC.csv** soubor zkopírován do účtu Data Lake Store. Měli přístup k datům v účtu Data Lake Store pomocí následující vzor adresy URL.

    * Pokud máte Data Lake Store jako výchozí úložiště, HVAC.csv bude v cestě podobná následující adresu URL:

            adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

        Nebo můžete použít taky zkrácení formátu například následující:

            adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

    * Pokud máte Data Lake Store jako další úložiště, HVAC.csv bude v umístění, kam jste zkopírovali, jako například:

            adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     Do prázdné buňky vložte následující příklad kódu, nahraďte **MYDATALAKESTORE** s názvem účtu Data Lake Store a stiskněte klávesu **SHIFT + ENTER**. Tento ukázkový kód registruje data do dočasné tabulky nazývané **TVK**.

            # Load the data. The path below assumes Data Lake Store is default storage for the Spark cluster
            hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            # Create the schema
            hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

            # Parse the data in hvacText
            hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

            # Create a data frame
            hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

            # Register the data fram as a table to run queries against
            hvacdf.registerTempTable("hvac")

6. Vzhledem k tomu, že používáte jádro PySpark, můžete nyní přímo spustit dotaz SQL na dočasnou tabulku **TVK**, kterou jste právě vytvořili pomocí `%%sql` magic. Další informace o `%%sql` magic a také dalších magic, které jsou k dispozici s jádrem PySpark, naleznete v části [Jádra dostupná v poznámkových blocích Jupyter s clustery Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. Po úspěšném dokončení úlohy se ve výchozím nastavení zobrazí následující tabulkový výstup.

      ![Tabulkový výstup výsledků dotazu](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Tabulkový výstup výsledků dotazu")

     Výsledky můžete také zobrazit v dalších vizualizacích. Například plošný graf pro stejný výstup bude vypadat následovně.

     ![Plošný graf výsledku dotazu](./media/apache-spark-use-with-data-lake-store/jupyter-area-output.png "Plošný graf výsledku dotazu")

8. Po dokončení spuštění aplikace byste měli poznámkový blok vypnout a uvolnit tak prostředky. To provedete kliknutím na položku **Zavřít a zastavit** z nabídky **Soubor** v poznámkovém bloku. Dojde k vypnutí a zavření poznámkového bloku.


## <a name="next-steps"></a>Další postup

* [Vytvořit samostatný spuštění v clusteru Apache Spark Scala aplikace](apache-spark-create-standalone-application.md)
* [Použití nástrojů HDInsight v Azure nástrojů pro IntelliJ k vytvoření aplikací Spark pro cluster HDInsight Spark Linux](apache-spark-intellij-tool-plugin.md)
* [Použití nástrojů HDInsight v Azure nástrojů pro Eclipse k vytvoření aplikací Spark pro cluster HDInsight Spark Linux](apache-spark-eclipse-tool-plugin.md)
