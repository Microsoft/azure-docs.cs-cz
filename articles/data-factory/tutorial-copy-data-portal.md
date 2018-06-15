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
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: 34c78a114c1d106c400a94941aa113153383e206
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30173334"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Kopírování dat z úložiště Azure Blob Storage do databáze SQL Database pomocí služby Azure Data Factory
V tomto kurzu vytvoříte datovou továrnu pomocí uživatelského rozhraní služby Azure Data Factory. Kanál v této datové továrně kopíruje data z úložiště Azure Blob Storage do databáze SQL Database. Schéma konfigurace v tomto kurzu se vztahuje na kopírování z úložiště dat založeného na souborech do relačního úložiště dat. Seznam úložišť dat, která jsou podporovaná jako zdroje a jímky, najdete v tabulce [podporovaných úložišť dat](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Pokud se službou Data Factory teprve začínáte, přečtěte si téma [Úvod do Azure Data Factory](introduction.md).
>
> - Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Jestliže používáte verzi 1 služby Data Factory, která je obecně dostupná, podívejte se na téma [Úvod do Data Factory (verze 1)](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

V tomto kurzu budete provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu s aktivitou kopírování
> * Testovací spuštění kanálu
> * Ruční aktivace kanálu
> * Aktivace kanálu podle plánu
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet služby Azure Storage**. Blob Storage použijete jako *zdrojové* úložiště dat. Pokud účet úložiště nemáte, přečtěte si téma [Vytvoření účtu služby Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account), kde najdete postup jeho vytvoření.
* **Azure SQL Database**. Tuto databázi použijete jako úložiště dat *jímky*. Pokud databázi SQL Database nemáte, přečtěte si téma [Vytvoření databáze SQL Database](../sql-database/sql-database-get-started-portal.md), kde najdete postup jejího vytvoření.

### <a name="create-a-blob-and-a-sql-table"></a>Vytvoření objektu blob a tabulky SQL

Teď si připravte úložiště Blob Storage a databázi SQL Database pro tento kurz, a to podle těchto kroků.

#### <a name="create-a-source-blob"></a>Vytvoření zdrojového objektu blob

1. Spusťte Poznámkový blok. Zkopírujte následující text a uložte si ho na disk jako soubor **emp.txt**:

    ```
    John,Doe
    Jane,Doe
    ```

2. V úložišti Blob Storage vytvořte kontejner s názvem **adftutorial**. V tomto kontejneru vytvořte složku **input**. Pak do složky **input** nahrajte soubor **emp.txt**. K provedení těchto úloh použijte Azure Portal nebo nástroj, jako je například [Průzkumník služby Azure Storage](http://storageexplorer.com/).

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

2. Povolte službám Azure přístup k SQL Serveru. Zkontrolujte, že je nastavení **Povolit přístup ke službám Azure** pro váš SQL Server **ZAPNUTÉ**, aby mohla služba Data Factory na tento SQL Server zapisovat data. Toto nastavení můžete zkontrolovat a zapnout podle následujících kroků:

    a. Vlevo vyberte **Další služby** > **SQL Servery**.

    b. Vyberte svůj server a v části **NASTAVENÍ** vyberte **Brána firewall**.

    c. Na stránce **Nastavení brány firewall** vyberte **ZAPNUTO** u možnosti **Povolit přístup ke službám Azure**.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
V tomto kroku vytvoříte datovou továrnu a spustíte uživatelské rozhraní služby Data Factory, ve kterém v této datové továrně vytvoříte kanál. 

1. Otevřete webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
2. V nabídce vlevo vyberte **Nový** > **Data + analýzy** > **Datová továrna**. 
  
   ![Vytvoření nové datové továrny](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
3. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**. 
      
     ![Nová datová továrna](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Název objektu pro vytváření dat Azure musí být *globálně jedinečný*. Pokud se u pole s názvem zobrazí následující chybová zpráva, tak název datové továrny změňte (třeba na vaše_jméno_ADFTutorialDataFactory). Pravidla pro pojmenovávání artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
  
   ![Chybová zpráva](./media/tutorial-copy-data-portal/name-not-available-error.png)
4. Vyberte **předplatné** Azure, v rámci kterého chcete datovou továrnu vytvořit. 
5. U položky **Skupina prostředků** proveďte jeden z následujících kroků:
     
    a. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

    b. Vyberte **Vytvořit novou** a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md). 
6. V rozevíracím seznamu **Verze** vyberte **V2 (Preview)**.
7. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používaná datovou továrnou můžou být v jiných oblastech.
8. Zaškrtněte **Připnout na řídicí panel**. 
9. Vyberte **Vytvořit**. 
10. Na řídicím panelu se zobrazí následující dlaždice se stavem **Nasazování datové továrny**: 

    ![Dlaždice Nasazování datové továrny](media/tutorial-copy-data-portal/deploying-data-factory.png)
11. Po dokončení procesu vytváření se zobrazí stránka **Datová továrna**, kterou vidíte na obrázku.
   
    ![Domovská stránka objektu pro vytváření dat](./media/tutorial-copy-data-portal/data-factory-home-page.png)
12. Vyberte **Vytvořit a monitorovat**. Na samostatné kartě se spustí uživatelské rozhraní služby Data Factory.

## <a name="create-a-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte v datové továrně kanál s aktivitou kopírování. Aktivita kopírování kopíruje data z úložiště Blob Storage do databáze SQL Database. V [kurzu Rychlý start](quickstart-create-data-factory-portal.md) jste vytvořili kanál pomocí tohoto postupu:

1. Vytvoření propojené služby 
2. Vytvoření vstupní a výstupní datové sady
3. Vytvoření kanálu

V tomto kurzu začnete vytvořením kanálu. Potom vytvoříte propojené služby a datové sady, které budete potřebovat ke konfiguraci kanálu. 

1. Na stránce **Pusťme se do toho** vyberte **Vytvořit kanál**. 

   ![Vytvoření kanálu](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
2. V okně **Vlastnosti** pro příslušný kanál nastavte **Název** kanálu na **CopyPipeline**.

    ![Název kanálu](./media/tutorial-copy-data-portal/pipeline-name.png)
3. Na panelu nástrojů **Aktivity** rozbalte kategorii **Tok dat** a přetáhněte aktivitu **Kopírování** z panelu nástrojů na plochu návrháře kanálu. 

    ![Aktivita kopírování](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)
4. Na kartě **Obecné** v okně **Vlastnosti** nastavte název aktivity na **CopyFromBlobToSql**.

    ![Název aktivity](./media/tutorial-copy-data-portal/activity-name.png)
5. Přejděte na kartu **Zdroj**. Výběrem **+ Nová** vytvořte zdrojovou datovou sadu. 

    ![Karta Zdroj](./media/tutorial-copy-data-portal/new-source-dataset-button.png)
6. V okně **Nová datová sada** vyberte **Azure Blob Storage** a pak vyberte **Dokončit**. Zdrojová data jsou v úložišti Blob Storage, takže jako zdrojovou datovou sadu vyberete **Azure Blob Storage**. 

    ![Výběr úložiště](./media/tutorial-copy-data-portal/select-azure-storage.png)
7. V aplikaci se otevře nová karta s názvem **AzureBlob1**.

    ![Karta AzureBlob1 ](./media/tutorial-copy-data-portal/new-tab-azure-blob1.png)        
8. Do pole **Název** na kartě **Obecné** v dolní části okna **Vlastnosti** zadejte **SourceBlobDataset**.

    ![Název datové sady](./media/tutorial-copy-data-portal/dataset-name.png)
9. V okně **Vlastnosti** přejděte na kartu **Připojení**. Vyberte **+ Nová** vedle textového pole **Propojená služba**. 

    Propojená služba propojuje úložiště dat nebo výpočetní prostředí s datovou továrnou. V tomto případě vytvoříte propojenou službu Storage, která propojí váš účet úložiště s úložištěm dat. Propojená služba obsahuje informace o připojení, které služba Data Factory používá pro připojení k úložišti Blob Storage za běhu. Datová sada určuje kontejner, složku a soubor (volitelné) obsahující zdrojová data. 

    ![Tlačítko Nová propojená služba](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)
10. V okně **Nová propojená služba** proveďte následující kroky: 

    a. Do pole **Název** zadejte **AzureStorageLinkedService**. 

    b. V rozevíracím seznamu **Název účtu úložiště** vyberte svůj účet úložiště.

    c. Vyberte **Testovat připojení** a otestujte připojení k účtu úložiště.

    d. Vyberte **Uložit** a uložte propojenou službu.

    ![Nová propojená služba](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)
11. Vedle pole **Cesta k souboru** vyberte **Procházet**.

    ![Tlačítko Procházet pro vyhledání cesty k souboru](./media/tutorial-copy-data-portal/file-browse-button.png)
12. Přejděte do složky **adftutorial/input**, vyberte soubor **emp.txt** a potom vyberte **Dokončit**. Nebo také můžete na soubor **emp.txt** dvakrát kliknout. 

    ![Výběr vstupního souboru](./media/tutorial-copy-data-portal/select-input-file.png)
13. Ověřte, že je v rozevíracím seznamu **Formát souboru** vybraná položka **Formát textu** a v rozevíracím seznamu **Oddělovač sloupců** položka **Čárka (`,`)**. Pokud se ve zdrojovém souboru používají jiné oddělovače řádků a sloupců, můžete vybrat **Rozpoznat formát textu** vedle rozevíracího seznamu **Formát souboru**. Nástroj pro kopírování dat pro vás automaticky rozpozná formát souboru a oddělovače. Tyto hodnoty však můžete přepsat. Pokud se na této stránce chcete podívat na náhled dat, vyberte **Náhled dat**.

    ![Rozpoznat formát textu](./media/tutorial-copy-data-portal/detect-text-format.png)
14. V okně **Vlastnosti** přejděte na kartu **Schéma** a vyberte **Importovat schéma**. Všimněte si, že aplikace ve zdrojovém souboru rozpoznala dva sloupce. Tady naimportujete schéma, abyste mohli namapovat sloupce ze zdrojového úložiště dat na sloupce v úložišti dat jímky. Pokud sloupce mapovat nepotřebujete, můžete tento krok přeskočit. Pro účely tohoto kurzu schéma naimportujte.

    ![Rozpoznání schématu zdroje](./media/tutorial-copy-data-portal/detect-source-schema.png)  
15. Teď přejděte na kartu s kanálem (vyberte kanál na levé straně).

    ![Karta Kanál](./media/tutorial-copy-data-portal/pipeline-tab.png)
16. Ověřte, že je v rozevíracím seznamu **Zdrojová datová sada** v okně **Vlastnosti** vybraná sada **SourceBlobDataset**. Pokud se na této stránce chcete podívat na náhled dat, vyberte **Náhled dat**. 
    
    ![Zdrojová datová sada](./media/tutorial-copy-data-portal/source-dataset-selected.png)
17. Přejděte na kartu **Jímka**, vyberte **+ Nová** a vytvořte datovou sadu jímky. 

    ![Datová sada jímky](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
18. V okně **Nová datová sada** vyberte **Azure SQL Database** a potom vyberte **Dokončit**. V tomto kurzu zkopírujte data do databáze SQL Database. 

    ![Výběr databáze SQL Database](./media/tutorial-copy-data-portal/select-azure-sql-database.png)
19. Do pole **Název** na kartě **Obecné** v okně **Vlastnosti** zadejte **OutputSqlDataset**. 
    
    ![Název výstupní datové sady](./media/tutorial-copy-data-portal/output-dataset-name.png)
20. Přejděte na kartu **Připojení** a vedle pole **Propojená služba** vyberte **+ Nová**. Datová sada musí být přidružená k propojené službě. Propojená služba obsahuje připojovací řetězec, který služba Data Factory používá pro připojení k databázi SQL Database za běhu. Datová sada určuje kontejner, složku a soubor (volitelné), do kterého se data kopírují. 
    
    ![Propojená služba](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
21. V okně **Nová propojená služba** proveďte následující kroky: 

    a. Do pole **Název** zadejte **AzureSqlDatabaseLinkedService**.

    b. V rozevíracím seznamu **Název serveru** vyberte příslušný název instance SQL Serveru.

    c. V rozevíracím seznamu **Název databáze** vyberte svoji databázi SQL Database.

    d. Do pole **Uživatelské jméno** zadejte jméno uživatele.

    e. Do pole **Heslo** zadejte heslo pro tohoto uživatele.

    f. Vyberte **Otestovat připojení** a připojení otestujte.

    g. Vyberte **Uložit** a uložte propojenou službu. 
    
    ![Uložení nové propojené služby](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

22. V rozevíracím seznamu **Tabulka** vyberte **[dbo].[emp]**. 

    ![Table](./media/tutorial-copy-data-portal/select-emp-table.png)
23. Přejděte na kartu **Schéma** a vyberte **Importovat schéma**. 

    ![Výběr tlačítka Importovat schéma](./media/tutorial-copy-data-portal/import-destination-schema.png)
24. Vyberte sloupec **ID** a potom vyberte **Odstranit**. Sloupec **ID** je v databázi SQL Database sloupcem identity, takže aktivita kopírování nemusí do tohoto sloupce vkládat žádná data.

    ![Odstranění sloupce ID](./media/tutorial-copy-data-portal/delete-id-column.png)
25. Přejděte na kartu s kanálem a zkontrolujte, že je v rozevíracím seznamu **Datová sada jímky** vybraná sada **OutputSqlDataset**.

    ![Karta Kanál](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        
26. V dolní části okna **Vlastnosti** přejděte na kartu **Mapování** a vyberte **Importovat schémata**. Všimněte si mapování prvního a druhého sloupce ve zdrojovém souboru na pole **FirstName** (Jméno) a **LastName** (Příjmení) v databázi SQL Database.

    ![Mapování schémat](./media/tutorial-copy-data-portal/map-schemas.png)
27. Vyberte **Ověřit** a kanál ověřte. Výběrem tlačítka se šipkou doprava, které se nachází v pravém horním rohu, okno ověření zavřete.

    ![Výstup ověření kanálu](./media/tutorial-copy-data-portal/pipeline-validation-output.png)   
28. V pravém horním rohu vyberte **Kód**. Zobrazí se kód JSON přidružený k příslušnému kanálu. 

    ![Tlačítko Kód](./media/tutorial-copy-data-portal/code-button.png)
29. Zobrazí se kód JSON podobný následujícímu fragmentu kódu: 

    ```json
    {
        "name": "CopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToSql",
                    "type": "Copy",
                    "dependsOn": [],
                    "policy": {
                        "timeout": "7.00:00:00",
                        "retry": 0,
                        "retryIntervalInSeconds": 20
                    },
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "SqlSink",
                            "writeBatchSize": 10000
                        },
                        "enableStaging": false,
                        "parallelCopies": 0,
                        "cloudDataMovementUnits": 0,
                        "translator": {
                            "type": "TabularTranslator",
                            "columnMappings": "Prop_0: FirstName, Prop_1: LastName"
                        }
                    },
                    "inputs": [
                        {
                            "referenceName": "SourceBlobDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "OutputSqlDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ]
                }
            ]
        }
    }
    ```

## <a name="test-run-the-pipeline"></a>Testovací spuštění kanálu
Před publikováním artefaktů (propojených služeb, datových sad a kanálu) do služby Data Factory nebo vlastního úložiště VSTS (Visual Studio Team Services) GIT můžete spuštění kanálu otestovat. 

1. Pokud chcete spuštění kanálu otestovat, vyberte na panelu nástrojů **Testovací běh**. Na kartě **Výstup** v dolní části okna se zobrazí stav spuštění kanálu. 

    ![Otestování kanálu](./media/tutorial-copy-data-portal/test-run-output.png)
2. Ověřte, že se data ze zdrojového souboru vložila do cílové databáze SQL. 

    ![Ověření výstupu SQL](./media/tutorial-copy-data-portal/verify-sql-output.png)
3. V levém podokně vyberte **Publikovat vše**. Touto akcí publikujete vytvořené entity (propojené služby, datové sady a kanály) do služby Data Factory.

    ![Publikování](./media/tutorial-copy-data-portal/publish-button.png)
4. Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**. Pokud chcete zobrazit oznámení, vyberte na levém bočním panelu kartu **Zobrazit oznámení**. Když budete chtít okno s oznámeními zavřít, vyberte **Zavřít**.

    ![Zobrazit oznámení](./media/tutorial-copy-data-portal/show-notifications.png)

## <a name="configure-code-repository"></a>Konfigurace úložiště kódu
Kód přidružený k artefaktům datové továrny můžete publikovat do úložiště kódu Visual Studio Team Services. V tomto kroku vytvoříte úložiště kódu.  Další informace o vizuálním vytváření s využitím integrace VSTS najdete v tématu popisujícím [vytváření s využitím integrace VSTS Git](author-visually.md#author-with-vsts-git-integration).

Pokud s úložištěm kódu Visual Studio Team Services pracovat nechcete, můžete tento krok přeskočit. Můžete dál publikovat do služby Data Factory, jako jste to dělali v předchozím kroku. 

1. V levém horním rohu vyberte **Data Factory** (nebo použijte šipku dolů vedle této položky) a vyberte **Konfigurace úložiště kódu**. 

    ![Konfigurace úložiště kódu](./media/tutorial-copy-data-portal/configure-code-repository-button.png)
2. Na stránce **Nastavení úložiště** proveďte následující kroky:

    a. V rozevíracím seznamu **Typ úložiště** vyberte **Visual Studio Team Services Git**.

    b. V rozevíracím seznamu **Účet služby Visual Studio Team Services** vyberte svůj účet služby Visual Studio Team Services.

    c. V rozevíracím seznamu **Název projektu** vyberte projekt na svém účtu služby Visual Studio Team Services.

    d. Do pole **Název úložiště Git** zadejte **Tutorial2**. Úložiště Git se tím přidruží k vaší datové továrně.

    e. Zkontrolujte, jestli je zaškrtnuté políčko **Importovat do úložiště stávající prostředky Data Factory**.

    f. Vyberte **Uložit** a nastavení se uloží. 

    ![Nastavení úložiště](./media/tutorial-copy-data-portal/repository-settings.png)
3. Ověřte, že je vybrané úložiště **VSTS GIT**.

    ![Výběr úložiště VSTS GIT](./media/tutorial-copy-data-portal/vsts-git-selected.png)
4. Na samostatné kartě ve webovém prohlížeči přejděte do úložiště **Tutorial2**. Zobrazí se dvě větve: **adf_publish** a **master**.

    ![Větve master a adf_publish](./media/tutorial-copy-data-portal/initial-branches-vsts-git.png)
5. Ověřte, že jsou soubory JSON pro entity služby Data Factory ve větvi **master**.

    ![Soubory ve větvi master](./media/tutorial-copy-data-portal/master-branch-files.png)
6. Ověřte, že soubory JSON zatím nejsou ve větvi **adf_publish**. 

    ![Soubory ve větvi adf_publish](./media/tutorial-copy-data-portal/adf-publish-files.png)
7. Do pole **Popis** zadejte popis kanálu a na panelu nástrojů vyberte **Uložit**. 

    ![Popis kanálu](./media/tutorial-copy-data-portal/pipeline-description.png)
8. Teď v úložišti **Tutorial2** vidíte větev se svým uživatelským jménem. Provedená změna je ve vaší vlastní větvi, ne ve větvi master. Publikovat můžete jenom entity z větve master.

    ![Vaše větev](./media/tutorial-copy-data-portal/your-branch.png)
9. Najeďte myší na tlačítko **Synchronizovat** (ale zatím na něj neklikejte), zaškrtněte políčko **Potvrdit změny** a vyberte **Synchronizovat**. Tím svoje změny synchronizujete s větví master. 

    ![Potvrzení a synchronizace změn](./media/tutorial-copy-data-portal/commit-and-sync.png)
10. V okně **Synchronizace změn** proveďte následující akce: 

    a. Ověřte, že se v aktualizovaném seznamu **Kanály** zobrazuje kanál **CopyPipeline**.

    b. Ověřte, že je vybraná možnost **Po synchronizaci publikovat změny**. Jestliže zaškrtnutí tohoto políčka zrušíte, budou se změny, které jste ve své větvi udělali, jenom synchronizovat s větví master. Nebudou se publikovat do služby Data Factory. Můžete je publikovat později pomocí tlačítka **Publikovat**. Pokud toto políčko zaškrtnete, budou se změny nejprve synchronizovat s větví master a pak se publikují do služby Data Factory.

    c. Vyberte **Synchronizovat**. 

    ![Synchronizace provedených změn](./media/tutorial-copy-data-portal/sync-your-changes.png)
11. Teď se ve větvi **adf_publish** úložiště **Tutorial2** zobrazují soubory. V této větvi najdete také šablonu Azure Resource Manageru pro vaše řešení Data Factory. 

    ![Seznam souborů ve větvi adf_publish](./media/tutorial-copy-data-portal/adf-publish-files-after-publish.png)


## <a name="trigger-the-pipeline-manually"></a>Ruční aktivace kanálu
V tomto kroku ručně aktivujete kanál, který jste publikovali v minulém kroku. 

1. Vyberte na panelu nástrojů **Aktivační událost** a potom vyberte **Aktivovat**. Na stránce **Spuštění kanálu** vyberte **Dokončit**.  

    ![Aktivace kanálu](./media/tutorial-copy-data-portal/trigger-now-menu.png)
2. Vlevo přejděte na kartu **Monitorování**. Zobrazí se stav ručně aktivovaného spuštění kanálu. Pomocí odkazů ve sloupci **Akce** můžete zobrazit podrobnosti o aktivitě a spustit kanál znovu.

    ![Monitorování spuštění kanálu](./media/tutorial-copy-data-portal/monitor-pipeline.png)
3. Pokud se chcete podívat na spuštění aktivit, která souvisí se spuštěním kanálu, vyberte odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Tento příklad obsahuje pouze jednu aktivitu, takže se v seznamu zobrazí pouze jedna položka. Podrobnosti o operaci kopírování zobrazíte výběrem odkazu **Podrobnosti** (ikona brýlí) ve sloupci **Akce**. Pokud se chcete vrátit do zobrazení **Spuštění kanálu**, vyberte **Kanály** v horní části. Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**.

    ![Monitorování spuštění aktivit](./media/tutorial-copy-data-portal/view-activity-runs.png)
4. Ověřte, že se do tabulky **emp** v databázi SQL Database přidaly další dva řádky. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Aktivace kanálu podle plánu
V tomto kroku vytvoříte pro kanál aktivační událost plánovače. Tato aktivační událost spouští kanál podle zadaného plánu (například každou hodinu nebo každý den). V tomto příkladu nastavíte aktivační událost tak, aby se spouštěla každou minutu až do uplynutí koncového data a času. 

1. Vlevo přejděte na kartu **Upravit**. 

    ![Karta Upravit](./media/tutorial-copy-data-portal/edit-tab.png)
2. Vyberte **Aktivační událost** a vyberte **Nová/Upravit**. Pokud kanál není aktivní, přejděte do něj. 

    ![Možnost aktivace](./media/tutorial-copy-data-portal/trigger-new-edit-menu.png)
3. V okně **Přidat aktivační události** vyberte **Zvolit aktivační událost** a pak vyberte **+ Nová**. 

    ![Tlačítko Nová](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
4. V okně **Nová aktivační událost** proveďte následující kroky: 

    a. Do pole **Název** zadejte **RunEveryMinute**.

    b. V části **Konec** vyberte **Dne**.

    c. Klikněte na šipku rozevíracího seznamu **Konec dne**.

    d. Vyberte **aktuální den**. Ve výchozím nastavení je koncový den nastavený na další den.

    e. Aktualizujte část **minuty** tak, aby byla pár minut po aktuálním datu a času. Aktivační událost se aktivuje pouze po publikování změn. Pokud tedy zadáte čas jenom pár minut od aktuálního času a během této doby změny nepublikujete, spuštění aktivační události se nezobrazí.

    f. Vyberte **Použít**. 

    ![Vlastnosti aktivační události](./media/tutorial-copy-data-portal/set-trigger-properties.png)

    g. Zaškrtněte políčko **Aktivováno**. Pomocí tohoto zaškrtávacího políčka můžete aktivační událost deaktivovat a aktivovat ji později.

    h. Vyberte **Next** (Další).

    ![Zaškrtávací políčko Aktivováno](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > S každým spuštěním kanálu jsou spojené určité náklady, takže nastavte koncové datum správně. 
5. Na stránce **Parametry spuštění aktivační události** si přečtěte upozornění a pak vyberte **Dokončit**. Kanál v tomto příkladu nepoužívá žádné parametry. 

    ![Parametry spuštění aktivační události](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)
6. Vyberte **Synchronizovat** a synchronizujte změny ve svojí větvi s větví mater. Ve výchozím nastavení je políčko **Po synchronizaci publikovat změny** zaškrtnuté. Když vyberete **Synchronizovat**, budou se do služby Data Factory publikovat také aktualizované entity z větve master. Aktivační událost se neaktivuje, dokud publikování neproběhne úspěšně.

    ![Synchronizace provedených změn](./media/tutorial-copy-data-portal/sync-your-changes-with-trigger.png) 
7. Vlevo přejděte na kartu **Monitorování**, kde uvidíte aktivovaná spuštění kanálu. 

    ![Aktivovaná spuštění kanálu](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
8. Pokud chcete ze zobrazení **Spuštění kanálu** přejít do zobrazení **Spuštění aktivační události**, vyberte **Spuštění kanálu** a potom **Spuštění aktivačních událostí**.
    
    ![Spuštění aktivačních událostí](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
9. V seznamu se zobrazí spuštění aktivační události. 

    ![Seznam spuštění aktivační události](./media/tutorial-copy-data-portal/trigger-runs-list.png)
10. Ověřte, že se až do uplynutí zadaného koncového času budou do tabulky **emp** vkládat dva řádky za minutu (pro každé spuštění kanálu). 

## <a name="next-steps"></a>Další kroky
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
