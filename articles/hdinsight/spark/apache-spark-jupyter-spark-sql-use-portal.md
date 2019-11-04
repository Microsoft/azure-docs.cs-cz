---
title: 'Rychlý Start: Vytvoření clusteru Spark ve službě HDInsight pomocí Azure Portal'
description: V tomto rychlém startu se dozvíte, jak pomocí webu Azure Portal vytvořit cluster Apache Spark v Azure HDInsight a jak spustit Spark SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 09/27/2019
ms.custom: mvc
ms.openlocfilehash: 214a88ad8b0f5dd0352004edc125705ad2f04fc7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494133"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Rychlý Start: Vytvoření clusteru Apache Spark ve službě Azure HDInsight pomocí Azure Portal

Zjistěte, jak vytvořit cluster Apache Spark v Azure HDInsight a jak spustit dotazy Spark SQL na tabulky Hive. Apache Spark umožňuje rychlou analýzu dat a clusterové výpočty s využitím zpracování v paměti. Informace o Apache Spark ve službě HDInsight najdete v tématu [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md).

V tomto rychlém startu pomocí webu Azure Portal vytvoříte cluster HDInsight Spark. Cluster jako úložiště využívá Azure Storage Blob. Další informace o použití Data Lake Storage Gen2 najdete v tématu [Rychlý start: Nastavení clusterů ve službě HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

> [!IMPORTANT]  
> Clustery HDInsight se fakturují za minutu bez ohledu na to, jestli je používáte, nebo ne. Až přestanete cluster používat, nezapomeňte ho odstranit. Další informace najdete v části [Vyčištění prostředků](#clean-up-resources) tohoto článku.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-hdinsight-spark-cluster"></a>Vytvoření clusteru HDInsight Spark

1. V Azure Portal vyberte **vytvořit prostředek** > **Analytics** > **HDInsight**.

    ![Azure Portal vytvoření prostředku HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster.png "HDInsight na Azure Portal")

1. V části **Základy** zadejte tyto hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Předplatné  | V rozevíracím seznamu vyberte předplatné Azure používané pro tento cluster. Předplatné použité pro tento rychlý Start je **Azure**. |
    |Skupina prostředků | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Název skupiny prostředků, který se používá pro tento rychlý Start, je **myResourceGroup**. |
    |Název clusteru | Pojmenujte svůj cluster HDInsight Spark. Název clusteru, který se používá pro tento rychlý Start, je **myspark2019**.|
    |Umístění   | Vyberte umístění skupiny prostředků. Šablona toto umístění používá k vytvoření clusteru i jako výchozí úložiště clusteru. Umístění použité pro tento rychlý Start je **východní USA**. |
    |Typ clusteru| Jako typ clusteru vyberte **Spark** .|
    |Verze clusteru|Po výběru typu clusteru bude toto pole automaticky vyplněno výchozí verzí.|
    |Uživatelské jméno přihlášení clusteru| Zadejte uživatelské jméno přihlášení clusteru.  Výchozí název je *admin*. Tento účet použijete k přihlášení do poznámkového bloku Jupyter později v rychlém startu. |
    |Heslo přihlášení clusteru| Zadejte přihlašovací heslo clusteru. |
    |Uživatelské jméno Secure Shell (SSH)| Zadejte uživatelské jméno SSH. V tomto rychlém startu se používá uživatelské jméno SSH **sshuser**. Ve výchozím nastavení má tento účet stejné heslo jako účet *Uživatelské jméno přihlášení clusteru*. |

    ![Vytvoření základních konfigurací clusteru HDInsight Spark](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-basics-spark.png "Vytvoření clusteru Spark v HDInsight základní konfigurace")

    Kliknutím na tlačítko **Další: úložiště > >** pokračujte na stránku **úložiště** .

1. V části **Úložiště** zadejte tyto hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Typ primárního úložiště|Použijte výchozí hodnotu **Azure Storage**.|
    |Metoda výběru|Použijte výchozí hodnotu **vybrat ze seznamu**.|
    |Účet primárního úložiště|Použijte automaticky vyplněnou hodnotu.|
    |Kontejner|Použijte automaticky vyplněnou hodnotu.|

    ![Vytvoření základních konfigurací clusteru HDInsight Spark](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-storage-spark.png "Vytvoření clusteru Spark v HDInsight základní konfigurace")

    Pokračujte výběrem **Zobrazit + vytvořit** .

1. V nabídce **Revize + vytvořit**vyberte **vytvořit**. Vytvoření clusteru trvá přibližně 20 minut. Než budete moct pokračovat k další relaci, musí se cluster nejdříve vytvořit.

Pokud narazíte na problém s vytvářením clusterů HDInsight, může to být způsobeno tím, že k tomu nemáte správná oprávnění. Další informace najdete v tématu popisujícím [požadavky na řízení přístupu](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="create-a-jupyter-notebook"></a>Vytvoření poznámkového bloku Jupyter

Jupyter Notebook je interaktivní prostředí poznámkového bloku, které podporuje různé programovací jazyky. Poznámkový blok umožňuje pracovat s daty, kombinovat kód s textem markdownu a provádět jednoduché vizualizace.

1. Otevřete web [Azure Portal](https://portal.azure.com).

1. Vyberte **Clustery HDInsight** a pak vyberte cluster, který jste vytvořili.

    ![otevření clusteru HDInsight na webu Azure Portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

1. Na portálu vyberte **Řídicí panely clusteru** a pak vyberte **Jupyter Notebook**. Po zobrazení výzvy zadejte přihlašovací údaje clusteru.

   ![Otevřete Jupyter Notebook pro spuštění interaktivního dotazu Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Otevřete Jupyter Notebook pro spuštění interaktivního dotazu Spark SQL")

1. Vyberte **Nový** > **PySpark** a vytvořte poznámkový blok.

   ![Vytvoření Jupyter Notebook pro spuštění interaktivního dotazu Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Vytvoření Jupyter Notebook pro spuštění interaktivního dotazu Spark SQL")

   Nový poznámkový blok se vytvoří a otevře s názvem Bez názvu (Bez názvu.pynb).

## <a name="run-spark-sql-statements"></a>Spouštění příkazů Spark SQL

Jazyk SQL (Structured Query Language) je nejběžnějším a široce používaným jazykem pro dotazování a definování dat. Spark SQL funguje jako rozšíření Apache Spark pro zpracování strukturovaných dat a používá známou syntaxi jazyka SQL.

1. Ověřte, že je jádro připravené. Jádro bude připravené, až se vedle názvu jádra v poznámkovém bloku zobrazí prázdný kroužek. Plný kruh označuje, že je jádro zaneprázdněno.

    ![Apache Hive dotazování v HDInsight Spark1](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Dotaz na podregistr v HDInsight Spark1")

    Při prvním spuštění poznámkového bloku jádro provede některé úlohy na pozadí. Počkejte, až bude jádro připravené.

1. Do prázdné buňky vložte následující kód a stisknutím **SHIFT + ENTER** kód spusťte. Příkaz vypíše tabulky Hive v clusteru:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Pokud používáte poznámkový blok Jupyter s clusterem HDInsight Spark, získáte přednastavený kontext `sqlContext`, který můžete použít ke spouštění dotazů Hive pomocí Spark SQL. `%%sql` říká poznámkovému bloku Jupyter, aby ke spuštění dotazu Hive použil přednastavený kontext `sqlContext`. Dotaz načte prvních 10 řádků z tabulky Hive (**hivesampletable**), která je ve výchozím nastavení k dispozici na všech clusterech HDInsight. Získání výsledků trvá přibližně 30 sekund. Výstup bude vypadat následovně:

    ![Apache Hive dotazování v HDInsight Spark2](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Dotaz na podregistr v HDInsight Spark2")

    Při každém spuštění dotazu v Jupyter se v názvu okna webového prohlížeče zobrazí stav **(Busy)** (Zaneprázdněn) společně s názvem poznámkového bloku. Zobrazí se také plný kroužek vedle textu **PySpark** v pravém horním rohu.

1. Spuštěním dalšího dotazu zobrazíte data v tabulce `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Obrazovka by se měla aktualizovat a zobrazit výstup dotazu.

    ![Výstup dotazů na podregistr v HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Výstup dotazů na podregistr v HDInsight Spark")

1. V nabídce **Soubor** poznámkového bloku vyberte **Zavřít a zastavit**. Ukončením poznámkového bloku se uvolní prostředky clusteru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

HDInsight ukládá vaše data ve službě Azure Storage nebo v úložišti Azure Data Lake, takže můžete cluster bezpečně odstranit, když se nepoužívá. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány. Pokud se chystáte hned začít pracovat na kurzu uvedeném v části [Další kroky](#next-steps), měli byste cluster zachovat.

Přepněte zpět na web Azure Portal a vyberte **Odstranit**.

![Azure Portal odstranit cluster HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Odstranit cluster HDInsight")

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight Spark i výchozí účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit cluster HDInsight Spark a spustit základní dotaz Spark SQL. V dalším kurzu se dozvíte, jak pomocí clusteru HDInsight Spark spouštět interaktivní dotazy na ukázková data.

> [!div class="nextstepaction"]
> [Spouštění interaktivních dotazů na Apache Spark](./apache-spark-load-data-run-query.md)
