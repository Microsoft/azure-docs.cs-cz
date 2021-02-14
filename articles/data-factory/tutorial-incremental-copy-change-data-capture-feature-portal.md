---
title: Přírůstkové kopírování dat pomocí Change Data Capture
description: V tomto kurzu vytvoříte kanál Azure Data Factory, který postupně kopíruje rozdílová data z tabulky v databázi spravované instance Azure SQL do Azure Storage.
ms.author: nihurt
author: hurtn
ms.service: data-factory
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: ba4e5c3998c7c6218d2f0232e3c05930ca82e378
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391265"
---
# <a name="incrementally-load-data-from-azure-sql-managed-instance-to-azure-storage-using-change-data-capture-cdc"></a>Přírůstkové načtení dat ze spravované instance Azure SQL do Azure Storage pomocí Change Data Capture (CDC)

V tomto kurzu vytvoříte datovou továrnu Azure s kanálem, který načte rozdílová data na základě **Change Data Capture (CDC)** v zdrojové databázi Azure SQL Managed instance do úložiště objektů BLOB v Azure.  

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Příprava zdrojového úložiště dat
> * Vytvoření datové továrny
> * Vytvoření propojených služeb
> * Vytvoření zdrojové datové sady a datové sady jímky
> * Vytvoření, ladění a spuštění kanálu pro kontrolu změněných dat
> * Úprava dat ve zdrojové tabulce
> * Dokončení, spuštění a monitorování úplného kanálu přírůstkového kopírování

## <a name="overview"></a>Přehled
Technologie Change Data Capture, která je podporovaná v úložištích dat, jako jsou Azure SQL Managed Instances (MI) a SQL Server, se dá použít k identifikaci změněných dat.  V tomto kurzu se dozvíte, jak použít Azure Data Factory s technologií SQL Change Data Capture pro přírůstkové načtení rozdílových dat ze spravované instance Azure SQL do Azure Blob Storage.  Další konkrétní informace o technologii SQL Change Data Capture najdete v tématu [Změna data Capture v SQL Server](/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

## <a name="end-to-end-workflow"></a>Ucelený pracovní postup
Tady jsou typické kroky koncového pracovního postupu pro přírůstkové načtení dat pomocí technologie Change Data Capture.

> [!NOTE]
> Technologie Azure SQL MI i SQL Server podporují technologii Change Data Capture. V tomto kurzu se jako zdrojové úložiště dat používá spravovaná instance Azure SQL. Můžete také využít místní SQL Server.

## <a name="high-level-solution"></a>Řešení na nejvyšší úrovni
V tomto kurzu vytvoříte kanál, který provede následující operace:  

   1. Vytvořte **aktivitu vyhledávání** pro zjištění počtu změněných záznamů v tabulce SQL Database CDC a předejte ji do aktivity if Condition.
   2. Vytvořte **podmínku if** , která zkontroluje, jestli existují změněné záznamy, a pokud ano, vyvolejte aktivitu kopírování.
   3. Vytvořte **aktivitu kopírování** pro zkopírování vložených/aktualizovaných nebo odstraněných dat mezi tabulkami CDC do Azure Blob Storage.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
* **Azure SQL Database spravovaná instance**. Tuto databázi použijete jako **zdrojové** úložiště dat. Pokud nemáte Azure SQL Database spravovanou instanci, přečtěte si článek [vytvoření Azure SQL Database spravované instance](../azure-sql/managed-instance/instance-create-quickstart.md) , kde najdete kroky pro jeho vytvoření.
* **Účet Azure Storage**. Úložiště objektů blob použijete jako úložiště dat **jímky**. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../storage/common/storage-account-create.md) , kde najdete kroky, jak ho vytvořit. Vytvořte kontejner s názvem **raw**. 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>Vytvoření tabulky zdroje dat v Azure SQL Database

1. Spusťte **SQL Server Management Studio** a připojte se k serveru Azure SQL Managed Instances.
2. V **Průzkumníku serveru** klikněte pravým tlačítkem na **databázi** a potom zvolte **Nový dotaz**.
3. Spusťte následující příkaz SQL pro databázi Azure SQL Managed Instances a vytvořte tabulku s názvem `customers` jako úložiště zdroje dat.  

    ```sql
    create table customers 
    (
    customer_id int, 
    first_name varchar(50), 
    last_name varchar(50), 
    email varchar(100), 
    city varchar(50), CONSTRAINT "PK_Customers" PRIMARY KEY CLUSTERED ("customer_id") 
     );
    ```
4. Povolte mechanismus **Change Data Capture** pro vaši databázi a zdrojovou tabulku (Customers) spuštěním následujícího dotazu SQL:

    > [!NOTE]
    > - Nahraďte &lt; název zdrojového schématu &gt; schématem Azure SQL mi, který má tabulku Customers.
    > - Change Data Capture nedělá cokoli v rámci transakcí, které mění sledovanou tabulku. Místo toho jsou operace INSERT, Update a DELETE zapisovány do transakčního protokolu. Data, která jsou uložena v tabulkách změn, se nebudou spravovat, pokud tato data pravidelně a systematickě vyřadíte. Další informace najdete v tématu [Povolení služby Change Data Capture pro databázi](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server#enable-change-data-capture-for-a-database) .

    ```sql
    EXEC sys.sp_cdc_enable_db 
    
    EXEC sys.sp_cdc_enable_table
    @source_schema = 'dbo',
    @source_name = 'customers', 
    @role_name = 'null',
    @supports_net_changes = 1
    ```
5. Do tabulky Customers vložte data spuštěním následujícího příkazu:

    ```sql
     insert into customers 
         (customer_id, first_name, last_name, email, city) 
     values 
         (1, 'Chevy', 'Leward', 'cleward0@mapy.cz', 'Reading'),
         (2, 'Sayre', 'Ateggart', 'sateggart1@nih.gov', 'Portsmouth'),
        (3, 'Nathalia', 'Seckom', 'nseckom2@blogger.com', 'Portsmouth');
    ```

    > [!NOTE]
    > Před povolením Change Data Capture nejsou zachyceny žádné historické změny v tabulce.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
1. V nabídce vlevo vyberte **vytvořit prostředek**  >  **data a analýzy**  >  **Data Factory**:

   ![Výběr datové továrny v podokně Nový](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory-menu.png)

2. Na stránce **Nová datová továrna** jako **název** zadejte **ADFTutorialDataFactory**.

     ![Stránka Nová datová továrna](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory.png)

   Název objektu pro vytváření dat Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba, změňte název objektu pro vytváření dat (třeba na váš_název_ADFTutorialDataFactory) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).

    *Název objektu pro vytváření dat "ADFTutorialDataFactory" není k dispozici.*
3. Jako **verzi** vyberte **V2**.
4. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu.
5. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:

   1. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.
   2. Vyberte **vytvořit novou** a zadejte název skupiny prostředků.   
         
    Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
5. Vyberte **umístění** pro objekt pro vytváření dat. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.
6. Zrušte výběr **Povolit Git**.     
7. Klikněte na **Vytvořit**.
8. Po dokončení nasazení klikněte na **Přejít k prostředku** .

   ![Snímek obrazovky se zobrazí zpráva, že vaše nasazení je hotové a možnost přejít k prostředku.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-deploy-complete.png)
9. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.

   ![Snímek obrazovky znázorňující datovou továrnu, kterou jste nasadili.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-home-page.png)
10. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Azure Data Factory.
11. Na stránce **Začínáme** přepněte na levém panelu na kartu **Upravit**, jak je znázorněno na následujícím obrázku:

    ![Tlačítko Nový kanál](./media/tutorial-incremental-copy-change-data-capture-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V datové továrně vytvoříte propojené služby, abyste svá úložiště dat a výpočetní služby spojili s datovou továrnou. V této části vytvoříte propojené služby pro účet Azure Storage a Azure SQL MI.

### <a name="create-azure-storage-linked-service"></a>Vytvořte propojenou službu pro Azure Storage
V tomto kroku s datovou továrnou propojíte svůj účet služby Azure Storage.

1. Klikněte na **Připojení** a pak na **+ Nové**.

   ![Tlačítko Nové připojení](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-connection-button-storage.png)
2. V okně **Nová propojená služba** vyberte **Azure Blob Storage** a klikněte na **Pokračovat**.

   ![Výběr služby Azure Blob Storage](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-storage.png)
3. V okně **Nová propojená služba** proveďte následující kroky:

   1. Jako **Název** zadejte **AzureStorageLinkedService**.
   2. Jako **Název účtu úložiště** vyberte svůj účet služby Azure Storage.
   3. Klikněte na **Uložit**.

   ![Nastavení účtu služby Azure Storage](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-mi-database-linked-service"></a>Vytvořte propojenou službu databáze Azure SQL MI.
V tomto kroku propojíte databázi Azure SQL MI s datovou továrnou.

> [!NOTE]
> Pro ty, kteří používají SQL MI, najdete [tady](./connector-azure-sql-managed-instance.md#prerequisites) informace týkající se přístupu prostřednictvím veřejného a privátního koncového bodu. Pokud se používá privátní koncový bod, bude nutné, aby tento kanál běžel pomocí prostředí Integration runtime v místním prostředí. Totéž platí pro ty, na kterých běží SQL Server Prem, ve scénářích virtuálního počítače nebo virtuální sítě.

1. Klikněte na **Připojení** a pak na **+ Nové**.
2. V okně **Nová propojená služba** vyberte **Azure SQL Database spravovaná instance** a klikněte na **pokračovat**.
3. V okně **Nová propojená služba** proveďte následující kroky:

   1. Do pole **název** zadejte **AzureSqlMI1** .
   2. V poli **název serveru** vyberte SQL Server.
   4. V poli **název databáze** vyberte svou databázi SQL.
   5. Do pole **Uživatelské jméno** zadejte jméno uživatele.
   6. Do pole **Heslo** zadejte heslo pro tohoto uživatele.
   7. Klikněte na **Test připojení** a otestujte připojení.
   8. Klikněte na **Uložit** a uložte propojenou službu.

   ![Nastavení propojené služby databáze Azure SQL MI](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-sql-managed-instance-database-linked-service-settings.png)

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte datové sady, které reprezentují zdroj dat a cíl dat.

### <a name="create-a-dataset-to-represent-source-data"></a>Vytvoření datové sady představující zdrojová data
V tomto kroku vytvoříte datovou sadu pro reprezentaci zdrojových dat.

1. Ve stromovém zobrazení klikněte na symbol **+ (plus)** a pak klikněte na **Datová sada**.

   ![Nabídka Nová datová sada](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. Vyberte **Azure SQL Database spravovaná instance** a klikněte na **pokračovat**.

   ![Typ zdrojové datové sady – Azure SQL Database](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-sql-database.png)
   
3. Na kartě **nastavit vlastnosti** nastavte název datové sady a informace o připojení:
 
   1. Jako **propojená služba** vyberte **AzureSqlMI1** .
   2. Vyberte **[dbo]. [ dbo_customers_CT]** pro **název tabulky**  Poznámka: Tato tabulka byla automaticky vytvořena, když byla v tabulce Customers povolena funkce CDC. Změněná data se nikdy z této tabulky nedotazují přímo, ale místo toho se extrahují prostřednictvím [funkcí CDC](/sql/relational-databases/system-functions/change-data-capture-functions-transact-sql).

   ![Připojení ke zdroji](./media/tutorial-incremental-copy-change-data-capture-feature-portal/source-dataset-configuration.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Vytvořte datovou sadu, která bude představovat data kopírovaná do úložiště dat jímky.
V tomto kroku vytvoříte datovou sadu pro reprezentaci dat, která se kopírují ze zdrojového úložiště dat. Vytvořili jste kontejner Data Lake v Azure Blob Storage jako součást požadavků. Pokud tento kontejner neexistuje, vytvořte ho nebo použijte název existujícího kontejneru. V tomto kurzu se název výstupního souboru dynamicky generuje pomocí času triggeru, který se nakonfiguruje později.

1. Ve stromovém zobrazení klikněte na symbol **+ (plus)** a pak klikněte na **Datová sada**.

   ![Nabídka Nová datová sada](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. Vyberte **Azure Blob Storage** a klikněte na **pokračovat**.

   ![Typ datové sady jímky – Azure Blob Storage](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-type.png)
3. Vyberte **DelimitedText** a klikněte na **pokračovat**.

   ![Formát datové sady jímky – DelimitedText](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-format.png)
4. Na kartě **nastavit vlastnosti** nastavte název datové sady a informace o připojení:

   1. Jako **Propojená služba** vyberte **AzureStorageLinkedService**.
   2. Do části **kontejneru** **FilePath** zadejte **raw** .
   3. Povolit **první řádek jako záhlaví**
   4. Klikněte na **OK** .

   ![Datová sada jímky – připojení](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration.png)

## <a name="create-a-pipeline-to-copy-the-changed-data"></a>Vytvoření kanálu ke zkopírování změněných dat
V tomto kroku vytvoříte kanál, který nejprve kontroluje počet změněných záznamů v tabulce změn pomocí **aktivity vyhledávání**. Aktivita IF podmínky kontroluje, jestli je počet změněných záznamů větší než nula, a spustí **aktivitu kopírování** , která zkopíruje vložená, aktualizovaná nebo Odstraněná data z Azure SQL Database do Azure Blob Storage. Nakonec se aktivuje aktivační událost bubnového okna a časy zahájení a ukončení budou do aktivit předány jako parametry počátečního a koncového okna. 

1. V uživatelském rozhraní Data Factory přepněte na kartu **Upravit** . Klikněte na **+ (plus)** v levém podokně a pak klikněte na **kanál**.

    ![Nabídka Nový kanál](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-pipeline-menu.png)
2. Zobrazí se nová karta, na které můžete kanál konfigurovat. Kanál se zobrazí také ve stromovém zobrazení. V okně **Vlastnosti** změňte název kanálu na **IncrementalCopyPipeline**.

    ![Název kanálu](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-name.png)
3. V sadě nástrojů **Aktivity** rozbalte **Obecné** a přetáhněte aktivitu **Vyhledávání** na plochu návrháře kanálu. Nastavte název aktivity na **GetChangeCount**. Tato aktivita Získá počet záznamů v tabulce změn pro daný časový interval.

    ![Aktivita vyhledávání – název](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-name.png)
4. V okně **vlastnosti** přepněte na **Nastavení** :
   1. Zadejte název datové sady SQL MI pro pole **zdrojové datové sady** .
   2. Vyberte možnost dotazu a do pole dotaz zadejte následující:
    ```sql
    DECLARE  @from_lsn binary(10), @to_lsn binary(10);  
    SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers');  
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal',  GETDATE());
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
    ```
   3. Povolit **pouze první řádek**

    ![Aktivita vyhledávání – nastavení](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-settings.png)
5. Klikněte na tlačítko **Náhled dat** , abyste zajistili, že aktivita vyhledávání získá platný výstup.

    ![Aktivita vyhledávání – Preview](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-preview.png)
6. Rozbalte položku **iterace & podmíněné** v sadě nástrojů **aktivity** a přetáhněte aktivitu **podmínka if** na plochu návrháře kanálu. Nastavte název aktivity na **HasChangedRows**. 

    ![Pokud podmínka aktivita – název](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-name.png)
7. V okně **vlastnosti** přepněte na **aktivity** :

   1. Zadejte následující **výraz**
   
    ```adf
    @greater(int(activity('GetChangeCount').output.firstRow.changecount),0)
    ```

   2. Kliknutím na ikonu tužky upravíte podmínku true.

   ![Aktivita podmínky if – nastavení](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-setting.png)

   3. V sadě nástrojů **aktivity** rozbalte **Obecné** a přetáhněte aktivitu **čekání** na plochu návrháře kanálu. Jedná se o dočasnou aktivitu, aby se mohla ladit podmínka IF a později se v tomto kurzu změní. 

   ![Podmínka true – čekání](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-wait.png)

   4. Klikněte na IncrementalCopyPipeline s popisem cesty a vraťte se do hlavního kanálu.

8. Spusťte kanál v režimu **ladění** , abyste ověřili, že se kanál úspěšně spustí. 

   ![Kanál – ladění](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug.png)
9. Pak se vraťte k kroku pravdivé podmínky a odstraňte aktivitu **čekání** . Na panelu nástrojů **aktivity** rozbalte **přesunout & transformovat** a přetáhněte aktivitu **kopírování** na plochu návrháře kanálu. Nastavte název aktivity na **IncrementalCopyActivity**. 

   ![Aktivita kopírování – název](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-name.png)
10. V okně **Vlastnosti** přepněte na kartu **Zdroj** a proveďte následující kroky:

   1. Zadejte název datové sady SQL MI pro pole **zdrojové datové sady** . 
   2. Jako **Použít dotaz** vyberte **Dotaz**.
   3. Pro **dotaz** zadejte následující.

      ```sql
      DECLARE @from_lsn binary(10), @to_lsn binary(10); 
      SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers'); 
      SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal', GETDATE());
      SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
      ```

   ![Aktivita kopírování – nastavení zdroje](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-settings.png)

11. Kliknutím na Náhled Ověřte, že dotaz vrátí správně změněné řádky.

    ![Snímek obrazovky zobrazující náhled k ověření dotazu.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-preview.png)
12. Přepněte na kartu **jímka** a určete Azure Storage datovou sadu pro pole **datové sady jímky** .

    ![Snímek obrazovky se zobrazí karta jímka.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-sink-settings.png)
13. Klikněte zpátky na hlavní plátno kanálu a připojte aktivitu **vyhledávání** k aktivitě **podmínky if** jednu po jedné. Přetáhněte **zelené** tlačítko připojené k aktivitě **vyhledávání** do aktivity **podmínka if** .

    ![Propojení aktivit vyhledávání a kopírování](./media/tutorial-incremental-copy-change-data-capture-feature-portal/connect-lookup-if.png)
14. Klikněte na **Ověřit** na panelu nástrojů. Ověřte, že se nezobrazí žádné chyby ověření. Zavřete okno **Sestava ověření kanálu** kliknutím na **>>**.

    ![Tlačítko Ověřit](./media/tutorial-incremental-copy-change-data-capture-feature-portal/validate-button.png)
15. Kliknutím na ladit otestujete kanál a ověřte, že se v umístění úložiště vygeneroval soubor.

    ![Ladění přírůstkového kanálu – 2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-2.png)
16. Kliknutím na tlačítko **publikovat vše** publikujte entity (propojené služby, datové sady a kanály) do služby Data Factory. Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**.

    ![Tlačítko Publikovat](./media/tutorial-incremental-copy-change-data-capture-feature-portal/publish-button-2.png)    

### <a name="configure-the-tumbling-window-trigger-and-cdc-window-parameters"></a>Konfigurovat parametry okna pro zastavení bubnu a v okně CDC 
V tomto kroku vytvoříte aktivační událost bubnového okna pro spuštění úlohy v častém plánu. Použijete systémové proměnné WindowStart a WindowEnd triggeru bubnového okna a předáte je jako parametry vašeho kanálu, který se použije v dotazu CDC.

1. Přejděte na kartu **parametry** kanálu **IncrementalCopyPipeline** a pomocí tlačítka **+ New** přidejte do kanálu dva parametry (**triggerStartTime** a **triggerEndTime**), které budou představovat počáteční a koncový čas okna s bubnem. Pro účely ladění přidejte výchozí hodnoty ve formátu **RRRR-MM-DD HH24: mi: ss. FFF** , ale zajistěte, aby triggerStartTime nebyla předtím, než je v tabulce povolená možnost CDC, jinak to bude mít za následek chybu.

    ![Nabídka Aktivovat](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-parameters.png)
2. Klikněte na kartu Nastavení aktivity **vyhledávání** a nakonfigurujte dotaz tak, aby používal počáteční a koncový parametr. Zkopírujte následující do dotazu:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```

3. Přejděte do aktivity **kopírování** v poli skutečný případ aktivity **podmínka if** a klikněte na kartu **zdroj** . Zkopírujte následující do dotazu:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```
4. Klikněte na kartu **jímka** aktivity **kopírování** a kliknutím na tlačítko **otevřít** upravte vlastnosti datové sady. Klikněte na kartu **parametry** a přidejte nový parametr s názvem **triggerStart** .    

    ![Snímek obrazovky ukazuje přidání nového parametru na kartu parametry.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-2.png)
5. Dále nakonfigurujte vlastnosti datové sady tak, aby ukládaly data ve **vašich zákaznících/přírůstkovém** podadresáři s oddíly na základě data.
   1. Klikněte na kartu **připojení** vlastností datové sady a přidejte dynamický obsah pro **adresář** i pro oddíly **souborů** . 
   2. V části **adresář** zadejte následující výraz kliknutím na odkaz dynamického obsahu pod textovým polem:
    
    ```sql
    @concat('customers/incremental/',formatDateTime(dataset().triggerStart,'yyyy/MM/dd'))
    ```
   3. V části **soubor** zadejte následující výraz. Tím se vytvoří názvy souborů na základě počátečního a koncového data triggeru s příponou CSV:
    
    ```sql
    @concat(formatDateTime(dataset().triggerStart,'yyyyMMddHHmmssfff'),'.csv')
    ```
    ![Konfigurace datové sady jímky – 3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-3.png)

   4. Kliknutím na kartu **IncrementalCopyPipeline** přejděte zpátky k nastavení **jímky** v aktivitě **kopírování** . 
   5. Rozbalte Vlastnosti datové sady a do hodnoty parametru triggerStart zadejte dynamický obsah s následujícím výrazem:
     ```sql
     @pipeline().parameters.triggerStartTime
     ```
    ![Konfigurace datové sady jímky – 4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-4.png)

6. Kliknutím na ladit otestujete kanál a ujistěte se, že struktura složky a výstupní soubor jsou vygenerované podle očekávání. Stáhněte a otevřete soubor pro ověření obsahu. 

    ![Přírůstkové kopírování – ladění – 3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-3.png)
7. Zkontrolujte vstupní parametry spuštění kanálu a ujistěte se, že jsou parametry vloženy do dotazu.

    ![Ladění přírůstkové kopie-4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-4.png)
8. Kliknutím na tlačítko **publikovat vše** publikujte entity (propojené služby, datové sady a kanály) do služby Data Factory. Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**.
9. Nakonec nakonfigurujte aktivační událost bubnového okna tak, aby spouštěla kanál v pravidelných intervalech a nastavila parametry počátečního a koncového času. 
   1. Klikněte na tlačítko **Přidat aktivační událost** a vyberte **Nový/upravit** .

   ![Přidat novou aktivační událost](./media/tutorial-incremental-copy-change-data-capture-feature-portal/add-trigger.png)

   2. Zadejte název triggeru a zadejte čas spuštění, který je stejný jako čas ukončení okna ladění výše.

   ![Aktivační událost pro bubnový interval](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger.png)

   3. Na další obrazovce zadejte následující hodnoty pro počáteční a koncové parametry v uvedeném pořadí.
    ```sql
    @formatDateTime(trigger().outputs.windowStartTime,'yyyy-MM-dd HH:mm:ss.fff')
    @formatDateTime(trigger().outputs.windowEndTime,'yyyy-MM-dd HH:mm:ss.fff')
    ```

   ![Aktivační událost pro bubnové okno – 2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger-2.png)

> [!NOTE]
> Poznámka: aktivační událost se spustí až poté, co byla publikována. Kromě toho očekávané chování bubnového okna je spouštět všechny historické intervaly od počátečního data až do okamžiku. Další informace o aktivačních událostech bubnového okna najdete [tady](./how-to-create-tumbling-window-trigger.md). 
  
10. Pomocí **SQL Server Management Studio** Udělejte další změny v tabulce zákazníka spuštěním následujícího kódu SQL:
    ```sql
    insert into customers (customer_id, first_name, last_name, email, city) values (4, 'Farlie', 'Hadigate', 'fhadigate3@zdnet.com', 'Reading');
    insert into customers (customer_id, first_name, last_name, email, city) values (5, 'Anet', 'MacColm', 'amaccolm4@yellowbook.com', 'Portsmouth');
    insert into customers (customer_id, first_name, last_name, email, city) values (6, 'Elonore', 'Bearham', 'ebearham5@ebay.co.uk', 'Portsmouth');
    update customers set first_name='Elon' where customer_id=6;
    delete from customers where customer_id=5;
    ```
11. Klikněte na tlačítko **publikovat vše** . Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**.  
12. Po několika minutách se kanál aktivuje a nový soubor se načte do Azure Storage


### <a name="monitor-the-incremental-copy-pipeline"></a>Monitorování kanálu přírůstkového kopírování
1. Klikněte na kartu **Monitorování** na levé straně. V seznamu se zobrazí spuštění kanálu a jeho stav. Pokud chcete seznam aktualizovat, klikněte na **Aktualizovat**. Pokud chcete získat přístup k akci opětovného spuštění a sestavě spotřeby, najeďte na název kanálu.

    ![Spuštění kanálu](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-pipeline-runs.png)
2. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, klikněte na název kanálu. Pokud byla zjištěna změněná data, budou existovat tři aktivity, včetně aktivity kopírování, jinak budou v seznamu uvedeny pouze dvě položky. Pokud chcete přejít zpátky k zobrazení spuštění kanálu, klikněte na odkaz **všechny kanály** v horní části.

    ![Spuštění aktivit](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-activity-runs.png)


### <a name="review-the-results"></a>Kontrola výsledků
Ve složce `customers/incremental/YYYY/MM/DD` kontejneru `raw` uvidíte druhý soubor.

![Výstupní soubor pro přírůstkové kopírování](media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-run.png)
 

## <a name="next-steps"></a>Další kroky
Přejděte k následujícímu kurzu, kde se dozvíte, jak kopírovat nové a změněné soubory pouze na základě jejich LastModifiedDate:

> [!div class="nextstepaction"]
>[Kopírovat nové soubory podle LastModifiedDate](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
