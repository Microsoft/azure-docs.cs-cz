---
title: 'Rychlý Start: Vytvoření clusteru Apache Spark pomocí šablony – Azure HDInsight'
description: V tomto rychlém startu se dozvíte, jak pomocí šablony Správce prostředků vytvořit cluster služby Apache Spark ve službě Azure HDInsight a spustit dotaz Spark SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 132b0f095ef1767d7416aefc71dced2a15769701
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89230479"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-arm-template"></a>Rychlý Start: Vytvoření clusteru Apache Spark ve službě Azure HDInsight pomocí šablony ARM

V tomto rychlém startu použijete šablonu Azure Resource Manager (šablona ARM) k vytvoření clusteru [Apache Spark](./apache-spark-overview.md) ve službě Azure HDInsight. Pak vytvoříte soubor Jupyter Notebook a použijete ho ke spouštění dotazů Spark SQL pro tabulky Apache Hive. Azure HDInsight je spravovaná opensourcová analytická služba určená pro podniky. Rozhraní Apache Spark Framework for HDInsight umožňuje rychlé analýzy dat a výpočetní výkon clusteru pomocí zpracování v paměti. Jupyter Notebook vám umožní pracovat s daty, kombinovat kód s textem Markdownu a provádět jednoduché vizualizace.

Pokud používáte více clusterů společně, budete chtít vytvořit virtuální síť a pokud používáte cluster Spark, budete také chtít použít konektor pro skladiště z podregistru. Další informace najdete v tématu [plánování virtuální sítě pro Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) a [integrace Apache Spark a Apache Hive pomocí konektoru skladu s podregistru](../interactive-query/apache-hive-warehouse-connector.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux).

:::code language="json" source="~/quickstart-templates/101-hdinsight-spark-linux/azuredeploy.json":::

V šabloně jsou definované dva prostředky Azure:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): vytvořte účet Azure Storage.
* [Microsoft. HDInsight/cluster](/azure/templates/microsoft.hdinsight/clusters): Vytvořte cluster HDInsight.

## <a name="deploy-the-template"></a>Nasazení šablony

1. Kliknutím na tlačítko **nasadit do Azure** níže se přihlaste k Azure a otevřete šablonu ARM.

    [![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

1. Zadejte nebo vyberte tyto hodnoty:

    |Vlastnost |Popis |
    |---|---|
    |Předplatné|V rozevíracím seznamu vyberte předplatné Azure, které se používá pro cluster.|
    |Skupina prostředků|V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte **vytvořit novou**.|
    |Umístění|Hodnota bude automaticky vyplněna umístěním použitým pro skupinu prostředků.|
    |Název clusteru|Zadejte globálně jedinečný název. Pro tuto šablonu použijte jenom malá písmena a čísla.|
    |Uživatelské jméno přihlášení clusteru|Zadejte uživatelské jméno, výchozí nastavení je **admin**.|
    |Heslo přihlášení clusteru|Zadejte heslo. Heslo musí mít minimálně 10 znaků a musí obsahovat aspoň jedno číslo, jedno velké písmeno a jedno malé písmeno, jeden jiný než alfanumerický znak (kromě znaků). |
    |Uživatelské jméno SSH|Zadejte uživatelské jméno, výchozí hodnota je **sshuser**|
    |Heslo SSH|Zadejte heslo.|

    ![Vytvoření clusteru Spark ve službě HDInsight pomocí šablony Azure Resource Manager](./media/apache-spark-jupyter-spark-sql/resource-manager-template-spark.png "Vytvoření clusteru Spark ve službě HDInsight pomocí šablony Azure Resource Manager")

1. Přečtěte si podmínky **a ujednání**. Pak vyberte Souhlasím **s výše uvedenými podmínkami a ujednáními a**pak na **koupit**. Obdržíte oznámení, že vaše nasazení probíhá. Vytvoření clusteru trvá přibližně 20 minut.

Pokud narazíte na problém s vytvářením clusterů HDInsight, může to být tím, že nemáte správná oprávnění k tomu. Další informace najdete v tématu popisujícím [požadavky na řízení přístupu](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Po vytvoření clusteru obdržíte oznámení o **úspěšném nasazení** s odkazem **na prostředek přejít na prostředek** . Na stránce skupiny prostředků se zobrazí seznam nového clusteru HDInsight a výchozí úložiště přidružené ke clusteru. Každý cluster má účet [Azure Storage](../hdinsight-hadoop-use-blob-storage.md) nebo závislost [Azure Data Lake Storageho účtu](../hdinsight-hadoop-use-data-lake-store.md) . Označuje se jako výchozí účet úložiště. Cluster HDInsight a jeho výchozí účet úložiště musí být společně umístěné ve stejné oblasti Azure. Odstraněním clusterů nedojde k odstranění účtu úložiště.

## <a name="create-a-jupyter-notebook-file"></a>Vytvoření souboru Jupyter Notebook

[Jupyter notebook](https://jupyter.org/) je interaktivní prostředí poznámkového bloku, které podporuje různé programovací jazyky. Soubor Jupyter Notebook můžete použít k interakci s daty, kombinování kódu s textem Markdownu a provádění jednoduchých vizualizací.

1. Otevřete web [Azure Portal](https://portal.azure.com).

2. Vyberte **Clustery HDInsight** a pak vyberte cluster, který jste vytvořili.

    ![Otevřete cluster HDInsight v Azure Portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. Na portálu v části **řídicí panely clusteru** vyberte **Jupyter notebook**. Po zobrazení výzvy zadejte přihlašovací údaje clusteru.

   ![Otevřete Jupyter Notebook pro spuštění interaktivního dotazu Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Otevřete Jupyter Notebook pro spuštění interaktivního dotazu Spark SQL")

4. Vyberte **Nový**  >  **PySpark** a vytvořte Poznámkový blok.

   ![Vytvoření souboru Jupyter Notebook pro spuštění interaktivního dotazu Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Vytvoření Jupyter Notebook pro spuštění interaktivního dotazu Spark SQL")

   Nový poznámkový blok se vytvoří a otevře s názvem Bez názvu (Bez názvu.pynb).

## <a name="run-apache-spark-sql-statements"></a>Spustit Apache Spark příkazy SQL

Jazyk SQL (Structured Query Language) je nejběžnějším a široce používaným jazykem pro dotazování a transformaci dat. Spark SQL funguje jako rozšíření Apache Spark pro zpracování strukturovaných dat a používá známou syntaxi jazyka SQL.

1. Ověřte, že je jádro připravené. Jádro bude připravené, až se vedle názvu jádra v poznámkovém bloku zobrazí prázdný kroužek. Plný kruh označuje, že je jádro zaneprázdněno.

    ![Stav jádra](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Stav jádra")

    Při prvním spuštění poznámkového bloku jádro provede některé úlohy na pozadí. Počkejte, až bude jádro připravené.

1. Do prázdné buňky vložte následující kód a stisknutím **SHIFT + ENTER** kód spusťte. Příkaz vypíše tabulky Hive v clusteru:

    ```sql
    %%sql
    SHOW TABLES
    ```

    Když použijete Jupyter Notebook soubor s clusterem HDInsight, získáte přednastavenou `spark` relaci, kterou můžete použít ke spouštění dotazů na podregistr pomocí Spark SQL. `%%sql` říká poznámkovému bloku Jupyter, aby ke spuštění dotazu Hive použil přednastavenou relaci `spark`. Dotaz načte prvních 10 řádků z tabulky Hive (**hivesampletable**), která je ve výchozím nastavení k dispozici na všech clusterech HDInsight. Při prvním odeslání dotazu Jupyter vytvoří aplikaci Spark pro Poznámkový blok. Dokončení trvá přibližně 30 sekund. Jakmile je aplikace Spark připravená, dotaz se spustí za sekundu a vytvoří výsledky. Výstup vypadá takto:

    ![Apache Hive dotazování v HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Dotaz na podregistr v HDInsight")

    Při každém spuštění dotazu v Jupyter se v názvu okna webového prohlížeče zobrazí stav **(Busy)** (Zaneprázdněn) společně s názvem poznámkového bloku. Zobrazí se také plný kroužek vedle textu **PySpark** v pravém horním rohu.

1. Spuštěním dalšího dotazu zobrazíte data v tabulce `hivesampletable`.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Obrazovka by se měla aktualizovat a zobrazit výstup dotazu.

    ![Výstup dotazů na podregistr v HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Výstup dotazů na podregistr v HDInsight")

1. V nabídce **Soubor** poznámkového bloku vyberte **Zavřít a zastavit**. Vypnutí poznámkového bloku uvolní prostředky clusteru, včetně aplikace Spark.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu možná budete chtít cluster odstranit. Ve službě HDInsight jsou vaše data uložená v Azure Storage, takže můžete cluster bezpečně odstranit, pokud se nepoužívá. Účtují se vám také poplatky za cluster HDInsight, a to i v případě, že se už nepoužívá. Vzhledem k tomu, že se poplatky za cluster mnohokrát účtují rychleji než poplatky za úložiště, má ekonomický smysl odstraňovat clustery, když se nepoužívají.

Z Azure Portal přejděte do svého clusteru a vyberte **Odstranit**.

![Azure Portal odstranit cluster HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Odstranit cluster HDInsight")

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight i výchozí účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit cluster Apache Spark v HDInsight a spustit základní dotaz Spark SQL. Přejděte k dalšímu kurzu, kde se dozvíte, jak používat cluster HDInsight ke spouštění interaktivních dotazů na ukázkových datech.

> [!div class="nextstepaction"]
> [Spouštění interaktivních dotazů na Apache Spark](./apache-spark-load-data-run-query.md)
