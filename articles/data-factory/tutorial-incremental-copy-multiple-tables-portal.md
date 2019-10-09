---
title: Přírůstkové kopírování více tabulek pomocí Azure Data Factory | Microsoft Docs
description: V tomto kurzu vytvoříte kanál Azure Data Factory, který postupně kopíruje rozdílová data z několika tabulek v místní databázi SQL Server do Azure SQL Database.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/20/2018
ms.author: yexu
ms.openlocfilehash: 44ae433040c2c9cab47567cb663d4e588311a4a1
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177419"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Přírůstkové načtení dat z více tabulek v SQL Server do Azure SQL Database
V tomto kurzu vytvoříte datovou továrnu Azure s kanálem, který načte rozdílová data z několika tabulek v místním SQL Server do Azure SQL Database.    

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Připravte zdrojové a cílové úložiště dat.
> * Vytvořte datovou továrnu.
> * Vytvořte modul runtime integrace v místním prostředí.
> * Nainstalujte modul runtime integrace. 
> * Vytvořte propojené služby. 
> * Vytvoření zdroje, jímky a datových sad vodoznaku.
> * Vytvoření, spuštění a monitorování kanálu.
> * Zkontrolujte výsledky.
> * Přidání nebo aktualizace dat ve zdrojových tabulkách.
> * Znovu spusťte a Sledujte kanál.
> * Zkontrolujte konečné výsledky.

## <a name="overview"></a>Přehled
Tady jsou důležité kroky pro vytvoření tohoto řešení: 

1. **Vyberte sloupec meze**.
    
    Vyberte jeden sloupec pro každou tabulku ve zdrojovém úložišti dat, která se dá použít k identifikaci nových nebo aktualizovaných záznamů pro každé spuštění. Data v tomto vybraném sloupci (například last_modify_time nebo ID) se normálně při vytváření nebo aktualizaci řádků stále zvyšují. Maximální hodnota v tomto sloupci se používá jako meze.

1. **Připravte úložiště dat pro uložení hodnoty meze**.   
    
    V tomto kurzu uložíte hodnotu meze do databáze SQL.

1. **Vytvořte kanál s následujícími aktivitami**: 
    
    a. Vytvořte aktivitu ForEach, která prochází seznam názvů zdrojových tabulek, které jsou předány jako parametr kanálu. Pro každou zdrojovou tabulku vyvolá následující aktivity pro provádění rozdílového načítání pro tuto tabulku.

    b. Vytvořte dvě aktivity vyhledávání. První aktivitu vyhledávání použijte k načtení poslední hodnoty meze. Druhou aktivitu vyhledávání použijte k načtení nové hodnoty meze. Tyto hodnoty meze se předávají aktivitě kopírování.

    r. Vytvořte aktivitu kopírování, která kopíruje řádky ze zdrojového úložiště dat s hodnotou sloupce meze větší, než je původní hodnota meze a menší, než je nová hodnota meze. Pak zkopíruje rozdílová data ze zdrojového úložiště dat do úložiště objektů BLOB v Azure jako nový soubor.

    trojrozměrné. Vytvořte aktivitu StoredProcedure, která aktualizuje hodnotu meze pro kanál, který se bude spouštět příště. 

    Tady je diagram řešení vysoké úrovně: 

    ![Přírůstkové načtení dat](media/tutorial-incremental-copy-multiple-tables-portal/high-level-solution-diagram.png)


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
* **SQL Server**. V tomto kurzu použijete místní databázi SQL Server jako zdrojové úložiště dat. 
* **Azure SQL Database**. Databázi SQL používáte jako úložiště dat jímky. Pokud databázi SQL nemáte, přečtěte si téma [Vytvoření databáze SQL Azure](../sql-database/sql-database-get-started-portal.md) , kde najdete kroky pro její vytvoření. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Vytváření zdrojových tabulek v databázi SQL Server

1. Otevřete SQL Server Management Studio a připojte se k místní databázi SQL Server.

1. V **Průzkumník serveru**klikněte pravým tlačítkem na databázi a vyberte **Nový dotaz**.

1. Spuštěním následujícího příkazu SQL v databázi vytvořte tabulky s názvem `customer_table` a `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Vytvoření cílových tabulek v databázi SQL Azure
1. Otevřete SQL Server Management Studio a připojte se k databázi SQL Azure.

1. V **Průzkumník serveru**klikněte pravým tlačítkem na databázi a vyberte **Nový dotaz**.

1. Spusťte následující příkaz SQL pro vaši databázi SQL a vytvořte tabulky s názvem `customer_table` a `project_table`:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Vytvoření další tabulky v databázi SQL Azure za účelem uložení hodnoty horní meze
1. Spuštěním následujícího příkazu SQL pro vaši databázi SQL vytvořte tabulku s názvem `watermarktable` pro uložení hodnoty meze: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. Do tabulky meze vložte počáteční hodnoty meze pro obě zdrojové tabulky.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Vytvoření uložené procedury ve službě Azure SQL Database 

Spuštěním následujícího příkazu vytvořte v databázi SQL uloženou proceduru. Tato uložená procedura aktualizuje hodnotu meze po každém spuštění kanálu. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Vytvoření datových typů a dalších uložených procedur ve službě Azure SQL Database
Spusťte následující dotaz pro vytvoření dvou uložených procedur a dvou datových typů ve vaší databázi SQL. Používají se ke sloučení dat ze zdrojových tabulek do cílových tabulek.

Aby bylo možné cestu snadno začít používat, přímo tyto uložené procedury předají rozdílová data v rámci přes proměnnou tabulky a pak je sloučí do cílového úložiště. Buďte opatrní, protože neočekává "velký" počet rozdílových řádků (více než 100), které se mají Uložit do proměnné tabulky.  

Pokud potřebujete sloučit velký počet rozdílových řádků do cílového úložiště, doporučujeme použít aktivitu kopírování ke zkopírování všech rozdílových dat do dočasné tabulky "fázování" v cílovém úložišti a pak vytvořit vlastní uloženou proceduru bez použití tabulky VARI. je možné je sloučit z tabulky "fázování" do "konečné" tabulky. 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome** . V současné době je Data Factory uživatelské rozhraní podporováno pouze ve webových prohlížečích Microsoft Edge a Google Chrome.
1. V nabídce vlevo klikněte na **Nový** , klikněte na **data a analýzy**a pak klikněte na **Data Factory**. 
   
   ![New-> DataFactory](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory-menu.png)
1. Na stránce **Nová datová továrna** jako **název**zadejte **ADFMultiIncCopyTutorialDF** . 
      
     ![Stránka Nová datová továrna](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory.png)
 
   Název objektu pro vytváření dat Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba, změňte název objektu pro vytváření dat (třeba na yournameADFMultiIncCopyTutorialDF) a zkuste to znovu. Pravidla pojmenování pro Data Factory artefakty najdete v článku [pravidla pro Pojmenovávání Data Factory](naming-rules.md) .
  
       `Data factory name ADFMultiIncCopyTutorialDF is not available`
1. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
1. Pro **skupinu prostředků**proveďte jeden z následujících kroků:
     
      - Vyberte **použít existující**a z rozevíracího seznamu vyberte existující skupinu prostředků. 
      - Vyberte **vytvořit novou**a zadejte název skupiny prostředků.   
         
        Další informace o skupinách prostředků najdete v tématu [použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
1. Jako **verzi**vyberte **v2 (Preview)** .
1. Vyberte **umístění** pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou můžou být v jiných oblastech.
1. Vyberte **Připnout na řídicí panel**.     
1. Klikněte na **vytvořit**.      
1. Na řídicím panelu se zobrazí následující dlaždice se stavem: **nasazování datové továrny**. 

    ![dlaždice nasazování datové továrny](media/tutorial-incremental-copy-multiple-tables-portal/deploying-data-factory.png)
1. Po dokončení vytváření se zobrazí stránka **Data Factory** , jak je znázorněno na obrázku.
   
   ![Domovská stránka datové továrny](./media/tutorial-incremental-copy-multiple-tables-portal/data-factory-home-page.png)
1. Klikněte na dlaždici **vytvořit & monitorování** a na samostatné kartě spusťte Azure Data Factory uživatelské rozhraní (UI).
1. Na stránce Začínáme v uživatelském rozhraní Azure Data Factory klikněte na **vytvořit kanál** (nebo) přepněte na kartu **Upravit** . 

   ![Stránka Začínáme](./media/tutorial-incremental-copy-multiple-tables-portal/get-started-page.png)

## <a name="create-self-hosted-integration-runtime"></a>Vytvoření prostředí Integration runtime v místním prostředí
Když přesouváte data z úložiště dat v privátní síti (místně) do úložiště dat Azure, nainstalujte v místním prostředí místní prostředí Integration runtime (IR). Místní prostředí IR přesouvá data mezi vaší privátní sítí a Azure. 

1. Klikněte na **připojení** v dolní části levého podokna a přepněte se do **prostředí Integration runtime** v okně **připojení** . 

   ![Karta připojení](./media/tutorial-incremental-copy-multiple-tables-portal/connections-tab.png)
1. Na kartě **prostředí Integration runtime** klikněte na **+ Nový**. 

   ![Nový modul runtime integrace – tlačítko](./media/tutorial-incremental-copy-multiple-tables-portal/new-integration-runtime-button.png)
1. V okně **nastavení Integration runtime** vyberte možnost **provést přesun dat a odeslat aktivity externím výpočtům**a klikněte na tlačítko **Další**. 

   ![Vybrat typ prostředí Integration runtime](./media/tutorial-incremental-copy-multiple-tables-portal/select-integration-runtime-type.png)
1. Vyberte * * privátní síť * * a klikněte na **Další**. 

   ![Vybrat privátní síť](./media/tutorial-incremental-copy-multiple-tables-portal/select-private-network.png)
1. Jako **název**zadejte **MySelfHostedIR** a klikněte na **Další**. 

   ![Název místního prostředí IR](./media/tutorial-incremental-copy-multiple-tables-portal/self-hosted-ir-name.png)
1. Kliknutím **na tlačítko Kliknutím sem spustíte expresní instalaci pro tento počítač** v části **možnost 1: Expresní instalace** . 

   ![Klikněte na odkaz expresní nastavení.](./media/tutorial-incremental-copy-multiple-tables-portal/click-express-setup.png)
1. V okně **instalace Integration runtime (v místním prostředí) Express** klikněte na **Zavřít**. 

   ![Instalace prostředí Integration runtime – úspěšné](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
1. Ve webovém prohlížeči v okně **instalace Integration runtime** klikněte na **Dokončit**. 

   ![Instalace prostředí Integration runtime – dokončení](./media/tutorial-incremental-copy-multiple-tables-portal/click-finish-integration-runtime-setup.png)
1. Ověřte, že se v seznamu prostředí Integration runtime zobrazí **MySelfHostedIR** .

    ![Prostředí Integration runtime – seznam](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtimes-list.png)

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V datové továrně vytvoříte propojené služby, které propojí úložiště dat a výpočetní služby s datovou továrnou. V této části vytvoříte propojené služby pro místní SQL Server databázi a SQL Database. 

### <a name="create-the-sql-server-linked-service"></a>Vytvoření propojené služby SQL Server
V tomto kroku propojíte místní databázi SQL Server s datovou továrnou.

1. V okně **připojení** přepněte z karty **Integration runtime** na kartu **propojené služby** a klikněte na **+ Nový**.

    ![Tlačítko Nová propojená služba](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
1. V okně **Nová propojená služba** vyberte **SQL Server**a klikněte na **pokračovat**. 

    ![Vyberte SQL Server](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server.png)
1. V okně **Nová propojená služba** proveďte následující kroky:

    1. Jako **název**zadejte **SqlServerLinkedService** . 
    1. Jako připojení vyberte **MySelfHostedIR** **prostřednictvím prostředí Integration runtime**. Toto je **důležitý** krok. Výchozí prostředí Integration runtime se nemůže připojit k místnímu úložišti dat. Použijte místně hostovaný prostředí Integration runtime, které jste vytvořili dříve. 
    1. Jako **název serveru**zadejte název počítače, který má databázi SQL Server.
    1. Do pole **název databáze**zadejte název databáze ve vašem SQL Server, která má zdrojová data. Tabulku jste vytvořili a do této databáze jste vložili data jako součást požadavků. 
    1. Jako **typ ověřování**vyberte **typ ověřování** , který chcete použít pro připojení k databázi. 
    1. Do pole **uživatelské jméno**zadejte jméno uživatele, který má přístup k databázi SQL Server. Pokud v názvu uživatelského účtu nebo serveru potřebujete použít znak lomítka (`\`), použijte řídicí znak (`\`). Příklad je `mydomain\\myuser`.
    1. Jako **heslo**zadejte **heslo** pro uživatele. 
    1. Chcete-li otestovat, zda Data Factory se může připojit k databázi SQL Server, klikněte na tlačítko **Test připojení**. Opravte všechny chyby, dokud nebude připojení úspěšné. 
    1. Pokud chcete propojenou službu uložit, klikněte na **Uložit**.

        ![Propojená služba SQL Server – nastavení](./media/tutorial-incremental-copy-multiple-tables-portal/sql-server-linked-service-settings.png)

### <a name="create-the-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database
V posledním kroku vytvoříte propojenou službu, která propojí vaši zdrojovou SQL Server databázi s datovou továrnou. V tomto kroku propojíte svou cílovou databázi Azure SQL Database s daty a datovou jímku do objektu pro vytváření dat. 

1. V okně **připojení** přepněte z karty **Integration runtime** na kartu **propojené služby** a klikněte na **+ Nový**.

    ![Tlačítko Nová propojená služba](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
1. V okně **Nová propojená služba** vyberte **Azure SQL Database**a klikněte na **pokračovat**. 
1. V okně **Nová propojená služba** proveďte následující kroky:

    1. Jako **název**zadejte **AzureSqlDatabaseLinkedService** . 
    1. V poli **název serveru**vyberte z rozevíracího seznamu název vašeho serveru SQL Azure. 
    1. V poli **název databáze**vyberte Azure SQL Database, ve které jste v rámci požadavků vytvořili customer_table a project_table. 
    1. Do pole **uživatelské jméno**zadejte jméno uživatele, který má přístup k databázi SQL Azure. 
    1. Jako **heslo**zadejte **heslo** pro uživatele. 
    1. Chcete-li otestovat, zda Data Factory se může připojit k databázi SQL Server, klikněte na tlačítko **Test připojení**. Opravte všechny chyby, dokud nebude připojení úspěšné. 
    1. Pokud chcete propojenou službu uložit, klikněte na **Uložit**.

        ![Propojená služba Azure SQL – nastavení](./media/tutorial-incremental-copy-multiple-tables-portal/azure-sql-linked-service-settings.png)
1. Ověřte, že se v seznamu zobrazují dvě propojené služby. 
   
    ![Dvě propojené služby](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte datové sady, které reprezentují zdroj dat, cíl dat a místo pro uložení meze.

### <a name="create-a-source-dataset"></a>Vytvoření zdrojové datové sady

1. V levém podokně klikněte na **+ (plus)** a pak klikněte na **datová sada**.

   ![Nabídka Nová datová sada](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. V okně **Nová datová sada** vyberte **SQL Server**, klikněte na **Dokončit**. 

   ![Vyberte SQL Server](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server-for-dataset.png)
1. Ve webovém prohlížeči se zobrazí nová karta pro konfiguraci datové sady. Datová sada se zobrazí také ve stromovém zobrazení. Na kartě **Obecné** v okno Vlastnosti dole jako **název**zadejte **SourceDataset** . 

   ![Zdrojová datová sada – název](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-general.png)
1. V okno Vlastnosti přepněte na kartu **připojení** a jako **propojená služba**vyberte **SqlServerLinkedService** . Nebudete zde vybírat tabulku. Aktivita kopírování v kanálu používá k načtení dat dotaz SQL místo načtení celé tabulky.

   ![Zdrojová datová sada – připojení](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Vytvoření datové sady jímky
1. V levém podokně klikněte na **+ (plus)** a pak klikněte na **datová sada**.

   ![Nabídka Nová datová sada](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. V okně **Nová datová sada** vyberte **Azure SQL Database**a klikněte na **Dokončit**. 

   ![Vyberte Azure SQL Database](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
1. Ve webovém prohlížeči se zobrazí nová karta pro konfiguraci datové sady. Datová sada se zobrazí také ve stromovém zobrazení. Na kartě **Obecné** v okno Vlastnosti dole jako **název**zadejte **SinkDataset** .

   ![Datová sada jímky – obecné](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-general.png)
1. V okno Vlastnosti přepněte na kartu **parametry** a proveďte následující kroky: 

    1. V části **vytvořit nebo aktualizovat parametry** klikněte na **Nový** . 
    1. Jako **název**zadejte **SinkTableName** a **řetězec** pro **typ**. Tato datová sada používá **SinkTableName** jako parametr. Parametr SinkTableName se dynamicky nastavuje v rámci kanálu za běhu. Aktivita ForEach v kanálu prochází seznam názvů tabulek a předá do této datové sady v každé iteraci název tabulky.
   
       ![Datová sada jímky – vlastnosti](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)
1. V okno Vlastnosti přepněte na kartu **připojení** a jako **propojená služba**vyberte **AzureSqlLinkedService** . V případě vlastnosti **tabulka** klikněte na **Přidat dynamický obsah**. 

   ![Datová sada jímky – připojení](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection.png)
    
    
1. V části Parameters ( **parametry** ) vyberte **SinkTableName**
   
   ![Datová sada jímky – připojení](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-dynamicContent.png)

   
 1. Po kliknutí na **Dokončit**se zobrazí **\@dataset (). SinkTableName** jako název tabulky.
   
   ![Datová sada jímky – připojení](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-completion.png)

### <a name="create-a-dataset-for-a-watermark"></a>Vytvoření datové sady pro vodoznak
V tomto kroku vytvoříte datovou sadu pro uložení hodnoty horní meze. 

1. V levém podokně klikněte na **+ (plus)** a pak klikněte na **datová sada**.

   ![Nabídka Nová datová sada](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. V okně **Nová datová sada** vyberte **Azure SQL Database**a klikněte na **Dokončit**. 

   ![Vyberte Azure SQL Database](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
1. Na kartě **Obecné** v okno Vlastnosti dole jako **název**zadejte **WatermarkDataset** .
1. Přepněte na kartu **připojení** a proveďte následující kroky: 

    1. Jako **propojená služba**vyberte **AzureSqlDatabaseLinkedService** .
    1. Vyberte **[dbo]. [ vodotisk]** pro **tabulku**.

       ![Datová sada meze – připojení](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>Vytvoření kanálu
Kanál jako parametr převezme seznam názvů tabulek. Aktivita ForEach prochází seznam názvů tabulek a provádí následující operace: 

1. Aktivitu vyhledávání použijte k načtení původní hodnoty meze (počáteční hodnota nebo ta, která byla použita v poslední iteraci).

1. Aktivitu vyhledávání použijte k načtení nové hodnoty meze (maximální hodnota sloupce meze ve zdrojové tabulce).

1. Aktivitu kopírování použijte ke kopírování dat mezi těmito dvěma hodnotami meze ze zdrojové databáze do cílové databáze.

1. Aktivitu StoredProcedure použijte k aktualizaci staré hodnoty meze, která se má použít v prvním kroku další iterace. 

### <a name="create-the-pipeline"></a>Vytvoření kanálu

1. V levém podokně klikněte na **+ (plus)** a pak klikněte na **kanál**.

    ![Nový kanál – nabídka](./media/tutorial-incremental-copy-multiple-tables-portal/new-pipeline-menu.png)
1. Na kartě **Obecné** v okně **vlastnosti** jako **název**zadejte **IncrementalCopyPipeline** . 

    ![Název kanálu](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-name.png)
1. V okně **vlastnosti** proveďte následující kroky: 

    1. Klikněte na **+ Nový**. 
    1. Jako **název**parametru zadejte **tableList** . 
    1. Jako **typ**parametru vyberte **objekt** .

    ![Parametry kanálu](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-parameters.png) 
1. Na panelu nástrojů **aktivity** rozbalte položku **iterace & podmíněného**zpracování a přetáhněte aktivitu **foreach** na plochu návrháře kanálu. Na kartě **Obecné** v okně **vlastnosti** zadejte **IterateSQLTables**. 

    ![Aktivita ForEach – název](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-name.png)
1. V okně **vlastnosti** přepněte na kartu **Nastavení** a jako **položky**zadejte `@pipeline().parameters.tableList`. Aktivita ForEach prochází seznam tabulek a provádí operaci přírůstkového kopírování. 

    ![Aktivita ForEach – nastavení](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)
1. Vyberte aktivitu **foreach** v kanálu, pokud ještě není vybraná. Klikněte na tlačítko **Upravit (ikona tužky)** .

    ![Aktivita ForEach – upravit](./media/tutorial-incremental-copy-multiple-tables-portal/edit-foreach.png)
1. Na panelu nástrojů **aktivity** rozbalte **Obecné**, přetáhněte aktivitu **vyhledávání** na plochu návrháře kanálu a jako **název**zadejte **LookupOldWaterMarkActivity** .

    ![První aktivita vyhledávání – název](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-name.png)
1. V okně **vlastnosti** přepněte na kartu **Nastavení** a proveďte následující kroky: 

    1. Jako **zdrojovou datovou sadu**vyberte **WatermarkDataset** .
    1. Vyberte **dotaz** pro **použití dotazu**. 
    1. Pro **dotaz**zadejte následující dotaz SQL. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![První aktivita vyhledávání – nastavení](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
1. Přetáhněte aktivitu **vyhledávání** z panelu nástrojů **aktivity** a jako **název**zadejte **LookupNewWaterMarkActivity** .
        
    ![Druhá aktivita vyhledávání – název](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-name.png)
1. Přepněte na kartu **Nastavení** .

    1. Jako **zdrojovou datovou sadu**vyberte **SourceDataset** . 
    1. Vyberte **dotaz** pro **použití dotazu**.
    1. Pro **dotaz**zadejte následující dotaz SQL.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![Druhá aktivita vyhledávání – nastavení](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
1. Přetáhněte aktivitu **kopírování** z panelu nástrojů **aktivity** a jako **název**zadejte **IncrementalCopyActivity** . 

    ![Aktivita kopírování – název](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-name.png)
1. Propojte aktivity **vyhledávání** s aktivitou **kopírování** jednu po jedné. Pokud se chcete připojit, začněte přetahovat na **zelené** pole připojené k aktivitě **vyhledávání** a přetáhnout ho na aktivitu **kopírování** . Pokud se barva ohraničení aktivity kopírování změní na **modrou**, uvolněte tlačítko myši.

    ![Propojit aktivity vyhledávání s aktivitou kopírování](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
1. Vyberte aktivitu **kopírování** v kanálu. V okně **vlastnosti** přepněte na kartu **zdroj** . 

    1. Jako **zdrojovou datovou sadu**vyberte **SourceDataset** . 
    1. Vyberte **dotaz** pro **použití dotazu**. 
    1. Pro **dotaz**zadejte následující dotaz SQL.

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![Aktivita kopírování – nastavení zdroje](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
1. Přepněte na kartu **jímka** a jako **datovou sadu jímky**vyberte **SinkDataset** . 
        
    ![Aktivita kopírování – nastavení jímky](./media/tutorial-incremental-copy-multiple-tables-portal/copy-sink-settings.png)
1. Proveďte následující kroky:

    1. Do vlastnosti **DataSet** pro parametr **SinkTableName** zadejte `@{item().TABLE_NAME}`.
    1. Jako vlastnost **název uložené procedury** zadejte `@{item().StoredProcedureNameForMergeOperation}`.
    1. Do vlastnosti **typ tabulky** zadejte `@{item().TableType}`.


        ![Aktivita kopírování – parametry](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
1. Přetáhněte aktivitu **uložená procedura** z panelu nástrojů **aktivity** na plochu návrháře kanálu. Připojte aktivitu **kopírování** k aktivitě **uložené procedury** . 

    ![Aktivita kopírování – parametry](./media/tutorial-incremental-copy-multiple-tables-portal/connect-copy-to-sproc.png)
1. Vyberte aktivitu **uložená procedura** v kanálu a jako **název** zadejte **StoredProceduretoWriteWatermarkActivity** na kartě **Obecné** v okně **vlastnosti** . 

    ![Aktivita uložená procedura – název](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-name.png)
1. Přepněte na kartu **účet SQL** a jako **propojená služba**vyberte **AzureSqlDatabaseLinkedService** .

    ![Aktivita uložených procedur – účet SQL](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
1. Přepněte na kartu **uložená procedura** a proveďte následující kroky:

    1. Jako **název uložené procedury**vyberte `usp_write_watermark`. 
    1. Vyberte možnost **importovat parametr**. 
    1. Zadejte následující hodnoty parametrů: 

        | Name | Typ | Hodnota | 
        | ---- | ---- | ----- |
        | Časposledníúpravy | DateTime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | Tabulky | String | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Aktivita uložených procedur – nastavení uložených procedur](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
1. V levém podokně klikněte na **publikovat**. Tato akce publikuje entity, které jste vytvořili ve službě Data Factory. 

    ![Tlačítko publikovat](./media/tutorial-incremental-copy-multiple-tables-portal/publish-button.png)
1. Počkejte, dokud se nezobrazí zpráva o **úspěšném publikování** . Pokud se chcete podívat na oznámení, klikněte na odkaz **Zobrazit oznámení** . Kliknutím na **X**zavřete okno oznámení.

    ![Zobrazit oznámení](./media/tutorial-incremental-copy-multiple-tables-portal/notifications.png)

 
## <a name="run-the-pipeline"></a>Spuštění kanálu

1. Na panelu nástrojů pro kanál klikněte na **aktivační událost**a pak klikněte na **aktivovat**.     

    ![Aktivovat hned](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. V okně **spuštění kanálu** zadejte následující hodnotu parametru **tableList** a klikněte na **Dokončit**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

    ![Argumenty spuštění kanálu](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>Monitorování kanálu

1. Na levé straně přepněte na kartu **monitorování** . Zobrazí se spuštění kanálu aktivovaného **Ruční triggerem**. Kliknutím na tlačítko **aktualizovat** seznam aktualizujte. Pomocí odkazů ve sloupci **Akce** můžete zobrazit spuštění aktivit související se spuštěním kanálu a znovu spustit kanál. 

    ![Spuštění kanálu](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
1. Klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce** . Zobrazí se všechna spuštění aktivit přidružená k vybranému spuštění kanálu. 

    ![Spuštění aktivit](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png)

## <a name="review-the-results"></a>Kontrola výsledků
V SQL Server Management Studio spusťte následující dotazy na cílovou databázi SQL a ověřte, že se data zkopírovala ze zdrojových tabulek do cílových tabulek: 

**Dotaz** 
```sql
select * from customer_table
```

**Output**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Dotaz**

```sql
select * from project_table
```

**Output**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Dotaz**

```sql
select * from watermarktable
```

**Output**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Všimněte si, že hodnoty meze pro obě tabulky byly aktualizovány. 

## <a name="add-more-data-to-the-source-tables"></a>Přidání dalších dat do zdrojových tabulek

Spusťte následující dotaz proti zdrojové SQL Server databázi, aby se aktualizoval existující řádek v customer_table. Vloží nový řádek do project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Opětovné spuštění kanálu
1. V okně webového prohlížeče přepněte na levé straně na kartu **Upravit** . 
1. Na panelu nástrojů pro kanál klikněte na **aktivační událost**a pak klikněte na **aktivovat**.   

    ![Aktivovat hned](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. V okně **spuštění kanálu** zadejte následující hodnotu parametru **tableList** a klikněte na **Dokončit**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline-again"></a>Znovu monitorovat kanál

1. Na levé straně přepněte na kartu **monitorování** . Zobrazí se spuštění kanálu aktivovaného **Ruční triggerem**. Kliknutím na tlačítko **aktualizovat** seznam aktualizujte. Pomocí odkazů ve sloupci **Akce** můžete zobrazit spuštění aktivit související se spuštěním kanálu a znovu spustit kanál. 

    ![Spuštění kanálu](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
1. Klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce** . Zobrazí se všechna spuštění aktivit přidružená k vybranému spuštění kanálu. 

    ![Spuštění aktivit](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png) 

## <a name="review-the-final-results"></a>Kontrola konečných výsledků
V SQL Server Management Studio spusťte následující dotazy na cílovou databázi, abyste ověřili, že aktualizovaná/nová data byla ze zdrojových tabulek zkopírována do cílových tabulek. 

**Dotaz** 
```sql
select * from customer_table
```

**Output**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Všimněte si nových hodnot **název** a **LastModifytime** pro **PersonID** pro číslo 3. 

**Dotaz**

```sql
select * from project_table
```

**Output**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Všimněte si, že položka **NewProject** byla přidána do project_table. 

**Dotaz**

```sql
select * from watermarktable
```

**Output**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Všimněte si, že hodnoty meze pro obě tabulky byly aktualizovány.
     
## <a name="next-steps"></a>Další kroky
V tomto kurzu jste provedli následující kroky: 

> [!div class="checklist"]
> * Připravte zdrojové a cílové úložiště dat.
> * Vytvořte datovou továrnu.
> * Vytvoření místního prostředí Integration runtime (IR).
> * Nainstalujte modul runtime integrace.
> * Vytvořte propojené služby. 
> * Vytvoření zdroje, jímky a datových sad vodoznaku.
> * Vytvoření, spuštění a monitorování kanálu.
> * Zkontrolujte výsledky.
> * Přidání nebo aktualizace dat ve zdrojových tabulkách.
> * Znovu spusťte a Sledujte kanál.
> * Zkontrolujte konečné výsledky.

Pokud se chcete dozvědět víc o transformaci dat pomocí clusteru Spark v Azure, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Přírůstkové načtení dat z Azure SQL Database do úložiště objektů BLOB v Azure pomocí technologie Change Tracking](tutorial-incremental-copy-change-tracking-feature-portal.md)


