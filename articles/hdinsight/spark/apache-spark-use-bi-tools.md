---
title: Spark BI pomocí nástrojů vizualizace data v Azure HDInsight | Microsoft Docs
description: Pomocí nástrojů vizualizace dat pro analýzu pomocí Apache Spark BI v clusterech prostředí HDInsight
keywords: Apache spark bi, spark bi vizualizace dat spark, spark business intelligence
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: jgao
ms.openlocfilehash: 0e728e17a64acd990b301bac8139c7bb395a3098
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI nástroje vizualizaci dat pomocí Azure HDInsight

Další informace o použití [Microsoft Power BI](http://powerbi.microsoft.com) k vizualizaci dat v clusteru Apache Spark v Azure HDInsight.

## <a name="prerequisites"></a>Požadavky

* **Dokončení článek [Spusťte interaktivní dotazy na clustery Spark v HDInsight](./apache-spark-load-data-run-query.md)**.
* **Power BI**: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) a [zkušební předplatné Power BI](https://app.powerbi.com/signupredirect?pbi_source=web) (volitelné).


## <a name="hivetable"></a>Ověřit data

Poznámkového bloku Jupyter, který jste vytvořili v [předchozí kurzu](apache-spark-load-data-run-query.md) obsahuje kód k vytvoření `hvac` tabulky. Tato tabulka podle souboru CSV, k dispozici na všech clusterech HDInsight Spark v **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Pomocí následujícího postupu ověřit data.

1. Z poznámkového bloku Jupyter, vložte následující kód a stiskněte klávesu **SHIFT + ENTER**. Kód ověřuje, že bude existence tabulky.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Výstup bude vypadat následovně:

    ![Zobrazení tabulky v Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Pokud jste zavřeli poznámkového bloku před zahájením tohoto kurzu `hvactemptable` je vyčištěna, takže není zahrnut ve výstupu.
    Pouze tabulky, které jsou uložené v metaúložiště Hive (indikován **False** pod **isTemporary** sloupec) lze přistupovat z nástrojů BI. V tomto kurzu jste se připojit k **TVK** tabulky, který jste vytvořili.

2. Do prázdné buňky vložte následující kód a stiskněte klávesu **SHIFT + ENTER**. Kód ověřuje dat v tabulce.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    Výstup bude vypadat následovně:

    ![Zobrazit řádky z tabulky TVK v Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. V nabídce **Soubor** poznámkového bloku klikněte na **Zavřít a zastavit**. Vypněte poznámkového bloku k uvolnění prostředků. 















## <a name="powerbi"></a>Pomocí Power BI

V této části použijete k vytváření vizualizace, sestavy a řídicí panely z dat clusteru Spark Power BI. 

### <a name="create-a-report-in-power-bi-desktop"></a>Vytvoření sestavy v Power BI Desktop
První kroky při práci se službou Spark se připojte ke clusteru v Power BI Desktop, načtení dat z clusteru a vytvořit základní vizualizace založené na těchto datech.

> [!NOTE]
> Konektor ukázáno v tomto článku je aktuálně ve verzi preview. Poskytování zpětné vazby máte prostřednictvím [Power BI komunity](https://community.powerbi.com/) lokality nebo [Power BI nápady](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Otevřete [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).
1. Z **Domů** , klikněte na **načíst Data**, pak **Další**.

    ![Načíst data do Power BI Desktop z HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "načíst data do Power BI z Apache Spark BI")


2. Zadejte `Spark` do vyhledávacího pole vyberte **Azure HDInsight Spark (Beta)** a potom klikněte na **Connect**.

    ![Načíst data do Power BI z Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "načíst data do Power BI z Apache Spark BI")

3. Zadejte adresu URL clusteru (ve formě `mysparkcluster.azurehdinsight.net`), vyberte **DirectQuery**a potom klikněte na **OK**.

    Můžete buď režim připojení dat pomocí Spark. Pokud používáte DirectQuery, změny se projeví v sestavách bez obnovení celou datovou sadu. Pokud importujete data, je nutné aktualizovat sadu dat a podívejte se změny. Další informace, jak a kdy použít DirectQuery najdete v tématu [pomocí DirectQuery v Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Zadejte přihlašovací údaje k účtu HDInsight a pak klikněte na **Connect**. Výchozí název účtu je *správce*.

5. Vyberte `hvac` tabulky, počkejte zobrazte náhled dat a pak klikněte na tlačítko **zatížení**.

    ![Spark clusteru uživatelské jméno a heslo](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark clusteru uživatelské jméno a heslo")

    Power BI Desktop obsahuje informace, musí se připojit k Spark clusteru a načtení dat z `hvac` tabulky. Tabulka a její sloupce jsou zobrazeny v **pole** podokně.  Podívejte se na následující snímek obrazovky:

6. Vizualizace odchylka mezi teploty cíl a skutečný teploty pro každé sestavení: 

    1. V **VIZUALIZACE** podokně, vyberte **plošný graf**. 
    2. Přetáhněte **BuildingID** do **osy**a přetáhněte ji **ActualTemp** a **TargetTemp** polí k **hodnotu**.

        ![Vytvořit vizualizaci dat pomocí Apache Spark BI Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Spark vytvořit vizualizaci dat pomocí Apache Spark BI")

        Diagram vypadá takto:

        ![Vytvořit vizualizaci dat pomocí Apache Spark BI Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Spark vytvořit vizualizaci dat pomocí Apache Spark BI")

        Ve výchozím nastavení zobrazuje vizualizaci součet **ActualTemp** a **TargetTemp**. Klikněte na šipku dolů vedle **ActualTemp** a **TragetTemp** v podokně vizualizace se zobrazí **součet** je vybrána.

    3. Klikněte na šipku dolů vedle **ActualTemp** a **TragetTemp** v podokně vizualizace vyberte **průměrná** získat v průměru skutečné a teploty cíl pro každou vytváření.

        ![Vytvořit vizualizaci dat pomocí Apache Spark BI Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Spark vytvořit vizualizaci dat pomocí Apache Spark BI")

        Vaše vizualizace dat musí být podobné na snímku obrazovky. Přesuňte kurzor vizualizaci zobrazíte popisy s příslušnými daty.

        ![Vytvořit vizualizaci dat pomocí Apache Spark BI Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Spark vytvořit vizualizaci dat pomocí Apache Spark BI")

7. Klikněte na tlačítko **soubor** pak **Uložit**a zadejte název `BuildingTemperature.pbix` souboru. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Sestava bude publikována ve službě Power BI (volitelné)

Služba Power BI umožňuje sdílet sestavy a řídicí panely v rámci vaší organizace. V této části nejprve publikovat datové sady a sestavy. Potom připnete sestavy na řídicí panel. Řídicí panely jsou obvykle používány umožňuje zaměřit se na podmnožinu dat v sestavě; v sestavě obsahuje pouze jeden vizualizace, ale je stále užitečné projít kroky.

1. Otevřít Power BI Desktop.
2. Z **Domů** , klikněte na **publikovat**.

    ![Publikování z Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "publikování z Power BI Desktop")

2. Vyberte pracovní prostor k publikování datovou sadu a nahlásit, pak klikněte na tlačítko **vyberte**. Na následujícím obrázku, výchozí **pracovní prostor** je vybrána.

    ![Vyberte pracovní prostor k publikování datové sady a nahlásit](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "vyberte pracovní prostor k publikování datové sady a nahlásit") 

3. Po publikování je úspěšné, klikněte na tlačítko **otevřete 'BuildingTemperature.pbix' v Power BI**.

    ![Publikovat úspěšné, klikněte na tlačítko zadat přihlašovací údaje](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "publikovat úspěšné, klikněte na tlačítko zadat přihlašovací údaje") 

4. V rámci služby Power BI, klikněte na **zadejte přihlašovací údaje**.

    ![Zadejte přihlašovací údaje ve službě Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "zadejte přihlašovací údaje ve službě Power BI")

5. Klikněte na tlačítko **upravit přihlašovací údaje**.

    ![Upravit přihlašovací údaje do služby Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "upravit přihlašovací údaje do služby Power BI")

6. Zadejte přihlašovací údaje k účtu HDInsight a pak klikněte na tlačítko **přihlášení**. Výchozí název účtu je *správce*.

    ![Přihlaste se k clusteru Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Přihlaste se k clusteru Spark")

7. V levém podokně přejděte do **pracovních prostorů** > **pracovní prostor** > **sestavy**, pak klikněte na tlačítko **BuildingTemperature**.

    ![Sestava uvedené v části sestavy v levém podokně](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "sestavy uvedené v části sestavy v levém podokně")

    Měli byste taky vidět **BuildingTemperature** uvedené v části **datové sady** v levém podokně.

    Vizuál, který jste vytvořili v Power BI Desktop je nyní k dispozici v rámci služby Power BI. 

8. Ukazatele myši vizualizaci a klikněte na ikonu připnutí v pravém horním rohu.

    ![Sestavy v rámci služby Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "sestavy ve službě Power BI")

9. Vyberte "Novým řídicím panelem", zadejte název `Building temperature`, pak klikněte na tlačítko **Pin**.

    ![Připnout na řídicí panel nové](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "připnout na řídicí panel nové")

10. V sestavě, klikněte na tlačítko **přejděte na řídicí panel**. 

Vizuál je připnutá na řídicí panel - můžete přidat jiných vizuálních prvcích do sestavy a připnete ji na stejné řídicí panel. Další informace o sestavy a řídicí panely, najdete v části [sestavy v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/)a [řídicí panely v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

<!--
## <a name="tableau"></a>Use Tableau Desktop 

> [!NOTE]
> This section is applicable only for Spark 1.5.2 clusters created in Azure HDInsight.
>
>

1. Install [Tableau Desktop](http://www.tableau.com/products/desktop) on the computer where you are running this Apache Spark BI tutorial.

2. Make sure that computer also has Microsoft Spark ODBC driver installed. You can install the driver from [here](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Launch Tableau Desktop. In the left pane, from the list of server to connect to, click **Spark SQL**. If Spark SQL is not listed by default in the left pane, you can find it by click **More Servers**.
2. In the Spark SQL connection dialog box, provide the values as shown in the screenshot, and then click **OK**.

    ![Connect to a cluster for Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Connect to a cluster for Apache Spark BI")

    The authentication drop-down lists **Microsoft Azure HDInsight Service** as an option, only if you installed the [Microsoft Spark ODBC Driver](http://go.microsoft.com/fwlink/?LinkId=616229) on the computer.
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

Pokud jste zjistili, jak vytvořit cluster, vytvořit Spark datové rámce k dotazování na data a pak z nástrojů BI přístup k datům. Teď můžete prohlížet pokyny, jak spravovat prostředky clusteru a ladění úloh, které jsou spuštěné v clusteru služby HDInsight Spark.

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)

