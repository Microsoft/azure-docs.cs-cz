---
title: 'Tutorial: Analyze Azure HDInsight Apache Spark data with Power BI'
description: Tutorial - Use Microsoft Power BI to visualize Apache Spark data stored HDInsight clusters
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 10/03/2019
ms.openlocfilehash: 3fd1405d8421d71f52d9cd215dd055ce1595abd0
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327279"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Tutorial: Analyze Apache Spark data using Power BI in HDInsight

In this tutorial, you learn how to use [Microsoft Power BI](https://powerbi.microsoft.com/) to visualize data in an Apache Spark cluster in [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vizualizace dat Sparku pomocí Power BI

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

* Complete the article [Tutorial: Load data and run queries on an Apache Spark cluster in Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Optional: [Power BI trial subscription](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Ověření dat

The [Jupyter Notebook](https://jupyter.org/) that you created in the [previous tutorial](apache-spark-load-data-run-query.md) includes code to create an `hvac` table. This table is based on the CSV file available on all HDInsight Spark clusters at `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`. Pomocí následujícího postupu ověřte data.

1. Do poznámkového bloku Jupyter vložte následující kód a pak stiskněte **SHIFT + ENTER**. Kód ověří existenci tabulek.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Výstup bude vypadat následovně:

    ![Zobrazení tabulek ve Sparku](./media/apache-spark-use-bi-tools/apache-spark-show-tables.png)

    Pokud jste poznámkový blok před zahájením tohoto kurzu zavřeli, tabulka `hvactemptable` je vyčištěná, takže se ve výstupu nezobrazí.  Z nástrojů BI je možný přístup pouze k tabulkám Hive uloženým v metastoru (ty mají ve sloupci **isTemporary** označení **False**). V tomto kurzu se připojíte k tabulce **hvac**, kterou jste vytvořili.

2. Do prázdné buňky vložte následující kód a pak stiskněte **SHIFT + ENTER**. Kód ověří data v tabulce.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    Výstup bude vypadat následovně:

    ![Zobrazení řádků tabulky hvac ve Sparku](./media/apache-spark-use-bi-tools/apache-spark-select-limit.png)

3. V nabídce **Soubor** poznámkového bloku vyberte **Zavřít a zastavit**. Vypněte poznámkový blok a uvolněte tak prostředky.

## <a name="visualize-the-data"></a>Vizualizace dat

V této části pomocí Power BI vytvoříte vizualizace, sestavy a řídicí panely z dat v clusteru Spark.

### <a name="create-a-report-in-power-bi-desktop"></a>Vytvoření sestavy v Power BI Desktopu

Prvními kroky při práci se Sparkem je připojení ke clusteru v Power BI Desktopu, načtení dat z clusteru a vytvoření základní vizualizace na základě těchto dat.

> [!NOTE]  
> Konektor ukázaný v tomto článku je aktuálně ve verzi Preview. Případnou zpětnou vazbu můžete poskytnout přes web [komunity Power BI](https://community.powerbi.com/) nebo na fóru [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas) (Nápady ohledně Power BI).

1. Otevřete Power BI Desktop. Close the start-up splash screen if it opens.

2. From the **Home** tab, navigate to **Get Data** > **More..** .

    ![Get data into Power BI Desktop from HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Get data into Power BI from Apache Spark BI")

3. Enter `Spark` in the search box, select **Azure HDInsight Spark**, and then select **Connect**.

    ![Get data into Power BI from Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Get data into Power BI from Apache Spark BI")

4. Enter your cluster URL (in the form `mysparkcluster.azurehdinsight.net`) in the **Server** text box.

5. Under **Data connectivity mode**, select **DirectQuery**. Pak vyberte **OK**.

    V případě Sparku můžete použít jakýkoli režim připojení dat. Pokud použijete DirectQuery, změny se v sestavách projeví bez nutnosti aktualizace celé datové sady. Pokud data importujete, musíte datovou sadu aktualizovat, aby se změny projevily. Další informace o tom, jak a kdy použít DirectQuery, najdete v tématu [Použití DirectQuery v Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Enter the HDInsight login account information, then select **Connect**. Výchozí název účtu je *admin*.

7. Select the `hvac` table, wait to see a preview of the data, and then select **Load**.

    ![Spark cluster user name and password](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark cluster user name and password")

    Power BI Desktop má všechny potřebné informace pro připojení ke clusteru Spark a načtení dat z tabulky `hvac`. Tabulka a její sloupce se zobrazí v podokně **Pole**.

8. Vizualizujte rozdíl mezi cílovou teplotou a skutečnou teplotou jednotlivých budov:

    1. V podokně **VIZUALIZACE** vyberte **Plošný graf**.

    2. Přetáhněte pole **BuildingID** (ID budovy) do části **Osa** a pole **ActualTemp** (Skutečná teplota) a **TargetTemp** (Cílová teplota) do části **Hodnota**.

        ![add value columns](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "add value columns")

        Diagram vypadá takto:

        ![area graph sum](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "area graph sum")

        Vizualizace ve výchozím nastavení zobrazí pro **ActualTemp** a **TargetTemp** součet hodnot. Select the down arrow next to **ActualTemp** and **TragetTemp** in the Visualizations pane, you can see **Sum** is selected.

    3. Select the down arrows next to **ActualTemp** and **TragetTemp** in the Visualizations pane, select **Average** to get an average of actual and target temperatures for each building.

        ![average of values](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "average of values")

        Vaše vizualizace dat by měla vypadat podobně jako na následujícím snímku obrazovky. Přesunutím kurzoru nad vizualizaci zobrazte popisky s relevantními daty.

        ![area graph](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "area graph")

9. Navigate to **File** > **Save**, enter the name `BuildingTemperature` for the file, then select **Save**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publikování sestavy ve službě Power BI (volitelné)

Služba Power BI umožňuje sdílet sestavy a řídicí panely napříč organizací. V této části nejprve publikujete datovou sadu a sestavu. Pak sestavu připnete na řídicí panel. Dashboards are typically used to focus on a subset of data in a report. You have only one visualization in your report, but it's still useful to go through the steps.

1. Otevřete Power BI Desktop.

1. Na kartě **Domů** klikněte na **Publikovat**.

    ![Publikování z Power BI Desktopu](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publikování z Power BI Desktopu")

1. Vyberte pracovní prostor, do kterého chcete datovou sadu a sestavu publikovat, a klikněte na **Vybrat**. Na následujícím obrázku je vybraný výchozí pracovní prostor **My Workspace**.

    ![Select workspace to publish dataset and report to](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Select workspace to publish dataset and report to") 

1. Po úspěšném publikování klikněte na **Otevřít soubor BuildingTemperature.pbix v Power BI**.

    ![Publish success, click to enter credentials](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publish success, click to enter credentials") 

1. Ve službě Power BI klikněte na **Zadat přihlašovací údaje**.

    ![Enter credentials in Power BI service](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Enter credentials in Power BI service")

1. Klikněte na **Upravit přihlašovací údaje**.

    ![Edit credentials in Power BI service](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Edit credentials in Power BI service")

1. Zadejte přihlašovací údaje účtu služby HDInsight a pak klikněte na **Přihlásit se**. Výchozí název účtu je *admin*.

    ![Sign in to Spark cluster](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Sign in to Spark cluster")

1. V levém podokně přejděte do části **Pracovní prostory** > **My Workspace** > **SESTAVY** a klikněte na **BuildingTemperature**.

    ![Report listed under reports in left pane](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Report listed under reports in left pane")

    V části **DATOVÉ SADY** v levém podokně by se také měla zobrazit datová sada **BuildingTemperature**.

    Vizuál, který jste vytvořili v Power BI Desktopu, je teď dostupný ve službě Power BI. 

1. Najeďte kurzorem na vizualizaci a klikněte na ikonu připínáčku v pravém horním rohu.

    ![Report in the Power BI service](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Report in the Power BI service")

1. Vyberte Nový řídicí panel, zadejte název `Building temperature` a klikněte na **Připnout**.

    ![Pin to new dashboard](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Pin to new dashboard")

1. V sestavě klikněte na **Přejít na řídicí panel**.

Váš vizuál je připnutý na řídicím panelu. Do sestavy můžete přidat další vizuály a připnout je na stejný řídicí panel. For more information about reports and dashboards, see [Reports in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) and [Dashboards in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení kurzu můžete cluster odstranit. With HDInsight, your data is stored in Azure Storage, so you can safely delete a cluster when it isn't in use. You're also charged for an HDInsight cluster, even when it isn't in use. Since the charges for the cluster are many times more than the charges for storage, it makes economic sense to delete clusters when they aren't in use.

To delete a cluster, see [Delete an HDInsight cluster using your browser, PowerShell, or the Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

In this tutorial, you learned how to use [Microsoft Power BI](https://powerbi.microsoft.com/) to visualize data in an Apache Spark cluster in [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/). Advance to the next article to see you can create a machine learning application.

> [!div class="nextstepaction"]
> [Create a machine learning application](./apache-spark-ipython-notebook-machine-learning.md)