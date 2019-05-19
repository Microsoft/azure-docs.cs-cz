---
title: 'Kurz: Analýza dat Apache Spark v Azure HDInsight pomocí Power BI '
description: Pomocí Microsoft Power BI k vizualizaci dat Apache Spark uložené clustery HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/16/2019
ms.openlocfilehash: bf70abd2b3119a97af5ad1d4c56274f8e575fd3e
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65860968"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Kurz: Analýza dat Apache Spark v HDInsight pomocí Power BI

V tomto kurzu se dozvíte, jak používat [Microsoft Power BI](https://powerbi.microsoft.com/) k vizualizaci dat v clusteru Apache Spark v [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vizualizace dat Sparku pomocí Power BI

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Dokončení článku [kurzu: Načtení dat a spouštění dotazů v clusteru Apache Spark v Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Volitelné: [Power BI zkušební verze předplatného](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Ověření dat

[Poznámkový blok Jupyter](https://jupyter.org/) , kterou jste vytvořili v [předchozí kurz o službě](apache-spark-load-data-run-query.md) obsahuje kód pro vytvoření `hvac` tabulky. Tato tabulka je založen na souboru CSV, k dispozici na všech clusterech HDInsight Spark na `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`. Pomocí následujícího postupu ověřte data.

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

3. V nabídce **Soubor** poznámkového bloku vyberte **Zavřít a zastavit**. Vypněte poznámkový blok a uvolněte tak prostředky.

## <a name="visualize-the-data"></a>Vizualizace dat

V této části pomocí Power BI vytvoříte vizualizace, sestavy a řídicí panely z dat v clusteru Spark.

### <a name="create-a-report-in-power-bi-desktop"></a>Vytvoření sestavy v Power BI Desktopu

Prvními kroky při práci se Sparkem je připojení ke clusteru v Power BI Desktopu, načtení dat z clusteru a vytvoření základní vizualizace na základě těchto dat.

> [!NOTE]  
> Konektor ukázaný v tomto článku je aktuálně ve verzi Preview. Případnou zpětnou vazbu můžete poskytnout přes web [komunity Power BI](https://community.powerbi.com/) nebo na fóru [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas) (Nápady ohledně Power BI).

1. Otevřete Power BI Desktop. Pokud se otevře, zavřete spouštění úvodní obrazovka.

2. Z **Domů** kartu, přejděte na **získat Data** > **více...** .

    ![Načtení dat do Power BI Desktopu z Apache Sparku ve službě HDInsight](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Načtení dat do Power BI z Apache Spark BI")

3. Zadejte `Spark` do vyhledávacího pole, vyberte **Azure HDInsight Spark**a pak vyberte **připojit**.

    ![Načtení dat do Power BI z Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Načtení dat do Power BI z Apache Spark BI")

4. Zadejte adresu URL vašeho clusteru (ve formě `mysparkcluster.azurehdinsight.net`) v **Server** textového pole.

5. V části **režim připojení dat**vyberte **DirectQuery**. Pak vyberte **OK**.

    V případě Sparku můžete použít jakýkoli režim připojení dat. Pokud použijete DirectQuery, změny se v sestavách projeví bez nutnosti aktualizace celé datové sady. Pokud data importujete, musíte datovou sadu aktualizovat, aby se změny projevily. Další informace o tom, jak a kdy použít DirectQuery, najdete v tématu [Použití DirectQuery v Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Zadejte přihlašovací údaje účtu HDInsight a pak vyberte **připojit**. Výchozí název účtu je *admin*.

7. Vyberte `hvac` tabulky, počkejte zobrazíte náhled dat a pak vyberte **zatížení**.

    ![Uživatelské jméno a heslo clusteru Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Uživatelské jméno a heslo clusteru Spark")

    Power BI Desktop má všechny potřebné informace pro připojení ke clusteru Spark a načtení dat z tabulky `hvac`. Tabulka a její sloupce se zobrazí v podokně **Pole**.

8. Vizualizujte rozdíl mezi cílovou teplotou a skutečnou teplotou jednotlivých budov:

    1. V podokně **VIZUALIZACE** vyberte **Plošný graf**.

    2. Přetáhněte pole **BuildingID** (ID budovy) do části **Osa** a pole **ActualTemp** (Skutečná teplota) a **TargetTemp** (Cílová teplota) do části **Hodnota**.

        ![Vytvoření vizualizací dat Sparku pomocí Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Vytvoření vizualizací dat Sparku pomocí Apache Spark BI")

        Diagram vypadá takto:

        ![Vytvoření vizualizací dat Sparku pomocí Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Vytvoření vizualizací dat Sparku pomocí Apache Spark BI")

        Vizualizace ve výchozím nastavení zobrazí pro **ActualTemp** a **TargetTemp** součet hodnot. Vyberte šipku dolů vedle **ActualTemp** a **TragetTemp** v podokně vizualizace, můžete zobrazit **součet** zaškrtnuto.

    3. Vyberte šipku dolů vedle **ActualTemp** a **TragetTemp** v podokně vizualizace vyberte **průměrné** získáte průměr skutečné a teploty cíl pro každý sestavování.

        ![Vytvoření vizualizací dat Sparku pomocí Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Vytvoření vizualizací dat Sparku pomocí Apache Spark BI")

        Vaše vizualizace dat by měla vypadat podobně jako na následujícím snímku obrazovky. Přesunutím kurzoru nad vizualizaci zobrazte popisky s relevantními daty.

        ![Vytvoření vizualizací dat Sparku pomocí Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Vytvoření vizualizací dat Sparku pomocí Apache Spark BI")

9. Přejděte do **souboru** > **Uložit**, zadejte název `BuildingTemperature` souboru, vyberte **Uložit**.

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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení kurzu můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Odstranění clusteru, naleznete v tématu [odstranění clusteru HDInsight pomocí prohlížeče, Powershellu nebo rozhraní příkazového řádku Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak používat [Microsoft Power BI](https://powerbi.microsoft.com/) k vizualizaci dat v clusteru Apache Spark v [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/). V dalším článku se dozvíte, jak můžete data zaregistrovaná ve Sparku přetáhnout do nástroje pro analýzu BI, jako je Power BI.

> [!div class="nextstepaction"]
> [Spuštění úlohy streamování Apache Spark](apache-spark-eventhub-streaming.md)