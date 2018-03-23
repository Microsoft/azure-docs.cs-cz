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
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/12/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: d1dcec26529c747a209dd10fcefbbadaa40365a3
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Spuštění poznámkového bloku Databricks s využitím aktivity poznámkového bloku ve službě Azure Data Factory

V tomto kurzu pomocí webu Azure Portal vytvoříte kanál Azure Data Factory, který spustí poznámkový blok Databricks pro cluster úloh Databricks. Kanál do poznámkového bloku Databricks během provádění také předá parametry Azure Data Factory.

V tomto kurzu provedete následující kroky:

  - Vytvoření datové továrny

  - Vytvoření kanálu využívajícího aktivitu poznámkového bloku Databricks

  - Aktivace spuštění kanálu

  - Monitorování spuštění kanálu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

  - **Pracovní prostor Azure Databricks**. [Vytvořte pracovní prostor Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) nebo použijte existující. V pracovním prostoru Azure Databricks vytvoříte poznámkový blok Python. Pak poznámkový blok spustíte a pomocí služby Azure Data Factory do něj předáte parametry.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1.  Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.

2.  Vyberte **Nový** v nabídce vlevo, vyberte **Data a analýzy** a pak vyberte **Datová továrna**.

    ![Vytvoření nové datové továrny](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image1.png)

3.  V podokně **Nová datová továrna** zadejte do pole **Název** text **ADFTutorialDataFactory**.

    Název objektu pro vytváření dat Azure musí být *globálně jedinečný*. Pokud se zobrazí následující chyba, změňte název datové továrny. (Použijte třeba název **\<vaše_jméno\>ADFTutorialDataFactory**). Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](https://docs.microsoft.com/en-us/azure/data-factory/naming-rules).

    ![Zadání názvu nové datové továrny](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image2.png)

4.  Jako **Předplatné** vyberte své předplatné Azure, ve kterém chcete datovou továrnu vytvořit.

5.  U položky **Skupina prostředků** proveďte jeden z následujících kroků:
    
    - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.
    
    - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.

    Některé kroky v tomto rychlém startu vychází z předpokladu, že pro skupinu prostředků použijete název **ADFTutorialResourceGroup**. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).

1.  Jako **Verze** vyberte **V2 (Preview)**.

2.  Jako **Umístění** vyberte umístění datové továrny.

    Data Factory V2 v současné době umožňuje vytváření datových továren jenom v oblastech Východní USA, Východní USA 2 a Západní Evropa. Úložiště dat (třeba Azure Storage a Azure SQL Database) a výpočetní prostředí (jako HDInsight) používaná službou Data Factory můžou být v jiných oblastech.

3.  Zaškrtněte **Připnout na řídicí panel**.

4.  Vyberte **Vytvořit**.

5.  Na řídicím panelu se zobrazí následující dlaždice se stavem **Nasazování datové továrny**:

    ![](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image3.png)

6.  Po vytvoření se zobrazí stránka **Datová továrna**. Kliknutím na dlaždici **Vytvořit a monitorovat** spusťte na samostatné kartě aplikaci uživatelského rozhraní služby Data Factory.

    ![Spuštění aplikace uživatelského rozhraní datové továrny](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Vytvoření propojených služeb

V této části vytvoříte propojenou službu Databricks. Tato propojená služba obsahuje informace o připojení ke clusteru Databricks:

### <a name="create-an-azure-databricks-linked-service"></a>Vytvoření propojené služby Azure Databricks

1.  Na stránce **Začínáme** přepněte na levém panelu na kartu **Upravit**.

    ![Úprava nové propojené služby](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image5.png)

2.  Ve spodní části okna vyberte možnost **Připojení** a potom možnost **+ Nové**.
    
    ![Vytvoření nového připojení](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

3.  V okně **Nová propojená služba** vyberte **Úložiště dat**\>**Azure Databricks** a pak vyberte **Pokračovat**.
    
    ![Zadání propojené služby Databricks](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

4.  V okně **Nová propojená služba** proveďte následující kroky:
    
    1.  Jako **Název** zadejte ***AzureDatabricks\_LinkedService***.
    
    2.  Jako **Cluster** vyberte **Nový cluster**.
    
    3.  Jako **Doména / Oblast** vyberte oblast, ve které se nachází váš pracovní prostor Azure Databricks.
    
    4.  Jako **Typ uzlu clusteru** vyberte pro účely tohoto kurzu **Standard\_D3\_v2**.
    
    5.  **Přístupový token** vygenerujte z pracovního prostoru Azure Databricks. Postup najdete [tady](https://docs.databricks.com/api/latest/authentication.html#generate-token).
    
    6.  Jako **verzi clusteru** vyberte **4.0 Beta** (nejnovější verze).
    
    7.  Jako **Počet pracovních uzlů** zadejte **2**.
    
    8.  Vyberte **Dokončit**.

        ![Dokončení vytváření propojené služby](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image8.png)

## <a name="create-a-pipeline"></a>Vytvoření kanálu

1.  Vyberte tlačítko **+** (plus) a potom v nabídce vyberte **Kanál**.

    ![Tlačítka pro vytvoření nového kanálu](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

2.  Vytvořte **parametr**, který se použije v **kanálu**. Později tento parametr předáte do aktivity poznámkového bloku Databricks. V prázdném kanálu klikněte na kartu **Parametry**, pak klikněte na **Nový** a pojmenujte parametr **name** (název).

    ![Vytvoření nového parametru](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![Vytvoření parametru name (název)](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

3.  Na panelu nástrojů **Aktivity** rozbalte **Databricks**. Přetáhněte aktivitu **Poznámkový blok** z panelu nástrojů **Aktivity** na plochu návrháře kanálu.

    ![Přetažení poznámkového bloku na plochu návrháře](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image12.png)

4.  Ve vlastnostech v dolní části okna aktivity **Poznámkový blok** **Databricks** proveďte následující kroky:

    a. Přepněte na kartu **Nastavení**.

    b. Vyberte službu **myAzureDatabricks\_LinkedService** (tu jste vytvořili v předchozím kroku).

    c. Vyberte **cestu k poznámkovému bloku** Databricks. Teď vytvoříme poznámkový blok a zadáme cestu. Cestu k poznámkovému bloku získáte pomocí následujících několika kroků.

       1. Spusťte svůj pracovní prostor Azure Databricks.

       2. Vytvořte v pracovním prostoru **novou složku** a pojmenujte ji **adftutorial**.

          ![Vytvoření nové složky](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       3. [Vytvořte nový poznámkový blok](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python) **mynotebook** ve složce **adftutorial** a klikněte na **Vytvořit**.

          ![Vytvoření nového poznámkového bloku](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Nastavení vlastností nového poznámkového bloku](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       4. Do nově vytvořeného poznámkového bloku mynotebook přidejte následující kód:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           dbutils.widgets.get("input")
           y = getArgument("input")
           print "Param -\'input':"
           print y
           ```

           ![Vytvoření widgetů pro parametry](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       5. **Cesta k poznámkovému bloku** je v tomto případě **/adftutorial/mynotebook**.

5.  Přepněte zpět do **nástroje pro vytváření v uživatelském rozhraní Data Factory**. Přejděte na kartu **Nastavení** pro **aktivitu Notebook1**. 
    
    a.  Do aktivity poznámkového bloku **přidejte parametr**. Použijte stejný parametr, který jste dříve přidali do **kanálu**.

       ![Přidání parametru](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image17.png)

    b.  Pojmenujte parametr **input** (vstup) a jako hodnotu zadejte výraz **@pipeline().parameters.name**.

6.  Pokud chcete kanál ověřit, vyberte tlačítko **Ověřit** na panelu nástrojů. Okno ověřování zavřete výběrem tlačítka **\>\>** (šipka doprava).

    ![Ověření kanálu](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

7.  Vyberte **Publikovat vše**. Uživatelské rozhraní služby Data Factory publikuje entity (propojené služby a kanál) do služby Azure Data Factory.

    ![Publikování nových entit datové továrny](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Aktivace spuštění kanálu

Vyberte na panelu nástrojů **Aktivační událost** a potom vyberte **Aktivovat**.

![Výběr příkazu Aktivovat](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

Dialogové okno **Spuštění kanálu** vyzve k zadání parametru **name** (název). Jako parametr zde použijte **/path/filename**. Klikněte na **Dokončit**.

![Zadání hodnoty pro parametr name (název)](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1.  Přepněte na kartu **Monitorování**. Ověřte, že se zobrazuje spuštění kanálu. Vytvoření clusteru úloh Databricks, ve kterém se poznámkový blok spustí, trvá přibližně 5 až 8 minut.

    ![Monitorování kanálu](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

2.  Pravidelně klikejte na **Aktualizovat** a kontrolujte stav spuštění kanálu.

3.  Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte možnost **Zobrazit spuštění aktivit** ve sloupci **Akce**.

    ![Zobrazení spuštění aktivit](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

Zpátky na zobrazení spuštění kanálu můžete přepnout výběrem odkazu **Kanály** v horní části.

## <a name="verify-the-output"></a>Ověření výstupu

Můžete se přihlásit k **pracovnímu prostoru Azure Databricks** a přejít do části **Clustery**, kde se jako stav **úlohy** zobrazí *Čeká na provedení, Spuštěno nebo Ukončeno*.

![Zobrazení clusteru úloh a úlohy](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

Kliknutím na **název úlohy** můžete přejít k dalším podrobnostem. Po úspěšném spuštění můžete ověřit předané parametry a výstup poznámkového bloku Python.

![Zobrazení podrobností o spuštění a výstupu](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image25.png)

## <a name="next-steps"></a>Další kroky

Kanál v této ukázce aktivuje aktivitu poznámkového bloku Databricks a předává do ní parametr. Naučili jste se tyto postupy:

  - Vytvoření datové továrny

  - Vytvoření kanálu využívajícího aktivitu poznámkového bloku Databricks

  - Aktivace spuštění kanálu

  - Monitorování spuštění kanálu
