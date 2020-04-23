---
title: 'Kurz: Analýza dat Azure HDInsight Apache Spark pomocí Power BI'
description: Výuka – Vizualizovat clustery HDInsight uložených dat Apache Spark pomocí Microsoft Power BI
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 04/21/2020
ms.openlocfilehash: dd0b4d1b0998bd4b13a17fb8345b87924bc27e1d
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869971"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Kurz: Analýza dat Apache Spark pomocí Power BI v HDInsightu

V tomto kurzu se dozvíte, jak pomocí Microsoft Power BI vizualizovat data v clusteru Apache Spark v Azure HDInsight.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vizualizace dat Sparku pomocí Power BI

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadované součásti

* Dokončete článek [Kurz: Načítání dat a spouštění dotazů v clusteru Apache Spark ve službě Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Volitelné: [Zkušební předplatné Power BI](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Ověření dat

[Jupyter notebook,](https://jupyter.org/) který jste vytvořili v předchozím `hvac` [kurzu](apache-spark-load-data-run-query.md) obsahuje kód pro vytvoření tabulky. Tato tabulka je založena na souboru CSV, který `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`je k dispozici ve všech clusterech HDInsight Spark na adrese . Pomocí následujícího postupu ověřte data.

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

1. Otevřete Power BI Desktop. Pokud se otevře úvodní obrazovka při spuštění, zavřete ji.

2. Na kartě **Domů** přejděte na Získat**další data..** **Get Data** > .

    ![Získání dat do Power BI Desktopu z HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Získání dat do Power BI z Apache Spark BI")

3. Zadejte `Spark` do vyhledávacího pole, vyberte **Azure HDInsight Spark**a pak vyberte **Připojit**.

    ![Získání dat do Power BI z Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Získání dat do Power BI z Apache Spark BI")

4. Do textového pole `mysparkcluster.azurehdinsight.net` **Server** zadejte adresu URL clusteru (ve formuláři).

5. V části **Režim datového připojení**vyberte **DirectQuery**. Pak vyberte **OK**.

    V případě Sparku můžete použít jakýkoli režim připojení dat. Pokud použijete DirectQuery, změny se v sestavách projeví bez nutnosti aktualizace celé datové sady. Pokud data importujete, musíte datovou sadu aktualizovat, aby se změny projevily. Další informace o tom, jak a kdy použít DirectQuery, najdete v tématu [Použití DirectQuery v Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Zadejte přihlašovací účet HDInsight a pak vyberte **Připojit**. Výchozí název účtu je *admin*.

7. Vyberte `hvac` tabulku, počkejte, až se zobrazí náhled dat, a pak vyberte **Načíst**.

    ![Uživatelské jméno a heslo clusteru Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Uživatelské jméno a heslo clusteru Spark")

    Power BI Desktop má všechny potřebné informace pro připojení ke clusteru Spark a načtení dat z tabulky `hvac`. Tabulka a její sloupce se zobrazí v podokně **Pole**.

8. Vizualizujte rozdíl mezi cílovou teplotou a skutečnou teplotou jednotlivých budov:

    1. V podokně **VIZUALIZACE** vyberte **Plošný graf**.

    2. Přetáhněte pole **BuildingID** (ID budovy) do části **Osa** a pole **ActualTemp** (Skutečná teplota) a **TargetTemp** (Cílová teplota) do části **Hodnota**.

        ![přidání sloupců hodnoty](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "přidání sloupců hodnoty")

        Diagram vypadá takto:

        ![součet plošného grafu](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "součet plošného grafu")

        Vizualizace ve výchozím nastavení zobrazí pro **ActualTemp** a **TargetTemp** součet hodnot. Vyberte šipku dolů vedle **ActualTemp** a **TragetTemp** v podokně Vizualizace, můžete vidět **Sum** je vybrán.

    3. Vyberte šipky dolů vedle **AktuálníTemp** a **TragetTemp** v podokně Vizualizace, vyberte **Průměr,** chcete-li získat průměr skutečných a cílových teplot pro každou budovu.

        ![průměr hodnot](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "průměr hodnot")

        Vaše vizualizace dat by měla vypadat podobně jako na následujícím snímku obrazovky. Přesunutím kurzoru nad vizualizaci zobrazte popisky s relevantními daty.

        ![plošný graf](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "plošný graf")

9. Přejděte na **Soubor** > **uložit**, zadejte název `BuildingTemperature` souboru a vyberte **Uložit**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publikování sestavy ve službě Power BI (volitelné)

Služba Power BI umožňuje sdílet sestavy a řídicí panely napříč organizací. V této části nejprve publikujete datovou sadu a sestavu. Pak sestavu připnete na řídicí panel. Řídicí panely se obvykle používají k zaměření na podmnožinu dat v sestavě. V sestavě máte jenom jednu vizualizaci, ale pořád je užitečné projít si kroky.

1. Otevřete Power BI Desktop.

1. Na kartě **Domů** vyberte **Publikovat**.

    ![Publikování z Power BI Desktopu](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publikování z Power BI Desktopu")

1. Vyberte pracovní prostor, ve které chcete publikovat datovou sadu a sestavovat do ní, a pak vyberte **Vybrat**. Na následujícím obrázku je vybraný výchozí pracovní prostor **My Workspace**.

    ![Vyberte pracovní prostor, do kterýchcete publikovat datovou sadu a do které chcete vytvořit zprávu.](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Vyberte pracovní prostor, do kterýchcete publikovat datovou sadu a do které chcete vytvořit zprávu.")

1. Po úspěšném publikování vyberte **v Power BI možnost Otevřít položku BuildingTemperature.pbix**.

    ![Publikovat úspěch, kliknutím zadejte přihlašovací údaje](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publikovat úspěch, kliknutím zadejte přihlašovací údaje")

1. Ve službě Power BI vyberte **Zadat přihlašovací údaje**.

    ![Zadání přihlašovacích údajů ve službě Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Zadání přihlašovacích údajů ve službě Power BI")

1. Vyberte **Upravit přihlašovací údaje**.

    ![Úpravy přihlašovacích údajů ve službě Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Úpravy přihlašovacích údajů ve službě Power BI")

1. Zadejte přihlašovací účet HDInsight a pak **vyberte Přihlásit se**. Výchozí název účtu je *admin*.

    ![Přihlášení ke clusteru Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Přihlášení ke clusteru Spark")

1. V levém podokně přejděte na **Pracovní prostory** > **Moje pracovní prostor** > **zprávy**a pak vyberte **BuildingTemperature**.

    ![Sestava uvedená v části sestavy v levém podokně](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Sestava uvedená v části sestavy v levém podokně")

    V části **DATOVÉ SADY** v levém podokně by se také měla zobrazit datová sada **BuildingTemperature**.

    Vizuál, který jste vytvořili v Power BI Desktopu, je teď dostupný ve službě Power BI.

1. Najeďte kurzorem na vizualizaci a pak vyberte ikonu špendlíku v pravém horním rohu.

    ![Sestava ve službě Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Sestava ve službě Power BI")

1. Vyberte "Nový řídicí panel", zadejte název `Building temperature`a pak vyberte **Připnout**.

    ![Připnout na nový řídicí panel](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Připnout na nový řídicí panel")

1. V sestavě vyberte **Přejít na řídicí panel**.

Váš vizuál je připnutý na řídicím panelu. Do sestavy můžete přidat další vizuály a připnout je na stejný řídicí panel. Další informace o sestavách a řídicích panelech najdete [v tématu Sestavy v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) a [řídicích panelech v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení kurzu můžete cluster odstranit. S HDInsight, vaše data jsou uloženy ve službě Azure Storage, takže můžete bezpečně odstranit clusteru, když není v provozu. Účtuje se vám také cluster HDInsight, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster jsou mnohonásobně vyšší než poplatky za úložiště, má ekonomické smysl odstranit clustery, když nejsou používány.

Pokud chcete odstranit cluster, přečtěte si informace [o odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového příkazu k Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak pomocí Microsoft Power BI vizualizovat data v clusteru Apache Spark v Azure HDInsight. Přejdete k dalšímu článku a uvidíte, že můžete vytvořit aplikaci strojového učení.

> [!div class="nextstepaction"]
> [Vytvoření aplikace strojového učení](./apache-spark-ipython-notebook-machine-learning.md)