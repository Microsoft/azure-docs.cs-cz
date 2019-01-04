---
title: Spuštění poznámkového bloku Databricks s využitím aktivity poznámkového bloku ve službě Azure Data Factory
description: Zjistěte, jak můžete pomocí aktivity poznámkového bloku Databricks v datové továrně Azure spustit poznámkový blok Databricks pro cluster úloh Databricks.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/12/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 7035035823e00fb0c12de3f4eeae11d8b3e1d54d
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016909"
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Spuštění poznámkového bloku Databricks s využitím aktivity poznámkového bloku Databricks ve službě Azure Data Factory

V tomto kurzu pomocí webu Azure Portal vytvoříte kanál Azure Data Factory, který spustí poznámkový blok Databricks pro cluster úloh Databricks. Kanál do poznámkového bloku Databricks během provádění také předá parametry Azure Data Factory.

V tomto kurzu provedete následující kroky:

  - Vytvoření datové továrny

  - Vytvoření kanálu využívajícího aktivitu poznámkového bloku Databricks

  - Aktivace spuštění kanálu

  - Monitorování spuštění kanálu

Pokud ještě nemáte předplatné Azure, vytvořte si  [bezplatný účet](https://azure.microsoft.com/free/)  před tím, než začnete.

Jedenáctiminutové představení a ukázku této funkce najdete v tomto videu:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/ingest-prepare-and-transform-using-azure-databricks-and-data-factory/player]

## <a name="prerequisites"></a>Požadavky

  - **Pracovní prostor Azure Databricks**. [Vytvořte pracovní prostor Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) nebo použijte existující. V pracovním prostoru Azure Databricks vytvoříte poznámkový blok Python. Pak poznámkový blok spustíte a pomocí služby Azure Data Factory do něj předáte parametry.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1.  Spuštění **Microsoft Edge** nebo **Google Chrome** webového prohlížeče. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.

1.  Vyberte **vytvořit prostředek** v nabídce vlevo vyberte **Analytics**a pak vyberte **služby Data Factory**.

    ![Vytvoření nové datové továrny](media/transform-data-using-databricks-notebook/new-azure-data-factory-menu.png)

1.  V **nová datová továrna** podokně zadejte **ADFTutorialDataFactory** pod **název**.

    Název objektu pro vytváření dat Azure musí být *globálně jedinečný*. Pokud se zobrazí následující chyba, změňte název datové továrny. (Například použít **\<vaše_jméno\>ADFTutorialDataFactory**). Pravidla pojmenování artefaktů služby Data Factory, najdete v článku [služby Data Factory – pravidla pojmenování](https://docs.microsoft.com/azure/data-factory/naming-rules) článku.

    ![Zadání názvu nové datové továrny](media/transform-data-using-databricks-notebook/new-azure-data-factory.png)

1.  Pro **předplatné**, vyberte své předplatné Azure, ve kterém chcete datovou továrnu vytvořit.

1.  Pro **skupiny prostředků**, proveďte jednu z následujících kroků:
    
    - Vyberte **použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.
    
    - Vyberte **vytvořit nový** a zadejte název skupiny prostředků.

    Některé kroky v tomto rychlém startu se předpokládá, že použijete název **ADFTutorialResourceGroup** pro skupinu prostředků. Další informace o skupinách prostředků najdete v tématu [použití skupin prostředků ke správě prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

1.  Pro **verze**vyberte **V2**.

1.  Pro **umístění**, vyberte umístění datové továrny.

    Seznam oblastí Azure, ve kterých je momentálně dostupná Data Factory, vyberte oblasti, které vás zajímají na následující stránce a potom rozbalte **Analytics** najít **služby Data Factory**: [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat (třeba Azure Storage a Azure SQL Database) a výpočetní prostředí (jako HDInsight) používaná službou Data Factory můžou být v jiných oblastech.
1.  Vyberte **vytvořit**.


1.  Po vytvoření se zobrazí, se zobrazí **služby Data factory** stránky. Vyberte **vytvořit a monitorovat** dlaždice, spusťte na samostatné kartě aplikaci uživatelského rozhraní služby Data Factory.

    ![Spuštění aplikace uživatelského rozhraní datové továrny](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Vytvoření propojených služeb

V této části vytvoříte propojenou službu Databricks. Tato propojená služba obsahuje informace o připojení ke clusteru Databricks:

### <a name="create-an-azure-databricks-linked-service"></a>Vytvoření propojené služby Azure Databricks

1.  Na **pusťme se do práce** stránce, přejděte **upravit** kartu na panelu vlevo.

    ![Úprava nové propojené služby](media/transform-data-using-databricks-notebook/get-started-page.png)

1.  Vyberte **připojení** v dolní části okna a pak vyberte **+ nová**.
    
    ![Vytvoření nového připojení](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

1.  V **Nová propojená služba** okně **Compute** \> **Azure Databricks**a pak vyberte ** Pokračovat**.
    
    ![Zadání propojené služby Databricks](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

1.  V **Nová propojená služba** okno, proveďte následující kroky:
    
    1.  Do pole **Název** zadejte ***AzureDatabricks\_LinkedService***.
    
    1.  Vyberte příslušný **Pracovní prostor Databricks**, ve kterém poznámkový blok spustíte.

    1.  V poli **Vyberte cluster** zvolte možnost **Nový cluster úloh**.
    
    1.  V části **Doména/oblast** by se měly údaje vyplnit automaticky.

    1.  **Přístupový token** vygenerujte z pracovního prostoru Azure Databricks. Postup najdete [tady](https://docs.databricks.com/api/latest/authentication.html#generate-token).

    1.  V poli **Verze clusteru** vyberte **4.0** (s Apache Spark 2.3.0, Scala 2.11).

    1.  V části **Typ uzlu clusteru** vyberte **Standardní\_D3\_v2** pod kategorií **Pro obecné účely (HDD)** pro tento kurz. 
    
    1.  V části **Pracovní procesy** zadejte hodnotu **2**.
    
    1.  Vyberte **Dokončit**.

        ![Dokončení vytváření propojené služby](media/transform-data-using-databricks-notebook/new-databricks-linkedservice.png)

## <a name="create-a-pipeline"></a>Vytvoření kanálu

1.  Vyberte **+** (plus) tlačítko a pak vyberte **kanálu** v nabídce.

    ![Tlačítka pro vytvoření nového kanálu](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

1.  Vytvořte **parametr**, který se použije v **kanálu**. Později tento parametr předáte do aktivity poznámkového bloku Databricks. V prázdném kanálu klikněte na kartu **Parametry**, pak klikněte na **Nový** a pojmenujte parametr **name** (název).

    ![Vytvoření nového parametru](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![Vytvoření parametru name (název)](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

1.  V **aktivity** sady nástrojů, rozbalte **Databricks**. Přetáhněte **Poznámkový blok** aktivita z **aktivity** nástrojů na plochu návrháře kanálu.

    ![Přetažení poznámkového bloku na plochu návrháře](media/transform-data-using-databricks-notebook/new-adf-pipeline.png)

1.  Ve vlastnostech **Databricks** **Poznámkový blok** v dolní části okna aktivity proveďte následující kroky:

    a. Přepněte **Azure Databricks** kartu.

    b. Vyberte **Azure Databricks\_LinkedService** (který jste vytvořili v předchozím postupu).

    c. Přepněte na kartu **Nastavení**.

    c. Přejděte na **cestu k poznámkovému bloku** Databricks. Teď vytvoříme poznámkový blok a zadáme cestu. Cestu k poznámkovému bloku získáte pomocí následujících několika kroků.

       1. Spusťte svůj pracovní prostor Azure Databricks.

       1. Vytvořte v pracovním prostoru **novou složku** a pojmenujte ji **adftutorial**.

          ![Vytvoření nové složky](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       1. [Vytvořte nový poznámkový blok](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python) **mynotebook** ve složce **adftutorial** a **,** klikněte na **Vytvořit.**

          ![Vytvoření nového poznámkového bloku](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Nastavení vlastností nového poznámkového bloku](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       1. Do nově vytvořeného poznámkového bloku mynotebook přidejte následující kód:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           dbutils.widgets.get("input")
           y = getArgument("input")
           print "Param -\'input':"
           print y
           ```

           ![Vytvoření widgetů pro parametry](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       1. **Cesta k poznámkovému bloku** je v tomto případě **/adftutorial/mynotebook**.

1.  Přepněte zpět do **nástroje pro vytváření v uživatelském rozhraní Data Factory**. Přejděte na kartu **Nastavení** pro **aktivitu Notebook1**. 
    
    a.  Do aktivity poznámkového bloku **přidejte parametr**. Použijte stejný parametr, který jste dříve přidali do **kanálu**.

       ![Přidání parametru](media/transform-data-using-databricks-notebook/new-adf-parameters.png)

    b.  Pojmenujte parametr **input** (vstup) a jako hodnotu zadejte výraz **@pipeline().parameters.name**.

1.  Pokud chcete kanál ověřit, vyberte **ověřit** tlačítko na panelu nástrojů. Zavřete okno ověřování, vyberte **\>\>** tlačítko (šipka doprava).

    ![Ověření kanálu](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

1.  Vyberte **Publikovat vše**. Uživatelské rozhraní služby Data Factory publikuje entity (propojené služby a kanál) do služby Azure Data Factory.

    ![Publikování nových entit datové továrny](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Aktivace spuštění kanálu

Vyberte **aktivační událost** na panelu nástrojů a pak vyberte **aktivovat**.

![Výběr příkazu Aktivovat](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

Dialogové okno **Spuštění kanálu** vyzve k zadání parametru **name** (název). Jako parametr zde použijte **/path/filename**. Klikněte na **Dokončit**.

![Zadání hodnoty pro parametr name (název)](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1.  Přepněte **monitorování** kartu. Ověřte, že se zobrazuje spuštění kanálu. Vytvoření clusteru úloh Databricks, ve kterém se poznámkový blok spustí, trvá přibližně 5 až 8 minut.

    ![Monitorování kanálu](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

1.  Vyberte **aktualizovat** pravidelně, chcete-li zkontrolovat stav spuštění kanálu.

1.  Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte **zobrazit spuštění aktivit** v **akce** sloupce.

    ![Zobrazení spuštění aktivit](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

Zpět na zobrazení spuštění kanálu můžete přepnout výběrem **kanály** odkazu v horní části.

## <a name="verify-the-output"></a>Ověření výstupu

Můžete se přihlásit k **pracovnímu prostoru Azure Databricks** a přejít do části **Clustery**, kde se jako stav **úlohy** zobrazí *Čeká na provedení, Spuštěno nebo Ukončeno*.

![Zobrazení clusteru úloh a úlohy](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

Kliknutím na **název úlohy** můžete přejít k dalším podrobnostem. Po úspěšném spuštění můžete ověřit předané parametry a výstup poznámkového bloku Python.

![Zobrazení podrobností o spuštění a výstupu](media/transform-data-using-databricks-notebook/databricks-output.png)

## <a name="next-steps"></a>Další postup

Kanál v této ukázce aktivuje aktivitu poznámkového bloku Databricks a předává do ní parametr. Naučili jste se tyto postupy:

  - Vytvoření datové továrny

  - Vytvoření kanálu využívajícího aktivitu poznámkového bloku Databricks

  - Aktivace spuštění kanálu

  - Monitorování spuštění kanálu
