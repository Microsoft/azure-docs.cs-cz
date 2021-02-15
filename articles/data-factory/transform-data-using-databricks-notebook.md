---
title: Spuštění poznámkového bloku datacihly s aktivitou
description: Zjistěte, jak můžete pomocí aktivity poznámkového bloku Databricks v datové továrně Azure spustit poznámkový blok Databricks pro cluster úloh Databricks.
ms.service: data-factory
ms.topic: tutorial
ms.author: abnarain
author: nabhishek
ms.custom: seo-lt-2019
ms.date: 03/12/2018
ms.openlocfilehash: 20253954035798187f28c18c8207c114d27bc9c6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374078"
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Spuštění poznámkového bloku Databricks s využitím aktivity poznámkového bloku Databricks ve službě Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto kurzu pomocí webu Azure Portal vytvoříte kanál Azure Data Factory, který spustí poznámkový blok Databricks pro cluster úloh Databricks. Kanál do poznámkového bloku Databricks během provádění také předá parametry Azure Data Factory.

V tomto kurzu provedete následující kroky:

  - Vytvoření datové továrny

  - Vytvoření kanálu využívajícího aktivitu poznámkového bloku Databricks

  - Aktivace spuštění kanálu

  - Monitorování spuštění kanálu

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).

Jedenáctiminutové představení a ukázku této funkce najdete v tomto videu:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/ingest-prepare-and-transform-using-azure-databricks-and-data-factory/player]

## <a name="prerequisites"></a>Požadavky

  - **Azure Databricks pracovní prostor**. [Vytvořte pracovní prostor Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) nebo použijte existující. V pracovním prostoru Azure Databricks vytvoříte poznámkový blok Python. Pak poznámkový blok spustíte a pomocí služby Azure Data Factory do něj předáte parametry.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1.  Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.

1.  V levé nabídce vyberte **Vytvořit prostředek**, zvolte možnost **Analýza** a vyberte položku **Datová továrna**.

    ![Vytvoření nové datové továrny](media/transform-data-using-databricks-notebook/new-azure-data-factory-menu.png)

1.  V podokně **Nová datová továrna** zadejte do pole **Název** text **ADFTutorialDataFactory**.

    Název objektu pro vytváření dat Azure musí být *globálně jedinečný*. Pokud se zobrazí následující chyba, změňte název datové továrny. (Například použijte **\<yourname\> ADFTutorialDataFactory**). Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](./naming-rules.md).

    ![Zadání názvu nové datové továrny](media/transform-data-using-databricks-notebook/new-azure-data-factory.png)

1.  Jako **Předplatné** vyberte své předplatné Azure, ve kterém chcete datovou továrnu vytvořit.

1.  U položky **Skupina prostředků** proveďte jeden z následujících kroků:
    
    - Vyberte **použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.
    
    - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.

    U některých kroků v tomto rychlém startu se předpokládá, že pro skupinu prostředků použijete název **ADFTutorialResourceGroup** . Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).

1.  V poli **Verze** vyberte **V2**.

1.  V poli **Umístění** vyberte umístění pro datovou továrnu.

    Pokud chcete zobrazit seznam oblastí Azure, ve kterých je služba Data Factory aktuálně dostupná, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **Data Factory:**[Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat (třeba Azure Storage a Azure SQL Database) a výpočetní prostředí (jako HDInsight) používaná službou Data Factory můžou být v jiných oblastech.
1.  Vyberte **Vytvořit**.


1.  Po vytvoření se zobrazí stránka **Datová továrna**. Kliknutím na dlaždici **Vytvořit a monitorovat** spusťte na samostatné kartě aplikaci uživatelského rozhraní služby Data Factory.

    ![Spuštění aplikace uživatelského rozhraní datové továrny](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Vytvoření propojených služeb

V této části vytvoříte propojenou službu Databricks. Tato propojená služba obsahuje informace o připojení ke clusteru Databricks:

### <a name="create-an-azure-databricks-linked-service"></a>Vytvoření propojené služby Azure Databricks

1.  Na stránce **Začínáme** přepněte na levém panelu na kartu **Upravit**.

    ![Úprava nové propojené služby](media/transform-data-using-databricks-notebook/get-started-page.png)

1.  Ve spodní části okna vyberte možnost **Připojení** a potom možnost **+ Nové**.
    
    ![Vytvoření nového připojení](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

1.  V okně **Nová propojená služba** vyberte **Compute** \> **Azure Databricks** a potom vyberte **Pokračovat**.
    
    ![Zadání propojené služby Databricks](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

1.  V okně **Nová propojená služba** proveďte následující kroky:
    
    1.  Jako **název** zadejte **_AzureDatabricks \_ LinkedService_**
    
    1.  Vyberte příslušný **Pracovní prostor Databricks**, ve kterém poznámkový blok spustíte.

    1.  V poli **Vyberte cluster** zvolte možnost **Nový cluster úloh**.
    
    1.  V části **Doména/oblast** by se měly údaje vyplnit automaticky.

    1.  **Přístupový token** vygenerujte z pracovního prostoru Azure Databricks. Postup najdete [tady](https://docs.databricks.com/api/latest/authentication.html#generate-token).

    1.  V případě **verze clusteru** vyberte **4,2** (s Apache Spark 2.3.1, Scala 2,11).

    1.  V části **Typ uzlu clusteru** vyberte **Standardní\_D3\_v2** pod kategorií **Pro obecné účely (HDD)** pro tento kurz. 
    
    1.  V části **Pracovní procesy** zadejte hodnotu **2**.
    
    1.  Vyberte **Dokončit** .

        ![Dokončení vytváření propojené služby](media/transform-data-using-databricks-notebook/new-databricks-linkedservice.png)

## <a name="create-a-pipeline"></a>Vytvoření kanálu

1.  Vyberte **+** tlačítko (plus) a pak v nabídce vyberte **kanál** .

    ![Tlačítka pro vytvoření nového kanálu](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

1.  Vytvořte **parametr**, který se použije v **kanálu**. Později tento parametr předáte do aktivity poznámkového bloku Databricks. V prázdném kanálu klikněte na kartu **Parametry**, pak klikněte na **Nový** a pojmenujte parametr **name** (název).

    ![Vytvoření nového parametru](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![Vytvoření parametru name (název)](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

1.  Na panelu nástrojů **Aktivity** rozbalte **Databricks**. Přetáhněte aktivitu **Poznámkový blok** z panelu nástrojů **Aktivity** na plochu návrháře kanálu.

    ![Přetažení poznámkového bloku na plochu návrháře](media/transform-data-using-databricks-notebook/new-adf-pipeline.png)

1.  V dolní části vlastností okna aktivity **poznámkového bloku** **datacihly** proveďte následující kroky:

    a. Přepněte na kartu **Azure Databricks**.

    b. Vyberte službu **AzureDatabricks\_LinkedService** (tu jste vytvořili v předchozím kroku).

    c. Přepněte na kartu **Nastavení** .

    c. Přejděte na **cestu k poznámkovému bloku** Databricks. Teď vytvoříme poznámkový blok a zadáme cestu. Cestu k poznámkovému bloku získáte pomocí následujících několika kroků.

       1. Spusťte svůj pracovní prostor Azure Databricks.

       1. Vytvořte v pracovním prostoru **novou složku** a pojmenujte ji **adftutorial**.

          ![Vytvoření nové složky](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       1. [Vytvořte nový Poznámkový blok](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python), zavolejte ho **MyNotebook** ve složce **adftutorial** , klikněte na **vytvořit.**

          ![Vytvoření nového poznámkového bloku](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Nastavení vlastností nového poznámkového bloku](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       1. Do nově vytvořeného poznámkového bloku mynotebook přidejte následující kód:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           y = dbutils.widgets.get("input")
           print ("Param -\'input':")
           print (y)
           ```

           ![Vytvoření widgetů pro parametry](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       1. **Cesta k poznámkovému bloku** je v tomto případě **/adftutorial/mynotebook**.

1.  Přepněte zpět do **nástroje pro vytváření v uživatelském rozhraní Data Factory**. Přejděte na kartu **Nastavení** pro **aktivitu Notebook1**.

    a.  Do aktivity poznámkového bloku **přidejte parametr**. Použijte stejný parametr, který jste dříve přidali do **kanálu**.

       ![Přidání parametru](media/transform-data-using-databricks-notebook/new-adf-parameters.png)

    b.  Pojmenujte parametr jako **vstup** a zadejte hodnotu jako **\@ kanál výrazu (). Parameters. Name**.

1.  Pokud chcete kanál ověřit, vyberte tlačítko **Ověřit** na panelu nástrojů. Chcete-li zavřít okno ověřování, vyberte **\>\>** tlačítko (šipka doprava).

    ![Ověření kanálu](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

1.  Vyberte **Publikovat vše**. Uživatelské rozhraní služby Data Factory publikuje entity (propojené služby a kanál) do služby Azure Data Factory.

    ![Publikování nových entit datové továrny](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Aktivace spuštění kanálu

Vyberte na panelu nástrojů **Aktivační událost** a potom vyberte **Aktivovat**.

![Výběr příkazu Aktivovat](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

Dialogové okno **Spuštění kanálu** vyzve k zadání parametru **name** (název). Jako parametr zde použijte **/path/filename**. Klikněte na tlačítko **Dokončit.**

![Zadání hodnoty pro parametr name (název)](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1.  Přepněte na kartu **monitorování** . Potvrďte, že se zobrazí spuštění kanálu. Vytvoření clusteru úloh Databricks, ve kterém se poznámkový blok spustí, trvá přibližně 5 až 8 minut.

    ![Monitorování kanálu](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

1.  Pravidelně klikejte na **Aktualizovat** a kontrolujte stav spuštění kanálu.

1.  Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte možnost **Zobrazit spuštění aktivit** ve sloupci **Akce**.

    ![Zobrazení spuštění aktivit](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

Zpátky na zobrazení spuštění kanálu můžete přepnout výběrem odkazu **Kanály** v horní části.

## <a name="verify-the-output"></a>Ověření výstupu

Můžete se přihlásit k **pracovnímu prostoru Azure Databricks** a přejít do části **Clustery**, kde se jako stav **úlohy** zobrazí *Čeká na provedení, Spuštěno nebo Ukončeno*.

![Zobrazení clusteru úloh a úlohy](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

Kliknutím na **název úlohy** můžete přejít k dalším podrobnostem. Po úspěšném spuštění můžete ověřit předané parametry a výstup poznámkového bloku Python.

![Zobrazení podrobností o spuštění a výstupu](media/transform-data-using-databricks-notebook/databricks-output.png)

## <a name="next-steps"></a>Další kroky

Kanál v této ukázce aktivuje aktivitu poznámkového bloku Databricks a předává do ní parametr. Naučili jste se:

  - Vytvoření datové továrny

  - Vytvoření kanálu využívajícího aktivitu poznámkového bloku Databricks

  - Aktivace spuštění kanálu

  - Monitorování spuštění kanálu