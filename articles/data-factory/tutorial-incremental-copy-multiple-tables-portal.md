---
title: Přírůstkové kopírování více tabulek pomocí Azure Data Factory | Microsoft Docs
description: V tomto kurzu vytvoříte kanál Azure Data Factory, který přírůstkově kopíruje rozdílová data z několika tabulek v místní databázi SQL Serveru do databáze Azure SQL.
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
ms.date: 01/20/2018
ms.author: yexu
ms.openlocfilehash: ea1e3ca76f779f442c9d22478ea93de3d5ab83f2
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43088102"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Přírůstkové načtení dat z více tabulek v SQL Serveru do databáze Azure SQL
V tomto kurzu vytvoříte Azure Data Factory s kanálem, který načítá rozdílová data z několika tabulek v místním SQL Serveru do databáze Azure SQL.    

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Příprava zdrojového a cílového datového úložiště
> * Vytvoření datové továrny
> * Vytvořte místní prostředí Integration Runtime.
> * Instalace prostředí Integration Runtime 
> * Vytvoření propojených služeb 
> * Vytvoření zdroje, jímky a datových sad mezí
> * Vytvoření a spuštění kanálu a jeho monitorování
> * Kontrola výsledků
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

    ![Přírůstkové načtení dat](media\tutorial-incremental-copy-multiple-tables-portal\high-level-solution-diagram.png)


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
* **SQL Server**. V tomto kurzu použijete místní databázi SQL Serveru jako zdrojové úložiště dat. 
* **Azure SQL Database**. Použijete databázi SQL jako úložiště dat jímky. Pokud databázi SQL nemáte, přečtěte si téma [Vytvoření databáze Azure SQL](../sql-database/sql-database-get-started-portal.md), kde najdete kroky pro její vytvoření. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Vytvoření zdrojových tabulek v databázi SQL Serveru

1. Otevřete SQL Server Management Studio a připojte se k místní databázi SQL Serveru.

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

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Vytvoření cílových tabulek v databázi SQL Azure
1. Otevřete aplikaci SQL Server Management Studio a připojte se ke své databázi SQL Azure.

1. V **Průzkumníku serveru** klikněte pravým tlačítkem na databázi a zvolte **Nový dotaz**.

1. Spusťte na databázi SQL následující příkaz SQL, aby se vytvořily tabulky s názvem `customer_table` a `project_table`:  
    
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

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Vytvoření další tabulky v databázi SQL Azure pro ukládání hodnoty horní meze
1. Spuštěním následujícího příkazu SQL na databázi SQL vytvořte tabulku s názvem `watermarktable` pro uložení hodnoty meze: 
    
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

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Vytvoření uložené procedury v databázi SQL Azure 

Spuštěním následujícího příkazu vytvořte v databázi SQL uloženou proceduru. Tato uložená procedura aktualizuje hodnotu meze po každém spuštění kanálu. 

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Vytvoření datových typů a dalších uložených procedur v databázi SQL Azure
Spuštěním následujícího dotazu vytvořte v databázi SQL dvě uložené procedury a dva datové typy. Slouží ke slučování dat ze zdrojových tabulek do cílových tabulek.

```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE sp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
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

CREATE PROCEDURE sp_upsert_project_table @project_table DataTypeforProjectTable READONLY
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
1. V nabídce vlevo klikněte na **Nový**, klikněte na **Data + analýzy** a pak na **Data Factory**. 
   
   ![Nový -> Datová továrna](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory-menu.png)
1. Na stránce **Nová datová továrna** jako **název** zadejte **ADFMultiIncCopyTutorialDF**. 
      
     ![Stránka Nová datová továrna](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory.png)
 
   Název datové továrny Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba, změňte název datové továrny (třeba na váš_název_ADFMultiIncCopyTutorialDF) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
       `Data factory name ADFMultiIncCopyTutorialDF is not available`
1. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
1. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
     
      - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
      - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
        Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
1. Jako **verzi** vyberte **V2 (Preview)**.
1. Vyberte **umístění** pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.
1. Zaškrtněte **Připnout na řídicí panel**.     
1. Klikněte na možnost **Vytvořit**.      
1. Na řídicím panelu vidíte následující dlaždice se statusem: **Nasazování datové továrny**. 

    ![nasazování dlaždice datové továrny](media/tutorial-incremental-copy-multiple-tables-portal/deploying-data-factory.png)
1. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.
   
   ![Domovská stránka datové továrny](./media/tutorial-incremental-copy-multiple-tables-portal/data-factory-home-page.png)
1. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Azure Data Factory.
1. Na stránce Začínáme uživatelského rozhraní služby Azure Data Factory klikněte na **Vytvořit kanál** (nebo) přepněte na kartu **Upravit**. 

   ![Stránka Začínáme](./media/tutorial-incremental-copy-multiple-tables-portal/get-started-page.png)

## <a name="create-self-hosted-integration-runtime"></a>Vytvoření místního prostředí Integration Runtime
Vzhledem k tomu, že přesouváte data z úložiště dat v privátní síti (v místním prostředí) do úložiště dat Azure, nainstalujte do svého místního prostředí místní prostředí Integration Runtime (IR). Místní prostředí IR přesouvá data mezi privátní sítí a Azure. 

1. Klikněte na **Připojení** v dolní části levého podokna a v okně **Připojení** přepněte na kartu **Prostředí Integration Runtime**. 

   ![Karta Připojení](./media/tutorial-incremental-copy-multiple-tables-portal/connections-tab.png)
1. Na kartě **Prostředí Integration Runtime** klikněte na **+ Nové**. 

   ![Nové prostředí Integration Runtime – tlačítko](./media/tutorial-incremental-copy-multiple-tables-portal/new-integration-runtime-button.png)
1. V okně **Instalace prostředí Integration Runtime** vyberte **Provést přesun dat a odeslání aktivit do externích výpočetních prostředí** a klikněte na **Další**. 

   ![Výběr typu prostředí Integration Runtime](./media/tutorial-incremental-copy-multiple-tables-portal/select-integration-runtime-type.png)
1. Vyberte **Privátní síť** a klikněte na **Další**. 

   ![Výběr privátní sítě](./media/tutorial-incremental-copy-multiple-tables-portal/select-private-network.png)
1. Jako **Název** zadejte **MySelfHostedIR** a klikněte na **Další**. 

   ![Název místního prostředí IR](./media/tutorial-incremental-copy-multiple-tables-portal/self-hosted-ir-name.png)
1. Klikněte na text **Kliknutím sem spustíte expresní instalaci pro tento počítač** v části **Možnost 1: Expresní instalace**. 

   ![Kliknutí na odkaz na expresní instalaci](./media/tutorial-incremental-copy-multiple-tables-portal/click-exress-setup.png)
1. V okně **Expresní instalace Integration Runtime (v místním prostředí)** klikněte na **Zavřít**. 

   ![Instalace prostředí Integration Runtime – úspěch](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
1. V okně **Instalace prostředí Integration Runtime** ve webovém prohlížeči klikněte na **Dokončit**. 

   ![Instalace prostředí Integration Runtime – dokončení](./media/tutorial-incremental-copy-multiple-tables-portal/click-finish-integration-runtime-setup.png)
1. Zkontrolujte, že se v seznamu prostředí Integration Runtime zobrazuje **MySelfHostedIR**.

       ![Integration runtimes - list](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtimes-list.png)

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V datové továrně vytvoříte propojené služby, abyste svá úložiště dat a výpočetní služby spojili s datovou továrnou. V této části vytvoříte propojené služby pro místní databázi SQL Serveru a databázi SQL. 

### <a name="create-the-sql-server-linked-service"></a>Vytvoření propojené služby SQL Serveru
V tomto kroku s datovou továrnou propojíte místní databázi SQL Serveru.

1. V okně **Připojení** přepněte z karty **Prostředí Integration Runtime** na kartu **Propojené služby** a klikněte na **+ Nová**.

    ![Tlačítko Nová propojená služba](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
1. V okně **Nová propojená služba** vyberte **SQL Server** a klikněte na **Pokračovat**. 

    ![Výběr SQL Serveru](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server.png)
1. V okně **Nová propojená služba** proveďte následující kroky:

    1. Jako **Název** zadejte **SqlServerLinkedService**. 
    1. V části **Připojit prostřednictvím prostředí Integration Runtime** zadejte **MySelfHostedIR**. Toto je **důležitý** krok. Výchozí prostředí Integration Runtime se nemůže připojit k místnímu úložišti dat. Použijte místní prostředí Integration Runtime, které jste vytvořili dříve. 
    1. Jako **Název serveru** zadejte název vašeho počítače, který obsahuje databázi SQL Serveru.
    1. Jako **Název databáze** zadejte název databáze ve vašem SQL Serveru, která obsahuje zdrojová data. Tabulku jste vytvořili a do této databáze jste vložili data jako součást požadavků. 
    1. Jako **Typ ověřování** vyberte **typ ověřování**, který chcete použít pro připojení k databázi. 
    1. Jako **Uživatelské jméno** zadejte jméno uživatele, který má přístup k této databázi SQL Serveru. Pokud v názvu uživatelského účtu nebo serveru potřebujete použít znak lomítko (`\`), použijte řídicí znak (`\`). Příklad: `mydomain\\myuser`.
    1. Jako **Heslo** zadejte **heslo** pro tohoto uživatele. 
    1. Pokud chcete otestovat, jestli se služba Data Factory může připojit k vaší databázi SQL Serveru, klikněte na **Test připojení**. Opravte všechny chyby, dokud připojení nebude úspěšné. 
    1. Pokud chcete propojenou službu uložit, klikněte na **Uložit**.

        ![Propojená služba SQL Serveru – nastavení](./media/tutorial-incremental-copy-multiple-tables-portal/sql-server-linked-service-settings.png)

### <a name="create-the-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database
V posledním kroku vytvoříte propojenou službu, která propojí vaši zdrojovou databázi SQL Serveru s datovou továrnou. V tomto kroku s datovou továrnou propojíte cílovou databázi SQL Azure nebo databázi SQL Azure jímky. 

1. V okně **Připojení** přepněte z karty **Prostředí Integration Runtime** na kartu **Propojené služby** a klikněte na **+ Nová**.

    ![Tlačítko Nová propojená služba](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
1. V okně **Nová propojená služba** vyberte **Azure SQL Database** a klikněte na **Pokračovat**. 
1. V okně **Nová propojená služba** proveďte následující kroky:

    1. Jako **Název** zadejte **AzureSqlDatabaseLinkedService**. 
    1. Jako **Název serveru** vyberte z rozevíracího seznamu název vašeho serveru SQL Azure. 
    1. Jako **Název databáze** vyberte databázi SQL Azure, ve které jste jako součást požadavků vytvořili tabulky customer_table a project_table. 
    1. Jako **Uživatelské jméno** zadejte jméno uživatele, který má přístup k této databázi SQL Azure. 
    1. Jako **Heslo** zadejte **heslo** pro tohoto uživatele. 
    1. Pokud chcete otestovat, jestli se služba Data Factory může připojit k vaší databázi SQL Serveru, klikněte na **Test připojení**. Opravte všechny chyby, dokud připojení nebude úspěšné. 
    1. Pokud chcete propojenou službu uložit, klikněte na **Uložit**.

        ![Propojená služba Azure SQL – nastavení](./media/tutorial-incremental-copy-multiple-tables-portal/azure-sql-linked-service-settings.png)
1. Zkontrolujte, že se v seznamu zobrazují dvě propojené služby. 
   
    ![Dvě propojené služby](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte datové sady, které představují zdroj dat, cíl dat a místo pro uložení hodnoty meze.

### <a name="create-a-source-dataset"></a>Vytvoření zdrojové datové sady

1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Datová sada**.

   ![Nabídka Nová datová sada](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. V okně **Nová datová sada** vyberte **SQL Server** a klikněte na **Dokončit**. 

   ![Výběr SQL Serveru](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server-for-dataset.png)
1. Ve webovém prohlížeči se otevře nová karta, na které můžete datovou sadu konfigurovat. Datová sada se zobrazí také ve stromovém zobrazení. Na kartě **Obecné** v dolní části okna Vlastnosti jako **Název** zadejte **SourceDataset**. 

   ![Zdrojová datová sada – název](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-general.png)
1. V okně Vlastnosti přepněte na kartu **Připojení** a jako **Propojená služba** vyberte **SqlServerLinkedService**. Tabulku tady nevybíráte. Aktivita kopírování v kanálu používá místo načtení celé tabulky dotaz SQL pro načtení dat.

   ![Zdrojová sada dat – připojení](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Vytvoření datové sady jímky
1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Datová sada**.

   ![Nabídka Nová datová sada](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. V okně **Nová datová sada** vyberte **Azure SQL Database** a klikněte na **Dokončit**. 

   ![Výběr služby Azure SQL Database](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
1. Ve webovém prohlížeči se otevře nová karta, na které můžete datovou sadu konfigurovat. Datová sada se zobrazí také ve stromovém zobrazení. Na kartě **Obecné** v dolní části okna Vlastnosti jako **Název** zadejte **SinkDataset**.

   ![Datová sada jímky – obecné](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-general.png)
1. V okně Vlastnosti přepněte na kartu **Parametry** a proveďte následující kroky: 

    1. V části **Vytvořit nebo aktualizovat parametry** klikněte na **Nový**. 
    1. Jako **název** zadejte **SinkTableName** a jako **typ** zadejte **Řetězec**. Tato datová sada jako parametr přijímá **SinkTableName**. Parametr SinkTableName nastavuje kanál dynamicky za běhu. Aktivita ForEach v kanálu prochází seznam názvů tabulek a při každé iteraci předává název tabulky této datové sadě.
   
       ![Datová sada jímky – vlastnosti](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)
1. V okně Vlastnosti přepněte na kartu **Připojení** a jako **Propojená služba** vyberte **AzureSqlLinkedService**. U vlastnosti **Tabulka** klikněte na **Přidat dynamický obsah**. 

   ![Datová sada jímky – připojení](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection.png)
    
    
1. V části **Parametry** vyberte **SinkTableName**.
   
   ![Datová sada jímky – připojení](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-dynamicContent.png)

   
 1. Po kliknutí na **Dokončit** se jako název tabulky zobrazí **@dataset().SinkTableName**.
   
   ![Datová sada jímky – připojení](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-completion.png)

### <a name="create-a-dataset-for-a-watermark"></a>Vytvoření datové sady pro mez
V tomto kroku vytvoříte datovou sadu pro uložení hodnoty horní meze. 

1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Datová sada**.

   ![Nabídka Nová datová sada](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. V okně **Nová datová sada** vyberte **Azure SQL Database** a klikněte na **Dokončit**. 

   ![Výběr služby Azure SQL Database](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
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

    ![Nový kanál – nabídka](./media/tutorial-incremental-copy-multiple-tables-portal/new-pipeline-menu.png)
1. Na kartě **Obecné** v okně **Vlastnosti** jako **Název** zadejte **IncrementalCopyPipeline**. 

    ![Název kanálu](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-name.png)
1. V okně **Vlastnosti** proveďte následující kroky: 

    1. Klikněte na **+ Nový**. 
    1. Jano **název parametru** zadejte **tableList**. 
    1. Jako **typ** parametru vyberte **Objekt**.

    ![Parametry kanálu](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-parameters.png) 
1. V sadě nástrojů **Aktivity** rozbalte **Iterace a podmíněné výrazy** a přetáhněte aktivitu **ForEach** na plochu návrháře kanálu. Na kartě **Obecné** v okně **Vlastnosti** jako název zadejte **IterateSQLTables**. 

    ![Aktivita ForEach – název](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-name.png)
1. V okně **Vlastnosti** přepněte na kartu **Nastavení** a jako **Položky** zadejte `@pipeline().parameters.tableList`. Aktivita ForEach prochází seznam tabulek a provádí operaci přírůstkového kopírování. 

    ![Aktivita ForEach – nastavení](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)
1. Pokud ještě není vybraná, vyberte v kanálu aktivitu **ForEach**. Klikněte na tlačítko **Upravit (ikona tužky)**.

    ![Aktivita ForEach – úprava](./media/tutorial-incremental-copy-multiple-tables-portal/edit-foreach.png)
1. V sadě nástrojů **Aktivity** rozbalte **Obecné** a přetáhněte aktivitu **Vyhledávání** na plochu návrháře kanálu. Pak jako **Název** zadejte **LookupOldWaterMarkActivity**.

    ![První aktivita vyhledávání – název](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-name.png)
1. V okně **Vlastnosti** přepněte na kartu **Nastavení** a proveďte následující kroky: 

    1. Jako **Zdrojová datová sada** vyberte **WatermarkDataset**.
    1. Jako **Použít dotaz** vyberte **Dotaz**. 
    1. Jako **Dotaz** zadejte následující příkaz jazyka SQL. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![První aktivita vyhledávání – nastavení](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
1. Z panelu nástrojů **Aktivity** přetáhněte aktivitu **Vyhledávání** a jako **Název** zadejte **LookupNewWaterMarkActivity**.
        
    ![Druhá aktivita vyhledávání – název](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-name.png)
1. Přepněte na kartu **Nastavení**.

    1. Jako **Zdrojová datová sada** vyberte **SourceDataset**. 
    1. Jako **Použít dotaz** vyberte **Dotaz**.
    1. Jako **Dotaz** zadejte následující příkaz jazyka SQL.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![Druhá aktivita vyhledávání – nastavení](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
1. Z panelu nástrojů **Aktivity** přetáhněte aktivitu **Kopírování** a jako **Název** zadejte **IncrementalCopyActivity**. 

    ![Aktivita kopírování – název](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-name.png)
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
        
    ![Aktivita jímky – nastavení jímky](./media/tutorial-incremental-copy-multiple-tables-portal/copy-sink-settings.png)
1. Přepněte na kartu **Parametry** a proveďte následující kroky:

    1. Jako hodnotu vlastnosti **Název uložené procedury jímky** zadejte `@{item().StoredProcedureNameForMergeOperation}`.
    1. Jako hodnotu vlastnosti **Typ tabulky jímky** zadejte `@{item().TableType}`.
    1. V části **Datová sada jímky** jako hodnotu parametru **SinkTableName** zadejte `@{item().TABLE_NAME}`.

        ![Aktivita kopírování – parametry](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
1. Přetáhněte aktivitu **Uložená procedura** z panelu nástrojů **Aktivity** na plochu návrháře kanálu. Propojte aktivitu **kopírování** s aktivitou **Uložená procedura**. 

    ![Aktivita kopírování – parametry](./media/tutorial-incremental-copy-multiple-tables-portal/connect-copy-to-sproc.png)
1. Vyberte v kanálu aktivitu **Uložená procedura** a na kartě **Obecné** v okně **Vlastnosti** jako **Název** zadejte **StoredProceduretoWriteWatermarkActivity**. 

    ![Aktivita Uložená procedura – název](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-name.png)
1. Přepněte na kartu **Účet SQL** a jako **Propojená služba** vyberte **AzureSqlDatabaseLinkedService**.

    ![Aktivita Uložená procedura – účet SQL](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
1. Přepněte na kartu **Uložená procedura** a proveďte následující kroky:

    1. Jako **Název uložené procedury** vyberte `sp_write_watermark`. 
    1. Vyberte **Importovat parametr**. 
    1. Zadejte následující hodnoty parametrů: 

        | Název | Typ | Hodnota | 
        | ---- | ---- | ----- |
        | LastModifiedtime | DateTime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | Řetězec | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Aktivita Uložená procedura – nastavení uložené procedury](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
1. V levém podokně klikněte na **Publikovat**. Tato akce publikuje vytvořené entity do služby Data Factory. 

    ![Tlačítko Publikovat](./media/tutorial-incremental-copy-multiple-tables-portal/publish-button.png)
1. Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**. Pokud chcete zobrazit oznámení, klikněte na odkaz **Zobrazit oznámení**. Zavřete okno oznámení kliknutím na **X**.

    ![Zobrazit oznámení](./media/tutorial-incremental-copy-multiple-tables-portal/notifications.png)

 
## <a name="run-the-pipeline"></a>Spuštění kanálu

1. Na panelu nástrojů pro kanál klikněte na **Aktivační událost** a pak klikněte na **Aktivovat**.     

    ![Aktivovat](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. V okně **Spuštění kanálu** zadejte následující hodnotu parametru **tableList** a klikněte na **Dokončit**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
        }
    ]
    ```

    ![Argumenty spuštění kanálu](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>Monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. Zobrazí se **ručně aktivované** spuštění kanálu. Kliknutím na tlačítko **Aktualizovat** seznam aktualizujte. Pomocí odkazů ve sloupci **Akce** můžete zobrazit spuštění aktivit související se spuštěním kanálu nebo spustit kanál znovu. 

    ![Spuštění kanálu](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
1. Klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Zobrazí se spuštění aktivit související s vybraným spuštěním kanálu. 

    ![Spuštění aktivit](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png)

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
1. Na panelu nástrojů pro kanál klikněte na **Aktivační událost** a pak klikněte na **Aktivovat**.   

    ![Aktivovat](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. V okně **Spuštění kanálu** zadejte následující hodnotu parametru **tableList** a klikněte na **Dokončit**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline-again"></a>Opětovné monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. Zobrazí se **ručně aktivované** spuštění kanálu. Kliknutím na tlačítko **Aktualizovat** seznam aktualizujte. Pomocí odkazů ve sloupci **Akce** můžete zobrazit spuštění aktivit související se spuštěním kanálu nebo spustit kanál znovu. 

    ![Spuštění kanálu](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
1. Klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Zobrazí se spuštění aktivit související s vybraným spuštěním kanálu. 

    ![Spuštění aktivit](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png) 

## <a name="review-the-final-results"></a>Kontrola konečných výsledků
V SQL Server Management Studiu spusťte následující dotazy na cílovou databázi a ověřte, že aktualizovaná/nová data byla ze zdrojových tabulek zkopírována do cílových tabulek. 

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
> * Kontrola výsledků
> * Přidání nebo aktualizace dat ve zdrojových tabulkách
> * Opakované spuštění kanálu a jeho monitorování
> * Kontrola konečných výsledků

Pokud se chcete dozvědět víc o transformaci dat pomocí clusteru Spark v Azure, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Přírůstkové načtení dat ze služby Azure SQL Database do úložiště Azure Blob Storage pomocí technologie Change Tracking](tutorial-incremental-copy-change-tracking-feature-portal.md)


