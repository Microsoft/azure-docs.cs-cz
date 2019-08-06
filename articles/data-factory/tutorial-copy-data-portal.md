---
title: Vytvoření kanálu datové továrny pomocí portálu Azure Portal | Microsoft Docs
description: Tento kurz obsahuje podrobné pokyny k vytvoření datové továrny s kanálem pomocí portálu Azure Portal. Kanál využívá aktivitu kopírování ke kopírování dat z úložiště Azure Blob Storage do databáze SQL Database.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: jingwang
ms.openlocfilehash: 5174b46bae5bbcc18f5a2575fb68cce1305507a3
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68826707"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Kopírování dat z úložiště Azure Blob Storage do databáze SQL Database pomocí služby Azure Data Factory
V tomto kurzu vytvoříte datovou továrnu pomocí uživatelského rozhraní služby Azure Data Factory. Kanál v této datové továrně kopíruje data z úložiště Azure Blob Storage do databáze SQL Database. Schéma konfigurace v tomto kurzu se vztahuje na kopírování z úložiště dat založeného na souborech do relačního úložiště dat. Seznam úložišť dat, která jsou podporovaná jako zdroje a jímky, najdete v tabulce [podporovaných úložišť dat](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Pokud se službou Data Factory teprve začínáte, přečtěte si téma [Úvod do Azure Data Factory](introduction.md).

V tomto kurzu budete provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu s aktivitou kopírování
> * Testovací spuštění kanálu
> * Ruční aktivace kanálu
> * Aktivace kanálu podle plánu
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet služby Azure Storage**. Blob Storage použijete jako *zdrojové* úložiště dat. Pokud účet úložiště nemáte, přečtěte si téma [Vytvoření účtu služby Azure Storage](../storage/common/storage-quickstart-create-account.md), kde najdete postup jeho vytvoření.
* **Azure SQL Database**. Tuto databázi použijete jako úložiště dat *jímky*. Pokud databázi SQL Database nemáte, přečtěte si téma [Vytvoření databáze SQL Database](../sql-database/sql-database-get-started-portal.md), kde najdete postup jejího vytvoření.

### <a name="create-a-blob-and-a-sql-table"></a>Vytvoření objektu blob a tabulky SQL

Teď si připravte úložiště Blob Storage a databázi SQL Database pro tento kurz, a to podle těchto kroků.

#### <a name="create-a-source-blob"></a>Vytvoření zdrojového objektu blob

1. Spusťte Poznámkový blok. Zkopírujte následující text a uložte si ho na disk jako soubor **emp.txt**:

    ```
    John,Doe
    Jane,Doe
    ```

1. V úložišti Blob Storage vytvořte kontejner s názvem **adftutorial**. V tomto kontejneru vytvořte složku **input**. Pak do složky **input** nahrajte soubor **emp.txt**. K provedení těchto úloh použijte Azure Portal nebo nástroj, jako je například [Průzkumník služby Azure Storage](https://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Vytvoření tabulky SQL jímky

1. Použitím následujícího skriptu SQL si v databázi SQL Database vytvořte tabulku **emp**:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

1. Povolte službám Azure přístup k SQL Serveru. Zkontrolujte, že je nastavení **Povolit přístup ke službám Azure** pro váš SQL Server **ZAPNUTÉ**, aby mohla služba Data Factory na tento SQL Server zapisovat data. Pokud chcete toto nastavení ověřit a zapnout, přejděte na Azure SQL Server > Přehled > nastavení brány firewall serveru > nastavte možnost **Povolit přístup ke službám Azure** na **zapnuto**.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
V tomto kroku vytvoříte datovou továrnu a spustíte uživatelské rozhraní služby Data Factory, ve kterém v této datové továrně vytvoříte kanál. 

1. Otevřete **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
2. V nabídce vlevo vyberte vytvořit**Data Factory** **analýzy** >  **prostředků** > : 
  
   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**. 
 
   Název datové továrny Azure musí být *globálně jedinečný*. Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. (například yournameADFTutorialDataFactory). Pravidla pro pojmenovávání artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
        
     ![Nová datová továrna](./media/doc-common-process/name-not-available-error.png)
4. Vyberte **předplatné** Azure, v rámci kterého chcete datovou továrnu vytvořit. 
5. U položky **Skupina prostředků** proveďte jeden z následujících kroků:
     
    a. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

    b. Vyberte **Vytvořit novou** a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md). 
6. Jako **Verzi** vyberte **V2**.
7. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používaná datovou továrnou můžou být v jiných oblastech.
8. Vyberte **Vytvořit**. 
9. Po dokončení vytváření se zobrazí oznámení v centru oznámení. Vyberte **Přejít k prostředku** a přejděte na stránku Datová továrna.
10. Vyberte **Vytvořit a monitorovat**. Na samostatné kartě se spustí uživatelské rozhraní služby Data Factory.


## <a name="create-a-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte v datové továrně kanál s aktivitou kopírování. Aktivita kopírování kopíruje data z úložiště Blob Storage do databáze SQL Database. V [kurzu Rychlý start](quickstart-create-data-factory-portal.md) jste vytvořili kanál pomocí tohoto postupu:

1. Vytvoření propojené služby 
1. Vytvoření vstupní a výstupní datové sady
1. Vytvoření kanálu

V tomto kurzu začnete vytvořením kanálu. Potom vytvoříte propojené služby a datové sady, které budete potřebovat ke konfiguraci kanálu. 

1. Na stránce **Pusťme se do toho** vyberte **Vytvořit kanál**. 

   ![Vytvoření kanálu](./media/doc-common-process/get-started-page.png)
1. Na kartě kanálu **Obecné** zadejte **CopyPipeline** jako **Název** kanálu.

1. V poli nástroje **aktivity** rozbalte kategorii **přesunout a transformovat** a přetáhněte **kopírování dat** aktivity z panelu nástrojů na plochu návrháře kanálu. Jako **Název** zadejte **CopyFromBlobToSql**.

    ![Zkopírovat aktivitu](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Konfigurace zdroje

1. Přejděte na kartu **Zdroj**. Výběrem **+ Nová** vytvořte zdrojovou datovou sadu. 

1. V dialogovém okně **Nová datová sada** vyberte **Azure Blob Storage**a pak vyberte **pokračovat**. Zdrojová data jsou v úložišti Blob Storage, takže jako zdrojovou datovou sadu vyberete **Azure Blob Storage**. 

1. V dialogovém okně **Vybrat formát** zvolte typ formátu dat a pak vyberte **pokračovat**.

    ![Typ formátu dat](./media/doc-common-process/select-data-format.png)

1. V dialogovém okně **nastavit vlastnosti** zadejte **SourceBlobDataset** do pole název. Vyberte **+ Nová** vedle textového pole **Propojená služba**. 
    
1. V dialogovém okně **Nová propojená služba (Azure Blob Storage)** jako název zadejte **AzureStorageLinkedService** a v seznamu **název účtu úložiště** vyberte svůj účet úložiště. Otestujte připojení a pak kliknutím na **Dokončit** nasaďte propojenou službu.

1. Po vytvoření propojené služby přejdete zpátky na stránku **Vlastnosti sady** . Vedle pole **Cesta k souboru** vyberte **Procházet**.

1. Přejděte do složky **adftutorial/input**, vyberte soubor **emp.txt** a potom vyberte **Dokončit**.

1. Automaticky přejde na stránku kanálu. Na kartě **zdroj** potvrďte, že je vybraná možnost **SourceBlobDataset** . Pokud se na této stránce chcete podívat na náhled dat, vyberte **Náhled dat**. 
    
    ![Zdrojová datová sada](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Konfigurace jímky

1. Přejděte na kartu **Jímka**, vyberte **+ Nová** a vytvořte datovou sadu jímky. 

1. V dialogovém okně **Nová datová sada** zadejte "SQL" do vyhledávacího pole, chcete-li filtrovat konektory, vyberte **Azure SQL Database**a pak vyberte **pokračovat**. V tomto kurzu zkopírujte data do databáze SQL Database. 

1. V dialogovém okně **nastavit vlastnosti** zadejte **OutputSqlDataset** do pole název. Vyberte **+ Nová** vedle textového pole **Propojená služba**. Datová sada musí být přidružená k propojené službě. Propojená služba obsahuje připojovací řetězec, který služba Data Factory používá pro připojení k databázi SQL Database za běhu. Datová sada určuje kontejner, složku a soubor (volitelné), do kterého se data kopírují. 
      
1. V dialogovém okně **Nová propojená služba (Azure SQL Database)** proveďte následující kroky: 

    a. Do pole **Název** zadejte **AzureSqlDatabaseLinkedService**.

    b. V rozevíracím seznamu **Název serveru** vyberte příslušný název instance SQL Serveru.

    c. V rozevíracím seznamu **Název databáze** vyberte svoji databázi SQL Database.

    d. Do pole **Uživatelské jméno** zadejte jméno uživatele.

    e. Do pole **Heslo** zadejte heslo pro tohoto uživatele.

    f. Vyberte **Otestovat připojení** a připojení otestujte.

    g. Vyberte **Dokončit** a nasaďte propojenou službu. 
    
    ![Uložení nové propojené služby](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Automaticky přejde do dialogového okna **nastavit vlastnosti** . V rozevíracím seznamu **Tabulka** vyberte **[dbo].[emp]** . Pak vyberte **Dokončit**.

1. Přejděte na kartu s kanálem a zkontrolujte, že je v rozevíracím seznamu **Datová sada jímky** vybraná sada **OutputSqlDataset**.

    ![Karta Kanál](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       
## <a name="validate-the-pipeline"></a>Ověření kanálu
Vyberte **Ověřit** z panelu nástrojů a kanál ověřte.
 
Kliknutím na **kód** v pravém horním rohu uvidíte kód JSON přidružený k kanálu.

## <a name="debug-and-publish-the-pipeline"></a>Ladění a publikování kanálu
Před publikováním artefaktů (propojených služeb, datových sad a kanálu) do služby Data Factory nebo vlastního úložiště Gitu Azure Repos můžete kanál odladit. 

1. K ladění kanálu vyberte na panelu nástrojů **Ladit**. Na kartě **Výstup** v dolní části okna se zobrazí stav spuštění kanálu. 

1. Po úspěšném spuštění kanálu klikněte na horním panelu nástrojů na **publikovat vše**. Touto akcí publikujete vytvořené entity (datové sady a kanály) do služby Data Factory.

1. Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**. Pokud chcete zobrazit zprávy oznámení, klikněte vpravo nahoře (tlačítko zvonečku) na **Zobrazit oznámení**. 

## <a name="trigger-the-pipeline-manually"></a>Ruční aktivace kanálu
V tomto kroku ručně aktivujete kanál, který jste publikovali v minulém kroku. 

1. Vyberte **Přidat aktivační událost** na panelu nástrojů a pak vyberte **aktivovat nyní**. Na stránce **Spuštění kanálu** vyberte **Dokončit**.  

1. Vlevo přejděte na kartu **Monitorování**. Zobrazí se stav ručně aktivovaného spuštění kanálu. Pomocí odkazů ve sloupci **Akce** můžete zobrazit podrobnosti o aktivitě a spustit kanál znovu.

    ![Monitorování spuštění kanálu](./media/tutorial-copy-data-portal/monitor-pipeline.png)

1. Pokud se chcete podívat na spuštění aktivit, která souvisí se spuštěním kanálu, vyberte odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. V tomto příkladu je k dispozici pouze jedna aktivita, takže se v seznamu zobrazí pouze jedna položka. Podrobnosti o operaci kopírování zobrazíte výběrem odkazu **Podrobnosti** (ikona brýlí) ve sloupci **Akce**. Vyberte možnost **spuštění kanálu** v horní části a vraťte se do zobrazení spuštění kanálu. Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**.

    ![Monitorování spuštění aktivit](./media/tutorial-copy-data-portal/view-activity-runs.png)

1. Ověřte, že se do tabulky **emp** v databázi SQL Database přidaly další dva řádky. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Aktivace kanálu podle plánu
V tomto kroku vytvoříte pro kanál aktivační událost plánovače. Tato aktivační událost spouští kanál podle zadaného plánu (například každou hodinu nebo každý den). Tady nastavíte, aby se aktivační událost spouštěla každou minutu až do zadané koncové hodnoty DateTime. 

1. Vlevo nad kartou monitorování přejděte na kartu **Autor**. 

1. Přejděte do kanálu, klikněte na tlačítko **Přidat aktivační událost** na panelu nástrojů a vyberte možnost **Nový/upravit**. 

1. V dialogovém okně **Přidat triggery** vyberte **+ Nová** pro **možnost zvolit oblast aktivace** .

1. V okně **Nová aktivační událost** proveďte následující kroky: 

    a. Do pole **Název** zadejte **RunEveryMinute**.

    b. V části **Konec** vyberte **Dne**.

    c. Klikněte na šipku rozevíracího seznamu **Konec dne**.

    d. Vyberte **aktuální den**. Ve výchozím nastavení je koncový den nastavený na další den.

    e. Aktualizujte část **čas ukončení** tak, aby byla několik minut po aktuálním datu a času. Aktivační událost se aktivuje pouze po publikování změn. Pokud nakonfigurujete tuto hodnotu jenom na pár minut a nepublikujete ji, neuvidíte Trigger spustit.

    f. Vyberte **Použít**. 

    g. Pro možnost aktivováno vyberte **Ano**. 

    h. Vyberte **Další**.

    ![Zaškrtávací políčko Aktivováno](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > S každým spuštěním kanálu jsou spojené určité náklady, takže nastavte koncové datum správně. 
1. Na stránce **Parametry spuštění aktivační události** si přečtěte upozornění a pak vyberte **Dokončit**. Kanál v tomto příkladu nepoužívá žádné parametry. 

1. Kliknutím na **Publikovat vše** publikujte změnu. 

1. Vlevo přejděte na kartu **Monitorování**, kde uvidíte aktivovaná spuštění kanálu. 

    ![Aktivovaná spuštění kanálu](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)   
 
1. Pokud chcete přepnout ze zobrazení **spuštění kanálu** na zobrazení **spuštění aktivační události** , vyberte v horní části okna **spouštění aktivační události** .

1. V seznamu se zobrazí spuštění aktivační události. 

1. Ověřte, že se až do uplynutí zadaného koncového času budou do tabulky **emp** vkládat dva řádky za minutu (pro každé spuštění kanálu). 

## <a name="next-steps"></a>Další postup
Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti Blob Storage. Naučili jste se tyto postupy: 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu s aktivitou kopírování
> * Testovací spuštění kanálu
> * Ruční aktivace kanálu
> * Aktivace kanálu podle plánu
> * Monitorování spuštění aktivit a kanálu


Pokud se chcete dozvědět, jak kopírovat data z místního prostředí do cloudu, přejděte k následujícímu kurzu: 

> [!div class="nextstepaction"]
>[Kopírování dat z místního prostředí do cloudu](tutorial-hybrid-copy-portal.md)
