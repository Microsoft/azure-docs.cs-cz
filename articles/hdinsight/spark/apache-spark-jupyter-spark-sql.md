---
title: 'Úvodní příručka: Vytvoření clusteru Apache Spark pomocí šablony – Azure HDInsight'
description: Tento úvodní příručka ukazuje, jak pomocí šablony Správce prostředků vytvořit cluster Apache Spark v Azure HDInsight a spustit dotaz Spark SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 6d590659a4ed73fa27193961721d949b555c3444
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064537"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Úvodní příručka: Vytvoření clusteru Apache Spark v Azure HDInsight pomocí šablony Správce prostředků

V tomto rychlém startu použijete šablonu Azure Resource Manager k vytvoření clusteru [Apache Spark](./apache-spark-overview.md) v Azure HDInsight. Potom vytvoříte poznámkový blok Jupyter a použijete ho ke spuštění dotazů Spark SQL v tabulkách Apache Hive. Azure HDInsight je spravovaná opensourcová analytická služba určená pro podniky. Architektura Apache Spark pro HDInsight umožňuje rychlou analýzu dat a clusterové výpočty pomocí zpracování v paměti. Jupyter notebook umožňuje interakci s daty, kombinovat kód s textem markdown, a dělat jednoduché vizualizace.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-an-apache-spark-cluster"></a>Vytvoření clusteru Apache Spark

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-spark-linux).

:::code language="json" source="~/quickstart-templates/101-hdinsight-spark-linux/azuredeploy.json" range="1-143":::

V šabloně jsou definovány dva prostředky Azure:

* [Microsoft.Storage/storageAccounts:](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts)vytvořte účet úložiště Azure.
* [Microsoft.HDInsight/cluster:](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters)vytvořte cluster HDInsight.

### <a name="deploy-the-template"></a>Nasazení šablony

1. Kliknutím na tlačítko **Nasadit do Azure** se přihlaste do Azure a otevřete šablonu Správce prostředků.

    [![Nasazení do Azure](./media/apache-spark-jupyter-spark-sql/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

1. Zadejte nebo vyberte tyto hodnoty:

    |Vlastnost |Popis |
    |---|---|
    |Předplatné|V rozevíracím seznamu vyberte předplatné Azure, které se používá pro cluster.|
    |Skupina prostředků|V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte **Vytvořit nový**.|
    |Umístění|Hodnota se automaticky naplní umístěním použitým pro skupinu prostředků.|
    |Název clusteru|Zadejte globálně jedinečný název. Pro tuto šablonu používejte pouze malá písmena a čísla.|
    |Uživatelské jméno přihlášení clusteru|Zadejte uživatelské jméno, výchozí je **admin**.|
    |Heslo přihlášení clusteru|Zadejte heslo. Heslo musí mít délku alespoň 10 znaků a musí obsahovat alespoň jednu číslici, jedno velké a jedno malé písmeno, jeden nealfanumerický znak (kromě znaků " " ). |
    |Uživatelské jméno SSH|Zadejte uživatelské jméno, výchozí je **sshuser**|
    |Ssh heslo|Zadejte heslo.|

    ![Vytvoření clusteru Spark v HDInsightu pomocí šablony Azure Resource Manager](./media/apache-spark-jupyter-spark-sql/resource-manager-template-spark.png "Vytvoření clusteru Spark v HDInsightu pomocí šablony Azure Resource Manageru")

1. Přečtěte si **podmínky**. Pak vyberte **Souhlasím s podmínkami uvedenými výše**, pak **nákup**. Obdržíte oznámení, že probíhá nasazení. Vytvoření clusteru trvá přibližně 20 minut.

Pokud narazíte na problém s vytvářením clusterů HDInsight, může se podařit, že k tomu nemáte správná oprávnění. Další informace najdete v tématu popisujícím [požadavky na řízení přístupu](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Po vytvoření clusteru obdržíte oznámení **o úspěšném nasazení** s odkazem **přejít na prostředek.** Na stránce skupiny prostředků bude uveden nový cluster HDInsight a výchozí úložiště přidružené k clusteru. Každý cluster má účet [Azure Storage](../hdinsight-hadoop-use-blob-storage.md) nebo závislost na účtu Azure Data [Lake Storage.](../hdinsight-hadoop-use-data-lake-store.md) Označuje se jako výchozí účet úložiště. Cluster HDInsight a jeho výchozí účet úložiště musí být společně umístěny ve stejné oblasti Azure. Odstraněním clusterů účet úložiště neodstraníte.

## <a name="create-a-jupyter-notebook"></a>Vytvoření poznámkového bloku Jupyter

[Jupyter Notebook](https://jupyter.org/) je interaktivní notebook prostředí, které podporuje různé programovací jazyky. Poznámkový blok umožňuje interakci s daty, kombinaci kódu s textem markdownu a provádění jednoduchých vizualizací.

1. Otevřete [portál Azure](https://portal.azure.com).

2. Vyberte **Clustery HDInsight** a pak vyberte cluster, který jste vytvořili.

    ![Otevření clusteru HDInsight na webu Azure Portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. Na portálu vyberte v části **Řídicí panely clusteru** **poznámkový blok Jupyter .** Po zobrazení výzvy zadejte přihlašovací údaje clusteru.

   ![Otevření poznámkového bloku Jupyter spustí interaktivní dotaz Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Otevření poznámkového bloku Jupyter spustí interaktivní dotaz Spark SQL")

4. Chcete-li vytvořit poznámkový blok, vyberte **nový** > **PySpark.**

   ![Vytvoření poznámkového bloku Jupyter pro spuštění interaktivního dotazu Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Vytvoření poznámkového bloku Jupyter pro spuštění interaktivního dotazu Spark SQL")

   Nový poznámkový blok se vytvoří a otevře s názvem Bez názvu (Bez názvu.pynb).

## <a name="run-apache-spark-sql-statements"></a>Spuštění příkazů APACHE Spark SQL

Jazyk SQL (Structured Query Language) je nejběžnějším a široce používaným jazykem pro dotazování a transformaci dat. Spark SQL funguje jako rozšíření Apache Spark pro zpracování strukturovaných dat a používá známou syntaxi jazyka SQL.

1. Ověřte, že je jádro připravené. Jádro bude připravené, až se vedle názvu jádra v poznámkovém bloku zobrazí prázdný kroužek. Plný kruh označuje, že je jádro zaneprázdněno.

    ![Stav jádra](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Stav jádra")

    Při prvním spuštění poznámkového bloku jádro provede některé úlohy na pozadí. Počkejte, až bude jádro připravené.

1. Do prázdné buňky vložte následující kód a stisknutím **SHIFT + ENTER** kód spusťte. Příkaz vypíše tabulky Hive v clusteru:

    ```sql
    %%sql
    SHOW TABLES
    ```

    Když používáte jupyterový poznámkový blok s clusterem `spark` HDInsight, získáte přednastavenou relaci, kterou můžete použít ke spuštění dotazů Hive pomocí Spark SQL. `%%sql` říká poznámkovému bloku Jupyter, aby ke spuštění dotazu Hive použil přednastavenou relaci `spark`. Dotaz načte prvních 10 řádků z tabulky Hive (**hivesampletable**), která je ve výchozím nastavení k dispozici na všech clusterech HDInsight. Při prvním odeslání dotazu vytvoří Jupyter pro poznámkový blok aplikaci Spark. Dokončení trvá přibližně 30 sekund. Jakmile je aplikace Spark připravená, dotaz se spustí přibližně za sekundu a vytvoří výsledky. Výstup bude vypadat následovně:

    ![Dotaz Apache Hive v HDInsightu](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Dotaz na hive v HDInsight")

    Při každém spuštění dotazu v Jupyter se v názvu okna webového prohlížeče zobrazí stav **(Busy)** (Zaneprázdněn) společně s názvem poznámkového bloku. Zobrazí se také plný kroužek vedle textu **PySpark** v pravém horním rohu.

1. Spuštěním dalšího dotazu zobrazíte data v tabulce `hivesampletable`.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Obrazovka by se měla aktualizovat a zobrazit výstup dotazu.

    ![Výstup dotazu hive v HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Výstup dotazu hive v HDInsight")

1. V nabídce **Soubor** poznámkového bloku vyberte **Zavřít a zastavit**. Vypnutí poznámkového bloku uvolní prostředky clusteru, včetně aplikace Spark.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu můžete cluster odstranit. S HDInsight, vaše data jsou uloženy ve službě Azure Storage, takže můžete bezpečně odstranit clusteru, když není v provozu. Účtuje se vám také cluster HDInsight, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster jsou mnohonásobně vyšší než poplatky za úložiště, má ekonomické smysl odstranit clustery, když nejsou používány.

Na webu Azure navigujte do svého clusteru a vyberte **Odstranit**.

![Azure Portal odstraní cluster HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Odstranění clusteru HDInsight")

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight i výchozí účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit cluster Apache Spark v HDInsightu a spustit základní dotaz Spark SQL. Přejdete k dalšímu kurzu, kde se dozvíte, jak pomocí clusteru HDInsight spouštět interaktivní dotazy na ukázková data.

> [!div class="nextstepaction"]
> [Spouštění interaktivních dotazů na Apache Spark](./apache-spark-load-data-run-query.md)
