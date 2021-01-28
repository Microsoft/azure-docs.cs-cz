---
title: 'Kurz: Analýza dat Apache Spark Azure HDInsight pomocí Power BI'
description: Kurz – použití Microsoft Power BI k vizualizaci Apache Spark uložených clusterů HDInsight
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc,seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 285c6c05a1a216303ee9d8019093c963cad60aa0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946484"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Kurz: analýza Apache Spark dat pomocí Power BI ve službě HDInsight

V tomto kurzu se naučíte používat Microsoft Power BI k vizualizaci dat v clusteru Apache Spark ve službě Azure HDInsight.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vizualizace dat Sparku pomocí Power BI

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

* Dokončete článek [Kurz: Načítání dat a spouštění dotazů v clusteru Apache Spark ve službě Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Volitelné: [předplatné Power BI zkušební verze](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Ověření dat

[Jupyter notebook](https://jupyter.org/) , který jste vytvořili v [předchozím kurzu](apache-spark-load-data-run-query.md) , obsahuje kód pro vytvoření `hvac` tabulky. Tato tabulka je založená na souboru CSV, který je k dispozici ve všech clusterech HDInsight Spark na adrese `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv` . Pomocí následujícího postupu ověřte data.

1. Z Jupyter Notebook vložte následující kód a stiskněte klávesu **SHIFT + ENTER**. Kód ověří existenci tabulek.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Výstup vypadá takto:

    ![Zobrazení tabulek ve Sparku](./media/apache-spark-use-bi-tools/apache-spark-show-tables.png)

    Pokud jste poznámkový blok před zahájením tohoto kurzu zavřeli, tabulka `hvactemptable` je vyčištěná, takže se ve výstupu nezobrazí.  Z nástrojů BI je možný přístup pouze k tabulkám Hive uloženým v metastoru (ty mají ve sloupci **isTemporary** označení **False**). V tomto kurzu se připojíte k tabulce **hvac**, kterou jste vytvořili.

2. Do prázdné buňky vložte následující kód a pak stiskněte **SHIFT + ENTER**. Kód ověří data v tabulce.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    Výstup vypadá takto:

    ![Zobrazení řádků tabulky hvac ve Sparku](./media/apache-spark-use-bi-tools/apache-spark-select-limit.png)

3. V nabídce **Soubor** poznámkového bloku vyberte **Zavřít a zastavit**. Vypněte poznámkový blok a uvolněte tak prostředky.

## <a name="visualize-the-data"></a>Vizualizace dat

V této části pomocí Power BI vytvoříte vizualizace, sestavy a řídicí panely z dat v clusteru Spark.

### <a name="create-a-report-in-power-bi-desktop"></a>Vytvoření sestavy v Power BI Desktopu

Prvními kroky při práci se Sparkem je připojení ke clusteru v Power BI Desktopu, načtení dat z clusteru a vytvoření základní vizualizace na základě těchto dat.

1. Otevřete Power BI Desktop. Zavře úvodní obrazovku, pokud se spustí.

2. Na kartě **Domů** přejděte na **získat data**  >  **..**.

    ![Načtení dat do Power BI Desktop ze služby HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Načtení dat do Power BI z Apache Spark BI")

3. `Spark`Do vyhledávacího pole zadejte, vyberte **Azure HDInsight Spark** a pak vyberte **připojit**.

    ![Načtení dat do Power BI z Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Načtení dat do Power BI z Apache Spark BI")

4. `mysparkcluster.azurehdinsight.net`Do textového pole **Server** zadejte adresu URL clusteru (ve formuláři).

5. V části **režim připojení dat** vyberte **DirectQuery**. Pak vyberte **OK**.

    V případě Sparku můžete použít jakýkoli režim připojení dat. Pokud použijete DirectQuery, změny se v sestavách projeví bez nutnosti aktualizace celé datové sady. Pokud data importujete, musíte datovou sadu aktualizovat, aby se změny projevily. Další informace o tom, jak a kdy použít DirectQuery, najdete v tématu [Použití DirectQuery v Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Zadejte informace o přihlašovacím účtu HDInsight a pak vyberte **připojit**. Výchozí název účtu je *admin*.

7. Vyberte `hvac` tabulku, počkejte, než se zobrazí náhled dat, a pak vyberte **načíst**.

    ![Uživatelské jméno a heslo clusteru Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Uživatelské jméno a heslo clusteru Spark")

    Power BI Desktop má všechny potřebné informace pro připojení ke clusteru Spark a načtení dat z tabulky `hvac`. Tabulka a její sloupce se zobrazí v podokně **Pole**.

8. Vizualizujte rozdíl mezi cílovou teplotou a skutečnou teplotou jednotlivých budov:

    1. V podokně **VIZUALIZACE** vyberte **Plošný graf**.

    2. Přetáhněte pole **BuildingID** (ID budovy) do části **Osa** a pole **ActualTemp** (Skutečná teplota) a **TargetTemp** (Cílová teplota) do části **Hodnota**.

        ![Přidat sloupce hodnot](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Přidat sloupce hodnot")

        Diagram vypadá takto:

        ![součet plošného grafu](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "součet plošného grafu")

        Vizualizace ve výchozím nastavení zobrazí pro **ActualTemp** a **TargetTemp** součet hodnot. V podokně vizualizace vyberte šipku dolů vedle **ActualTemp** a **TragetTemp** , kde vidíte, že je vybraná možnost **Sum** .

    3. V podokně vizualizace vyberte šipky dolů vedle **ActualTemp** a **TragetTemp** . Pokud chcete získat průměr skutečných a cílových teplot pro každé sestavení, vyberte **průměr** .

        ![průměr hodnot](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "průměr hodnot")

        Vaše vizualizace dat by měla vypadat podobně jako na následujícím snímku obrazovky. Přesunutím kurzoru nad vizualizaci zobrazte popisky s relevantními daty.

        ![plošný graf](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "plošný graf")

9. Přejděte do **souboru**  >  **Save (Uložit**), zadejte název `BuildingTemperature` souboru a pak vyberte **Uložit**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publikování sestavy ve službě Power BI (volitelné)

Služba Power BI umožňuje sdílet sestavy a řídicí panely napříč organizací. V této části nejprve publikujete datovou sadu a sestavu. Pak sestavu připnete na řídicí panel. Řídicí panely se obvykle používají k zaměření na podmnožinu dat v sestavě. V sestavě máte jenom jednu vizualizaci, ale je to ještě užitečné při Projděte si postup.

1. Otevřete Power BI Desktop.

1. Na kartě **Domů** vyberte **publikovat**.

    ![Publikování z Power BI Desktopu](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publikování z Power BI Desktopu")

1. Vyberte pracovní prostor, do kterého chcete datovou sadu a sestavu publikovat, a pak vyberte **Vybrat**. Na následujícím obrázku je vybraný výchozí pracovní prostor **My Workspace**.

    ![Vyberte pracovní prostor, pro který chcete publikovat datovou sadu a sestavu.](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Vyberte pracovní prostor, pro který chcete publikovat datovou sadu a sestavu.")

1. Po úspěšném publikování vyberte **otevřít BuildingTemperature. pbix v Power BI**.

    ![Úspěch publikování, kliknutím zadejte přihlašovací údaje.](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Úspěch publikování, kliknutím zadejte přihlašovací údaje.")

1. V služba Power BI vyberte možnost **zadat přihlašovací údaje**.

    ![Zadejte přihlašovací údaje v služba Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Zadejte přihlašovací údaje v služba Power BI")

1. Vyberte **Upravit přihlašovací údaje**.

    ![Upravit přihlašovací údaje v služba Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Upravit přihlašovací údaje v služba Power BI")

1. Zadejte informace o přihlašovacím účtu HDInsight a pak vyberte **Přihlásit** se. Výchozí název účtu je *admin*.

    ![Přihlášení ke clusteru Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Přihlášení ke clusteru Spark")

1. V levém podokně přejdete do části **pracovní** prostory sestavy pracovní  >  **prostor**  >  a pak vyberete **BuildingTemperature**.

    ![Sestava uvedená v části sestavy v levém podokně](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Sestava uvedená v části sestavy v levém podokně")

    V části **DATOVÉ SADY** v levém podokně by se také měla zobrazit datová sada **BuildingTemperature**.

    Vizuál, který jste vytvořili v Power BI Desktopu, je teď dostupný ve službě Power BI.

1. Najeďte myší na vizualizaci a vyberte ikonu připnutí v pravém horním rohu.

    ![Sestava v služba Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Sestava v služba Power BI")

1. Vyberte nový řídicí panel, zadejte název `Building temperature` a potom vyberte **připnout**.

    ![Připnout na nový řídicí panel](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Připnout na nový řídicí panel")

1. V sestavě vyberte **Přejít na řídicí panel**.

Váš vizuál je připnutý na řídicím panelu. Do sestavy můžete přidat další vizuály a připnout je na stejný řídicí panel. Další informace o sestavách a řídicích panelech najdete v tématu [sestavy v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) a [řídicích panelech v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení kurzu můžete cluster odstranit. Ve službě HDInsight jsou vaše data uložená v Azure Storage, takže můžete cluster bezpečně odstranit, pokud se nepoužívá. Účtují se vám také poplatky za cluster HDInsight, a to i v případě, že se už nepoužívá. Vzhledem k tomu, že se poplatky za cluster mnohokrát účtují rychleji než poplatky za úložiště, má ekonomický smysl odstraňovat clustery, když se nepoužívají.

Pokud chcete odstranit cluster, přečtěte si téma [odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového řádku Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat Microsoft Power BI k vizualizaci dat v clusteru Apache Spark ve službě Azure HDInsight. V dalším článku se dozvíte, jak můžete vytvořit aplikaci Machine Learning.

> [!div class="nextstepaction"]
> [Vytvoření aplikace Machine Learning](./apache-spark-ipython-notebook-machine-learning.md)