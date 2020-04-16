---
title: 'Výuka – Azure Synapse Analytics: Definice úlohy Spark pro Synapse'
description: Kurz – Pomocí Azure Synapse Analytics vytvořte definice úloh Spark a odešlete je do fondu Synapse Spark.
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: d5443a2db6f4fecbd84ef51166f44c3a6e920aee
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422967"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>Kurz: Pomocí Azure Synapse Analytics vytvořte definice úloh Apache Spark pro fondy Synapse Spark.

Tento kurz ukazuje, jak pomocí Azure Synapse Analytics vytvořit definice úloh Spark a pak je odeslat do fondu Synapse Spark. Modul plug-in můžete použít několika způsoby:

* Vytvořte a odešlete definici úlohy Spark do fondu Synapse Spark.
* Zobrazit podrobnosti o úloze po odeslání.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvořte a odešlete definici úlohy Spark do fondu Synapse Spark.
> * Zobrazit podrobnosti o úloze po odeslání.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor Azure Synapse Analytics. Pokyny najdete [v tématu Vytvoření pracovního prostoru Azure Synapse Analytics](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).

## <a name="get-started"></a>Začínáme

Před odesláním definice úlohy Spark musíte být vlastníkem dat objektů blob úložiště souborového systému ADLS Gen2, se kterým chcete pracovat. Pokud nejste, je třeba přidat oprávnění ručně.

### <a name="scenario-1-add-permission"></a>Scénář 1: Přidání oprávnění

1. Otevřete [Microsoft Azure](https://ms.portal.azure.com)a otevřete účet úložiště.

2. Klepněte na **položku Kontejnery**a vytvořte **systém souborů**. Tento kurz používá oblast `sparkjob`.

    ![Chcete-li odeslat definici úlohy jiskry, klepněte na tlačítko Odeslat.](./media/apache-spark-job-definitions/open-azure-container.png)

    ![Dialogové okno Odeslání jiskry](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. Otevřete `sparkjob`, klepněte na **položku Řízení přístupu (IAM)** a potom klepněte na **tlačítko Přidat** a vyberte přidat přiřazení **role**.

    ![Chcete-li odeslat definici úlohy jiskry, klepněte na tlačítko Odeslat.](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![Chcete-li odeslat definici úlohy jiskry, klepněte na tlačítko Odeslat.](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. Klikněte na **Přiřazení rolí**, zadejte uživatelské jméno a ověřte roli uživatele.

    ![Chcete-li odeslat definici úlohy jiskry, klepněte na tlačítko Odeslat.](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>Scénář 2: Příprava struktury složek

Před odesláním definice úlohy Spark je třeba provést jednu úlohu, kterou musíte udělat, nahrát soubory do ADLS Gen2 a připravit tam strukturu složek. K ukládání souborů používáme uzel úložiště v Synapse Studiu.

1. Otevřete [Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Klikněte na **Data**, vyberte **Účty úložiště**a Nahrajte příslušné soubory do souborového systému ADLS Gen2. Podporujeme Scala, Java, .NET a Python. Tento kurz používá příklad na obrázku jako ukázku, můžete změnit strukturu projektu, jak chcete.

    ![Nastavení hodnoty definice úlohy Spark](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>Vytvoření definice úlohy Spark

1. Otevřete [Azure Synapse Analytics](https://web.azuresynapse.net/)a vyberte **Rozvíjet**.

2. V levém podokně vyberte **Definice úloh Spark.**

3. Vpravo od "Definice úloh Spark" klikněte na uzel **Akce.**

     ![Vytvořit novou definici jiskrové úlohy](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. V rozevíracím seznamu **Akce** vyberte **Nová definice úlohy Spark.**

     ![Vytvořit novou definici jiskrové úlohy](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. V okně Definice úlohy Nová jiskra vyberte jazyk a zadejte následující informace:  

   * Vyberte **jazyk** jako **Spark(Scala).**

    |  Vlastnost   | Popis   |  
    | ----- | ----- |  
    |Název definice úlohy| Zadejte název definice úlohy Spark.  Tento kurz používá oblast `job definition sample`. Tento název lze kdykoli aktualizovat, dokud nebude publikován.|  
    |Hlavní definiční soubor| Hlavní soubor použitý pro úlohu. Vyberte soubor JAR z úložiště. Chcete-li soubor odeslat do účtu úložiště, můžete vybrat **Nahrát soubor.** |
    |Název hlavní třídy| Plně kvalifikovaný identifikátor nebo hlavní třída, která je v hlavním souboru definice.|
    |Argumenty příkazového řádku| Volitelné argumenty úlohy.|
    |Referenční soubory| Další soubory používané pro referenci v hlavním definičním souboru. Chcete-li soubor odeslat do účtu úložiště, můžete vybrat **Nahrát soubor.**|
    |Zapalovací bazén| Úloha bude odeslána do vybraného fondu Spark.|
    |Verze Spark| Verze Spark, že fond Spark běží.|
    |Exekutoři| Počet vykonavatelů, které mají být uvedeny v zadaném fondu Spark pro úlohu.|
    |Velikost exekutora| Počet jader a paměti, které mají být použity pro vykonavatele uvedené v zadaném fondu Spark pro úlohu.|  
    |Velikost ovladače| Počet jader a paměti, které mají být použity pro ovladač uvedený v zadaném fondu Spark pro úlohu.|

    ![Nastavení hodnoty definice úlohy Spark](./media/apache-spark-job-definitions/create-scala-definition.png)

   * Vyberte **jazyk** jako **PySpark(Python)**.

    |  Vlastnost   | Popis   |  
    | ----- | ----- |  
    |Název definice úlohy| Zadejte název definice úlohy Spark.  Tento kurz používá oblast `job definition sample`. Tento název lze kdykoli aktualizovat, dokud nebude publikován.|  
    |Hlavní definiční soubor| Hlavní soubor použitý pro úlohu. Vyberte soubor PY z úložiště. Chcete-li soubor odeslat do účtu úložiště, můžete vybrat **Nahrát soubor.**|
    |Argumenty příkazového řádku| Volitelné argumenty úlohy.|
    |Referenční soubory| Další soubory používané pro referenci v hlavním definičním souboru. Chcete-li soubor odeslat do účtu úložiště, můžete vybrat **Nahrát soubor.**|
    |Zapalovací bazén| Úloha bude odeslána do vybraného fondu Spark.|
    |Verze Spark| Verze Spark, že fond Spark běží.|
    |Exekutoři| Počet vykonavatelů, které mají být uvedeny v zadaném fondu Spark pro úlohu.|
    |Velikost exekutora| Počet jader a paměti, které mají být použity pro vykonavatele uvedené v zadaném fondu Spark pro úlohu.|  
    |Velikost ovladače| Počet jader a paměti, které mají být použity pro ovladač uvedený v zadaném fondu Spark pro úlohu.|

    ![Nastavení hodnoty definice úlohy Spark](./media/apache-spark-job-definitions/create-py-definition.png)

   * Vyberte **jazyk** jako **.NET Spark(C#/F#)**.

    |  Vlastnost   | Popis   |  
    | ----- | ----- |  
    |Název definice úlohy| Zadejte název definice úlohy Spark.  Tento kurz používá oblast `job definition sample`. Tento název lze kdykoli aktualizovat, dokud nebude publikován.|  
    |Hlavní definiční soubor| Hlavní soubor použitý pro úlohu. Vyberte soubor ZIP, který obsahuje rozhraní .NET pro aplikaci Spark (tj. hlavní spustitelný soubor, knihovny DLL obsahující uživatelem definované funkce a další požadované soubory) z úložiště. Chcete-li soubor odeslat do účtu úložiště, můžete vybrat **Nahrát soubor.**|
    |Hlavní spustitelný soubor| Hlavní spustitelný soubor v souboru ZIP hlavní definice.|
    |Argumenty příkazového řádku| Volitelné argumenty úlohy.|
    |Referenční soubory| Další soubory potřebné pracovními uzly pro spuštění rozhraní .NET pro aplikaci Spark, která není zahrnuta v souboru ZIP hlavní definice(to znamená závislé nádoby, další uživatelem definované funkční knihovny DLL a další konfigurační soubory). Chcete-li soubor odeslat do účtu úložiště, můžete vybrat **Nahrát soubor.**|
    |Zapalovací bazén| Úloha bude odeslána do vybraného fondu Spark.|
    |Verze Spark| Verze Spark, že fond Spark běží.|
    |Exekutoři| Počet vykonavatelů, které mají být uvedeny v zadaném fondu Spark pro úlohu.|
    |Velikost exekutora| Počet jader a paměti, které mají být použity pro vykonavatele uvedené v zadaném fondu Spark pro úlohu.|  
    |Velikost ovladače| Počet jader a paměti, které mají být použity pro ovladač uvedený v zadaném fondu Spark pro úlohu.|

    ![Nastavení hodnoty definice úlohy Spark](./media/apache-spark-job-definitions/create-net-definition.png)

6. Vyberte **Publikovat,** chcete-li uložit definici úlohy Spark.

    ![Publikovat definici úlohy Spark](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>Odeslání definice úlohy Spark

Po vytvoření definice úlohy Spark, můžete ji odeslat do fondu Synapse Spark. Ujistěte se, že jste prošli kroky v **Začínáme** před vyzkoušení vzorků v této části.

### <a name="scenario-1-submit-spark-job-definition"></a>Scénář 1: Odeslat definici úlohy Spark

1. Kliknutím otevřete okno definice úlohy jiskry.

      ![Otevřít definici úlohy jiskry k odeslání ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. Kliknutím na ikonu **Odeslat** odešlete projekt do vybraného fondu Spark. Kliknutím na kartu **Sparku pro sledování adres URL** zobrazíte logquery aplikace Spark.

    ![Chcete-li odeslat definici úlohy jiskry, klepněte na tlačítko Odeslat.](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Dialogové okno Odeslání jiskry](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>Scénář 2: Zobrazit průběh spuštění úlohy Spark

1. Klepněte na **tlačítko Sledovat**a vyberte možnost **Aplikace Spark.** Odeslanou žádost Spark najdete.

    ![Zobrazit aplikaci Spark](./media/apache-spark-job-definitions/view-spark-application.png)

2. Pak klikněte na aplikaci Spark, zobrazí se okno **LogQuery.** Průběh spuštění úlohy můžete zobrazit z **aplikace LogQuery**.

    ![Zobrazit spark aplikace LogQuery](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Scénář 3: Kontrola výstupního souboru

 1. Klepněte na **položku Data**a vyberte **možnost Účty úložiště**. Po úspěšném spuštění můžete přejít do úložiště ADLS Gen2 a zkontrolovat, zda jsou generovány výstupy.

    ![Zobrazit výstupní soubor](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>Další kroky

Tento kurz ukázal, jak pomocí Azure Synapse Analytics vytvořit definice úloh Spark a pak je odeslat do fondu Synapse Spark. Dále můžete pomocí Azure Synapse Analytics vytvářet datové sady Power BI a spravovat data Power BI. 

- [Připojení k datům v Power BI Desktopu](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [Vizualizace pomocí Power BI](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
