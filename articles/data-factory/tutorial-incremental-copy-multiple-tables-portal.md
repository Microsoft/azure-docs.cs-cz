---
title: Přírůstkové kopírování více tabulek pomocí Azure Portal
description: V tomto kurzu vytvoříte datovou továrnu Azure s kanálem, který načte rozdílová data z několika tabulek v databázi SQL Server do databáze v Azure SQL Database.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 1fad6274b1dbbc4bf255caabd79352b3c836e352
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606684"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-a-database-in-azure-sql-database-using-the-azure-portal"></a>Přírůstkové načtení dat z více tabulek v SQL Server do databáze v Azure SQL Database pomocí Azure Portal

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V tomto kurzu vytvoříte datovou továrnu Azure s kanálem, který načte rozdílová data z několika tabulek v databázi SQL Server do databáze v Azure SQL Database.    

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Příprava zdrojového a cílového datového úložiště
> * Vytvoření datové továrny
> * Vytvořte místní prostředí Integration Runtime.
> * Instalace prostředí Integration Runtime 
> * Vytvoření propojených služeb 
> * Vytvoření zdroje, jímky a datových sad mezí
> * Vytvoření a spuštění kanálu a jeho monitorování
> * Prohlédněte si výsledky.
> * Přidání nebo aktualizace dat ve zdrojových tabulkách
> * Opakované spuštění kanálu a jeho monitorování
> * Kontrola konečných výsledků

## <a name="overview"></a>Přehled
Tady jsou důležité kroky pro vytvoření tohoto řešení: 

1. **Vyberte sloupec meze**.
    
    Ve zdrojovém úložišti dat vyberte pro každou tabulku jeden sloupec, který je možné použít k identifikaci nových nebo aktualizovaných záznamů pro každé spuštění. Data v tomto vybraném sloupci (například čas_poslední_změny nebo ID) se při vytváření nebo aktualizaci řádků obvykle zvyšují. Maximální hodnota v tomto sloupci se používá jako horní mez.

1. **Připravte úložiště dat pro uložení hodnoty meze**.   
    
    V tomto kurzu uložíte hodnotu meze do databáze SQL.

1. **Vytvořte kanál s následujícími aktivitami**: 
    
    a. Vytvořte aktivitu ForEach, která prochází seznam názvů zdrojových tabulek, který je předaný kanálu jako parametr. Pro každou zdrojovou tabulku vyvolá následující aktivity, aby pro tabulku provedl rozdílové načtení.

    b. Vytvořte dvě aktivity vyhledávání. První aktivitu vyhledávání použijte k načtení poslední hodnoty meze. Druhou aktivitu vyhledávání použijte k načtení nové hodnoty meze. Tyto hodnoty meze se předají aktivitě kopírování.

    c. Vytvořte aktivitu kopírování, která ze zdrojového úložiště dat zkopíruje řádky, které ve sloupci horní meze mají hodnotu vyšší, než je stará hodnota meze, a nižší, než je nová hodnota meze. Potom tato rozdílová data zkopíruje ze zdrojového úložiště dat do úložiště Azure Blob Storage jako nový soubor.

    d. Vytvořte aktivitu uložené procedury StoredProcedure, která aktualizuje hodnotu meze pro příští spuštění kanálu. 

    Tady je souhrnný diagram tohoto řešení: 

    ![Přírůstkové načtení dat](media/tutorial-incremental-copy-multiple-tables-portal/high-level-solution-diagram.png)


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady
* **SQL Server**. V tomto kurzu použijete databázi SQL Server jako zdrojové úložiště dat. 
* **Azure SQL Database**. Jako úložiště dat jímky použijete databázi v Azure SQL Database. Pokud nemáte databázi v SQL Database, přečtěte si téma [Vytvoření databáze v Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) , kde najdete kroky pro její vytvoření. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Vytvoření zdrojových tabulek v databázi SQL Serveru

1. Otevřete SQL Server Management Studio a připojte se k databázi SQL Serveru.

1. V **Průzkumníku serveru** klikněte pravým tlačítkem na databázi a zvolte **Nový dotaz**.

1. Spusťte na databázi následující příkaz SQL, aby se vytvořily tabulky s názvem `customer_table` a `project_table`:

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

### <a name="create-destination-tables-in-your-database"></a>Vytvoření cílových tabulek v databázi

1. Otevřete SQL Server Management Studio a připojte se k databázi v Azure SQL Database.

1. V **Průzkumníku serveru** klikněte pravým tlačítkem na databázi a zvolte **Nový dotaz**.

1. Spusťte na databázi následující příkaz SQL, aby se vytvořily tabulky s názvem `customer_table` a `project_table`:  
    
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

### <a name="create-another-table-in-your-database-to-store-the-high-watermark-value"></a>Vytvoření další tabulky v databázi pro ukládání hodnoty horní meze

1. Spusťte následující příkaz SQL pro vaši databázi a vytvořte tabulku s názvem `watermarktable` pro uložení hodnoty meze: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. Do tabulky mezí vložte hodnoty počátečních mezí pro obě zdrojové tabulky.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-your-database"></a>Vytvoření uložené procedury v databázi

Spuštěním následujícího příkazu vytvořte v databázi uloženou proceduru. Tato uložená procedura aktualizuje hodnotu meze po každém spuštění kanálu. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-your-database"></a>Vytvoření datových typů a dalších uložených procedur v databázi

Spusťte následující dotaz pro vytvoření dvou uložených procedur a dvou datových typů ve vaší databázi. Slouží ke slučování dat ze zdrojových tabulek do cílových tabulek.

Aby bylo možné cestu snadno začít používat, přímo tyto uložené procedury předají rozdílová data v rámci přes proměnnou tabulky a pak je sloučí do cílového úložiště. Buďte opatrní, protože neočekává "velký" počet rozdílových řádků (více než 100), které se mají Uložit do proměnné tabulky.  

Pokud potřebujete sloučit velký počet rozdílových řádků do cílového úložiště, doporučujeme použít aktivitu kopírování ke zkopírování všech rozdílových dat do dočasné tabulky "fázování" v cílovém úložišti a pak vytvořit vlastní uloženou proceduru bez použití proměnné tabulky pro jejich sloučení z "pracovní" tabulky do "konečné" tabulky. 


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

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
2. V nabídce vlevo vyberte vytvořit data Factory **pro**  >  **integraci** prostředků  >  : 
   
   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Na stránce **Nová datová továrna** jako **název** zadejte **ADFMultiIncCopyTutorialDF**. 
 
   Název objektu pro vytváření dat Azure musí být **globálně jedinečný**. Pokud se zobrazí červený vykřičník s následující chybou, změňte název datové továrny (například na vaše_jméno_ADFIncCopyTutorialDF) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
   `Data factory name "ADFIncCopyTutorialDF" is not available`

4. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
5. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
     
    - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
    - Vyberte **vytvořit novou** a zadejte název skupiny prostředků.   
    Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
6. Jako **verzi** vyberte **V2**.
7. Vyberte **umístění** pro objekt pro vytváření dat. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.
8. Klikněte na **Vytvořit**.      
9. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.
   
    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Domovská stránka Azure Data Factory s dlaždicí pro monitorování autora &.":::
10. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Azure Data Factory.

## <a name="create-self-hosted-integration-runtime"></a>Vytvoření místního prostředí Integration Runtime
Vzhledem k tomu, že přesouváte data z úložiště dat v privátní síti (v místním prostředí) do úložiště dat Azure, nainstalujte do svého místního prostředí místní prostředí Integration Runtime (IR). Místní prostředí IR přesouvá data mezi privátní sítí a Azure. 

1. Na stránce **Začínáme** v uživatelském rozhraní Azure Data Factory vyberte [kartu spravovat](./author-management-hub.md) z levého podokna.

   ![Tlačítko Správa domovské stránky](media/doc-common-process/get-started-page-manage-button.png)

1. V levém podokně vyberte **modul runtime integrace** a pak vyberte **+ Nový**.

   ![Vytvoření prostředí Integration Runtime](media/doc-common-process/manage-new-integration-runtime.png)

1. V okně **nastavení Integration runtime** vyberte možnost **provést přesun dat a odeslat aktivity do externích výpočtů** a klikněte na **pokračovat**. 

1. Vyberte místní **hostování** a klikněte na **pokračovat**. 
1. Jako **název** zadejte **MySelfHostedIR** a klikněte na **vytvořit**. 

1. Klikněte na text **Kliknutím sem spustíte expresní instalaci pro tento počítač** v části **Možnost 1: Expresní instalace**. 

   ![Kliknutí na odkaz na expresní instalaci](./media/tutorial-incremental-copy-multiple-tables-portal/click-express-setup.png)
1. V okně **Expresní instalace Integration Runtime (v místním prostředí)** klikněte na **Zavřít**. 

   ![Instalace prostředí Integration Runtime – úspěch](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
1. V okně **Instalace prostředí Integration Runtime** ve webovém prohlížeči klikněte na **Dokončit**. 

 
1. Zkontrolujte, že se v seznamu prostředí Integration Runtime zobrazuje **MySelfHostedIR**.

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V datové továrně vytvoříte propojené služby, abyste svá úložiště dat a výpočetní služby spojili s datovou továrnou. V této části vytvoříte propojené služby pro SQL Server databázi a databázi v Azure SQL Database. 

### <a name="create-the-sql-server-linked-service"></a>Vytvoření propojené služby SQL Serveru
V tomto kroku propojíte databázi SQL Server s datovou továrnou.

1. V okně **Připojení** přepněte z karty **Prostředí Integration Runtime** na kartu **Propojené služby** a klikněte na **+ Nová**.

    :::image type="content" source="./media/doc-common-process/new-linked-service.png" alt-text="Nová propojená služba.":::
1. V okně **Nová propojená služba** vyberte **SQL Server** a klikněte na **Pokračovat**. 

1. V okně **Nová propojená služba** proveďte následující kroky:

    1. Jako **Název** zadejte **SqlServerLinkedService**. 
    1. V části **Připojit prostřednictvím prostředí Integration Runtime** zadejte **MySelfHostedIR**. Toto je **důležitý** krok. Výchozí prostředí Integration Runtime se nemůže připojit k místnímu úložišti dat. Použijte místní prostředí Integration Runtime, které jste vytvořili dříve. 
    1. Jako **Název serveru** zadejte název vašeho počítače, který obsahuje databázi SQL Serveru.
    1. Jako **Název databáze** zadejte název databáze ve vašem SQL Serveru, která obsahuje zdrojová data. Tabulku jste vytvořili a do této databáze jste vložili data jako součást požadavků. 
    1. Jako **Typ ověřování** vyberte **typ ověřování**, který chcete použít pro připojení k databázi. 
    1. Jako **Uživatelské jméno** zadejte jméno uživatele, který má přístup k této databázi SQL Serveru. Pokud v názvu uživatelského účtu nebo serveru potřebujete použít znak lomítko (`\`), použijte řídicí znak (`\`). Příklad: `mydomain\\myuser`.
    1. Jako **Heslo** zadejte **heslo** pro tohoto uživatele. 
    1. Pokud chcete otestovat, jestli se služba Data Factory může připojit k vaší databázi SQL Serveru, klikněte na **Test připojení**. Opravte všechny chyby, dokud připojení nebude úspěšné. 
    1. Pokud chcete propojenou službu uložit, klikněte na **Dokončit**.

### <a name="create-the-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database
V posledním kroku vytvoříte propojenou službu, která propojí vaši zdrojovou databázi SQL Serveru s datovou továrnou. V tomto kroku propojíte svou cílovou databázi nebo databázi jímky s datovou továrnou. 

1. V okně **Připojení** přepněte z karty **Prostředí Integration Runtime** na kartu **Propojené služby** a klikněte na **+ Nová**.
1. V okně **Nová propojená služba** vyberte **Azure SQL Database** a klikněte na **Pokračovat**. 
1. V okně **Nová propojená služba** proveďte následující kroky:

    1. Jako **Název** zadejte **AzureSqlDatabaseLinkedService**. 
    1. V rozevíracím seznamu **název serveru** vyberte název serveru. 
    1. V poli **název databáze** vyberte databázi, ve které jste vytvořili customer_table a project_table jako součást požadavků. 
    1. Do pole **uživatelské jméno** zadejte jméno uživatele, který má přístup k databázi. 
    1. Jako **Heslo** zadejte **heslo** pro tohoto uživatele. 
    1. Pokud chcete otestovat, jestli se služba Data Factory může připojit k vaší databázi SQL Serveru, klikněte na **Test připojení**. Opravte všechny chyby, dokud připojení nebude úspěšné. 
    1. Pokud chcete propojenou službu uložit, klikněte na **Dokončit**.

1. Zkontrolujte, že se v seznamu zobrazují dvě propojené služby. 
   
    ![Dvě propojené služby](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte datové sady, které představují zdroj dat, cíl dat a místo pro uložení hodnoty meze.

### <a name="create-a-source-dataset"></a>Vytvoření zdrojové datové sady

1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Datová sada**.

1. V okně **Nová datová sada** vyberte **SQL Server**, klikněte na **pokračovat**. 

1. Ve webovém prohlížeči se otevře nová karta, na které můžete datovou sadu konfigurovat. Datová sada se zobrazí také ve stromovém zobrazení. Na kartě **Obecné** v dolní části okna Vlastnosti jako **Název** zadejte **SourceDataset**. 

1. V okně Vlastnosti přepněte na kartu **Připojení** a jako **Propojená služba** vyberte **SqlServerLinkedService**. Tabulku tady nevybíráte. Aktivita kopírování v kanálu používá místo načtení celé tabulky dotaz SQL pro načtení dat.

   ![Zdrojová sada dat – připojení](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Vytvoření datové sady jímky
1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Datová sada**.

1. V okně **Nová datová sada** vyberte **Azure SQL Database** a klikněte na **pokračovat**. 

1. Ve webovém prohlížeči se otevře nová karta, na které můžete datovou sadu konfigurovat. Datová sada se zobrazí také ve stromovém zobrazení. Na kartě **Obecné** v dolní části okna Vlastnosti jako **Název** zadejte **SinkDataset**.

1. V okně Vlastnosti přepněte na kartu **Parametry** a proveďte následující kroky: 

    1. V části **Vytvořit nebo aktualizovat parametry** klikněte na **Nový**. 
    1. Jako **název** zadejte **SinkTableName** a jako **typ** zadejte **Řetězec**. Tato datová sada jako parametr přijímá **SinkTableName**. Parametr SinkTableName nastavuje kanál dynamicky za běhu. Aktivita ForEach v kanálu prochází seznam názvů tabulek a při každé iteraci předává název tabulky této datové sadě.
   
        ![Datová sada jímky – vlastnosti](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)
1. V okno Vlastnosti přepněte na kartu **připojení** a jako **propojená služba** vyberte **AzureSqlDatabaseLinkedService** . U vlastnosti **Tabulka** klikněte na **Přidat dynamický obsah**.   
    
1. V okně **Přidat dynamický obsah** vyberte v části **parametry** možnost **SinkTableName** . 
 
1. Po kliknutí na **Dokončit** se zobrazí zpráva @dataset (). SinkTableName jako název tabulky.

   ![Datová sada jímky – připojení](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-completion.png)

### <a name="create-a-dataset-for-a-watermark"></a>Vytvoření datové sady pro mez
V tomto kroku vytvoříte datovou sadu pro uložení hodnoty horní meze. 

1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Datová sada**.

1. V okně **Nová datová sada** vyberte **Azure SQL Database** a klikněte na **pokračovat**. 

1. Na kartě **Obecné** v dolní části okna Vlastnosti jako **Název** zadejte **WatermarkDataset**.
1. Přepněte na kartu **Připojení** a proveďte následující kroky: 

    1. Jako **Propojená služba** vyberte **AzureSqlDatabaseLinkedService**.
    1. Jako **Tabulka** vyberte **[dbo].[watermarktable]**.

        ![Datová sada meze – připojení](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>Vytvoření kanálu
Tento kanál dostává jako parametr seznam tabulek. Aktivita ForEach prochází seznam názvů tabulek a provádí následující operace: 

1. Použije aktivitu vyhledávání k načtení původní hodnoty meze (počáteční hodnota nebo hodnota použitá v poslední iteraci).

1. Použije aktivitu vyhledávání k načtení nové hodnoty meze (maximální hodnota sloupce mezí ve zdrojové tabulce).

1. Použije aktivitu kopírování ke kopírování dat, která leží mezi těmito dvěma hodnotami mezí, ze zdrojové databáze do cílové databáze.

1. Použije aktivitu uložené procedury StoredProcedure k aktualizaci staré hodnoty meze, která se má použít v prvním kroku další iterace. 

### <a name="create-the-pipeline"></a>Vytvoření kanálu

1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Kanál**.

1. Na panelu Obecné v části **vlastnosti** zadejte **IncrementalCopyPipeline** pro **název**. Pak panel sbalíte kliknutím na ikonu vlastnosti v pravém horním rohu.  

1. Na kartě **parametry** proveďte následující kroky: 

    1. Klikněte na **+ Nový**. 
    1. Jano **název parametru** zadejte **tableList**. 
    1. Vyberte **pole** pro **typ** parametru.

1. V sadě nástrojů **Aktivity** rozbalte **Iterace a podmíněné výrazy** a přetáhněte aktivitu **ForEach** na plochu návrháře kanálu. Na kartě **Obecné** v okně **Vlastnosti** jako název zadejte **IterateSQLTables**. 

1. Přepněte na kartu **Nastavení** a jako **Položky** zadejte `@pipeline().parameters.tableList`. Aktivita ForEach prochází seznam tabulek a provádí operaci přírůstkového kopírování. 

    ![Aktivita ForEach – nastavení](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)

1. Pokud ještě není vybraná, vyberte v kanálu aktivitu **ForEach**. Klikněte na tlačítko **Upravit (ikona tužky)** .

1. V sadě nástrojů **Aktivity** rozbalte **Obecné** a přetáhněte aktivitu **Vyhledávání** na plochu návrháře kanálu. Pak jako **Název** zadejte **LookupOldWaterMarkActivity**.

1. V okně **Vlastnosti** přepněte na kartu **Nastavení** a proveďte následující kroky: 

    1. Jako **Zdrojová datová sada** vyberte **WatermarkDataset**.
    1. Jako **Použít dotaz** vyberte **Dotaz**. 
    1. Jako **Dotaz** zadejte následující příkaz jazyka SQL. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![První aktivita vyhledávání – nastavení](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
1. Z panelu nástrojů **Aktivity** přetáhněte aktivitu **Vyhledávání** a jako **Název** zadejte **LookupNewWaterMarkActivity**.
        
1. Přepněte na kartu **Nastavení**.

    1. Jako **Zdrojová datová sada** vyberte **SourceDataset**. 
    1. Jako **Použít dotaz** vyberte **Dotaz**.
    1. Jako **Dotaz** zadejte následující příkaz jazyka SQL.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![Druhá aktivita vyhledávání – nastavení](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
1. Z panelu nástrojů **Aktivity** přetáhněte aktivitu **Kopírování** a jako **Název** zadejte **IncrementalCopyActivity**. 

1. Jednu po druhé propojte aktivity **vyhledávání** s aktivitou **kopírování**. Propojte je tak, že začnete přetahovat **zelené** pole připojené k aktivitě **vyhledávání** a přemístíte ho na aktivitu **kopírování**. Jakmile se barva ohraničení aktivity kopírování změní na **modrou**, uvolněte tlačítko myši.

    ![Propojení aktivit vyhledávání s aktivitou kopírování](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
1. Vyberte v kanálu aktivitu **kopírování**. V okně **Vlastnosti** přepněte na kartu **Zdroj**. 

    1. Jako **Zdrojová datová sada** vyberte **SourceDataset**. 
    1. Jako **Použít dotaz** vyberte **Dotaz**. 
    1. Jako **Dotaz** zadejte následující příkaz jazyka SQL.

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![Aktivita kopírování – nastavení zdroje](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
1. Přepněte na kartu **Jímka** a jako **Datová sada jímky** vyberte **SinkDataset**. 
        
1. Proveďte následující kroky:

    1. V části **Vlastnosti datové sady** zadejte do pole **SinkTableName** parametr `@{item().TABLE_NAME}` .
    1. Jako vlastnost **název uložené procedury** zadejte `@{item().StoredProcedureNameForMergeOperation}` .
    1. Jako vlastnost **typ tabulky** zadejte `@{item().TableType}` .
    1. Jako **název parametru typu tabulky** zadejte `@{item().TABLE_NAME}` .

        ![Aktivita kopírování – parametry](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
1. Přetáhněte aktivitu **Uložená procedura** z panelu nástrojů **Aktivity** na plochu návrháře kanálu. Propojte aktivitu **kopírování** s aktivitou **Uložená procedura**. 

1. Vyberte v kanálu aktivitu **Uložená procedura** a na kartě **Obecné** v okně **Vlastnosti** jako **Název** zadejte **StoredProceduretoWriteWatermarkActivity**. 

1. Přepněte na kartu **účet SQL** a jako **propojená služba** vyberte **AzureSqlDatabaseLinkedService** .

    ![Aktivita Uložená procedura – účet SQL](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
1. Přepněte na kartu **Uložená procedura** a proveďte následující kroky:

    1. Jako **Název uložené procedury** vyberte `[dbo].[usp_write_watermark]`. 
    1. Vyberte **Importovat parametr**. 
    1. Zadejte následující hodnoty parametrů: 

        | Název | Typ | Hodnota | 
        | ---- | ---- | ----- |
        | LastModifiedtime | DateTime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | Řetězec | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Aktivita Uložená procedura – nastavení uložené procedury](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
1. Výběrem **publikovat vše** publikujte entity, které jste vytvořili ve službě Data Factory. 

1. Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**. Pokud chcete zobrazit oznámení, klikněte na odkaz **Zobrazit oznámení**. Zavřete okno oznámení kliknutím na **X**.

 
## <a name="run-the-pipeline"></a>Spuštění kanálu

1. Na panelu nástrojů pro kanál klikněte na **Přidat aktivační událost** a potom klikněte na **aktivovat**.     

1. V okně **Spuštění kanálu** zadejte následující hodnotu parametru **tableList** a klikněte na **Dokončit**. 

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

1. Vlevo přepněte na kartu **Monitorování**. Zobrazí se **ručně aktivované** spuštění kanálu. Pomocí odkazů ve sloupci **název kanálu** můžete zobrazit podrobnosti o aktivitách a znovu spustit kanál.

1. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte odkaz pod sloupcem **název kanálu** . Chcete-li zobrazit podrobnosti o spuštění aktivity, vyberte odkaz **Podrobnosti** (ikona brýlí) ve sloupci **název aktivity** . 

1. Výběrem možnosti **všechny spuštěné kanály** v horní části přejdete zpátky k zobrazení spuštění kanálu. Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**.


## <a name="review-the-results"></a>Kontrola výsledků
V SQL Server Management Studiu spusťte následující dotazy na cílovou databázi SQL a ověřte, že data byla ze zdrojových tabulek zkopírována do cílových tabulek: 

**Dotaz** 
```sql
select * from customer_table
```

**Výstup**
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

**Výstup**

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

**Výstup**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Všimněte si, že hodnoty mezí pro obě tabulky byly aktualizovány. 

## <a name="add-more-data-to-the-source-tables"></a>Přidání dalších dat do zdrojových tabulek

Spusťte následující dotaz na zdrojovou databázi SQL Serveru, aby se aktualizoval stávající řádek v tabulce customer_table. Vložte nový řádek do tabulky project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Opětovné spuštění kanálu
1. V levé části okna webového prohlížeče přepněte na kartu **Upravit**. 
1. Na panelu nástrojů pro kanál klikněte na **Přidat aktivační událost** a potom klikněte na **aktivovat**.   
1. V okně **Spuštění kanálu** zadejte následující hodnotu parametru **tableList** a klikněte na **Dokončit**. 

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

## <a name="monitor-the-pipeline-again"></a>Opětovné monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. Zobrazí se **ručně aktivované** spuštění kanálu. Pomocí odkazů ve sloupci **název kanálu** můžete zobrazit podrobnosti o aktivitách a znovu spustit kanál.

1. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte odkaz pod sloupcem **název kanálu** . Chcete-li zobrazit podrobnosti o spuštění aktivity, vyberte odkaz **Podrobnosti** (ikona brýlí) ve sloupci **název aktivity** . 

1. Výběrem možnosti **všechny spuštěné kanály** v horní části přejdete zpátky k zobrazení spuštění kanálu. Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**.

## <a name="review-the-final-results"></a>Kontrola konečných výsledků
V SQL Server Management Studio spusťte následující dotazy na cílovou databázi SQL, abyste ověřili, že aktualizovaná/nová data byla ze zdrojových tabulek zkopírována do cílových tabulek. 

**Dotaz** 
```sql
select * from customer_table
```

**Výstup**
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

Všimněte si nových hodnot položek **Name** a **LastModifytime** pro **PersonID** pro číslo 3. 

**Dotaz**

```sql
select * from project_table
```

**Výstup**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Všimněte si, že do tabulky project_table byla přidána položka **NewProject**. 

**Dotaz**

```sql
select * from watermarktable
```

**Výstup**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Všimněte si, že hodnoty mezí pro obě tabulky byly aktualizovány.
     
## <a name="next-steps"></a>Další kroky
V tomto kurzu jste provedli následující kroky: 

> [!div class="checklist"]
> * Příprava zdrojového a cílového datového úložiště
> * Vytvoření datové továrny
> * Vytvoření místního prostředí Integration Runtime (IR)
> * Instalace prostředí Integration Runtime
> * Vytvoření propojených služeb 
> * Vytvoření zdroje, jímky a datových sad mezí
> * Vytvoření a spuštění kanálu a jeho monitorování
> * Prohlédněte si výsledky.
> * Přidání nebo aktualizace dat ve zdrojových tabulkách
> * Opakované spuštění kanálu a jeho monitorování
> * Kontrola konečných výsledků

Pokud se chcete dozvědět víc o transformaci dat pomocí clusteru Spark v Azure, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Přírůstkové načtení dat ze služby Azure SQL Database do úložiště Azure Blob Storage pomocí technologie Change Tracking](tutorial-incremental-copy-change-tracking-feature-portal.md)