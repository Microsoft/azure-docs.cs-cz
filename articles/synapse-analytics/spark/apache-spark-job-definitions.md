---
title: 'Kurz – Apache Spark pro Azure synapse Analytics: definice úlohy Apache Spark pro synapse'
description: Kurz – použití analýzy Azure Synapse k vytvoření definic úloh Spark a jejich odeslání do Apache Spark pro fond Azure synapse Analytics.
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 5fc9dffaa73d195c842381b6682a00e9834c0fe7
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587931"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>Kurz: použití analýzy Azure Synapse k vytváření definicí úloh Apache Spark pro fondy synapse Spark

Tento kurz ukazuje, jak pomocí analýzy Azure synapse vytvořit definice úloh Spark a pak je odeslat do fondu synapse Spark. Modul plug-in můžete použít několika způsoby:

* Vývoj a odeslání definice úlohy Sparku ve fondu synapse Spark.
* Zobrazit podrobnosti o úloze po odeslání.

V tomto kurzu:

> [!div class="checklist"]
>
> * Vývoj a odeslání definice úlohy Sparku ve fondu synapse Spark.
> * Zobrazit podrobnosti o úloze po odeslání.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor analýzy Azure synapse. Pokyny najdete v tématu [Vytvoření pracovního prostoru Azure synapse Analytics](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).

## <a name="get-started"></a>Začínáme

Před odesláním definice úlohy Sparku musíte být vlastníkem dat objektu BLOB úložiště, se kterým chcete pracovat, se systémem ADLS Gen2. Pokud ne, budete muset oprávnění přidat ručně.

### <a name="scenario-1-add-permission"></a>Scénář 1: Přidání oprávnění

1. Otevřete [Microsoft Azure](https://ms.portal.azure.com)a pak otevřete účet úložiště.

2. Klikněte na **kontejnery**a pak vytvořte **systém souborů**. Tento kurz používá oblast `sparkjob`.

    ![Kliknutím na tlačítko Odeslat odešlete definici úlohy Spark.](./media/apache-spark-job-definitions/open-azure-container.png)

    ![Dialogové okno pro odeslání Sparku](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. Otevřete `sparkjob` , klikněte na **Access Control (IAM)** a pak klikněte na **Přidat** a vyberte **Přidat přiřazení role**.

    ![Kliknutím na tlačítko Odeslat odešlete definici úlohy Spark.](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![Kliknutím na tlačítko Odeslat odešlete definici úlohy Spark.](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. Klikněte na **přiřazení rolí**, zadejte uživatelské jméno a pak ověřte roli uživatele.

    ![Kliknutím na tlačítko Odeslat odešlete definici úlohy Spark.](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>Scénář 2: Příprava struktury složek

Před odesláním definice úlohy Sparku jedna úloha potřebuje udělat nahrávání souborů do ADLS Gen2 a příprava struktury složek tam. V synapse studiu používáme k ukládání souborů uzel úložiště.

1. Otevřete [Azure synapse Analytics](https://web.azuresynapse.net/).

2. Klikněte na **data**, vyberte **účty úložiště**a nahrajte příslušné soubory do adls Gen2 systému souborů. Podporujeme Scala, Java, .NET a Python. V tomto kurzu se používá příklad na obrázku jako ukázka, kde můžete změnit strukturu projektu, jak chcete.

    ![Nastavení hodnoty definice úlohy Spark](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>Vytvoření definice úlohy Spark

1. Otevřete [Azure synapse Analytics](https://web.azuresynapse.net/)a vyberte **vývoj**.

2. V levém podokně vyberte **Definice úloh Spark** .

3. Klikněte na uzel **Akce** napravo od položky "Definice úloh Spark".

     ![Vytvořit novou definici úlohy Spark](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. V rozevíracím seznamu **Akce** vyberte **Nová definice úlohy Spark** .

     ![Vytvořit novou definici úlohy Spark](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. V okně Nová definice úlohy Spark vyberte jazyk a potom zadejte následující informace:  

   * Vyberte **jazyk** jako **Spark (Scala)**.

    |  Vlastnost   | Popis   |  
    | ----- | ----- |  
    |Název definice úlohy| Zadejte název definice úlohy Spark.  Tento kurz používá oblast `job definition sample`. Tento název se dá kdykoli aktualizovat, dokud nebude publikovaný.|  
    |Soubor hlavní definice| Hlavní soubor, který se používá pro úlohu. Vyberte ze svého úložiště soubor JAR. Můžete vybrat **Odeslat soubor** a nahrát ho do účtu úložiště. |
    |Název hlavní třídy| Plně kvalifikovaný identifikátor nebo hlavní třída, která je v hlavním definičním souboru.|
    |Argumenty příkazového řádku| Nepovinné argumenty úlohy.|
    |Referenční soubory| Další soubory používané pro reference v hlavním definičním souboru. Můžete vybrat **Odeslat soubor** a nahrát ho do účtu úložiště.|
    |Fond Spark| Úloha se odešle do vybraného fondu Spark.|
    |Verze Sparku| Verze Sparku, kterou používá fond Spark.|
    |Prováděcí moduly| Počet prováděcích modulů, které se mají v zadaném fondu Spark pro úlohu udělit|
    |Velikost prováděcího modulu| Počet jader a paměti, které se mají použít pro vykonavatele zadané v zadaném fondu Spark pro úlohu.|  
    |Velikost ovladače| Počet jader a paměti, které se mají použít pro ovladač zadaný v zadaném fondu Spark pro úlohu.|

    ![Nastavení hodnoty definice úlohy Spark](./media/apache-spark-job-definitions/create-scala-definition.png)

   * Vyberte **jazyk** jako **PySpark (Python)**.

    |  Vlastnost   | Popis   |  
    | ----- | ----- |  
    |Název definice úlohy| Zadejte název definice úlohy Spark.  Tento kurz používá oblast `job definition sample`. Tento název se dá kdykoli aktualizovat, dokud nebude publikovaný.|  
    |Soubor hlavní definice| Hlavní soubor, který se používá pro úlohu. Vyberte ze svého úložiště soubor PY. Můžete vybrat **Odeslat soubor** a nahrát ho do účtu úložiště.|
    |Argumenty příkazového řádku| Nepovinné argumenty úlohy.|
    |Referenční soubory| Další soubory používané pro reference v hlavním definičním souboru. Můžete vybrat **Odeslat soubor** a nahrát ho do účtu úložiště.|
    |Fond Spark| Úloha se odešle do vybraného fondu Spark.|
    |Verze Sparku| Verze Sparku, kterou používá fond Spark.|
    |Prováděcí moduly| Počet prováděcích modulů, které se mají v zadaném fondu Spark pro úlohu udělit|
    |Velikost prováděcího modulu| Počet jader a paměti, které se mají použít pro vykonavatele zadané v zadaném fondu Spark pro úlohu.|  
    |Velikost ovladače| Počet jader a paměti, které se mají použít pro ovladač zadaný v zadaném fondu Spark pro úlohu.|

    ![Nastavení hodnoty definice úlohy Spark](./media/apache-spark-job-definitions/create-py-definition.png)

   * Vyberte **jazyk** jako **.NET Spark (C#/f #)**.

    |  Vlastnost   | Popis   |  
    | ----- | ----- |  
    |Název definice úlohy| Zadejte název definice úlohy Spark.  Tento kurz používá oblast `job definition sample`. Tento název se dá kdykoli aktualizovat, dokud nebude publikovaný.|  
    |Soubor hlavní definice| Hlavní soubor, který se používá pro úlohu. Vyberte soubor ZIP, který obsahuje vaši aplikaci .NET for Spark (tj. hlavní spustitelný soubor, knihovny DLL obsahující uživatelsky definované funkce a další požadované soubory) ze svého úložiště. Můžete vybrat **Odeslat soubor** a nahrát ho do účtu úložiště.|
    |Hlavní spustitelný soubor| Hlavní spustitelný soubor v hlavní definici souboru ZIP.|
    |Argumenty příkazového řádku| Nepovinné argumenty úlohy.|
    |Referenční soubory| Další soubory, které jsou potřeba pro pracovní uzly pro spuštění aplikace .NET pro Spark, která není zahrnutá do hlavní definice souboru ZIP (to znamená závislých jar, dalších uživatelsky definovaných funkcí DLL a dalších konfiguračních souborů). Můžete vybrat **Odeslat soubor** a nahrát ho do účtu úložiště.|
    |Fond Spark| Úloha se odešle do vybraného fondu Spark.|
    |Verze Sparku| Verze Sparku, kterou používá fond Spark.|
    |Prováděcí moduly| Počet prováděcích modulů, které se mají v zadaném fondu Spark pro úlohu udělit|
    |Velikost prováděcího modulu| Počet jader a paměti, které se mají použít pro vykonavatele zadané v zadaném fondu Spark pro úlohu.|  
    |Velikost ovladače| Počet jader a paměti, které se mají použít pro ovladač zadaný v zadaném fondu Spark pro úlohu.|

    ![Nastavení hodnoty definice úlohy Spark](./media/apache-spark-job-definitions/create-net-definition.png)

6. Vyberte **publikovat** a uložte definici úlohy Spark.

    ![Publikování definice úlohy Sparku](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>Odeslání definice úlohy Sparku

Po vytvoření definice úlohy Spark ji můžete odeslat do fondu synapse Spark. Před vyzkoušením ukázek v této části se ujistěte, že jste prošli kroky v části **Začínáme** .

### <a name="scenario-1-submit-spark-job-definition"></a>Scénář 1: odeslání definice úlohy Spark

1. Kliknutím otevřete okno Definice úlohy Spark.

      ![Otevření definice úlohy Spark k odeslání ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. Kliknutím na ikonu **Odeslat** odešlete projekt do vybraného fondu Spark. Kliknutím na kartu **Adresa URL monitorování Spark** si můžete prohlédnout LogQuery aplikace Spark.

    ![Kliknutím na tlačítko Odeslat odešlete definici úlohy Spark.](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Dialogové okno pro odeslání Sparku](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>Scénář 2: zobrazení průběhu spuštěné úlohy Spark

1. Klikněte na **monitorování**a pak vyberte možnost **aplikace Spark** . Můžete najít odeslanou aplikaci Spark.

    ![Zobrazit aplikaci Spark](./media/apache-spark-job-definitions/view-spark-application.png)

2. Pak klikněte na tlačítko aplikace Spark, zobrazí se okno **LogQuery** . Průběh provádění úlohy můžete zobrazit z **LogQuery**.

    ![Zobrazit LogQuery aplikace Spark](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Scénář 3: zkontrolování výstupního souboru

 1. Klikněte na **data**a pak vyberte **účty úložiště**. Po úspěšném spuštění můžete přejít do úložiště ADLS Gen2 a vygeneruje se výstupy kontroly.

    ![Zobrazit výstupní soubor](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>Další kroky

Tento kurz ukazuje, jak pomocí služby Azure synapse Analytics vytvořit definice úloh Spark a pak je odeslat do fondu synapse Spark. Dále můžete pomocí služby Azure synapse Analytics vytvářet Power BI datové sady a spravovat Power BI data. 

- [Připojení k datům v Power BI Desktopu](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [Vizualizace pomocí Power BI](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
