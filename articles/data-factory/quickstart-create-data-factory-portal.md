---
title: Vytvoření datové továrny Azure pomocí uživatelského rozhraní služby Azure Data Factory | Dokumentace Microsoftu
description: Vytvořte datovou továrnu s kanálem, který kopíruje data z jednoho umístění v úložišti objektů blob v Azure do jiného.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 02/01/2018
ms.author: jingwang
ms.openlocfilehash: 79b19121b25b03181eeda1bedd800f45a2adf57e
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Vytvoření datové továrny pomocí uživatelského rozhraní služby Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [Verze 1 – GA](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Verze 2 – Preview](quickstart-create-data-factory-portal.md)

Tento rychlý start popisuje vytvoření a monitorování datové továrny pomocí uživatelského rozhraní služby Azure Data Factory. Kanál, který vytvoříte v této datové továrně, *kopíruje* data z jedné složky do jiné složky v úložišti objektů blob v Azure. Kurz předvádějící způsoby *transformace* dat pomocí služby Azure Data Factory najdete v tématu [Kurz: Transformace dat pomocí Sparku](tutorial-transform-data-spark-portal.md). 


> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](data-factory-introduction.md), než s tímto rychlým startem začnete. 
>
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby, která je všeobecně dostupná (GA), prostudujte si [Kurz pro službu Data Factory verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Video 
Toto video vám pomůže seznámit se s uživatelským rozhraním služby Data Factory: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
2. Přejděte na [portál Azure](https://portal.azure.com). 
3. Vyberte **Nový** v nabídce vlevo, vyberte **Data a analýzy** a pak vyberte **Datová továrna**. 
   
   ![Výběr datové továrny v podokně Nový](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. Na stránce **Nová datová továrna** jako **Název** zadejte **ADFTutorialDataFactory**. 
      
   ![Stránka Nová datová továrna](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   Název objektu pro vytváření dat Azure musí být *globálně jedinečný*. Pokud se zobrazí následující chyba, změňte název datové továrny (třeba na **&lt;váš_název_&gt;ADFTutorialDataFactory**) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
   ![Chyba: název není k dispozici](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. Jako **Předplatné** vyberte své předplatné Azure, ve kterém chcete datovou továrnu vytvořit. 
4. Pro položku **Skupina prostředků** proveďte jeden z následujících kroků:
     
   - Vyberte **Použít existující** a ze seznamu vyberte existující skupinu prostředků. 
   - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
   Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
4. Jako **Verze** vyberte **V2 (Preview)**.
5. Jako **Umístění** vyberte umístění datové továrny.

   V seznamu se zobrazí pouze umístěná, která služba Data Factory podporuje. Úložiště dat (např. Azure Storage nebo Azure SQL Database) a výpočetní prostředí (např. Azure HDInsight) používaná službou Data Factory můžou být v jiných umístěních.
6. Zaškrtněte **Připnout na řídicí panel**.     
7. Vyberte **Vytvořit**.
8. Na řídicím panelu se zobrazí následující dlaždice se stavem **Nasazování datové továrny**: 

   ![Dlaždice Nasazování datové továrny](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
9. Po vytvoření se zobrazí stránka **Datová továrna**. Kliknutím na dlaždici **Vytvořit a monitorovat** spusťte na samostatné kartě aplikaci uživatelského rozhraní služby Azure Data Factory.
   
   ![Domovská stránka datové továrny s dlaždici Vytvořit a monitorovat](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)
10. Na stránce **Začínáme** přepněte na levém panelu na kartu **Upravit**. 

    ![Stránka Začínáme](./media/quickstart-create-data-factory-portal/get-started-page.png)

## <a name="create-a-linked-service"></a>Vytvoření propojené služby
V tomto postupu vytvoříte propojenou službu, která propojí váš účet úložiště Azure s datovou továrnou. Tato propojená služba má informace o připojení, které služba Data Factory používá pro připojení za běhu.

1. Vyberte **Připojení** a pak vyberte tlačítko **Nové** na řídicím panelu. 

   ![Tlačítka pro vytvoření nového připojení](./media/quickstart-create-data-factory-portal/new-connection-button.png)    
2. Na stránce **Nová propojená služba** vyberte **Azure Blob Storage** a pak vyberte **Pokračovat**. 

   ![Výběr dlaždice Azure Blob Storage](./media/quickstart-create-data-factory-portal/select-azure-storage.png)
3. Proveďte následující kroky: 

   a. Jako **Název** zadejte **AzureStorageLinkedService**.

   b. Jako **Název účtu úložiště** vyberte název svého účtu úložiště Azure.

   c. Vyberte **Test připojení** a potvrďte, že se služba Data Factory dokáže připojit k účtu úložiště. 

   d. Vyberte **Uložit** a uložte propojenou službu. 

   ![Nastavení propojené služby Azure Storage](./media/quickstart-create-data-factory-portal/azure-storage-linked-service.png) 
4. Ověřte, že se služba **AzureStorageLinkedService** zobrazí v seznamu propojených služeb. 

   ![Propojená služba Azure Storage v seznamu](./media/quickstart-create-data-factory-portal/azure-storage-linked-service-in-list.png)

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto postupu vytvoříte dvě datové sady:**InputDataset** a **OutputDataset**. Tyto datové sady jsou typu **AzureBlob**. Odkazují na propojenou službu Azure Storage, kterou jste vytvořili v předchozí části. 

Vstupní datová sada představuje zdrojová data ve vstupní složce. V definici vstupní datové sady určíte kontejner objektů blob (**adftutorial**), složku (**input**) a soubor (**emp.txt**) obsahující zdrojová data. 

Výstupní datová sada představuje data kopírovaná do cíle. V definici výstupní datové sady určíte kontejner objektů blob (**adftutorial**), složku (**output**) a soubor, do kterého se data kopírují. Ke každému spuštění kanálu je přiřazené jedinečné ID. Přístup k tomuto ID můžete získat pomocí systémové proměnné **RunId**. Název výstupního souboru se dynamicky vyhodnocuje na základě ID spuštění kanálu.   

V nastavení propojené služby jste zadali účet úložiště Azure obsahující zdrojová data. V nastavení zdrojové datové sady určíte, kde přesně se data nacházejí (kontejner objektů blob, složka a soubor). V nastavení datové sady jímky určíte, kam se data kopírují (kontejner objektů blob, složka a soubor). 
 
1. Vyberte tlačítko **+** (plus) a pak vyberte **Datová sada**.

   ![Nabídka pro vytvoření datové sady](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
2. Na stránce **Nová datová sada** vyberte **Azure Blob Storage** a pak vyberte **Dokončit**. 

   ![Výběr možnosti Azure Blob Storage](./media/quickstart-create-data-factory-portal/select-azure-blob-storage.png)
3. V okně **Vlastnosti** datové sady jako **Název** zadejte **InputDataset**. 

   ![Obecná nastavení datové sady](./media/quickstart-create-data-factory-portal/dataset-general-page.png)
4. Přepněte na kartu **Připojení** a proveďte následující kroky: 

   a. Jako **Propojená služba** vyberte **AzureStorageLinkedService**.

   b. V části **Cesta k souboru** vyberte tlačítko **Procházet**.

      ![Karta Připojení a tlačítko Procházet](./media/quickstart-create-data-factory-portal/file-path-browse-button.png) V okně **Zvolte soubor nebo složku** přejděte do složky **input** v kontejneru **adftutorial**, vyberte soubor **emp.txt** a pak vyberte **Dokončit**.

      ![Vyhledání vstupního souboru](./media/quickstart-create-data-factory-portal/choose-file-folder.png)
    
   d. (volitelné) Výměrem možnosti **Náhled dat** zobrazíte náhled dat v souboru emp.txt.     
5. Pomocí stejného postupu vytvořte výstupní datovou sadu:  

   a. Vyberte tlačítko **+** (plus) a pak vyberte **Datová sada**.

   b. Na stránce **Nová datová sada** vyberte **Azure Blob Storage** a pak vyberte **Dokončit**.

   c. Jako název zadejte **OutputDataset**.

   d. Jako složku zadejte **adftutorial/output**. Pokud složka **output** neexistuje, aktivita kopírování ji vytvoří za běhu.

   e. Jako název souboru zadejte `@CONCAT(pipeline().RunId, '.txt')`. 
   
      Při každém spuštění kanálu se ke spuštění kanálu přidruží jedinečné ID. Výraz vytvoří název výstupního souboru zřetězením ID spuštění kanálu a přípony **.txt**. Seznam podporovaných systémových proměnných a výrazů najdete v tématech [Systémové proměnné](control-flow-system-variables.md) a [Jazyk výrazů](control-flow-expression-language-functions.md).

   ![Nastavení výstupní datové sady](./media/quickstart-create-data-factory-portal/output-dataset-settings.png)

## <a name="create-a-pipeline"></a>Vytvoření kanálu 
V tomto postupu vytvoříte a ověříte kanál s aktivitou kopírování, která používá vstupní a výstupní datovou sadu. Aktivita kopírování kopíruje data ze souboru zadaného v nastavení vstupní datové sady do souboru zadaného v nastavení výstupní datové sady. Pokud vstupní datová sada určuje pouze složku (bez názvu souboru), aktivita kopírování zkopíruje do cíle všechny soubory ve zdrojové složce. 

1. Vyberte tlačítko **+** (plus) a pak vyberte **Kanál**. 

   ![Nabídka pro vytvoření nového kanálu](./media/quickstart-create-data-factory-portal/new-pipeline-menu.png)
2. V okně **Vlastnosti** jako **Název** zadejte **CopyPipeline**. 

   ![Obecná nastavení kanálu](./media/quickstart-create-data-factory-portal/pipeline-general-settings.png)
3. Na panelu nástrojů **Aktivity** rozbalte **Tok dat**. Přetáhněte aktivitu **Kopírování** z panelu nástrojů **Aktivity** na plochu návrháře kanálu. Na panelu nástrojů **Aktivity** můžete aktivity také vyhledávat. Jako **Název** zadejte **CopyFromBlobToBlob**.

   ![Obecná nastavení aktivity kopírování](./media/quickstart-create-data-factory-portal/copy-activity-general-settings.png)
4. V nastavení aktivity kopírování přepněte na kartu **Zdroj** a jako **Zdrojová datová sada** vyberte **InputDataset**.

   ![Nastavení zdroje aktivity kopírování](./media/quickstart-create-data-factory-portal/copy-activity-source-settings.png)    
5. V nastavení aktivity kopírování přepněte na kartu **Jímka** a jako **Datová sada jímky** vyberte **OutputDataset**.

   ![Nastavení jímky aktivity kopírování](./media/quickstart-create-data-factory-portal/copy-activity-sink-settings.png)    
7. Vyberte **Ověřit** a ověřte nastavení kanálu. Ověřte úspěšné ověření kanálu. Pokud chcete zavřít výstup ověřování, vyberte tlačítko **>>** (šipky doprava). 

   ![Ověření kanálu](./media/quickstart-create-data-factory-portal/pipeline-validate-button.png)

## <a name="test-run-the-pipeline"></a>Testovací spuštění kanálu
V tomto kroku provedete testovací spuštění kanálu před tím, než ho nasadíte do služby Data Factory. 

1. Na panelu nástrojů pro kanál vyberte **Testovací běh**. 
    
   ![Testovací běhy kanálu](./media/quickstart-create-data-factory-portal/pipeline-test-run.png)
2. Ověřte, že se na kartě **Výstup** v nastavení kanálu zobrazí stav spuštění kanálu. 

   ![Výstup testovacího běhu](./media/quickstart-create-data-factory-portal/test-run-output.png)    
3. Ověřte, že složka **output** kontejneru **adftutorial** obsahuje výstupní soubor. Pokud výstupní složka neexistuje, služba Data Factory ji automaticky vytvoří. 
    
   ![Ověření výstupu](./media/quickstart-create-data-factory-portal/verify-output.png)


## <a name="trigger-the-pipeline-manually"></a>Ruční aktivace kanálu
V tomto postupu nasadíte entity (propojené služby, datové sady a kanály) do služby Azure Data Factory. Pak ručně aktivujete spuštění kanálu. Entity můžete publikovat také do vlastního úložiště Visual Studio Team Services Git, čemuž se věnuje [jiný kurz](tutorial-copy-data-portal.md?#configure-code-repository).

1. Před aktivací kanálu je nutné publikovat entity do služby Data Factory. Pokud je chcete publikovat, v levém podokně vyberte **Publikovat vše**. 

   ![Tlačítko Publikovat](./media/quickstart-create-data-factory-portal/publish-button.png)
2. Pokud chcete kanál aktivovat ručně, vyberte **Aktivační událost** na panelu nástrojů a pak vyberte **Aktivovat**. 
    
   ![Příkaz Aktivovat](./media/quickstart-create-data-factory-portal/pipeline-trigger-now.png)

## <a name="monitor-the-pipeline"></a>Monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. Pomocí tlačítka **Aktualizovat** seznam aktualizujte.

   ![Karta pro monitorování spuštění kanálu s tlačítkem Aktualizovat](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
2. Vyberte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Na této stránce se zobrazí stav spuštění aktivity kopírování. 

   ![Spuštění aktivity kanálu](./media/quickstart-create-data-factory-portal/pipeline-activity-runs.png)
3. Pokud chcete zobrazit podrobnosti o operaci kopírování, vyberte odkaz **Podrobnosti** (obrázek brýlí) ve sloupci **Akce**. Podrobnosti o vlastnostech najdete v tématu [Přehled aktivity kopírování](copy-activity-overview.md). 

   ![Podrobnosti o aktivitě kopírování](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
4. Ověřte, že složka **output** obsahuje nový soubor. 
5. Ze zobrazení **Spuštění aktivit** zpět na zobrazení **Spuštění kanálu** můžete přepnout výběrem odkazu **Kanály**. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Aktivace kanálu podle plánu
Tento postup je v tomto kurzu volitelný. Můžete vytvořit *aktivační událost plánovače* a naplánovat pravidelné spouštění kanálu (každou hodinu, každý den atd.). V tomto postupu vytvoříte aktivační událost, která se bude spouštět každou minutu až do koncového data a času, které zadáte. 

1. Přepněte na kartu **Upravit**. 

   ![Tlačítko Upravit](./media/quickstart-create-data-factory-portal/switch-edit-tab.png)
1. V nabídce vyberte **Aktivační událost** a pak vyberte **Nová / upravit**. 

   ![Nabídka pro novou aktivační událost](./media/quickstart-create-data-factory-portal/new-trigger-menu.png)
2. Na stránce **Přidat aktivační události** vyberte **Zvolit aktivační událost** a pak vyberte **Nová**. 

   ![Vybrané možnosti pro přidání nové aktivační události](./media/quickstart-create-data-factory-portal/add-trigger-new-button.png)
3. Na stránce **Nová aktivační událost** v části **Konec** vyberte **K datu**, zadejte koncový čas pár minut od aktuálního času a pak vyberte **Použít**. 

   Každé spuštění kanálu je zpoplatněno, proto zadejte koncový čas pouze pár minut po času zahájení. Ujistěte se, že se jedná o stejný den. Zajistěte však, aby měl kanál mezi časem publikování a koncovým časem dostatek času na spuštění. Aktivační událost nabývá účinnosti po publikování řešení do služby Data Factory, a ne při uložení aktivační události v uživatelském rozhraní. 

   ![Nastavení aktivační události](./media/quickstart-create-data-factory-portal/trigger-settings.png)
4. Na stránce **Nová aktivační událost** zaškrtněte políčko **Aktivováno** a pak vyberte **Další**. 

   ![Zaškrtávací políčko Aktivováno a tlačítko Další](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
5. Prostudujte si zprávu upozornění a vyberte **Dokončit**.

   ![Upozornění a tlačítko Dokončit](./media/quickstart-create-data-factory-portal/new-trigger-finish.png)
6. Vyberte **Publikovat vše** a publikujte změny do služby Data Factory. 

   ![Tlačítko Publikovat](./media/quickstart-create-data-factory-portal/publish-button.png)
8. Vlevo přepněte na kartu **Monitorování**. Seznam můžete aktualizovat výběrem možnosti **Aktualizovat**. Uvidíte, že se kanál spustí každou minutu od času publikování až do koncového času. 

   Všimněte si hodnot ve sloupci **Aktivoval(a)**. Ruční spuštění aktivační události proběhlo v kroku, který jste provedli dříve (**Aktivovat**). 

   ![Seznam aktivovaných spuštění](./media/quickstart-create-data-factory-portal/monitor-triggered-runs.png)
9. Výběrem šipky dolů vedle **Spuštění kanálu** přepněte na zobrazení **Spuštění aktivační události**. 

   ![Přepnutí na zobrazení Spuštění aktivační události](./media/quickstart-create-data-factory-portal/monitor-trigger-runs.png)    
10. Ověřte, že se ve složce **output** vytvořil výstupní soubor pro každé spuštění kanálu až do zadaného koncového data a času. 

## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob v Azure. Pokud se chcete naučit používat službu Data Factory ve více scénářích, projděte si příslušné [kurzy](tutorial-copy-data-portal.md). 