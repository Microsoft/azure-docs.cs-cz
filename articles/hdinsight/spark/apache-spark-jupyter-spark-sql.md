---
title: 'Rychlý Start: Vytvoření clusteru Apache Spark pomocí šablony – Azure HDInsight'
description: V tomto rychlém startu se dozvíte, jak pomocí šablony Resource Manageru vytvořit cluster Apache Spark ve službě Azure HDInsight a jak spustit jednoduchý dotaz Spark SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.custom: mvc
ms.openlocfilehash: 2637603fa303d57340aa36786443508f1930a481
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381323"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Rychlý Start: Vytvoření clusteru Apache Spark ve službě Azure HDInsight pomocí šablony Správce prostředků

V tomto rychlém startu použijete šablonu Azure Resource Manager k vytvoření clusteru Apache Spark ve službě Azure HDInsight. Pak vytvoříte Poznámkový blok Jupyter a použijete ho ke spouštění dotazů Spark SQL pro Apache Hive tabulek. Azure HDInsight je spravovaná opensourcová analytická služba určená pro podniky. Rozhraní Apache Spark Framework for HDInsight umožňuje rychlé analýzy dat a výpočetní výkon clusteru pomocí zpracování v paměti. Poznámkový blok Jupyter vám umožňuje pracovat s daty, kombinovat kód s textem Markdownu a provádět jednoduché vizualizace.

[Přehled: Apache Spark ve službě Azure HDInsight](apache-spark-overview.md) | [Apache Spark](https://spark.apache.org/) | [Apache Hive](https://hive.apache.org/) | [Jupyter notebook](https://jupyter.org/) | [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-an-apache-spark-cluster"></a>Vytvoření clusteru Apache Spark

Vytvořte cluster Apache Spark v HDInsight pomocí šablony Azure Resource Manager. Šablonu najdete na [GitHubu](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Syntaxi a vlastnosti clusteru JSON najdete v tématu [Microsoft. HDInsight/clustery](/azure/templates/microsoft.hdinsight/clusters).

Cluster jako úložiště využívá Azure Storage Blob. Další informace o použití Data Lake Storage Gen2 najdete v tématu [Rychlý start: Nastavení clusterů ve službě HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

> [!IMPORTANT]  
> Clustery HDInsight se fakturují za minutu bez ohledu na to, jestli je používáte, nebo ne. Až přestanete cluster používat, nezapomeňte ho odstranit. Další informace najdete v části [Vyčištění prostředků](#clean-up-resources) tohoto článku.

1. Výběrem následujícího odkazu otevřete šablonu na webu Azure Portal na nové kartě prohlížeče:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank">Nasazení do Azure</a>

2. Zadejte následující hodnoty:

    | Vlastnost | Hodnota |
    |---|---|
    |**Předplatné**|Vyberte předplatné Azure použité k vytvoření tohoto clusteru. V tomto rychlém startu se používá předplatné **&lt;název předplatného Azure>** . |
    | **Skupina prostředků**|Vytvořte skupinu prostředků nebo vyberte existující. Skupina prostředků slouží ke správě prostředků Azure pro vaše projekty. V tomto rychlém startu se používá název nové skupiny prostředků **myspark20180403rg**.|
    | **Umístění**|Vyberte umístění skupiny prostředků. Šablona toto umístění používá k vytvoření clusteru i jako výchozí úložiště clusteru. V tomto rychlém startu se používá umístění **USA – východ 2**.|
    | **Název clusteru**|Zadejte název clusteru, který chcete vytvořit. V tomto rychlém startu se používá název nového clusteru **myspark20180403**.|
    | **Přihlašovací jméno a heslo clusteru**|Výchozí přihlašovací jméno je admin. Vyberte heslo pro přihlášení clusteru. V tomto rychlém startu se používá přihlašovací jméno **admin**.|
    | **Uživatelské jméno a heslo SSH**|Zvolte heslo pro uživatele SSH. V tomto rychlém startu se používá uživatelské jméno SSH **sshuser**.|

    ![Vytvoření clusteru Spark ve službě HDInsight pomocí šablony Azure Resource Manager](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Vytvoření clusteru Spark ve službě HDInsight pomocí šablony Azure Resource Manager")

3. Vyberte **Souhlasím s podmínkami a ujednáními uvedenými nahoře**, vyberte **Připnout na řídicí panel** a pak vyberte **Koupit**. Zobrazí se nová dlaždice s názvem **Nasazení šablony**. Vytvoření clusteru trvá přibližně 20 minut. Cluster se nejprve musí vytvořit, a až pak můžete pokračovat k další relaci.

Pokud narazíte na problém s vytvářením clusterů HDInsight, může to být tím, že nemáte správná oprávnění k tomu. Další informace najdete v tématu popisujícím [požadavky na řízení přístupu](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="install-intellijeclipse-for-spark-applications"></a>Instalace IntelliJ/zatmění pro aplikace Spark

Použijte modul plug-in Azure Toolkit for IntelliJ/zatmění k vývoji aplikací Spark napsaných v [Scala](https://www.scala-lang.org/)a pak je odešlete do clusteru Azure HDInsight přímo z integrovaného vývojového prostředí IntelliJ/zatmění (IDE). Další informace najdete v článcích [o použití IntelliJ k vytvoření/odeslání aplikace Spark](./apache-spark-intellij-tool-plugin.md) a [o použití Eclipse k vytvoření/odeslání aplikace Spark](./apache-spark-eclipse-tool-plugin.md).

## <a name="install-vscode-for-pysparkhive-applications"></a>Instalace VSCode pro aplikace PySpark/podregistr

Naučte se používat sadu nástrojů Azure HDInsight Tools for Visual Studio Code (VSCode) k vytvoření a odeslání dávkových úloh Hive, interaktivních dotazů Hive, dávky PySpark a interaktivních skriptů PySpark. Nástroje Azure HDInsight Tools můžete nainstalovat na platformách, které podporuje nástroj VSCode. Patří sem Windows, Linux a macOS. Další informace najdete v článku [o použití nástroje VSCode k vytvoření/odeslání aplikace PySpark](../hdinsight-for-vscode.md).

## <a name="create-a-jupyter-notebook"></a>Vytvoření poznámkového bloku Jupyter

[Jupyter notebook](https://jupyter.org/) je interaktivní prostředí poznámkového bloku, které podporuje různé programovací jazyky. Poznámkový blok vám umožní pracovat s daty, kombinovat kód s Markdownu textem a provádět jednoduché vizualizace.

1. Otevřete [portál Azure](https://portal.azure.com).

2. Vyberte **Clustery HDInsight** a pak vyberte cluster, který jste vytvořili.

    ![Otevřete cluster HDInsight v Azure Portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. Na portálu v části **řídicí panely clusteru** vyberte **Jupyter notebook**. Po zobrazení výzvy zadejte přihlašovací údaje clusteru.

   ![Otevřete Jupyter Notebook pro spuštění interaktivního dotazu Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Otevřete Jupyter Notebook pro spuštění interaktivního dotazu Spark SQL")

4. Vyberte **Nový** > **PySpark** a vytvořte poznámkový blok.

   ![Vytvoření Jupyter Notebook pro spuštění interaktivního dotazu Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Vytvoření Jupyter Notebook pro spuštění interaktivního dotazu Spark SQL")

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

    Když použijete Jupyter Notebook s clusterem HDInsight, získáte přednastavenou `spark` relaci, kterou můžete použít ke spouštění dotazů na podregistr pomocí Spark SQL. `%%sql` říká poznámkovému bloku Jupyter, aby ke spuštění dotazu Hive použil přednastavenou relaci `spark`. Dotaz načte prvních 10 řádků z tabulky Hive (**hivesampletable**), která je ve výchozím nastavení k dispozici na všech clusterech HDInsight. Při prvním odeslání dotazu Jupyter vytvoří aplikaci Spark pro Poznámkový blok. Dokončení trvá přibližně 30 sekund. Jakmile je aplikace Spark připravená, dotaz se spustí za sekundu a vytvoří výsledky. Výstup bude vypadat následovně:

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

HDInsight ukládá vaše data a poznámkové bloky Jupyter ve službě Azure Storage nebo Azure Data Lake Store, takže můžete cluster bezpečně odstranit, když se nepoužívá. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány. Pokud se chystáte hned začít pracovat na kurzu uvedeném v části [Další kroky](#next-steps), měli byste cluster zachovat.

Přepněte zpět na web Azure Portal a vyberte **Odstranit**.

![Azure Portal odstranit cluster HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Odstranit cluster HDInsight")

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight i výchozí účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit cluster Apache Spark v HDInsight a spustit základní dotaz Spark SQL. Přejděte k dalšímu kurzu, kde se dozvíte, jak používat cluster HDInsight ke spouštění interaktivních dotazů na ukázkových datech.

> [!div class="nextstepaction"]
>[Spouštění interaktivních dotazů na Apache Spark](./apache-spark-load-data-run-query.md)
