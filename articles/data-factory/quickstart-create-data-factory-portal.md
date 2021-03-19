---
title: Vytvoření datové továrny Azure pomocí Azure Data Factoryho uživatelského rozhraní
description: Vytvořte datovou továrnu s kanálem, který kopíruje data z jednoho umístění v úložišti objektů blob v Azure do jiného.
author: linda33wj
ms.service: data-factory
ms.topic: quickstart
ms.date: 12/14/2020
ms.author: jingwang
ms.openlocfilehash: 8b33005b7f0d08e6da963457813b012199d89f5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655029"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Rychlý start: Vytvoření datové továrny pomocí uživatelského rozhraní Azure Data Factory 

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuální verze](quickstart-create-data-factory-portal.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento rychlý start popisuje vytvoření a monitorování datové továrny pomocí uživatelského rozhraní služby Azure Data Factory. Kanál, který vytvoříte v této datové továrně, *kopíruje* data z jedné složky do jiné složky v úložišti objektů blob v Azure. Pokud chcete *transformovat* data pomocí Azure Data Factory, přečtěte si téma [mapování toku dat](concepts-data-flow-overview.md).

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md), než s tímto rychlým startem začnete. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Video 
Toto video vám pomůže seznámit se s uživatelským rozhraním služby Data Factory: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
1. Přejděte na [Azure Portal](https://portal.azure.com). 
1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**.
1. Vyberte **integrace** a pak vyberte **Data Factory**. 
   
   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="Data Factory výběru v novém podokně.":::

1. Na stránce **vytvořit data Factory** na kartě **základy** vyberte **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu.
1. U položky **Skupina prostředků** proveďte jeden z následujících kroků:

    a. Z rozevíracího seznamu vyberte existující skupinu prostředků.

    b. Vyberte **vytvořit novou** a zadejte název nové skupiny prostředků.
    
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md). 

1. V poli **oblast** vyberte umístění pro datovou továrnu.

   Seznam obsahuje jenom umístění podporovaná službou Data Factory, do kterých se budou ukládat vaše metadata Azure Data Factory. Přidružená úložiště dat (například Azure Storage a Azure SQL Database) a výpočetní prostředí (například Azure HDInsight), které Data Factory používá, mohou běžet v jiných oblastech.
 
1. Jako **název** zadejte **ADFTutorialDataFactory**.
   Název objektu pro vytváření dat Azure musí být *globálně jedinečný*. Pokud se zobrazí následující chyba, změňte název datové továrny (například na **&lt; &gt; ADFTutorialDataFactory**) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nová chybová zpráva objektu pro vytváření dat pro duplicitní název.":::

1. V poli **Verze** vyberte **V2**.

1. Vyberte **Další: Konfigurace Git** a pak zrušte zaškrtnutí políčka **Konfigurovat Git později** .

1. Vyberte možnost **zkontrolovat + vytvořit** a po ověření platnosti vyberte **vytvořit** . Po dokončení vytváření vyberte **Přejít k prostředku** a přejděte na stránku **Data Factory** . 

1. Výběrem dlaždice **autora & monitorování** spustíte aplikaci Azure Data Factory uživatelského rozhraní (UI) na samostatné kartě prohlížeče.
   
    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Domovská stránka Azure Data Factory s dlaždicí pro monitorování autora &.":::
   
   > [!NOTE]
   > Pokud vidíte, že webový prohlížeč je zablokovaný při autorizaci, zrušte zaškrtnutí políčka **Blokovat soubory cookie a data lokality třetích stran** . Nebo ho nechejte vybraný, vytvořte výjimku pro **Login.microsoftonline.com** a pak zkuste aplikaci znovu otevřít.
   

## <a name="create-a-linked-service"></a>Vytvoření propojené služby
V tomto postupu vytvoříte propojenou službu, která propojí váš Azure Storage účet s datovou továrnou. Tato propojená služba má informace o připojení, které služba Data Factory používá pro připojení za běhu.

1. Na stránce Azure Data Factory uživatelské rozhraní otevřete v levém podokně kartu [**Spravovat**](./author-management-hub.md) .

1. Na stránce propojené služby vyberte **+ Nová** a vytvořte novou propojenou službu.

   :::image type="content" source="./media/doc-common-process/new-linked-service.png" alt-text="Nová propojená služba.":::
   
1. Na stránce **Nová propojená služba** vyberte **Azure Blob Storage** a pak vyberte **Pokračovat**. 

1. Na stránce Nová propojená služba (Azure Blob Storage) proveďte následující kroky: 

   a. Jako **Název** zadejte **AzureStorageLinkedService**.

   b. V poli **název účtu úložiště** vyberte název účtu Azure Storage.

   c. Vyberte **Test připojení** a potvrďte, že se služba Data Factory dokáže připojit k účtu úložiště. 

   d. Vyberte **vytvořit** a uložte propojenou službu. 

      :::image type="content" source="./media/quickstart-create-data-factory-portal/linked-service.png" alt-text="Propojená služba.":::


## <a name="create-datasets"></a>Vytvoření datových sad
V tomto postupu vytvoříte dvě datové sady:**InputDataset** a **OutputDataset**. Tyto datové sady jsou typu **AzureBlob**. Odkazují na propojenou službu Azure Storage, kterou jste vytvořili v předchozí části. 

Vstupní datová sada představuje zdrojová data ve vstupní složce. V definici vstupní datové sady určíte kontejner objektů blob (**adftutorial**), složku (**input**) a soubor (**emp.txt**) obsahující zdrojová data. 

Výstupní datová sada představuje data kopírovaná do cíle. V definici výstupní datové sady určíte kontejner objektů blob (**adftutorial**), složku (**output**) a soubor, do kterého se data kopírují. Ke každému spuštění kanálu je přiřazené jedinečné ID. Přístup k tomuto ID můžete získat pomocí systémové proměnné **RunId**. Název výstupního souboru se dynamicky vyhodnocuje na základě ID spuštění kanálu.   

V nastavení propojené služby jste zadali Azure Storage účet, který obsahuje zdrojová data. V nastavení zdrojové datové sady určíte, kde přesně se data nacházejí (kontejner objektů blob, složka a soubor). V nastavení datové sady jímky určíte, kam se data kopírují (kontejner objektů blob, složka a soubor). 

1. V levém podokně vyberte kartu **Autor** .

1. Vyberte **+** tlačítko (plus) a pak vyberte **datová sada**.

   :::image type="content" source="./media/quickstart-create-data-factory-portal/new-dataset-menu.png" alt-text="Nabídka pro vytvoření datové sady.":::

1. Na stránce **Nová datová sada** vyberte **Azure Blob Storage** a pak vyberte **pokračovat**. 

1. Na stránce **Vybrat formát** zvolte typ formátu dat a pak vyberte **pokračovat**. V takovém případě vyberte **binární soubor** při kopírování souborů bez analýzy obsahu.

   :::image type="content" source="./media/quickstart-create-data-factory-portal/select-format.png" alt-text="Vyberte formát.":::   
   
1. Na stránce **nastavit vlastnosti** proveďte následující kroky:

    a. Do **pole název** zadejte **InputDataset**. 

    b. Jako **Propojená služba** vyberte **AzureStorageLinkedService**.

    c. V části **Cesta k souboru** vyberte tlačítko **Procházet**.

    d. V okně **Zvolte soubor nebo složku** přejděte do složky **input** v kontejneru **adftutorial** , vyberte soubor **emp.txt** a pak vyberte **OK**.
    
    e. Vyberte **OK**.   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png" alt-text="Nastavte vlastnosti pro InputDataset.":::

1. Pomocí stejného postupu vytvořte výstupní datovou sadu:  

    a. Vyberte **+** tlačítko (plus) a pak vyberte **datová sada**.

    b. Na stránce **Nová datová sada** vyberte **Azure Blob Storage** a pak vyberte **pokračovat**.

    c. Na stránce **Vybrat formát** zvolte typ formátu dat a pak vyberte **pokračovat**.

    d. Na stránce **Vlastnosti sady** zadejte **OutputDataset** pro název. Jako propojená služba vyberte **AzureStorageLinkedService** .

    e. V části **cesta k souboru** zadejte **adftutorial/Output**. Pokud **výstupní** složka neexistuje, aktivita kopírování ji vytvoří za běhu.

    f. Vyberte **OK**.   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-outputdataset.png" alt-text="Nastavte vlastnosti pro OutputDataset.":::    

## <a name="create-a-pipeline"></a>Vytvoření kanálu 
V tomto postupu vytvoříte a ověříte kanál s aktivitou kopírování, která používá vstupní a výstupní datovou sadu. Aktivita kopírování kopíruje data ze souboru zadaného v nastavení vstupní datové sady do souboru zadaného v nastavení výstupní datové sady. Pokud vstupní datová sada určuje pouze složku (bez názvu souboru), aktivita kopírování zkopíruje do cíle všechny soubory ve zdrojové složce. 

1. Vyberte **+** tlačítko (plus) a pak vyberte **kanál**. 

1. Na panelu Obecné v části **vlastnosti** zadejte **CopyPipeline** pro **název**. Pak panel sbalíte kliknutím na ikonu vlastnosti v pravém horním rohu.

1. Na panelu nástrojů **Aktivity** rozbalte **Přesunout a transformovat**. Přetáhněte aktivitu **kopírování dat** z panelu nástrojů **aktivity** na plochu návrháře kanálu. Na panelu nástrojů **Aktivity** můžete aktivity také vyhledávat. Jako **Název** zadejte **CopyFromBlobToBlob**.

    :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-activity.png" alt-text="Vytváření aktivity kopírování dat":::   

1. V nastavení aktivity kopírování přepněte na kartu **Zdroj** a jako **Zdrojová datová sada** vyberte **InputDataset**.

1. V nastavení aktivity kopírování přepněte na kartu **Jímka** a jako **Datová sada jímky** vyberte **OutputDataset**.

1. Kliknutím na **Ověřit** na panelu nástrojů kanálu nad plátnem ověřte nastavení kanálu. Ověřte úspěšné ověření kanálu. Pokud chcete výstup ověřování zavřít, vyberte v pravém horním rohu tlačítko ověření. 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-validate.png"   alt-text="Ověřte kanál.":::

## <a name="debug-the-pipeline"></a>Ladění kanálu
V tomto kroku provedete ladění kanálu před tím, než ho nasadíte do služby Data Factory. 

1. Kliknutím na **Ladit** na panelu nástrojů kanálu nad plátnem aktivujte testovací běh. 
    
1. Ověřte, že se na kartě **Výstup** v nastavení kanálu v dolní části zobrazí stav spuštění kanálu. 
 
    :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-output.png" alt-text="Výstup spuštění kanálu":::

1. Ověřte, že složka **output** kontejneru **adftutorial** obsahuje výstupní soubor. Pokud výstupní složka neexistuje, Služba Data Factory ji automaticky vytvoří. 

## <a name="trigger-the-pipeline-manually"></a>Ruční aktivace kanálu
V tomto postupu nasadíte entity (propojené služby, datové sady a kanály) do služby Azure Data Factory. Pak ručně aktivujete spuštění kanálu. 

1. Před aktivací kanálu je nutné publikovat entity do služby Data Factory. Pro publikování vyberte v horní části **publikovat vše** . 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/publish-all.png" alt-text="Publikovat vše":::    

1. Pokud chcete kanál aktivovat ručně, vyberte **Přidat aktivační událost** na panelu nástrojů kanálu a potom vyberte **aktivovat nyní**. Na stránce **spuštění kanálu** vyberte **OK**.

## <a name="monitor-the-pipeline"></a>Monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. Pomocí tlačítka **Aktualizovat** seznam aktualizujte.

    :::image type="content" source="./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png" alt-text="Karta pro monitorování spuštění kanálu"::: 
1. Vyberte odkaz **CopyPipeline** , na této stránce se zobrazí stav spuštění aktivity kopírování. 

1. Chcete-li zobrazit podrobnosti o operaci kopírování, vyberte odkaz **Podrobnosti** (brýlí image). Podrobnosti o vlastnostech najdete v tématu [Přehled aktivity kopírování](copy-activity-overview.md). 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-operation-details.png" alt-text="Kopírovat podrobnosti o operaci.":::
1. Ověřte, že složka **output** obsahuje nový soubor. 
1. Kliknutím na odkaz **všechny spuštění kanálu** můžete přepnout zpátky na zobrazení **spuštění kanálu** ze zobrazení **spuštění aktivity** . 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Aktivace kanálu podle plánu
Tento postup je v tomto kurzu volitelný. Můžete vytvořit *aktivační událost plánovače* a naplánovat pravidelné spouštění kanálu (každou hodinu, každý den atd.). V tomto postupu vytvoříte aktivační událost, která se bude spouštět každou minutu až do koncového data a času, které zadáte. 

1. Přepněte na kartu **Vytvořit**. 

1. Přejít na svůj kanál, na panelu nástrojů kanálu vyberte **Přidat aktivační událost** a pak vyberte **Nová/upravit**. 

1. Na stránce **Přidat aktivační události** vyberte **Zvolit aktivační událost** a pak vyberte **Nová**. 

1. Na stránce **Nová aktivační událost** v části **konec** vyberte **k datu**, zadejte koncový čas pár minut od aktuálního času a pak vyberte **OK**. 

   Každé spuštění kanálu je zpoplatněno, proto zadejte koncový čas pouze pár minut po času zahájení. Ujistěte se, že se jedná o stejný den. Ujistěte se však, že je dostatek času na spuštění kanálu mezi časem publikování a koncovým časem. Aktivační událost nabývá účinnosti po publikování řešení do služby Data Factory, a ne při uložení aktivační události v uživatelském rozhraní. 

1. Na stránce **Nová aktivační událost** zaškrtněte políčko **aktivováno** a pak vyberte **OK**. 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/trigger-settings-next.png" alt-text="Nové nastavení aktivační události.":::   
1. Přečtěte si zprávu upozornění a vyberte **OK**.

1. Kliknutím na **publikovat vše** publikujte změny data Factory. 

1. Vlevo přepněte na kartu **Monitorování**. Seznam můžete aktualizovat kliknutím na **Aktualizovat**. Uvidíte, že se kanál spustí každou minutu od času publikování až do koncového času. 

   Všimněte si hodnot v poli **aktivovaném** sloupcem. Ruční spuštění aktivační události proběhlo v kroku, který jste provedli dříve (**Aktivovat**). 

1. Přepněte na zobrazení **spuštění aktivační události** . 

1. Ověřte, že se ve složce **output** vytvořil výstupní soubor pro každé spuštění kanálu až do zadaného koncového data a času. 

## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob v Azure. Pokud se chcete naučit používat službu Data Factory ve více scénářích, projděte si příslušné [kurzy](tutorial-copy-data-portal.md).
