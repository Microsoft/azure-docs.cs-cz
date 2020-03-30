---
title: 'Úvodní příručka: Vytvoření clusteru Spark ve HDInsightu pomocí portálu Azure'
description: Tento úvodní příručka ukazuje, jak pomocí portálu Azure vytvořit cluster Apache Spark v Azure HDInsight a spustit dotaz Spark SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2020
ms.openlocfilehash: 5f4d1b8ef742a8dcafa2b8e34a6209f85ae050d9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77650604"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Úvodní příručka: Vytvoření clusteru Apache Spark v Azure HDInsight pomocí Portálu Azure

V tomto rychlém startu použijete portál Azure k vytvoření clusteru Apache Spark v Azure HDInsight. Potom vytvoříte poznámkový blok Jupyter a použijete ho ke spuštění dotazů Spark SQL v tabulkách Apache Hive. Azure HDInsight je spravovaná opensourcová analytická služba určená pro podniky. Architektura Apache Spark pro HDInsight umožňuje rychlou analýzu dat a clusterové výpočty pomocí zpracování v paměti. Jupyter notebook umožňuje interakci s daty, kombinovat kód s textem markdown, a dělat jednoduché vizualizace.

Podrobné vysvětlení dostupných konfigurací naleznete v tématu [Nastavení clusterů v hdinsightu](../hdinsight-hadoop-provision-linux-clusters.md). Další informace týkající se použití portálu k vytvoření clusterů naleznete [v tématu Vytváření clusterů na portálu](../hdinsight-hadoop-create-linux-clusters-portal.md).

> [!IMPORTANT]  
> Clustery HDInsight se fakturují za minutu bez ohledu na to, jestli je používáte, nebo ne. Až přestanete cluster používat, nezapomeňte ho odstranit. Další informace najdete v části [Vyčištění prostředků](#clean-up-resources) tohoto článku.

## <a name="prerequisites"></a>Požadavky

Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Vytvoření clusteru Apache Spark v HDInsightu

Portál Azure se používá k vytvoření clusteru HDInsight, který používá objekty BLOB azure storage jako úložiště clusteru. Další informace o použití Data Lake Storage Gen2 najdete v tématu [Rychlý start: Nastavení clusterů ve službě HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. V horní nabídce vyberte **+ Vytvořit prostředek**.

    ![Azure Portal vytvoření prostředku](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-resource.png "Vytvořit prostředek na webu Azure Portal")

1. Vyberte **Analytics** > **Azure HDInsight** a přejděte na stránku **vytvořit cluster HDInsight.**

1. Na kartě **Základy** zadejte následující informace:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Předplatné  | V rozevíracím seznamu vyberte předplatné Azure, které se používá pro cluster. |
    |Skupina prostředků | V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte **Vytvořit nový**.|
    |Název clusteru | Zadejte globálně jedinečný název.|
    |Region (Oblast)   | V rozevíracím seznamu vyberte oblast, ve které je cluster vytvořen. |
    |Typ clusteru| Chcete-li otevřít seznam, vyberte vybrat typ clusteru. V seznamu vyberte **Spark**.|
    |Verze clusteru|Toto pole se automaticky naplní výchozí verzí, jakmile bude vybrán typ clusteru.|
    |Uživatelské jméno přihlášení clusteru| Zadejte uživatelské jméno přihlášení clusteru.  Výchozí název je **admin**. Tento účet slouží k přihlášení do poznámkového bloku Jupyter později v rychlém startu. |
    |Heslo přihlášení clusteru| Zadejte přihlašovací heslo clusteru. |
    |Uživatelské jméno Secure Shell (SSH)| Zadejte uživatelské jméno SSH. V tomto rychlém startu se používá uživatelské jméno SSH **sshuser**. Ve výchozím nastavení má tento účet stejné heslo jako účet *Uživatelské jméno přihlášení clusteru*. |

    ![Vytvoření základních konfigurací clusteru HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-basics-spark.png "Vytvoření clusteru Spark v HDInsight u základních konfigurací")

    Vyberte **Další: Úložiště >>** a pokračujte na stránku **Úložiště.**

1. V části **Úložiště** zadejte tyto hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Typ primárního úložiště|Použijte výchozí hodnotu **Azure Storage**.|
    |Metoda výběru|Použijte výchozí hodnotu **Vybrat ze seznamu**.|
    |Účet primárního úložiště|Použijte automaticky vyplněnou hodnotu.|
    |Kontejner|Použijte automaticky vyplněnou hodnotu.|

    ![Vytvoření základních konfigurací clusteru HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-storage.png "Vytvoření clusteru Spark v HDInsight u základních konfigurací")

    Chcete-li pokračovat, vyberte **zkontrolovat + vytvořit.**

1. V části **Revize + vytvoření**vyberte **Vytvořit**. Vytvoření clusteru trvá přibližně 20 minut. Než budete moct pokračovat k další relaci, musí se cluster nejdříve vytvořit.

Pokud narazíte na problém s vytvářením clusterů HDInsight, může se podařit, že k tomu nemáte správná oprávnění. Další informace najdete v tématu popisujícím [požadavky na řízení přístupu](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="create-a-jupyter-notebook"></a>Vytvoření poznámkového bloku Jupyter

Jupyter Notebook je interaktivní prostředí poznámkového bloku, které podporuje různé programovací jazyky. Poznámkový blok umožňuje pracovat s daty, kombinovat kód s textem markdownu a provádět jednoduché vizualizace.

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net/jupyter`přejděte `CLUSTERNAME` na , kde je název clusteru. Po zobrazení výzvy zadejte přihlašovací údaje clusteru.

1. Chcete-li vytvořit poznámkový blok, vyberte **nový** > **PySpark.**

   ![Vytvoření poznámkového bloku Jupyter pro spuštění interaktivního dotazu Spark SQL](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Vytvoření poznámkového bloku Jupyter pro spuštění interaktivního dotazu Spark SQL")

   Nový poznámkový blok se vytvoří a otevře s názvem Bez názvu (Bez názvu.pynb).

## <a name="run-apache-spark-sql-statements"></a>Spuštění příkazů APACHE Spark SQL

Jazyk SQL (Structured Query Language) je nejběžnějším a široce používaným jazykem pro dotazování a definování dat. Spark SQL funguje jako rozšíření Apache Spark pro zpracování strukturovaných dat a používá známou syntaxi jazyka SQL.

1. Ověřte, že je jádro připravené. Jádro bude připravené, až se vedle názvu jádra v poznámkovém bloku zobrazí prázdný kroužek. Plný kruh označuje, že je jádro zaneprázdněno.

    ![Dotaz Apache Hive v HDInsightu](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Dotaz na hive v HDInsight")

    Při prvním spuštění poznámkového bloku jádro provede některé úlohy na pozadí. Počkejte, až bude jádro připravené.

1. Do prázdné buňky vložte následující kód a stisknutím **SHIFT + ENTER** kód spusťte. Příkaz vypíše tabulky Hive v clusteru:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Když používáte jupyterový poznámkový blok s clusterem `sqlContext` HDInsight, získáte přednastavení, které můžete použít ke spuštění dotazů Hive pomocí Spark SQL. `%%sql` říká poznámkovému bloku Jupyter, aby ke spuštění dotazu Hive použil přednastavený kontext `sqlContext`. Dotaz načte prvních 10 řádků z tabulky Hive (**hivesampletable**), která je ve výchozím nastavení k dispozici na všech clusterech HDInsight. Získání výsledků trvá přibližně 30 sekund. Výstup bude vypadat následovně:

    ![Dotaz Apache Hive v HDInsightu](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query.png "Dotaz na hive v HDInsight")

    Při každém spuštění dotazu v Jupyter se v názvu okna webového prohlížeče zobrazí stav **(Busy)** (Zaneprázdněn) společně s názvem poznámkového bloku. Zobrazí se také plný kroužek vedle textu **PySpark** v pravém horním rohu.

1. Spuštěním dalšího dotazu zobrazíte data v tabulce `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Obrazovka by se měla aktualizovat a zobrazit výstup dotazu.

    ![Výstup dotazu hive v HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query-output.png "Výstup dotazu hive v HDInsight")

1. V nabídce **Soubor** poznámkového bloku vyberte **Zavřít a zastavit**. Ukončením poznámkového bloku se uvolní prostředky clusteru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

HDInsight ukládá vaše data do Azure Storage nebo Azure Data Lake Storage, takže můžete bezpečně odstranit cluster, když se nepoužívá. Účtuje se vám také cluster HDInsight, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster jsou mnohonásobně vyšší než poplatky za úložiště, má ekonomické smysl odstranit clustery, když nejsou používány. Pokud se chystáte hned začít pracovat na kurzu uvedeném v části [Další kroky](#next-steps), měli byste cluster zachovat.

Přepněte zpět na web Azure Portal a vyberte **Odstranit**.

![Azure Portal odstraní cluster HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-azure-portal-delete-cluster.png "Odstranění clusteru HDInsight")

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight i výchozí účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit cluster Apache Spark v HDInsightu a spustit základní dotaz Spark SQL. Přejdete k dalšímu kurzu, kde se dozvíte, jak pomocí clusteru HDInsight spouštět interaktivní dotazy na ukázková data.

> [!div class="nextstepaction"]
> [Spouštění interaktivních dotazů na Apache Spark](./apache-spark-load-data-run-query.md)
