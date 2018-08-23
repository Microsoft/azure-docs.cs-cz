---
title: Přírůstkové kopírování tabulky pomocí Azure Data Factory | Microsoft Docs
description: V tomto kurzu vytvoříte kanál Azure Data Factory, který přírůstkově kopíruje data ze služby Azure SQL Database do úložiště Azure Blob Storage.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: yexu
ms.openlocfilehash: f1803dd051b380743b56f4f026ee5c5fb684ce69
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "41917945"
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage"></a>Přírůstkové načtení dat ze služby Azure SQL Database do úložiště Azure Blob Storage
V tomto kurzu vytvoříte službu Azure Data Factory s kanálem, který načítá rozdílová data z tabulky ve službě Azure SQL Database do úložiště Azure Blob Storage. 

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Příprava úložiště dat pro uložení hodnoty meze
> * Vytvoření datové továrny
> * Vytvoření propojených služeb 
> * Vytvoření zdroje, jímky a datových sad mezí
> * Vytvoření kanálu
> * Spuštění kanálu
> * Monitorování spuštění kanálu 
> * Kontrola výsledků
> * Přidání dalších dat do zdroje
> * Opětovné spuštění kanálu
> * Monitorování druhého spuštění kanálu
> * Kontrola výsledků druhého spuštění


## <a name="overview"></a>Přehled
Tady je souhrnný diagram tohoto řešení: 

![Přírůstkové načtení dat](media\tutorial-Incremental-copy-portal\incrementally-load.png)

Tady jsou důležité kroky pro vytvoření tohoto řešení: 

1. **Vyberte sloupec meze**.
    Ve zdrojovém úložišti dat vyberte jeden sloupec, který je možné použít k rozlišení nových nebo aktualizovaných záznamů pro každé spuštění. Data v tomto vybraném sloupci (například čas_poslední_změny nebo ID) se při vytváření nebo aktualizaci řádků obvykle zvyšují. Maximální hodnota v tomto sloupci se používá jako horní mez.

2. **Připravte úložiště dat pro uložení hodnoty meze**. V tomto kurzu uložíte hodnotu meze do databáze SQL.
    
3. **Vytvořte kanál s následujícím pracovním postupem**: 
    
    Kanál v tomto řešení má následující aktivity:
  
    * Vytvořte dvě aktivity vyhledávání. První aktivitu vyhledávání použijte k načtení poslední hodnoty meze. Druhou aktivitu vyhledávání použijte k načtení nové hodnoty meze. Tyto hodnoty meze se předají aktivitě kopírování. 
    * Vytvořte aktivitu kopírování, která ze zdrojového úložiště dat zkopíruje řádky, které ve sloupci horní meze mají hodnotu vyšší, než je stará hodnota meze, a nižší, než je nová hodnota meze. Potom tato rozdílová data zkopíruje ze zdrojového úložiště dat do úložiště objektů blob jako nový soubor. 
    * Vytvořte aktivitu uložené procedury StoredProcedure, která aktualizuje hodnotu meze pro příští spuštění kanálu. 


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
* **Azure SQL Database**. Tuto databázi použijete jako zdrojové úložiště dat. Pokud databázi SQL nemáte, přečtěte si téma [Vytvoření databáze Azure SQL](../sql-database/sql-database-get-started-portal.md), kde najdete kroky pro její vytvoření.
* **Azure Storage** Úložiště objektů blob použijete jako úložiště dat jímky. Pokud nemáte účet úložiště, přečtěte si téma [Vytvoření účtu úložiště](../storage/common/storage-quickstart-create-account.md), kde najdete kroky pro jeho vytvoření. Vytvořte kontejner s názvem adftutorial. 

### <a name="create-a-data-source-table-in-your-sql-database"></a>Vytvoření tabulky zdroje dat v databázi SQL
1. Otevřete SQL Server Management Studio. V **Průzkumníku serveru** klikněte pravým tlačítkem na databázi a zvolte **Nový dotaz**.

2. Spuštěním následujícího příkazu SQL na vaší databázi SQL vytvořte tabulku s názvem `data_source_table` jako úložiště zdroje dat: 
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    V tomto kurzu jako sloupec meze použijete LastModifytime. Data v úložišti zdroje dat zobrazuje následující tabulka:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Vytvoření další tabulky v databázi SQL pro ukládání hodnoty horní meze
1. Spuštěním následujícího příkazu SQL na databázi SQL vytvořte tabulku s názvem `watermarktable` pro uložení hodnoty meze:  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Jako výchozí hodnotu horní meze nastavte název tabulky zdrojového úložiště dat. V tomto kurzu má tato tabulka název data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Zkontrolujte data v tabulce `watermarktable`.
    
    ```sql
    Select * from watermarktable
    ```
    Výstup: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Vytvoření uložené procedury v databázi SQL 

Spuštěním následujícího příkazu vytvořte v databázi SQL uloženou proceduru:

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
1. V nabídce vlevo klikněte na **Nový**, klikněte na **Data + analýzy** a pak na **Data Factory**. 
   
   ![Nový -> Datová továrna](./media/tutorial-incremental-copy-portal/new-azure-data-factory-menu.png)
2. Na stránce **Nová datová továrna** jako **název** zadejte **ADFIncCopyTutorialDF**. 
      
     ![Stránka Nová datová továrna](./media/tutorial-incremental-copy-portal/new-azure-data-factory.png)
 
   Název datové továrny Azure musí být **globálně jedinečný**. Pokud se zobrazí červený vykřičník s následující chybou, změňte název datové továrny (například na vaše_jméno_ADFIncCopyTutorialDF) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
       `Data factory name "ADFIncCopyTutorialDF" is not available`
3. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
4. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
     
      - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
      - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
        Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
4. Jako **verzi** vyberte **V2**.
5. Vyberte **umístění** pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.
6. Zaškrtněte **Připnout na řídicí panel**.     
7. Klikněte na možnost **Vytvořit**.      
8. Na řídicím panelu vidíte následující dlaždice se statusem: **Nasazování datové továrny**. 

    ![nasazování dlaždice datové továrny](media/tutorial-incremental-copy-portal/deploying-data-factory.png)
9. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.
   
   ![Domovská stránka datové továrny](./media/tutorial-incremental-copy-portal/data-factory-home-page.png)
10. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Azure Data Factory.

## <a name="create-a-pipeline"></a>Vytvoření kanálu
V tomto kurzu vytvoříte kanál se dvěma aktivitami vyhledávání, jednou aktivitou kopírování a jednou aktivitou uložené procedury, a to všechno zřetězené v jednom kanálu. 

1. Na stránce **Začínáme** uživatelského rozhraní služby Data Factory klikněte na dlaždici **Vytvořit kanál**. 

   ![Stránka Začínáme uživatelského rozhraní služby Data Factory](./media/tutorial-incremental-copy-portal/get-started-page.png)    
3. Na stránce **Obecné** v okně **Vlastnosti** kanálu zadejte název **IncrementalCopyPipeline**. 

   ![Název kanálu](./media/tutorial-incremental-copy-portal/pipeline-name.png)
4. Přidejme první aktivitu vyhledávání, která načte poslední hodnotu meze. V sadě nástrojů **Aktivity** rozbalte **Obecné** a přetáhněte aktivitu **Vyhledávání** na plochu návrháře kanálu. Změňte název aktivity na **LookupOldWaterMarkActivity**.

   ![První aktivita vyhledávání – název](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. Přepněte na kartu **Nastavení** a klikněte na **+ Nová** u možnosti **Zdrojová datová sada**. V tomto kroku vytvoříte datovou sadu, která bude představovat data v tabulce **watermarktable**. Tato tabulka obsahuje starou mez, která se použila v předchozí operaci kopírování. 

   ![Nabídka Nová datová sada – stará mez](./media/tutorial-incremental-copy-portal/new-dataset-old-watermark.png)
6. V okně **Nová datová sada** vyberte **Azure SQL Database** a klikněte na **Dokončit**. Otevře se nová karta pro tuto datovou sadu. 

   ![Výběr služby Azure SQL Database](./media/tutorial-incremental-copy-portal/select-azure-sql-database-old-watermark.png)
7. V okně Vlastnosti datové sady jako **Název** zadejte **WatermarkDataset**.

   ![Datová sada meze – název](./media/tutorial-incremental-copy-portal/watermark-dataset-name.png)
8. Přepněte na kartu **Připojení** a kliknutím na **+ Nové** vytvořte připojení (vytvořte propojenou službu) k vaší databázi SQL Azure. 

   ![Tlačítko Nová propojená služba](./media/tutorial-incremental-copy-portal/watermark-dataset-new-connection-button.png)
9. V okně **Nová propojená služba** proveďte následující kroky:

    1. Jako **Název** zadejte **AzureSqlDatabaseLinkedService**. 
    2. Jako **Název serveru** vyberte váš server SQL Azure.
    3. Zadejte **jméno uživatele** pro přístup k serveru SQL Azure. 
    4. Zadejte **heslo** pro tohoto uživatele. 
    5. Pokud chcete otestovat připojení k databázi SQL Azure, klikněte na **Test připojení**.
    6. Klikněte na **Uložit**.
    7. Na kartě **Připojení** ověřte, že jako **Propojená služba** je vybraná služba **AzureSqlDatabaseLinkedService**.
       
        ![Okno Nová propojená služba](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
10. Jako **Tabulka** vyberte **[dbo].[watermarktable]**. Pokud chcete zobrazit náhled dat v tabulce, klikněte na **Náhled dat**.

    ![Datová sada meze – nastavení připojení](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
11. Přepněte na editor kanálu kliknutím na kartu kanálu v horní části nebo kliknutím na název kanálu ve stromovém zobrazení vlevo. V okně Vlastnosti aktivity **Vyhledávání** ověřte, že je v poli **Zdrojová datová sada** vybraná datová sada **WatermarkDataset**. 

    ![Kanál – stará datová sada meze](./media/tutorial-incremental-copy-portal/pipeline-old-watermark-dataset-selected.png)
12. V sadě nástrojů **Aktivity** rozbalte **Obecné** a přetáhněte další aktivitu **Vyhledávání** na plochu návrháře kanálu. Pak na kartě **Obecné** v okně vlastností nastavte název na **LookupNewWaterMarkActivity**. Tato aktivita vyhledávání získá hodnotu meze z tabulky se zdrojovými daty, která se mají zkopírovat do cíle. 

    ![Druhá aktivita vyhledávání – název](./media/tutorial-incremental-copy-portal/second-lookup-activity-name.png)
13. V okně Vlastnosti druhé aktivity **Vyhledávání** přepněte na kartu **Nastavení** a klikněte na **Nový**. Vytvoříte datovou sadu, která bude odkazovat na zdrojovou tabulku obsahující novou hodnotu meze (maximální hodnota LastModifyTime). 

    ![Druhá aktivita vyhledávání – nová datová sada](./media/tutorial-incremental-copy-portal/second-lookup-activity-settings-new-button.png)
14. V okně **Nová datová sada** vyberte **Azure SQL Database** a klikněte na **Dokončit**. Otevře se nová karta pro tuto datovou sadu. Datová sada se zobrazí také ve stromovém zobrazení. 
15. Na kartě **Obecné** v okně Vlastnosti jako **Název** zadejte **SourceDataset**. 

    ![Zdrojová datová sada – název](./media/tutorial-incremental-copy-portal/source-dataset-name.png)
16. Přepněte na kartu **Připojení** a proveďte následující kroky: 

    1. Jako **Propojená služba** vyberte **AzureSqlDatabaseLinkedService**.
    2. Jako Tabulka vyberte **[dbo].[tabulka_zdroje_dat]**. Později v tomto kurzu pro tuto datovou sadu zadáte dotaz. Dotaz má přednost před tabulkou, kterou zadáte v tomto kroku. 

        ![Druhá aktivita vyhledávání – nová datová sada](./media/tutorial-incremental-copy-portal/source-dataset-connection.png)
17. Přepněte na editor kanálu kliknutím na kartu kanálu v horní části nebo kliknutím na název kanálu ve stromovém zobrazení vlevo. V okně Vlastnosti aktivity **Vyhledávání** ověřte, že je v poli **Zdrojová datová sada** vybraná datová sada **SourceDataset**. 
18. V poli **Použít dotaz** vyberte **Dotaz** a zadejte následující dotaz: vybíráte pouze maximální hodnotu **LastModifytime** z tabulky **tabulka_zdroje_dat**. Pokud tento dotaz nemáte, datová sada získá všechny řádky z tabulky, jejíž název jste zadali (tabulka_zdroje_dat) v definici datové sady.

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![Druhá aktivita vyhledávání – dotaz](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. Na panelu nástrojů **Aktivity** rozbalte **Tok dat**, přetáhněte aktivitu **Kopírování** z panelu nástrojů Aktivity a nastavte její název na **IncrementalCopyActivity**. 

    ![Aktivita kopírování – název](./media/tutorial-incremental-copy-portal/copy-activity-name.png)
20. **Propojte obě aktivity vyhledávání s aktivitou kopírování** přetažením **zeleného tlačítka** připojeného k aktivitám vyhledávání na aktivitu kopírování. Jakmile se barva ohraničení aktivity kopírování změní na modrou, uvolněte tlačítko myši. 

    ![Propojení aktivit vyhledávání s aktivitou kopírování](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. Vyberte **aktivitu kopírování** a ověřte, že se v okně **Vlastnosti** zobrazí vlastnosti aktivity. 

    ![Vlastnosti aktivity kopírování](./media/tutorial-incremental-copy-portal/back-to-copy-activity-properties.png)
22. V okně **Vlastnosti** přepněte na kartu **Zdroj** a proveďte následující kroky:

    1. V poli **Zdrojová datová sada** vyberte **SourceDataset**. 
    2. V poli **Použít dotaz** vyberte **Dotaz**. 
    3. Do pole **Dotaz** zadejte následující příkaz jazyka SQL. 

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```
    
        ![Aktivita kopírování – zdroj](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. Přepněte na kartu **Jímka** a klikněte na **+ Nová** vedle pole **Datová sada jímky**. 

    ![Tlačítko Nová datová sada](./media/tutorial-incremental-copy-portal/new-sink-dataset-button.png)
24. V tomto kurzu je úložiště dat jímky typu Azure Blob Storage. Proto v okně **Nová datová sada** vyberte **Azure Blob Storage** a klikněte na **Dokončit**. 

    ![Výběr služby Azure Blob Storage](./media/tutorial-incremental-copy-portal/select-azure-blob-storage.png)
25. Na kartě **Obecné** v okně Vlastnosti datové sady jako **Název** zadejte **SinkDataset**. 

    ![Datová sada jímky – název](./media/tutorial-incremental-copy-portal/sink-dataset-name.png)
26. Přepněte na kartu **Připojení** a klikněte na **+ Nové**. V tomto kroku vytvoříte připojení (propojenou službu) ke svému **úložišti objektů blob v Azure**.

    ![Datová sada jímky – nové připojení](./media/tutorial-incremental-copy-portal/sink-dataset-new-connection.png)
26. V okně **Nová propojená služba** proveďte následující kroky: 

    1. Jako **Název** zadejte **AzureStorageLinkedService**. 
    2. Jako **Název účtu úložiště** vyberte svůj účet služby Azure Storage.
    3. Klikněte na **Uložit**. 

        ![Propojená služba Azure Storage – nastavení](./media/tutorial-incremental-copy-portal/azure-storage-linked-service-settings.png)
27. Na kartě **Připojení** proveďte následující kroky:

    1. Ověřte, že jako **Propojená služba** je vybraná služba **AzureStorageLinkedService**. 
    2. Do části **složka** v **cestě k souboru** zadejte **adftutorial/incrementalcopy**. **adftutorial** je název kontejneru objektů blob a **incrementalcopy** je název složky. Tento fragment kódu předpokládá, že ve svém úložišti objektů blob máte kontejner objektů blob s názvem adftutorial. Pokud tento kontejner neexistuje, vytvořte ho nebo použijte název existujícího kontejneru. Azure Data Factory automaticky vytvoří výstupní složku **incrementalcopy**, pokud neexistuje. Můžete také použít tlačítko **Procházet** u možnosti **Cesta k souboru** a přejít ke složce v kontejneru objektů blob. .RunId, '.txt')`.
    3. Do části **název souboru** v **cestě k souboru** zadejte `@CONCAT('Incremental-', pipeline().RunId, '.txt')`. Název souboru se vygeneruje dynamicky pomocí tohoto výrazu. Každé spuštění kanálu má jedinečné ID. Aktivita kopírování používá ID spuštění k vygenerování názvu souboru. 

        ![Datová sada jímky – nastavení připojení](./media/tutorial-incremental-copy-portal/sink-dataset-connection-settings.png)
28. Přepněte na editor **kanálu** kliknutím na kartu kanálu v horní části nebo kliknutím na název kanálu ve stromovém zobrazení vlevo. 
29. V sadě nástrojů **Aktivity** rozbalte **Obecné** a přetáhněte aktivitu **Uložená procedura** ze sady nástrojů **Aktivity** na plochu návrháře kanálu. **Připojte** zelený výstup (Úspěch) aktivity **Kopírování** k aktivitě **Uložená procedura**. 
    
    ![Aktivita kopírování – zdroj](./media/tutorial-incremental-copy-portal/connect-copy-to-stored-procedure-activity.png)
24. Vyberte v návrháři kanálu **aktivitu Uložená procedura** a změňte její název na **StoredProceduretoWriteWatermarkActivity**. 

    ![Aktivita Uložená procedura – název](./media/tutorial-incremental-copy-portal/stored-procedure-activity-name.png)
25. Přepněte na kartu **Účet SQL** a jako **Propojená služba** vyberte *AzureSqlDatabaseLinkedService**. 

    ![Aktivita Uložená procedura – účet SQL](./media/tutorial-incremental-copy-portal/sp-activity-sql-account-settings.png)
26. Přepněte na kartu **Uložená procedura** a proveďte následující kroky: 

    1. Jako **Název uložené procedury** vyberte **sp_write_watermark**. 
    2. Pokud chcete zadat hodnoty parametrů uložené procedury, klikněte na **Importovat parametr** a zadejte následující hodnoty parametrů: 

        | Název | Typ | Hodnota | 
        | ---- | ---- | ----- | 
        | LastModifiedtime | DateTime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | Řetězec | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

    ![Aktivita Uložená procedura – nastavení uložené procedury](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. Pokud chcete ověřit nastavení kanálu, klikněte na **Ověřit** na panelu nástrojů. Ověřte, že se nezobrazí žádné chyby ověření. Pokud chcete okno **Sestava ověření kanálu** zavřít, klikněte na >>.   

    ![Ověření kanálu](./media/tutorial-incremental-copy-portal/validate-pipeline.png)
28. Pomocí tlačítka **Publikovat vše** publikujte entity (propojené služby, datové sady a kanály) do služby Azure Data Factory. Počkejte, dokud se nezobrazí zpráva o úspěšném publikování. 

    ![Tlačítko Publikovat](./media/tutorial-incremental-copy-portal/publish-button.png)

## <a name="trigger-a-pipeline-run"></a>Aktivace spuštění kanálu
1. Klikněte na **Aktivační událost** na panelu nástrojů a pak klikněte na **Aktivovat**. 

    ![Tlačítko Aktivovat](./media/tutorial-incremental-copy-portal/trigger-now.png)
2. V okně **Spuštění kanálu** vyberte **Dokončit**. 

## <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1. Vlevo přepněte na kartu **Monitorování**. Zobrazí se stav ručně aktivovaného spuštění kanálu. Kliknutím na tlačítko **Aktualizovat** seznam aktualizujte. 
    
    ![Spuštění kanálu](./media/tutorial-incremental-copy-portal/pipeline-runs.png)
2. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, klikněte na první odkaz (**Zobrazit spuštění aktivit**) ve sloupci **Akce**. Zpět na předchozí zobrazení můžete přejít kliknutím na **Kanály** v horní části. Kliknutím na tlačítko **Aktualizovat** seznam aktualizujte.

    ![Spuštění aktivit](./media/tutorial-incremental-copy-portal/activity-runs.png)

## <a name="review-the-results"></a>Kontrola výsledků
1. Pomocí nástroje, jako je [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) se připojte ke svému účtu služby Azure Storage. Ověřte, že se ve složce **incrementalcopy** kontejneru **adftutorial** vytvořil výstupní soubor.

    ![První výstupní soubor](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. Otevřete výstupní soubor a všimněte si, že se do souboru objektu blob zkopírovala veškerá data z tabulky **tabulka_zdroje_dat**. 

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. Zkontrolujte nejnovější hodnotu z `watermarktable`. Uvidíte, že hodnota meze byla aktualizována.

    ```sql
    Select * from watermarktable
    ```
    
    Tady je výstup:
 
    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 | 

## <a name="add-more-data-to-source"></a>Přidání dalších dat do zdroje

Do databáze SQL (úložiště zdroje dat) vložte nová data.

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
``` 

Aktualizovaná data v databázi SQL vypadají takto:

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```


## <a name="trigger-another-pipeline-run"></a>Aktivace dalšího spuštění kanálu
1. Přepněte na kartu **Upravit**. Pokud kanál není otevřený v návrháři, klikněte na něj ve stromovém zobrazení. 

    ![Tlačítko Aktivovat](./media/tutorial-incremental-copy-portal/edit-tab.png)
2. Klikněte na **Aktivační událost** na panelu nástrojů a pak klikněte na **Aktivovat**. 

    ![Tlačítko Aktivovat](./media/tutorial-incremental-copy-portal/trigger-now.png)

## <a name="monitor-the-second-pipeline-run"></a>Monitorování druhého spuštění kanálu

1. Vlevo přepněte na kartu **Monitorování**. Zobrazí se stav ručně aktivovaného spuštění kanálu. Kliknutím na tlačítko **Aktualizovat** seznam aktualizujte. 
    
    ![Spuštění kanálu](./media/tutorial-incremental-copy-portal/pipeline-runs-2.png)
2. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, klikněte na první odkaz (**Zobrazit spuštění aktivit**) ve sloupci **Akce**. Zpět na předchozí zobrazení můžete přejít kliknutím na **Kanály** v horní části. Kliknutím na tlačítko **Aktualizovat** seznam aktualizujte.

    ![Spuštění aktivit](./media/tutorial-incremental-copy-portal/activity-runs-2.png)

## <a name="verify-the-second-output"></a>Ověření druhého výstupu

1. V úložišti objektů blob uvidíte, že se vytvořil další soubor. V tomto kurzu se tento nový jmenuje `Incremental-<GUID>.txt`. Otevřete tento soubor a uvidíte, že obsahuje dva řádky záznamů.

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. Zkontrolujte nejnovější hodnotu z `watermarktable`. Uvidíte, že hodnota meze byla znovu aktualizována.

    ```sql
    Select * from watermarktable
    ```
    Ukázkový výstup: 
    
    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |


     
## <a name="next-steps"></a>Další kroky
V tomto kurzu jste provedli následující kroky: 

> [!div class="checklist"]
> * Příprava úložiště dat pro uložení hodnoty meze
> * Vytvoření datové továrny
> * Vytvoření propojených služeb 
> * Vytvoření zdroje, jímky a datových sad mezí
> * Vytvoření kanálu
> * Spuštění kanálu
> * Monitorování spuštění kanálu 
> * Kontrola výsledků
> * Přidání dalších dat do zdroje
> * Opětovné spuštění kanálu
> * Monitorování druhého spuštění kanálu
> * Kontrola výsledků druhého spuštění

V tomto kurzu kanál zkopíroval data z jedné tabulky v databázi SQL do úložiště objektů blob. Přejděte na následující kurz, abyste se dozvěděli o kopírování dat z více tabulek v místní databázi SQL Serveru do databáze SQL. 

> [!div class="nextstepaction"]
>[Přírůstkové načtení dat z více tabulek v SQL Serveru do Azure SQL Database](tutorial-incremental-copy-multiple-tables-portal.md)



