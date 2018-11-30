---
title: 'Kurz: Analýza dat Apache Sparku pomocí Power BI v Azure HDInsight '
description: Pomocí Microsoft Power BI k vizualizaci dat Apache Spark uložené clustery HDInsight
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/07/2018
ms.openlocfilehash: e862000df1edc5101c0768f1f96c11953f1485c7
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52583274"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Kurz: Analýza dat Apache Spark v HDInsight pomocí Power BI 

Další informace o použití [Microsoft Power BI](https://powerbi.microsoft.com/) k vizualizaci dat [Apache Spark](https://spark.apache.org/) cluster v [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vizualizace dat Sparku pomocí Power BI

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* **Dokončete článek [Kurz: Načítání dat a spouštění dotazů v clusteru Apache Spark ve službě Azure HDInsight](./apache-spark-load-data-run-query.md)**.
* **Power BI:** [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) a [zkušební předplatné Power BI](https://app.powerbi.com/signupredirect?pbi_source=web) (volitelné).


## <a name="verify-the-data"></a>Ověření dat

[Poznámkový blok Jupyter](https://jupyter.org/) , kterou jste vytvořili v [předchozí kurz o službě](apache-spark-load-data-run-query.md) obsahuje kód pro vytvoření `hvac` tabulky. Tato tabulka je založená na souboru CSV, který je k dispozici ve všech clusterech HDInsight Spark v umístění **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Pomocí následujícího postupu ověřte data.

1. Do poznámkového bloku Jupyter vložte následující kód a pak stiskněte **SHIFT + ENTER**. Kód ověří existenci tabulek.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Výstup bude vypadat následovně:

    ![Zobrazení tabulek ve Sparku](./media/apache-spark-use-bi-tools/show-tables.png)

    Pokud jste poznámkový blok před zahájením tohoto kurzu zavřeli, tabulka `hvactemptable` je vyčištěná, takže se ve výstupu nezobrazí.  Z nástrojů BI je možný přístup pouze k tabulkám Hive uloženým v metastoru (ty mají ve sloupci **isTemporary** označení **False**). V tomto kurzu se připojíte k tabulce **hvac**, kterou jste vytvořili.

2. Do prázdné buňky vložte následující kód a pak stiskněte **SHIFT + ENTER**. Kód ověří data v tabulce.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    Výstup bude vypadat následovně:

    ![Zobrazení řádků tabulky hvac ve Sparku](./media/apache-spark-use-bi-tools/select-limit.png)

3. V nabídce **Soubor** poznámkového bloku klikněte na **Zavřít a zastavit**. Vypněte poznámkový blok a uvolněte tak prostředky. 

## <a name="visualize-the-data"></a>Vizualizace dat

V této části pomocí Power BI vytvoříte vizualizace, sestavy a řídicí panely z dat v clusteru Spark. 

### <a name="create-a-report-in-power-bi-desktop"></a>Vytvoření sestavy v Power BI Desktopu
Prvními kroky při práci se Sparkem je připojení ke clusteru v Power BI Desktopu, načtení dat z clusteru a vytvoření základní vizualizace na základě těchto dat.

> [!NOTE]
> Konektor ukázaný v tomto článku je aktuálně ve verzi Preview. Případnou zpětnou vazbu můžete poskytnout přes web [komunity Power BI](https://community.powerbi.com/) nebo na fóru [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas) (Nápady ohledně Power BI).

1. Otevřete [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).
1. Na kartě **Domů** klikněte na **Načíst data** a pak na **Další**.

    ![Načtení dat do Power BI Desktopu z Apache Sparku ve službě HDInsight](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Načtení dat do Power BI z Apache Spark BI")


2. Zadejte `Spark` do vyhledávacího pole, vyberte **Azure HDInsight Spark**a potom klikněte na tlačítko **připojit**.

    ![Načtení dat do Power BI z Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Načtení dat do Power BI z Apache Spark BI")

3. Zadejte adresu URL vašeho clusteru (ve formátu `mysparkcluster.azurehdinsight.net`), vyberte **DirectQuery** a pak klikněte na **OK**.

    V případě Sparku můžete použít jakýkoli režim připojení dat. Pokud použijete DirectQuery, změny se v sestavách projeví bez nutnosti aktualizace celé datové sady. Pokud data importujete, musíte datovou sadu aktualizovat, aby se změny projevily. Další informace o tom, jak a kdy použít DirectQuery, najdete v tématu [Použití DirectQuery v Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Zadejte přihlašovací údaje účtu služby HDInsight a pak klikněte na **Připojit**. Výchozí název účtu je *admin*.

5. Vyberte tabulku `hvac`, počkejte na zobrazení náhledu dat a pak klikněte na **Načíst**.

    ![Uživatelské jméno a heslo clusteru Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Uživatelské jméno a heslo clusteru Spark")

    Power BI Desktop má všechny potřebné informace pro připojení ke clusteru Spark a načtení dat z tabulky `hvac`. Tabulka a její sloupce se zobrazí v podokně **Pole**.  Viz následující snímek obrazovky:

6. Vizualizujte rozdíl mezi cílovou teplotou a skutečnou teplotou jednotlivých budov: 

    1. V podokně **VIZUALIZACE** vyberte **Plošný graf**. 
    2. Přetáhněte pole **BuildingID** (ID budovy) do části **Osa** a pole **ActualTemp** (Skutečná teplota) a **TargetTemp** (Cílová teplota) do části **Hodnota**.

        ![Vytvoření vizualizací dat Sparku pomocí Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Vytvoření vizualizací dat Sparku pomocí Apache Spark BI")

        Diagram vypadá takto:

        ![Vytvoření vizualizací dat Sparku pomocí Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Vytvoření vizualizací dat Sparku pomocí Apache Spark BI")

        Vizualizace ve výchozím nastavení zobrazí pro **ActualTemp** a **TargetTemp** součet hodnot. Když kliknete na šipku dolů vedle položek **ActualTemp** and **TragetTemp** v podokně Vizualizace, zobrazí se vybraná možnost **Součet**.

    3. Klikněte na šipky dolů vedle položek **ActualTemp** a **TragetTemp** v podokně Vizualizace a vyberte **Průměr**, abyste pro každou budovu získali průměrnou skutečnou a cílovou teplotu.

        ![Vytvoření vizualizací dat Sparku pomocí Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Vytvoření vizualizací dat Sparku pomocí Apache Spark BI")

        Vaše vizualizace dat by měla vypadat podobně jako na následujícím snímku obrazovky. Přesunutím kurzoru nad vizualizaci zobrazte popisky s relevantními daty.

        ![Vytvoření vizualizací dat Sparku pomocí Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Vytvoření vizualizací dat Sparku pomocí Apache Spark BI")

7. Klikněte na **Soubor**, pak na **Uložit** a zadejte název souboru `BuildingTemperature.pbix`. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publikování sestavy ve službě Power BI (volitelné)

Služba Power BI umožňuje sdílet sestavy a řídicí panely napříč organizací. V této části nejprve publikujete datovou sadu a sestavu. Pak sestavu připnete na řídicí panel. Řídicí panely se obvykle používají k zaměření na podmnožinu dat v sestavě. Vaše sestava obsahuje pouze jednu vizualizaci, ale přesto je užitečné si tyto kroky projít.

1. Otevřete Power BI Desktop.
2. Na kartě **Domů** klikněte na **Publikovat**.

    ![Publikování z Power BI Desktopu](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publikování z Power BI Desktopu")

2. Vyberte pracovní prostor, do kterého chcete datovou sadu a sestavu publikovat, a klikněte na **Vybrat**. Na následujícím obrázku je vybraný výchozí pracovní prostor **My Workspace**.

    ![Výběr pracovního prostoru, do kterého se mají datová sada a sestava publikovat](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Výběr pracovního prostoru, do kterého se mají datová sada a sestava publikovat") 

3. Po úspěšném publikování klikněte na **Otevřít soubor BuildingTemperature.pbix v Power BI**.

    ![Úspěšné publikování a kliknutí pro zadání přihlašovacích údajů](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Úspěšné publikování a kliknutí pro zadání přihlašovacích údajů") 

4. Ve službě Power BI klikněte na **Zadat přihlašovací údaje**.

    ![Zadání přihlašovacích údajů ve službě Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Zadání přihlašovacích údajů ve službě Power BI")

5. Klikněte na **Upravit přihlašovací údaje**.

    ![Úprava přihlašovacích údajů ve službě Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Úprava přihlašovacích údajů ve službě Power BI")

6. Zadejte přihlašovací údaje účtu služby HDInsight a pak klikněte na **Přihlásit se**. Výchozí název účtu je *admin*.

    ![Přihlášení ke clusteru Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Přihlášení ke clusteru Spark")

7. V levém podokně přejděte do části **Pracovní prostory** > **My Workspace** > **SESTAVY** a klikněte na **BuildingTemperature**.

    ![Sestava uvedená v části Sestavy v levém podokně](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Sestava uvedená v části Sestavy v levém podokně")

    V části **DATOVÉ SADY** v levém podokně by se také měla zobrazit datová sada **BuildingTemperature**.

    Vizuál, který jste vytvořili v Power BI Desktopu, je teď dostupný ve službě Power BI. 

8. Najeďte kurzorem na vizualizaci a klikněte na ikonu připínáčku v pravém horním rohu.

    ![Sestava ve službě Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Sestava ve službě Power BI")

9. Vyberte Nový řídicí panel, zadejte název `Building temperature` a klikněte na **Připnout**.

    ![Připnutí na nový řídicí panel](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Připnutí na nový řídicí panel")

10. V sestavě klikněte na **Přejít na řídicí panel**. 

Váš vizuál je připnutý na řídicím panelu. Do sestavy můžete přidat další vizuály a připnout je na stejný řídicí panel. Další informace o sestavách a řídicích panelů najdete v tématu [sestavy v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) a [řídicí panely v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

<!--
## <a name="tableau"></a>Use Tableau Desktop 

> [!NOTE]
> This section is applicable only for Spark 1.5.2 clusters created in Azure HDInsight.
>
>

1. Install [Tableau Desktop](http://www.tableau.com/products/desktop) on the computer where you are running this Apache Spark BI tutorial.

2. Make sure that computer also has Microsoft Spark ODBC driver installed. You can install the driver from [here](https://go.microsoft.com/fwlink/?LinkId=616229).

1. Launch Tableau Desktop. In the left pane, from the list of server to connect to, click **Spark SQL**. If Spark SQL is not listed by default in the left pane, you can find it by click **More Servers**.
2. In the Spark SQL connection dialog box, provide the values as shown in the screenshot, and then click **OK**.

    ![Connect to a cluster for Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Connect to a cluster for Apache Spark BI")

    The authentication drop-down lists **Microsoft Azure HDInsight Service** as an option, only if you installed the [Microsoft Spark ODBC Driver](https://go.microsoft.com/fwlink/?LinkId=616229) on the computer.
3. On the next screen, from the **Schema** drop-down, click the **Find** icon, and then click **default**.

    ![Find schema for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Find schema for Apache Spark BI")
4. For the **Table** field, click the **Find** icon again to list all the Hive tables available in the cluster. You should see the **hvac** table you created earlier using the notebook.

    ![Find table for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Find table for Apache Spark BI")
5. Drag and drop the table to the top box on the right. Tableau imports the data and displays the schema as highlighted by the red box.

    ![Add tables to Tableau for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Add tables to Tableau for Apache Spark BI")
6. Click the **Sheet1** tab at the bottom left. Make a visualization that shows the average target and actual temperatures for all buildings for each date. Drag **Date** and **Building ID** to **Columns** and **Actual Temp**/**Target Temp** to **Rows**. Under **Marks**, select **Area** to use an area map for Spark data visualization.

     ![Add fields for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Add fields for Spark data visualization")
7. By default, the temperature fields are shown as aggregate. If you want to show the average temperatures instead, you can do so from the drop-down, as shown in the following screenshot:

    ![Take average of temperature for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Take average of temperature for Spark data visualization")

8. You can also super-impose one temperature map over the other to get a better feel of difference between target and actual temperatures. Move the mouse to the corner of the lower area map until you see the handle shape highlighted in a red circle. Drag the map to the other map on the top and release the mouse when you see the shape highlighted in red rectangle.

    ![Merge maps for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Merge maps for Spark data visualization")

     Your data visualization should change as shown in the screenshot:

    ![Tableau output for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Tableau output for Spark data visualization")
9. Click **Save** to save the worksheet. You can create dashboards and add one or more sheets to it.
-->

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

- Vizualizace dat Apache Sparku s využitím Power BI.

V dalším článku se dozvíte, jak můžete data zaregistrovaná ve Sparku přetáhnout do nástroje pro analýzu BI, jako je Power BI. 
> [!div class="nextstepaction"]
> [Spuštění úlohy streamování Apache Spark](apache-spark-eventhub-streaming.md)

