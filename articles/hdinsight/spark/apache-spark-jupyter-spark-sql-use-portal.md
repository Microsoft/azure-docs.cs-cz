---
title: 'Rychlý Start: Vytvoření clusteru Spark v HDInsight pomocí Azure Portal'
description: V tomto rychlém startu se dozvíte, jak pomocí Azure Portal vytvořit cluster Apache Spark ve službě Azure HDInsight a spustit Spark SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 09/27/2019
ms.custom: mvc
ms.openlocfilehash: a4c7fe0d01bc9e5045cfe585c3f235636aa3dd22
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676974"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Rychlý Start: Vytvoření clusteru Apache Spark ve službě Azure HDInsight pomocí Azure Portal

Naučte se vytvářet clustery Apache Spark v Azure HDInsight a spouštět dotazy Spark SQL na tabulkách podregistru. Apache Spark umožňuje rychlé analýzy dat a výpočetní výkon clusteru pomocí zpracování v paměti. Informace o Sparku ve službě HDInsight najdete v tématu [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md).

V tomto rychlém startu použijete Azure Portal k vytvoření clusteru HDInsight Spark. Cluster používá jako úložiště clusteru Azure Storage objekty blob. Další informace o používání Data Lake Storage Gen2 najdete v tématu [rychlý Start: nastavení clusterů v HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

> [!IMPORTANT]  
> Fakturace za clustery HDInsight se účtuje poměrnou rychlostí za minutu, ať už je používáte, nebo ne. Po dokončení používání clusteru nezapomeňte tento cluster odstranit. Další informace najdete v části [vyčištění prostředků](#clean-up-resources) v tomto článku.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-hdinsight-spark-cluster"></a>Vytvoření clusteru HDInsight Spark

1. V Azure Portal vyberte **vytvořit prostředek** > **Analytics** > **HDInsight**.

    ![Azure Portal vytvoření prostředku HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster.png "HDInsight na Azure Portal")

1. V části **základy**zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |formě  | V rozevíracím seznamu vyberte předplatné Azure, které se používá pro tento cluster. Předplatné použité pro tento rychlý Start je **Azure**. |
    |Skupina prostředků | Určete, zda chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Název skupiny prostředků, který se používá pro tento rychlý Start, je **myResourceGroup**. |
    |Název clusteru | Zadejte název clusteru HDInsight Spark. Název clusteru, který se používá pro tento rychlý Start, je **myspark2019**.|
    |Umístění   | Vyberte umístění pro skupinu prostředků. Šablona používá toto umístění pro vytvoření clusteru i pro výchozí úložiště clusteru. Umístění použité pro tento rychlý Start je **východní USA**. |
    |Typ clusteru| Jako typ clusteru vyberte **Spark** .|
    |Verze clusteru|Po výběru typu clusteru bude toto pole automaticky vyplněno výchozí verzí.|
    |Uživatelské jméno přihlášení clusteru| Zadejte uživatelské jméno pro přihlášení ke clusteru.  Výchozí název je *admin*. Tento účet použijete k přihlášení do poznámkového bloku Jupyter později v rychlém startu. |
    |Heslo pro přihlášení ke clusteru| Zadejte heslo pro přihlášení ke clusteru. |
    |Uživatelské jméno Secure Shell (SSH)| Zadejte uživatelské jméno SSH. Uživatelské jméno SSH, které se používá pro tento rychlý Start, je **sshuser**. Ve výchozím nastavení tento účet sdílí stejné heslo jako účet *přihlášení ke clusteru* . |

    ![Vytvoření clusteru HDInsight Spark základní konfigurace](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-basics-spark.png "Vytvoření clusteru Spark v HDInsight základní konfigurace")

    Kliknutím na tlačítko **Další: úložiště > >** pokračujte na stránku **úložiště** .

1. V části **Storage (úložiště**) zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Typ primárního úložiště|Použijte výchozí hodnotu **Azure Storage**.|
    |Metoda výběru|Použijte výchozí hodnotu **vybrat ze seznamu**.|
    |Primární účet úložiště|Použijte automaticky vyplněnou hodnotu.|
    |Vnitřního|Použijte automaticky vyplněnou hodnotu.|

    ![Vytvoření clusteru HDInsight Spark základní konfigurace](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-storage-spark.png "Vytvoření clusteru Spark v HDInsight základní konfigurace")

    Pokračujte výběrem **Zobrazit + vytvořit** .

1. V nabídce **Revize + vytvořit**vyberte **vytvořit**. Vytvoření clusteru trvá přibližně 20 minut. Aby bylo možné přejít k další relaci, musí být cluster vytvořen.

Pokud narazíte na problém s vytvářením clusterů HDInsight, může to být tím, že nemáte správná oprávnění k tomu. Další informace najdete v tématu [požadavky na řízení přístupu](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="create-a-jupyter-notebook"></a>Vytvoření poznámkového bloku Jupyter

Jupyter Notebook je interaktivní prostředí poznámkového bloku, které podporuje různé programovací jazyky. Poznámkový blok vám umožní pracovat s daty, kombinovat kód s Markdownu textem a provádět jednoduché vizualizace.

1. Otevřete [Azure Portal](https://portal.azure.com).

1. Vyberte **clustery HDInsight**a pak vyberte cluster, který jste vytvořili.

    ![Otevřete cluster HDInsight v Azure Portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

1. Na portálu vyberte **řídicí panely clusteru**a pak vyberte **Jupyter notebook**. Pokud se zobrazí výzva, zadejte přihlašovací údaje clusteru pro cluster.

   ![Otevřete Jupyter notebook pro spuštění interaktivního dotazu Spark sql](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter notebook spuštění interaktivního dotazu Spark SQL") .

1. Pokud chcete vytvořit Poznámkový blok, vyberte **nový** > **PySpark** .

   ![Vytvoření Jupyter notebook pro spuštění interaktivního dotazu Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Vytvoření Jupyter notebook pro spuštění interaktivního dotazu Spark SQL")

   Vytvoří se nový Poznámkový blok a otevře se s názvem bez názvu (bez názvu. pynb).

## <a name="run-spark-sql-statements"></a>Spuštění příkazů Spark SQL

SQL (jazyk SQL (Structured Query Language)) je nejběžnějším a široce používaným jazykem pro dotazování a definování dat. Spark SQL funguje jako rozšíření Apache Spark pro zpracování strukturovaných dat pomocí známé syntaxe SQL.

1. Ověřte, jestli je jádro připravené. Jádro je připravené, když se vedle názvu jádra v poznámkovém bloku zobrazí prázdný kruh. Plný kroužek označuje, že je jádro zaneprázdněno.

    Dotaz ![Apache Hive v dotazu na podregistr HDInsight Spark1](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "v HDInsight Spark1")

    Při prvním spuštění poznámkového bloku jádro provede některé úlohy na pozadí. Počkejte, až bude jádro připravené.

1. Vložte do prázdné buňky následující kód a stisknutím klávesy **SHIFT + ENTER** kód spusťte. Příkaz vypíše tabulky podregistru v clusteru:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Když použijete Jupyter Notebook s clusterem HDInsight Spark, získáte přednastavení `sqlContext`, které můžete použít ke spouštění dotazů na podregistr pomocí Spark SQL. `%%sql` oznamuje Jupyter Notebook použití přednastaveného `sqlContext` pro spuštění dotazu na podregistr. Dotaz načte prvních 10 řádků z tabulky podregistru (**hivesampletable**), která je ve výchozím nastavení součástí všech clusterů HDInsight. Získání výsledků trvá přibližně 30 sekund. Výstup vypadá takto:

    Dotaz ![Apache Hive v dotazu na podregistr HDInsight Spark2](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "v HDInsight Spark2")

    Pokaždé, když spustíte dotaz v Jupyter, zobrazí se v názvu okna webového prohlížeče stav **(zaneprázdněn)** společně s názvem poznámkového bloku. Zobrazí se také plný kroužek vedle textu **PySpark** v pravém horním rohu.

1. Spusťte jiný dotaz, aby se zobrazila data v `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Obrazovka se aktualizuje a zobrazí se výstup dotazu.

    ![Výstup dotazů na podregistr ve](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "výstupu dotazů na podregistr HDInsight Spark v HDInsight Spark")

1. V nabídce **soubor** v poznámkovém bloku vyberte **Zavřít a zastavit**. Vypínání poznámkového bloku uvolní prostředky clusteru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

HDInsight ukládá vaše data do Azure Storage nebo Azure Data Lake Storage, takže můžete cluster bezpečně odstranit, pokud se nepoužívá. Účtují se vám také poplatky za cluster HDInsight, a to i v případě, že se nepoužívá. Vzhledem k tomu, že se poplatky za cluster mnohokrát účtují rychleji než poplatky za úložiště, má ekonomický smysl odstraňovat clustery, když se nepoužívají. Pokud plánujete pracovat v kurzu, který je uvedený v části [Další kroky](#next-steps) hned, možná budete chtít zachovat cluster.

Přepněte zpátky na Azure Portal a vyberte **Odstranit**.

![Azure Portal odstranění clusteru HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Odstranit cluster HDInsight")

Můžete také vybrat název skupiny prostředků a otevřít tak stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight Spark i výchozí účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit cluster HDInsight Spark a spustit základní dotaz Spark SQL. Přejděte k dalšímu kurzu, kde se dozvíte, jak pomocí clusteru HDInsight Spark spouštět interaktivní dotazy na vzorových datech.

> [!div class="nextstepaction"]
> [Spouštění interaktivních dotazů na Apache Spark](./apache-spark-load-data-run-query.md)
