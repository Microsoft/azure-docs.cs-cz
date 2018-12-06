---
title: Přírůstkové kopírování dat pomocí technologie Change Tracking a Azure Data Factory | Dokumentace Microsoftu
description: 'V tomto kurzu vytvoříte kanál Azure Data Factory, který přírůstkově kopíruje rozdílová data z několika tabulek v místní databázi SQL Serveru do databáze Azure SQL. '
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/12/2018
ms.author: yexu
ms.openlocfilehash: eaafc8acb73dd48e213d05d953d9ada457c53132
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957261"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information"></a>Přírůstkové kopírování dat z Azure SQL Database do Azure Blob Storage s využitím informací sledování změn 
V tomto kurzu vytvoříte datovou továrnu Azure s kanálem, který načítá rozdílová data na základě **sledování změn** ve zdrojové databázi Azure SQL do úložiště objektů blob Azure.  

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Příprava zdrojového úložiště dat
> * Vytvoření datové továrny
> * Vytvoření propojených služeb 
> * Vytvoření datových sad pro zdroj, jímku a sledování změn
> * Vytvoření, spuštění a monitorování kanálu úplného kopírování
> * Přidání nebo aktualizace dat ve zdrojové tabulce
> * Vytvoření, spuštění a monitorování kanálu přírůstkového kopírování

## <a name="overview"></a>Přehled
V řešení integrace dat je přírůstkové načítání dat po počátečním načtení dat často používaný scénář. V některých případech je změněná data ve vašem zdrojovém úložišti dat za určité období snadno rozdělit (například LastModifyTime, CreationTime). V některých případech ale neexistuje žádný explicitní způsob identifikace rozdílových dat od posledního zpracování dat. K identifikaci rozdílových dat je možné využít technologii Change Tracking, kterou podporují úložiště dat, jako je Azure SQL Database a SQL Server.  Tento kurz popisuje využití služby Azure Data Factory s technologií SQL Change Tracking k přírůstkovému načtení rozdílových dat z Azure SQL Database do Azure Blob Storage.  Další konkrétnější informace o technologii SQL Change Tracking najdete v článku věnovaném [sledování změn na SQL Serveru](/sql/relational-databases/track-changes/about-change-tracking-sql-server). 

## <a name="end-to-end-workflow"></a>Ucelený pracovní postup
Tady jsou obvyklé kroky uceleného pracovního postupu pro přírůstkové načtení dat s využitím technologie Change Tracking.

> [!NOTE]
> Azure SQL Database i SQL Server podporují technologii Change Tracking. Tento kurz využívá Azure SQL Database jako zdrojové úložiště dat. Můžete také využít místní SQL Server. 

1. **Počáteční načtení historických dat** (spouští se jednou):
    1. Povolte technologii Change Tracking ve zdrojové databázi Azure SQL.
    2. Získejte počáteční hodnotu SYS_CHANGE_VERSION v databázi Azure SQL jako základ pro zaznamenávání změněných dat.
    3. Načtěte kompletní data z Azure SQL Database do Azure Blob Storage. 
2. **Přírůstkové načítání rozdílových dat podle plánu** (spouští se pravidelně po počátečním načtení dat):
    1. Získejte starou a novou hodnotu SYS_CHANGE_VERSION.
    3. Načtěte rozdílová data připojením primárních klíčů změněných řádků (mezi dvěma hodnotami SYS_CHANGE_VERSION) ze **sys.change_tracking_tables** k datům ve **zdrojové tabulce** a potom přesuňte rozdílová data do cíle.
    4. Aktualizujte SYS_CHANGE_VERSION pro příští rozdílové načtení.

## <a name="high-level-solution"></a>Řešení na nejvyšší úrovni
V tomto kurzu vytvoříte dva kanály, které provádějí následující dvě operace:  

1. **Počáteční načtení:** Vytvoříte kanál s aktivitou kopírování, která zkopíruje všechna data ze zdrojového úložiště dat (Azure SQL Database) do cílového úložiště dat (Azure Blob Storage).

    ![Úplné načtení dat](media/tutorial-incremental-copy-change-tracking-feature-portal/full-load-flow-diagram.png)
1.  **Přírůstkové načtení:** Vytvoříte kanál s následujícími aktivitami a pravidelně ho budete spouštět. 
    1. Vytvořte **dvě aktivity vyhledávání** pro získání staré a nové hodnoty SYS_CHANGE_VERSION z Azure SQL Database a jejich předání aktivitě kopírování.
    2. Vytvořte **jednu aktivitu kopírování** pro zkopírování vložených/aktualizovaných/odstraněných dat mezi dvěma hodnotami SYS_CHANGE_VERSION z Azure SQL Database do Azure Blob Storage.
    3. Vytvořte **jednu aktivitu uložených procedur** pro aktualizaci hodnoty SYS_CHANGE_VERSION pro další spuštění kanálu.

    ![Diagram toku přírůstkového načtení](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-load-flow-diagram.png)


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
* **Azure SQL Database**. Tuto databázi použijete jako **zdrojové** úložiště dat. Pokud Azure SQL Database nemáte, přečtěte si článek věnovaný [vytvoření databáze Azure SQL](../sql-database/sql-database-get-started-portal.md), kde najdete kroky pro její vytvoření.
* **Účet služby Azure Storage**. Úložiště objektů blob použijete jako úložiště dat **jímky**. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../storage/common/storage-quickstart-create-account.md), kde najdete kroky pro jeho vytvoření. Vytvořte kontejner s názvem **adftutorial**. 

### <a name="create-a-data-source-table-in-your-azure-sql-database"></a>Vytvoření tabulky zdroje dat v databázi Azure SQL
1. Spusťte **SQL Server Management Studio** a připojte se k serveru SQL Azure. 
2. V **Průzkumníku serveru** klikněte pravým tlačítkem na **databázi** a potom zvolte **Nový dotaz**.
3. Spuštěním následujícího příkazu SQL na vaší databázi Azure SQL vytvořte tabulku s názvem `data_source_table` jako úložiště zdroje dat.  
    
    ```sql
    create table data_source_table
    (
        PersonID int NOT NULL,
        Name varchar(255),
        Age int
        PRIMARY KEY (PersonID)
    );

    INSERT INTO data_source_table
        (PersonID, Name, Age)
    VALUES
        (1, 'aaaa', 21),
        (2, 'bbbb', 24),
        (3, 'cccc', 20),
        (4, 'dddd', 26),
        (5, 'eeee', 22);

    ```
4. Povolte mechanismus **Change Tracking** pro vaši databázi a zdrojovou tabulku (data_source_table) spuštěním následujícího příkazu SQL: 

    > [!NOTE]
    > - Místo &lt;your database name&gt; použijte název databáze Azure SQL, která má data_source_table. 
    > - V uvedeném příkladě se změněná data uchovávají po dobu dvou dnů. Pokud načtete změněná data vždy po třech nebo více dnech, některá změněná data nejsou zahrnuta.  Musíte změnit hodnotu CHANGE_RETENTION a použít větší číslo. Další možností je zajistit, že interval načítání změněných dat spadá do dvou dnů. Další informace najdete v tématu věnovaném [povolení sledování změn pro databázi](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server#enable-change-tracking-for-a-database).
 
    ```sql
    ALTER DATABASE <your database name>
    SET CHANGE_TRACKING = ON  
    (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON)  
  
    ALTER TABLE data_source_table
    ENABLE CHANGE_TRACKING  
    WITH (TRACK_COLUMNS_UPDATED = ON)
    ```
5. Vytvořte novou tabulku a uložte ChangeTracking_version s výchozí hodnotou spuštěním následujícího dotazu: 

    ```sql
    create table table_store_ChangeTracking_version
    (
        TableName varchar(255),
        SYS_CHANGE_VERSION BIGINT,
    );

    DECLARE @ChangeTracking_version BIGINT
    SET @ChangeTracking_version = CHANGE_TRACKING_CURRENT_VERSION();  

    INSERT INTO table_store_ChangeTracking_version
    VALUES ('data_source_table', @ChangeTracking_version)
    ```
    
    > [!NOTE]
    > Pokud se data po povolení sledování změn pro SQL Database nezmění, hodnota verze sledování změn je 0.
6. Spuštěním následujícího dotazu ve vaší databázi Azure SQL vytvořte uloženou proceduru. Kanál vyvolá tuto uloženou proceduru pro aktualizaci verze sledování změn v tabulce, kterou jste vytvořili v předchozím kroku. 

    ```sql
    CREATE PROCEDURE Update_ChangeTracking_Version @CurrentTrackingVersion BIGINT, @TableName varchar(50)
    AS
    
    BEGIN
    
        UPDATE table_store_ChangeTracking_version
        SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
    WHERE [TableName] = @TableName
    
    END    
    ```

### <a name="azure-powershell"></a>Azure PowerShell
Nainstalujte nejnovější moduly Azure PowerShellu podle pokynů v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
1. V nabídce vlevo klikněte na **Nový**, klikněte na **Data + analýzy** a pak na **Data Factory**. 
   
   ![Nový -> Datová továrna](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-azure-data-factory-menu.png)
2. Na stránce **Nová datová továrna** jako **název** zadejte **ADFTutorialDataFactory**. 
      
     ![Stránka Nová datová továrna](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-azure-data-factory.png)
 
   Název datové továrny Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba, změňte název objektu pro vytváření dat (třeba na váš_název_ADFTutorialDataFactory) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
       `Data factory name “ADFTutorialDataFactory” is not available`
3. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
4. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
     
      - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
      - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
        Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
4. Jako **verzi** vyberte **V2 (Preview)**.
5. Vyberte **umístění** pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.
6. Zaškrtněte **Připnout na řídicí panel**.     
7. Klikněte na možnost **Vytvořit**.      
8. Na řídicím panelu vidíte následující dlaždice se statusem: **Nasazování datové továrny**. 

    ![nasazování dlaždice datové továrny](media/tutorial-incremental-copy-change-tracking-feature-portal/deploying-data-factory.png)
9. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.
   
   ![Domovská stránka datové továrny](./media/tutorial-incremental-copy-change-tracking-feature-portal/data-factory-home-page.png)
10. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Azure Data Factory.
11. Na stránce **Začínáme** přepněte na levém panelu na kartu **Upravit**, jak je znázorněno na následujícím obrázku: 

    ![Tlačítko Nový kanál](./media/tutorial-incremental-copy-change-tracking-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V datové továrně vytvoříte propojené služby, abyste svá úložiště dat a výpočetní služby spojili s datovou továrnou. V této části vytvoříte propojené služby pro účet Azure Storage a databázi Azure SQL. 

### <a name="create-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage
V tomto kroku s datovou továrnou propojíte svůj účet služby Azure Storage.

1. Klikněte na **Připojení** a pak na **+ Nové**.

   ![Tlačítko Nové připojení](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-connection-button-storage.png)
2. V okně **Nová propojená služba** vyberte **Azure Blob Storage** a klikněte na **Pokračovat**. 

   ![Výběr služby Azure Blob Storage](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-storage.png)
3. V okně **Nová propojená služba** proveďte následující kroky: 

    1. Jako **Název** zadejte **AzureStorageLinkedService**. 
    2. Jako **Název účtu úložiště** vyberte svůj účet služby Azure Storage. 
    3. Klikněte na **Uložit**. 
    
   ![Nastavení účtu služby Azure Storage](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database
V tomto kroku propojíte databázi SQL Azure s datovou továrnou.

1. Klikněte na **Připojení** a pak na **+ Nové**.
2. V okně **Nová propojená služba** vyberte **Azure SQL Database** a klikněte na **Pokračovat**. 
3. V okně **Nová propojená služba** proveďte následující kroky: 

    1. Do pole **Název** zadejte **AzureSqlDatabaseLinkedService**. 
    2. V poli **Název serveru** vyberte váš server SQL Azure.
    4. V poli **Název databáze** vyberte vaši databázi SQL Azure. 
    5. Do pole **Uživatelské jméno** zadejte jméno uživatele. 
    6. Do pole **Heslo** zadejte heslo pro tohoto uživatele. 
    7. Klikněte na **Test připojení** a otestujte připojení.
    8. Klikněte na **Uložit** a uložte propojenou službu. 
    
       ![Nastavení propojené služby Azure SQL Database](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-sql-database-linked-service-settings.png)

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte datové sady, které reprezentují zdroj a cíl dat a místo pro uložení SYS_CHANGE_VERSION.

### <a name="create-a-dataset-to-represent-source-data"></a>Vytvoření datové sady představující zdrojová data 
V tomto kroku vytvoříte datovou sadu pro reprezentaci zdrojových dat. 

1. Ve stromovém zobrazení klikněte na symbol **+ (plus)** a pak klikněte na **Datová sada**. 

   ![Nabídka Nová datová sada](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. Vyberte **Azure SQL Database** a klikněte na **Dokončit**. 

   ![Typ zdrojové datové sady – Azure SQL Database](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-sql-database.png)
3. Zobrazí se nová karta, na které můžete datovou sadu konfigurovat. Datová sada se zobrazí také ve stromovém zobrazení. V okně **Vlastnosti** změňte název datové sady na **SourceDataset**.

   ![Název zdrojové datové sady](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-name.png)    
4. Přepněte na kartu **Připojení** a proveďte následující kroky: 
    
    1. Jako **Propojená služba** vyberte **AzureSqlDatabaseLinkedService**. 
    2. Jako **Tabulka** vyberte **[dbo].[tabulka_zdroje_dat]**. 

   ![Připojení ke zdroji](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-connection.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Vytvořte datovou sadu, která bude představovat data kopírovaná do úložiště dat jímky. 
V tomto kroku vytvoříte datovou sadu pro reprezentaci dat, která se kopírují ze zdrojového úložiště dat. Kontejner adftutorial jste ve službě Azure Blob Storage vytvořili jako součást požadavků. Pokud tento kontejner neexistuje, vytvořte ho nebo použijte název existujícího kontejneru. V tomto kurzu se název výstupního souboru generuje dynamicky pomocí výrazu: `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.

1. Ve stromovém zobrazení klikněte na symbol **+ (plus)** a pak klikněte na **Datová sada**. 

   ![Nabídka Nová datová sada](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. Vyberte **Azure Blob Storage** a klikněte na **Dokončit**. 

   ![Typ datové sady jímky – Azure Blob Storage](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-type.png)
3. Zobrazí se nová karta, na které můžete datovou sadu konfigurovat. Datová sada se zobrazí také ve stromovém zobrazení. V okně **Vlastnosti** změňte název datové sady na **SinkDataset**.

   ![Datová sada jímky – název](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-name.png)
4. V okně Vlastnosti přepněte na kartu **Zdroj** a proveďte následující kroky:

    1. Jako **Propojená služba** vyberte **AzureStorageLinkedService**.
    2. Jako část **složka** v **cestě k souboru** zadejte **adftutorial/incchgtracking**.
    3. Jako část **soubor** v **cestě k souboru** zadejte **@CONCAT('Incremental-', pipeline().RunId, '.txt')**.  

       ![Datová sada jímky – připojení](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-connection.png)

### <a name="create-a-dataset-to-represent-change-tracking-data"></a>Vytvoření datové sady představující data sledování změn 
V tomto kroku vytvoříte datovou sadu pro uložení verze sledování změn.  Tabulku table_store_ChangeTracking_version jste vytvořili jako součást požadavků.

1. Ve stromovém zobrazení klikněte na symbol **+ (plus)** a pak klikněte na **Datová sada**. 
2. Vyberte **Azure SQL Database** a klikněte na **Dokončit**. 
3. Zobrazí se nová karta, na které můžete datovou sadu konfigurovat. Datová sada se zobrazí také ve stromovém zobrazení. V okně **Vlastnosti** změňte název datové sady na **ChangeTrackingDataset**.
4. Přepněte na kartu **Připojení** a proveďte následující kroky: 
    
    1. Jako **Propojená služba** vyberte **AzureSqlDatabaseLinkedService**. 
    2. Jako **Tabulka** vyberte **[dbo].[table_store_ChangeTracking_version]**. 

## <a name="create-a-pipeline-for-the-full-copy"></a>Vytvoření kanálu pro úplné kopírování
V tomto kroku vytvoříte kanál s aktivitou kopírování, která zkopíruje všechna data ze zdrojového úložiště dat (Azure SQL Database) do cílového úložiště dat (Azure Blob Storage).

1. Klikněte na symbol **+ (plus)** v levém podokně a pak klikněte na **Kanál**. 

    ![Nabídka Nový kanál](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu.png)
2. Zobrazí se nová karta, na které můžete kanál konfigurovat. Kanál se zobrazí také ve stromovém zobrazení. V okně **Vlastnosti** změňte název kanálu na **FullCopyPipeline**.

    ![Nabídka Nový kanál](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-name.png)
3. Na panelu nástrojů **Aktivity** rozbalte **Tok dat**, přetáhněte aktivitu **Kopírování** na plochu návrháře kanálu a nastavte její název na **FullCopyActivity**. 

    ![Úplný název aktivity kopírování](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-activity-name.png)
4. Přepněte na kartu **Zdroj** a v poli **Zdrojová datová sada** vyberte **SourceDataset**. 

    ![Aktivita kopírování – zdroj](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-source.png)
5. Přepněte na kartu **Jímka** a v poli **Datová sada jímky** vyberte **SinkDataset**. 

    ![Aktivita kopírování – jímka](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-sink.png)
6. Pokud chcete ověřit definici kanálu, klikněte na **Ověřit** na panelu nástrojů. Ověřte, že se nezobrazí žádná chyba ověření. Zavřete okno **Sestava ověření kanálu** kliknutím na **>>**. 

    ![Ověření kanálu](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-validate.png)
7. Pokud chcete publikovat entity (propojené služby, datové sady a kanály), klikněte na **Publikovat**. Počkejte na úspěšné dokončení publikování. 

    ![Tlačítko Publikovat](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button.png)
8. Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**. 

    ![Publikování proběhlo úspěšně](./media/tutorial-incremental-copy-change-tracking-feature-portal/publishing-succeeded.png)
9. Oznámení můžete zobrazit také kliknutím na tlačítko **Zobrazit oznámení** na levé straně. Pokud chcete okno oznámení zavřít, klikněte na **X**.

    ![Zobrazit oznámení](./media/tutorial-incremental-copy-change-tracking-feature-portal/show-notifications.png)


### <a name="run-the-full-copy-pipeline"></a>Spuštění kanálu úplného kopírování
Klikněte na **Aktivační událost** na panelu nástrojů pro kanál a pak klikněte na **Aktivovat**. 

![Nabídka Aktivovat](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu.png)

### <a name="monitor-the-full-copy-pipeline"></a>Monitorování kanálu úplného kopírování

1. Klikněte na kartu **Monitorování** na levé straně. V seznamu se zobrazí spuštění kanálu a jeho stav. Pokud chcete seznam aktualizovat, klikněte na **Aktualizovat**. Pomocí odkazů ve sloupci Akce můžete zobrazit spuštění aktivit související se spuštěním kanálu nebo spustit kanál znovu. 

    ![Spuštění kanálu](./media/tutorial-incremental-copy-change-tracking-feature-portal/monitor-full-copy-pipeline-run.png)
2. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Kanál obsahuje pouze jednu aktivitu, takže se v seznamu zobrazí pouze jedna položka. Pokud chcete přepnout zpět na zobrazení spuštění kanálu, klikněte na odkaz **Kanály** v horní části. 

    ![Spuštění aktivit](./media/tutorial-incremental-copy-change-tracking-feature-portal/activity-runs-full-copy.png)

### <a name="review-the-results"></a>Kontrola výsledků
Ve složce `incchgtracking` kontejneru `adftutorial` uvidíte soubor s názvem `incremental-<GUID>.txt`. 

![Výstupní soubor pro úplné kopírování](media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-output-file.png)

Tento soubor by měl obsahovat data z databáze SQL Azure:

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>Přidání dalších dat do zdrojové tabulky

Spusťte na databázi Azure SQL následující dotaz pro přidání a aktualizaci řádku. 

```sql
INSERT INTO data_source_table
(PersonID, Name, Age)
VALUES
(6, 'new','50');


UPDATE data_source_table
SET [Age] = '10', [name]='update' where [PersonID] = 1

``` 

## <a name="create-a-pipeline-for-the-delta-copy"></a>Vytvoření kanálu pro rozdílové kopírování
V tomto kroku vytvoříte kanál s následujícími aktivitami a pravidelně ho budete spouštět. **Aktivity vyhledávání** získají starou a novou hodnoty SYS_CHANGE_VERSION z Azure SQL Database a předají je aktivitě kopírování. **Aktivita kopírování** zkopíruje vložená/aktualizovaná/odstraněná data mezi dvěma hodnotami SYS_CHANGE_VERSION z Azure SQL Database do Azure Blob Storage. **Aktivita uložených procedur** aktualizuje hodnotu SYS_CHANGE_VERSION pro další spuštění kanálu.

1. V uživatelském prostředí služby Data Factory přepněte na kartu **Upravit**. Klikněte na symbol **+ (plus)** v levém podokně a pak klikněte na **Kanál**. 

    ![Nabídka Nový kanál](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu-2.png)
2. Zobrazí se nová karta, na které můžete kanál konfigurovat. Kanál se zobrazí také ve stromovém zobrazení. V okně **Vlastnosti** změňte název kanálu na **IncrementalCopyPipeline**.

    ![Název kanálu](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-pipeline-name.png)
3. V sadě nástrojů **Aktivity** rozbalte **Obecné** a přetáhněte aktivitu **Vyhledávání** na plochu návrháře kanálu. Nastavte název aktivity na **LookupLastChangeTrackingVersionActivity**. Tato aktivita získá verzi sledování změn použitou v poslední operaci kopírování uložené v tabulce **table_store_ChangeTracking_version**.

    ![Aktivita vyhledávání – název](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-name.png)
4. V okně **Vlastnosti** přepněte na kartu **Nastavení** a jako **Zdrojová datová sada** zadejte **ChangeTrackingDataset**. 

    ![Aktivita vyhledávání – nastavení](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-settings.png)
5. Přetáhněte aktivitu **Vyhledávání** z panelu nástrojů **Aktivity** na plochu návrháře kanálu. Nastavte název aktivity na **LookupCurrentChangeTrackingVersionActivity**. Tato aktivita získá aktuální verzi sledování změn.

    ![Aktivita vyhledávání – název](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-name.png)
6. V okně **Vlastnosti** přepněte na kartu **Nastavení** a proveďte následující kroky:

    1. V poli **Zdrojová datová sada** vyberte **SourceDataset**.
    2. Jako **Použít dotaz** vyberte **Dotaz**. 
    3. Jako **Dotaz** zadejte následující příkaz jazyka SQL. 

        ```sql
        SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion
        ```

    ![Aktivita vyhledávání – nastavení](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-settings.png)
7. Na panelu nástrojů **Aktivity** rozbalte **Tok dat** a přetáhněte aktivitu **Kopírování** na plochu návrháře kanálu. Nastavte název aktivity na **IncrementalCopyActivity**. Tato aktivita kopírujte data mezi poslední verzí sledování změn a aktuální verzí sledování změn pouze do cílového úložiště dat. 

    ![Aktivita kopírování – název](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-activity-name.png)
8. V okně **Vlastnosti** přepněte na kartu **Zdroj** a proveďte následující kroky:

    1. Jako **Zdrojová datová sada** vyberte **SourceDataset**. 
    2. Jako **Použít dotaz** vyberte **Dotaz**. 
    3. Jako **Dotaz** zadejte následující příkaz jazyka SQL. 

        ```sql
        select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}
        ```
    
    ![Aktivita kopírování – nastavení zdroje](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-source-settings.png)
9. Přepněte na kartu **Jímka** a v poli **Datová sada jímky** vyberte **SinkDataset**. 

    ![Aktivita jímky – nastavení jímky](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-sink-settings.png)
10. Jednu po druhé **propojte obě aktivity vyhledávání s aktivitou kopírování**. Přetáhněte **zelené** tlačítko připojené k aktivitě **Vyhledávání** do aktivity **Kopírování**. 

    ![Propojení aktivit vyhledávání a kopírování](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-lookup-and-copy.png)
11. Přetáhněte aktivitu **Uložená procedura** z panelu nástrojů **Aktivity** na plochu návrháře kanálu. Nastavte název aktivity na **StoredProceduretoUpdateChangeTrackingActivity**. Tato aktivita aktualizuje verzi sledování změn v tabulce **table_store_ChangeTracking_version**.

    ![Aktivita Uložená procedura – název](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-activity-name.png)
12. Přepněte na kartu *Účet SQL** a jako **Propojená služba** vyberte **AzureSqlDatabaseLinkedService**. 

    ![Aktivita Uložená procedura – účet SQL](./media/tutorial-incremental-copy-change-tracking-feature-portal/sql-account-tab.png)
13. Přepněte na kartu **Uložená procedura** a proveďte následující kroky: 

    1. Jako **Název uložené procedury** vyberte **Update_ChangeTracking_Version**.  
    2. Vyberte **Importovat parametr**. 
    3. V části **Parametry uložené procedury** zadejte následující hodnoty parametrů: 

        | Název | Typ | Hodnota | 
        | ---- | ---- | ----- | 
        | CurrentTrackingVersion | Int64 | @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion} | 
        | TableName | Řetězec | @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName} | 
    
        ![Aktivita Uložená procedura – parametry](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-parameters.png)
14. **Připojte aktivitu kopírování k aktivitě uložené procedury**. Přetáhněte **zelené** tlačítko připojené k aktivitě Kopírování do aktivity Uložená procedura. 

    ![Propojení aktivit Kopírování a Uložená procedura](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-copy-stored-procedure.png)
15. Klikněte na **Ověřit** na panelu nástrojů. Ověřte, že se nezobrazí žádné chyby ověření. Zavřete okno **Sestava ověření kanálu** kliknutím na **>>**. 

    ![Tlačítko Ověřit](./media/tutorial-incremental-copy-change-tracking-feature-portal/validate-button.png)
16.  Kliknutím na tlačítko **Publikovat vše** publikujte entity (propojené služby, datové sady a kanály) do služby Data Factory. Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**. 

        ![Tlačítko Publikovat](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button-2.png)    

### <a name="run-the-incremental-copy-pipeline"></a>Spuštění kanálu přírůstkového kopírování
1. Klikněte na **Aktivační událost** na panelu nástrojů pro kanál a pak klikněte na **Aktivovat**. 

    ![Nabídka Aktivovat](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu-2.png)
2. V okně **Spuštění kanálu** vyberte **Dokončit**.

### <a name="monitor-the-incremental-copy-pipeline"></a>Monitorování kanálu přírůstkového kopírování
1. Klikněte na kartu **Monitorování** na levé straně. V seznamu se zobrazí spuštění kanálu a jeho stav. Pokud chcete seznam aktualizovat, klikněte na **Aktualizovat**. Pomocí odkazů ve sloupci **Akce** můžete zobrazit spuštění aktivit související se spuštěním kanálu nebo spustit kanál znovu. 

    ![Spuštění kanálu](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-pipeline-runs.png)
2. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Kanál obsahuje pouze jednu aktivitu, takže se v seznamu zobrazí pouze jedna položka. Pokud chcete přepnout zpět na zobrazení spuštění kanálu, klikněte na odkaz **Kanály** v horní části. 

    ![Spuštění aktivit](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-activity-runs.png)


### <a name="review-the-results"></a>Kontrola výsledků
Ve složce `incchgtracking` kontejneru `adftutorial` uvidíte druhý soubor. 

![Výstupní soubor pro přírůstkové kopírování](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-output-file.png)

Tento soubor by měl obsahovat jenom rozdílová data z databáze SQL Azure. Záznam s `U` je aktualizovaný řádek v databázi a `I` je přidaný řádek. 

```
1,update,10,2,U
6,new,50,1,I
```
První tři sloupce představují změněná data z data_source_table. Poslední dva sloupce jsou metadata ze systémové tabulky sledování změn. Čtvrtý sloupec je SYS_CHANGE_VERSION pro každý změněný řádek. Pátý řádek představuje operaci: U = aktualizace, I = vložení.  Podrobné informace o sledování změn najdete v tématu [CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql). 

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10     2                     U
6        new     50     1                     I
```

    
## <a name="next-steps"></a>Další kroky
Pokud se chcete dozvědět víc o transformaci dat pomocí clusteru Spark v Azure, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Transformace dat pomocí clusteru Spark v cloudu](tutorial-transform-data-spark-portal.md)



