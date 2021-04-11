---
title: Hromadné kopírování dat pomocí Azure Portal
description: Pomocí Azure Data Factory a aktivity kopírování můžete kopírovat data ze zdrojového úložiště dat do cílového úložiště dat hromadně.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/29/2021
ms.openlocfilehash: bca2158f448f74ba596114fce5d1631249124a92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606731"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory-in-the-azure-portal"></a>Hromadné kopírování více tabulek pomocí Azure Data Factory v Azure Portal

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento kurz ukazuje **kopírování několika tabulek z Azure SQL Database do Azure synapse Analytics**. Stejný vzor můžete využít i u dalších scénářů kopírování. Například kopírujete tabulky z SQL Server/Oracle do služby Azure SQL Database/Azure synapse Analytics/Azure Blob a kopírujete různé cesty z objektu blob do tabulek Azure SQL Database.

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

Tento kurz zahrnuje následující základní kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvářejte Azure SQL Database, Azure synapse Analytics a Azure Storage propojené služby.
> * Vytváření Azure SQL Database a datových sad Azure synapse Analytics
> * Vytvořte kanál pro vyhledání tabulek ke zkopírování a dalšího kanálu k provedení skutečné operace kopírování. 
> * Zahajte spuštění kanálu.
> * Monitorování spuštění aktivit a kanálu

Tento kurz používá Azure Portal. Další informace o vytvoření datové továrny pomocí jiných nástrojů nebo sad SDK najdete v tématu [Šablony Rychlý start](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Ucelený pracovní postup
V tomto scénáři máte v Azure SQL Database několik tabulek, které chcete zkopírovat do služby Azure synapse Analytics. Tady je logická posloupnost kroků tohoto pracovního postupu, které se provádějí v kanálech:

![Pracovní postup](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* První kanál vyhledá seznam tabulek, které je potřeba zkopírovat do úložišť dat jímky.  Další možností je udržovat tabulku metadat se seznamem všech tabulek, které je potřeba zkopírovat do úložišť dat jímky. Kanál potom aktivuje jiný kanál, který postupně prochází všechny tabulky v databázi a provádí operaci kopírování dat.
* Tento druhý kanál provádí vlastní kopírování. Jako parametr používá seznam tabulek. Pro každou tabulku v seznamu zkopírujte konkrétní tabulku v Azure SQL Database do odpovídající tabulky ve službě Azure synapse Analytics pomocí [připravené kopie prostřednictvím služby Blob Storage a základu](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics) pro nejlepší výkon. V tomto příkladu první kanál předá seznam tabulek jako hodnotu parametru. 

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky
* **Účet Azure Storage**. Účet Azure Storage se v operaci hromadného kopírování používá jako pracovní úložiště objektů blob. 
* **Azure SQL Database**. Tato databáze obsahuje zdrojová data. Vytvořte v SQL Database databázi s ukázkovými daty Adventure Works LT, [a to podle článku Vytvoření databáze v Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) . V tomto kurzu se zkopírují všechny tabulky z této ukázkové databáze do Azure synapse Analytics.
* **Azure synapse Analytics**. Tento datový sklad obsahuje data zkopírovaná z SQL Database. Pokud nemáte pracovní prostor analýzy Azure synapse, přečtěte si článek Začínáme [se službou Azure synapse Analytics](..\synapse-analytics\get-started.md) , kde najdete kroky pro jeho vytvoření.

## <a name="azure-services-to-access-sql-server"></a>Služby Azure pro přístup k SQL serveru

Pro SQL Database i pro Azure synapse Analytics umožněte službám Azure přístup k SQL serveru. Zajistěte, aby **byla pro váš** Server zapnutá možnost **Povolit službám a prostředkům Azure přístup k tomuto serveru** . Toto nastavení umožňuje službě Data Factory číst data z vašeho Azure SQL Database a zapisovat data do Azure synapse Analytics. 

Pokud chcete toto nastavení ověřit a zapnout, přejděte na server > zabezpečení > brány firewall a virtuální sítě > nastavte **Povolit službám a prostředkům Azure přístup k tomuto serveru** na **zapnuto**.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
1. Přejděte na [Azure Portal](https://portal.azure.com). 
1. Na levé straně nabídky Azure Portal vyberte **vytvořit data Factory pro**  >  **integraci** prostředků  >  . 

   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na stránce **Nová datová továrna** jako **název** zadejte **ADFTutorialBulkCopyDF** . 
 
   Název objektu pro vytváření dat Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba pole názvu, změňte název datové továrny (například na vaše_jméno_ADFTutorialBulkCopyDF). Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
    ```text
    Data factory name "ADFTutorialBulkCopyDF" is not available
    ```
1. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
1. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
     
   - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
   - Vyberte **vytvořit novou** a zadejte název skupiny prostředků.   
         
     Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
1. Jako **verzi** vyberte **V2**.
1. Vyberte **umístění** pro objekt pro vytváření dat. Pokud chcete zobrazit seznam oblastí Azure, ve kterých je služba Data Factory aktuálně dostupná, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **Data Factory:**[Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.
1. Klikněte na **Vytvořit**.
1. Po dokončení vytváření vyberte **Přejít k prostředku** a přejděte na stránku **Data Factory** . 
   
1. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě aplikaci uživatelského rozhraní služby Data Factory.


## <a name="create-linked-services"></a>Vytvoření propojených služeb
Vytvoříte propojené služby, které propojí vaše úložiště dat a výpočetní prostředí s datovou továrnou. Propojená služba obsahuje informace o připojení, které služba Data Factory používá pro připojení k úložišti dat za běhu. 

V tomto kurzu propojíte Azure SQL Database, Azure synapse Analytics a Azure Blob Storage úložiště dat do vaší datové továrny. Azure SQL Database je zdrojové úložiště dat. Azure synapse Analytics je úložiště dat jímky a cíle. Azure Blob Storage slouží k přípravě dat, než se data načtou do Azure synapse Analytics pomocí základu. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database pro zdroj
V tomto kroku vytvoříte propojenou službu, která propojí vaši databázi v Azure SQL Database s datovou továrnou. 

1. V levém podokně otevřete [kartu spravovat](./author-management-hub.md) .

1. Na stránce propojené služby vyberte **+ Nová** a vytvořte novou propojenou službu.

   :::image type="content" source="./media/doc-common-process/new-linked-service.png" alt-text="Nová propojená služba.":::
1. V okně **Nová propojená služba** vyberte **Azure SQL Database** a klikněte na **Pokračovat**. 
1. V okně **Nová propojená služba (Azure SQL Database)** proveďte následující kroky: 

    a. Jako **Název** zadejte **AzureSqlDatabaseLinkedService**.

    b. Vyberte server pro **název serveru**
    
    c. Vyberte databázi pro **název databáze**. 
    
    d. Zadejte **jméno uživatele** pro připojení k databázi. 
    
    e. Zadejte **heslo** pro tohoto uživatele. 

    f. Pokud chcete otestovat připojení k databázi pomocí zadaných informací, klikněte na **Test připojení**.
  
    například Kliknutím na **vytvořit** uložte propojenou službu.


### <a name="create-the-sink-azure-synapse-analytics-linked-service"></a>Vytvoření propojené služby Azure synapse Analytics jímky

1. Na kartě **Připojení** znovu klikněte na **+ Nové** na panelu nástrojů. 
1. V okně **Nová propojená služba** vyberte **Azure synapse Analytics** a klikněte na **pokračovat**. 
1. V okně **Nová propojená služba (Azure synapse Analytics)** proveďte následující kroky: 
   
    a. Jako **Název** zadejte **AzureSqlDWLinkedService**.
     
    b. Vyberte server pro **název serveru**
     
    c. Vyberte databázi pro **název databáze**. 
     
    d. Zadejte **uživatelské jméno** pro připojení k databázi. 
     
    e. Zadejte **heslo** pro uživatele. 
     
    f. Pokud chcete otestovat připojení k databázi pomocí zadaných informací, klikněte na **Test připojení**.
     
    například Klikněte na **Vytvořit**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Vytvoření pracovní propojené služby Azure Storage
V tomto kurzu použijete Azure Blob Storage jako dočasné pracovní oblast, abyste zajistili lepší výkon kopírování pro funkci PolyBase.

1. Na kartě **Připojení** znovu klikněte na **+ Nové** na panelu nástrojů. 
1. V okně **Nová propojená služba** vyberte **Azure Blob Storage** a klikněte na **Pokračovat**. 
1. V okně **Nová propojená služba (Azure Blob Storage)** proveďte následující kroky: 

    a. Jako **Název** zadejte **AzureStorageLinkedService**.                                                 
    b. Jako **Název účtu úložiště** vyberte svůj **účet služby Azure Storage**.
    
    c. Klikněte na **Vytvořit**.

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kurzu vytvoříte zdrojovou datovou sadu a datovou sadu jímky, které určují umístění pro uložení dat. 

Vstupní datová sada **AzureSqlDatabaseDataset** odkazuje na službu **AzureSqlDatabaseLinkedService**. Propojená služba určuje připojovací řetězec pro připojení k databázi. Datová sada určuje název databáze a tabulky obsahující zdrojová data. 

Výstupní datová sada **AzureSqlDWDataset** odkazuje na službu **AzureSqlDWLinkedService**. Propojená služba Určuje připojovací řetězec pro připojení ke službě Azure synapse Analytics. Datová sada určuje databázi a tabulku, do kterých se data zkopírují. 

V tomto kurzu nejsou zdrojová a cílová tabulka SQL pevně zakódované v definicích datových sad. Místo toho aktivita ForEach předává název tabulky do aktivity kopírování za běhu. 

### <a name="create-a-dataset-for-source-sql-database"></a>Vytvoření datové sady pro zdrojovou databázi SQL Database

1. V levém podokně vyberte kartu **Autor** .

1. **+** V levém podokně vyberte (plus) a pak vyberte **datová sada**. 

    ![Nabídka Nová datová sada](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. V okně **Nová datová sada** vyberte **Azure SQL Database** a potom klikněte na **pokračovat**. 
    
1. V okně **nastavit vlastnosti** v části **název** zadejte **AzureSqlDatabaseDataset**. V části **propojená služba** vyberte **AzureSqlDatabaseLinkedService**. Pak klikněte na **OK**.

1. Přepněte na kartu **připojení** , vyberte libovolnou tabulku pro **tabulku**. Tato tabulka je fiktivní. Při vytváření kanálu zadáte dotaz na zdrojovou datovou sadu. Dotaz slouží k extrakci dat z databáze. Případně můžete kliknout na tlačítko **Upravit** a jako název tabulky zadat **dbo.** název. 
 

### <a name="create-a-dataset-for-sink-azure-synapse-analytics"></a>Vytvoření datové sady pro službu Azure synapse Analytics pro jímku 

1. Klikněte na symbol **+ (plus)** v levém podokně a pak klikněte na **Datová sada**. 
1. V okně **Nová datová sada** vyberte **Azure synapse Analytics** a potom klikněte na **pokračovat**.
1. V okně **nastavit vlastnosti** v části **název** zadejte **AzureSqlDWDataset**. V části **propojená služba** vyberte **AzureSqlDWLinkedService**. Pak klikněte na **OK**.
1. Přepněte na kartu **Parametry**, klikněte na **+Nové** a jako název parametru zadejte **DWTableName**. Znovu klikněte na **+ Nový** a jako název parametru zadejte **DWSchema** . Pokud zkopírujete nebo vložíte tento název ze stránky, zajistěte, aby na konci *DWTableName* a *DWSchema* nebyl žádný **znak mezery** na konci. 
1. Přepněte na kartu **Připojení**. 

    1. V části **tabulka** ověřte možnost **Upravit** . Vyberte do prvního vstupního pole a klikněte na odkaz **Přidat dynamický obsah** níže. Na stránce **Přidat dynamický obsah** klikněte v části **parametry** na **DWSchema** , který automaticky vyplní textové pole výrazu Top `@dataset().DWSchema` a pak klikněte na **Dokončit**.  
    
        ![Navázání spojení datové sady](./media/tutorial-bulk-copy-portal/dataset-connection-tablename.png)

    1. Vyberte druhý vstupní pole a klikněte na odkaz **Přidat dynamický obsah** níže. Na stránce **Přidat dynamický obsah** klikněte v části **parametry** na **DWTAbleName** , který automaticky vyplní textové pole výrazu Top `@dataset().DWTableName` a pak klikněte na **Dokončit**. 
    
    1. Vlastnost **TableName** datové sady je nastavena na hodnoty, které jsou předány jako argumenty pro parametry **DWSchema** a **DWTableName** . Aktivita ForEach iteruje seznam tabulek a jednu po druhé je předává aktivitě kopírování. 
    

## <a name="create-pipelines"></a>Vytvoření kanálů
V tomto kurzy vytvoříte dva kanály: **IterateAndCopySQLTables** a **GetTableListAndTriggerCopyData**. 

Kanál **GetTableListAndTriggerCopyData** provádí dvě akce:

* Vyhledá systémové tabulky Azure SQL Database a získá seznam tabulek, které se mají zkopírovat.
* Aktivuje kanál **IterateAndCopySQLTables**, který provede vlastní kopírování dat.

Kanál  **IterateAndCopySQLTables** jako parametr používá seznam tabulek. Pro každou tabulku v seznamu kopíruje data z tabulky v Azure SQL Database do služby Azure synapse Analytics pomocí připravené kopie a základu.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Vytvoření kanálu IterateAndCopySQLTables

1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Kanál**.

    ![Nabídka Nový kanál](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
 
1. Na panelu Obecné v části **vlastnosti** zadejte **IterateAndCopySQLTables** pro **název**. Pak panel sbalíte kliknutím na ikonu vlastnosti v pravém horním rohu.

1. Přepněte na kartu **Parametry** a proveďte následující akce: 

    a. Klikněte na **+ Nový**. 
    
    b. Jako **název** parametru zadejte **tableList** .
    
    c. Jako **Typ** vyberte **Pole**.

1. Na panelu nástrojů **Aktivity** rozbalte **Iterace a podmínky** a přetáhněte aktivitu **ForEach** na plochu návrháře kanálu. Na panelu nástrojů **Aktivity** můžete aktivity také vyhledávat. 

    a. Na kartě **Obecné** dole zadejte **IterateSQLTables** jako **Název**. 

    b. Přepněte na kartu **Nastavení** , klikněte na vstupní pole pro **položky** a pak klikněte na odkaz **Přidat dynamický obsah** níže. 

    c. Na stránce **Přidat dynamický obsah** sbalte oddíly **systémové proměnné** a **funkce** klikněte na **tableList** v části **parametry**. tím se automaticky naplní textové pole horního výrazu `@pipeline().parameter.tableList` . Klikněte na **Dokončit**. 

    ![Tvůrce parametru ForEach](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Přepněte na kartu **aktivity** , kliknutím na **ikonu tužky** přidejte podřízenou aktivitu do aktivity **foreach** .
    
    ![Tvůrce aktivit foreach](./media/tutorial-bulk-copy-portal/for-each-activity-builder.png)

1. Na panelu nástrojů **aktivity** rozbalte **přesunout & přenos** a přetáhněte aktivitu **Kopírovat data** na plochu návrháře kanálu. Všimněte si nabídky navigace s popisem cesty v horní části. **IterateAndCopySQLTable** je název kanálu a **IterateSQLTables** je název aktivity ForEach. Návrhář je v oboru aktivity. Chcete-li přepnout zpět na Editor kanálů z editoru ForEach, můžete kliknout na odkaz v nabídce popis cesty. 

    ![Kopírování v aktivitě ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)

1. Přepněte na kartu **Zdroj** a proveďte následující kroky:

    1. Jako **Zdrojová datová sada** vyberte **AzureSqlDatabaseDataset**. 
    1. Vyberte možnost **dotazu** pro **použití dotazu**. 
    1. Klikněte na vstupní pole **Dotaz** -> vyberte dole **Přidat dynamický obsah** -> zadejte následující výraz jako **Dotaz** -> vyberte **Dokončit**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 

1. Přepněte na kartu **Jímka** a proveďte následující kroky: 

    1. Jako **Datová sada jímky** vyberte **AzureSqlDWDataset**.
    1. Klikněte na vstupní pole pro hodnotu parametru DWTableName-> vyberte níže **Přidat dynamický obsah** a `@item().TABLE_NAME` jako skript zadejte výraz-> vyberte **Dokončit**.
    1. Klikněte na vstupní pole pro hodnotu parametru DWSchema-> vyberte níže **Přidat dynamický obsah** a `@item().TABLE_SCHEMA` jako skript zadejte výraz-> vyberte **Dokončit**.
    1. V případě metody Copy vyberte **základnu**. 
    1. Zrušte zaškrtnutí možnosti **použít výchozí typ** . 
    1. Pro možnost tabulka je výchozí nastavení "none". Pokud nemáte tabulky předem vytvořené v jímky Azure synapse Analytics, povolte možnost **automaticky vytvořit tabulku** , aktivita kopírování pak automaticky vytvoří tabulky založené na zdrojových datech. Podrobnosti najdete v tématu [Automatické vytváření tabulek jímky](copy-activity-overview.md#auto-create-sink-tables). 
    1. Klikněte na vstupní pole **Skript před kopírováním**, vyberte dole **Přidat dynamický obsah**, zadejte následující výraz jako skript a vyberte **Dokončit**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Nastavení jímky kopírování](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)

1. Přepněte na kartu **Nastavení** a proveďte následující kroky: 

    1. Zaškrtněte políčko **Povolit přípravu**.
    1. Jako **Propojená služba účtu úložiště** zadejte **AzureStorageLinkedService**.

1. Pokud chcete ověřit nastavení kanálu, klikněte na **Ověřit** na horním panelu nástrojů kanálu. Ujistěte se, že se nevyskytla žádná chyba ověřování. Pokud chcete **sestavu ověření kanálu** zavřít, klikněte na dvojité lomené závorky **>>** .

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Vytvoření kanálu GetTableListAndTriggerCopyData

Tento kanál provádí dvě akce:

* Vyhledá systémové tabulky Azure SQL Database a získá seznam tabulek, které se mají zkopírovat.
* Aktivuje kanál IterateAndCopySQLTables, který provede vlastní kopírování dat.

Postup vytvoření kanálu:

1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Kanál**.
1. Na panelu Obecné v části **vlastnosti** změňte název kanálu na **GetTableListAndTriggerCopyData**. 

1. Na panelu nástrojů **aktivity** rozbalte **Obecné** a přetáhněte aktivitu **vyhledávání** na plochu návrháře kanálu a proveďte následující kroky:

    1. Jako **Název** zadejte **LookupTableList**. 
    1. Pro **Popis** zadejte **načíst seznam tabulek z moje databáze** .

1. Přepněte na kartu **Nastavení** a proveďte následující kroky:

    1. Jako **Zdrojová datová sada** vyberte **AzureSqlDatabaseDataset**. 
    1. Vyberte **dotaz** pro **použití dotazu**. 
    1. Jako **Dotaz** zadejte následující příkaz jazyka SQL.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Zrušte zaškrtnutí pole **Pouze první řádek**.

        ![Aktivita vyhledávání – stránka Nastavení](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Přetáhněte aktivitu **Spustit kanál** z panelu nástrojů aktivity na plochu návrháře kanálu a nastavte název na **TriggerCopy**.

1. Pokud chcete aktivitu **vyhledávání** **připojit** k aktivitě **Spustit kanál** , přetáhněte **zelené pole** připojené k aktivitě vyhledávání nalevo od aktivity spustit kanál.

    ![Projení aktivit vyhledávání a spuštění kanálu](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)

1. Přepněte na kartu **Nastavení** aktivity **Spustit kanál** a proveďte následující kroky: 

    1. Jako **Vyvolaný kanál** vyberte **IterateAndCopySQLTables**. 
    1. Zrušte zaškrtnutí políčka **čekání na dokončení**.
    1. V části **parametry** klikněte na vstupní pole pod položkou hodnota – > vyberte **Přidat dynamický obsah** níže > zadejte `@activity('LookupTableList').output.value` hodnotu název tabulky – > vyberte **Dokončit**. Seznam výsledků se nastavuje z aktivity vyhledávání jako vstup druhého kanálu. Seznam výsledků obsahuje seznam tabulek, jejichž data se musí zkopírovat do cíle. 

        ![Aktivita spuštění kanálu – stránka Nastavení](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)

1. Pokud chcete kanál ověřit, klikněte na **Ověřit** na panelu nástrojů. Ověřte, že se nezobrazí žádné chyby ověření. Pokud chcete **Sestavu ověření kanálu** zavřít, klikněte na **>>**.

1. Pokud chcete publikovat entity (datové sady, kanály atd.) do služby Data Factory, klikněte na **publikovat vše** v horní části okna. Počkejte na úspěšné dokončení publikování. 

## <a name="trigger-a-pipeline-run"></a>Aktivace spuštění kanálu

1. Přejděte na kanály **GetTableListAndTriggerCopyData**, klikněte na **Přidat aktivační událost** na horním panelu nástrojů kanálu a potom klikněte na **aktivovat hned**. 

1. Potvrďte spuštění na stránce **spuštění kanálu** a pak vyberte **Dokončit**.

## <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1. Přepněte na kartu **monitorování** . Klikněte na **aktualizovat** , dokud se nezobrazí spuštění obou kanálů ve vašem řešení. Pokračujte v aktualizacích seznamu, dokud se nezobrazí stav **Úspěch**. 

1. Pokud chcete zobrazit spuštění aktivit související s kanálem **GetTableListAndTriggerCopyData** , klikněte na odkaz název kanálu pro daný kanál. Pro toto spuštění kanálu by se měla zobrazit dvě spuštění aktivit. 
    ![Monitorování spuštění kanálu](./media/tutorial-bulk-copy-portal/monitor-pipeline.png)
1. Výstup aktivity **vyhledávání** zobrazíte tak, že kliknete na odkaz **výstup** vedle aktivity pod sloupcem **název aktivity** . Okno **Výstup** můžete maximalizovat a obnovit. Po kontrole kliknutím na **X** zavřete okno **Výstup**.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
1. Chcete-li přepnout zpět na zobrazení **spuštění kanálu** , klikněte na odkaz **všechny spuštěné kanály** v horní části nabídky s popisem cesty. Kliknutím na odkaz **IterateAndCopySQLTables** (pod sloupcem **název kanálu** ) zobrazte spuštění aktivit kanálu. Všimněte si, že pro každou tabulku ve výstupu **vyhledávací** aktivity je spuštěná jedna aktivita **kopírování** . 

1. Potvrďte, že se data zkopírovala do cílové služby Azure synapse Analytics, kterou jste použili v tomto kurzu. 

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste provedli následující kroky: 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvářejte Azure SQL Database, Azure synapse Analytics a Azure Storage propojené služby.
> * Vytváření Azure SQL Database a datových sad Azure synapse Analytics
> * Vytvoření kanálu pro vyhledání tabulek ke zkopírování a dalšího kanálu pro provedení vlastní operace kopírování 
> * Zahajte spuštění kanálu.
> * Monitorování spuštění aktivit a kanálu

Pokud se chcete dozvědět víc o přírůstkovém kopírování ze zdroje do cíle, přejděte k následujícímu kurzu:
> [!div class="nextstepaction"]
>[Přírůstkové kopírování dat](tutorial-incremental-copy-portal.md)
