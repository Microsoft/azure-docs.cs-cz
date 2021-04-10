---
title: Přírůstkové kopírování tabulky pomocí Azure Portal
description: V tomto kurzu vytvoříte Azure Data Factory s kanálem, který načte rozdílová data z tabulky v Azure SQL Database do úložiště objektů BLOB v Azure.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 310182a3b46f0682efe420387bba0da311707e8a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606595"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-the-azure-portal"></a>Přírůstkové načtení dat z Azure SQL Database do úložiště objektů BLOB v Azure pomocí Azure Portal

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V tomto kurzu vytvoříte Azure Data Factory s kanálem, který načte rozdílová data z tabulky v Azure SQL Database do úložiště objektů BLOB v Azure.

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

![Přírůstkové načtení dat](media/tutorial-Incremental-copy-portal/incrementally-load.png)

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
* **Azure SQL Database**. Tuto databázi použijete jako zdrojové úložiště dat. Pokud nemáte databázi v Azure SQL Database, přečtěte si téma [Vytvoření databáze v Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) , kde najdete kroky pro její vytvoření.
* **Azure Storage**. Úložiště objektů blob použijete jako úložiště dat jímky. Pokud nemáte účet úložiště, přečtěte si téma [Vytvoření účtu úložiště](../storage/common/storage-account-create.md), kde najdete kroky pro jeho vytvoření. Vytvořte kontejner s názvem adftutorial. 

### <a name="create-a-data-source-table-in-your-sql-database"></a>Vytvoření tabulky zdroje dat v databázi SQL
1. Otevřete sadu SQL Server Management Studio. V **Průzkumník serveru** klikněte pravým tlačítkem na databázi a vyberte **Nový dotaz**.

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
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime
WHERE [TableName] = @TableName

END
```

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
2. V nabídce vlevo vyberte vytvořit data Factory **pro**  >  **integraci** prostředků  >  :

   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Na stránce **Nová datová továrna** jako **název** zadejte **ADFIncCopyTutorialDF**.

   Název Azure Data Factory musí být **globálně jedinečný**. Pokud se zobrazí červený vykřičník s následující chybou, změňte název datové továrny (například na vaše_jméno_ADFIncCopyTutorialDF) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).

    *Název objektu pro vytváření dat "ADFIncCopyTutorialDF" není k dispozici.*
4. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu.
5. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:

      - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.
      - Vyberte **vytvořit novou** a zadejte název skupiny prostředků.   
         
        Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
6. Jako **verzi** vyberte **V2**.
7. Vyberte **umístění** pro objekt pro vytváření dat. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (Azure Storage, Azure SQL Database, spravované instance Azure SQL atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou můžou být v jiných oblastech.
8. Klikněte na **Vytvořit**.      
9. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Domovská stránka Azure Data Factory s dlaždicí pro monitorování autora &.":::
10. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Azure Data Factory.

## <a name="create-a-pipeline"></a>Vytvoření kanálu
V tomto kurzu vytvoříte kanál se dvěma aktivitami vyhledávání, jednou aktivitou kopírování a jednou aktivitou uložené procedury, a to všechno zřetězené v jednom kanálu.

1. Na stránce **Začínáme** uživatelského rozhraní služby Data Factory klikněte na dlaždici **Vytvořit kanál**.

   ![Stránka Začínáme uživatelského rozhraní služby Data Factory](./media/doc-common-process/get-started-page.png)    
3. Na panelu Obecné v části **vlastnosti** zadejte **IncrementalCopyPipeline** pro **název**. Pak panel sbalíte kliknutím na ikonu vlastnosti v pravém horním rohu.

4. Přidejme první aktivitu vyhledávání, která načte poslední hodnotu meze. V sadě nástrojů **Aktivity** rozbalte **Obecné** a přetáhněte aktivitu **Vyhledávání** na plochu návrháře kanálu. Změňte název aktivity na **LookupOldWaterMarkActivity**.

   ![První aktivita vyhledávání – název](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. Přepněte na kartu **Nastavení** a klikněte na **+ Nová** u možnosti **Zdrojová datová sada**. V tomto kroku vytvoříte datovou sadu, která bude představovat data v tabulce **watermarktable**. Tato tabulka obsahuje starou mez, která se použila v předchozí operaci kopírování.

6. V okně **Nová datová sada** vyberte **Azure SQL Database** a klikněte na **pokračovat**. Zobrazí se nové okno otevřené pro datovou sadu.

7. V okně **nastavit vlastnosti** pro datovou sadu zadejte **WatermarkDataset** pro **název**.

8. V poli **propojená služba** vyberte **Nový** a pak proveďte následující kroky:

    1. Jako **Název** zadejte **AzureSqlDatabaseLinkedService**.
    2. Jako **název serveru** vyberte svůj server.
    3. Z rozevíracího seznamu vyberte **název vaší databáze** .
    4. Zadejte heslo k **uživatelskému jménu**  &  .
    5. Chcete-li otestovat připojení k vaší databázi SQL, klikněte na tlačítko **Test připojení**.
    6. Klikněte na **Finish** (Dokončit).
    7. Potvrďte, že je pro **propojenou službu** vybraná možnost **AzureSqlDatabaseLinkedService** .

        ![Okno Nová propojená služba](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
    8. Vyberte **Dokončit**.
9. Na kartě **připojení** vyberte **[dbo]. [ vodotisk]** pro **tabulku**. Pokud chcete zobrazit náhled dat v tabulce, klikněte na **Náhled dat**.

    ![Datová sada meze – nastavení připojení](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
10. Přepněte na editor kanálu kliknutím na kartu kanálu v horní části nebo kliknutím na název kanálu ve stromovém zobrazení vlevo. V okně Vlastnosti aktivity **Vyhledávání** ověřte, že je v poli **Zdrojová datová sada** vybraná datová sada **WatermarkDataset**.

11. V sadě nástrojů **Aktivity** rozbalte **Obecné** a přetáhněte další aktivitu **Vyhledávání** na plochu návrháře kanálu. Pak na kartě **Obecné** v okně vlastností nastavte název na **LookupNewWaterMarkActivity**. Tato aktivita vyhledávání získá hodnotu meze z tabulky se zdrojovými daty, která se mají zkopírovat do cíle.

12. V okně Vlastnosti druhé aktivity **Vyhledávání** přepněte na kartu **Nastavení** a klikněte na **Nový**. Vytvoříte datovou sadu, která bude odkazovat na zdrojovou tabulku obsahující novou hodnotu meze (maximální hodnota LastModifyTime).

13. V okně **Nová datová sada** vyberte **Azure SQL Database** a klikněte na **pokračovat**.
14. V okně **nastavit vlastnosti** jako **název** zadejte **SourceDataset** . Jako **Propojená služba** vyberte **AzureSqlDatabaseLinkedService**.
15. Vyberte **[dbo]. [ data_source_table]** pro tabulku. Později v tomto kurzu pro tuto datovou sadu zadáte dotaz. Dotaz má přednost před tabulkou, kterou zadáte v tomto kroku.
16. Vyberte **Dokončit**.
17. Přepněte na editor kanálu kliknutím na kartu kanálu v horní části nebo kliknutím na název kanálu ve stromovém zobrazení vlevo. V okně Vlastnosti aktivity **Vyhledávání** ověřte, že je v poli **Zdrojová datová sada** vybraná datová sada **SourceDataset**.
18. V poli **Použít dotaz** vyberte **Dotaz** a zadejte následující dotaz: vybíráte pouze maximální hodnotu **LastModifytime** z tabulky **tabulka_zdroje_dat**. Ujistěte se prosím, že jste taky zkontrolovali **jenom první řádek**.

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![Druhá aktivita vyhledávání – dotaz](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. Na panelu nástrojů **aktivity** rozbalte **přesunout & transformovat** a přetáhněte aktivitu **kopírování** z panelu nástrojů aktivity a nastavte název na **IncrementalCopyActivity**.

20. **Propojte obě aktivity vyhledávání s aktivitou kopírování** přetažením **zeleného tlačítka** připojeného k aktivitám vyhledávání na aktivitu kopírování. Jakmile se barva ohraničení aktivity kopírování změní na modrou, uvolněte tlačítko myši.

    ![Propojení aktivit vyhledávání s aktivitou kopírování](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. Vyberte **aktivitu kopírování** a ověřte, že se v okně **Vlastnosti** zobrazí vlastnosti aktivity.

22. V okně **Vlastnosti** přepněte na kartu **Zdroj** a proveďte následující kroky:

    1. V poli **Zdrojová datová sada** vyberte **SourceDataset**.
    2. V poli **Použít dotaz** vyberte **Dotaz**.
    3. Do pole **Dotaz** zadejte následující příkaz jazyka SQL.

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```

        ![Aktivita kopírování – zdroj](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. Přepněte na kartu **Jímka** a klikněte na **+ Nová** vedle pole **Datová sada jímky**.

24. V tomto kurzu je úložiště dat jímky typu Azure Blob Storage. Proto vyberte **Azure Blob Storage** a v okně **Nová datová sada** klikněte na **pokračovat** .
25. V okně **Vybrat formát** vyberte typ formátu dat a klikněte na **pokračovat**.
25. V okně **nastavit vlastnosti** jako **název** zadejte **SinkDataset** . V případě **propojené služby** vyberte **+ Nová**. V tomto kroku vytvoříte připojení (propojenou službu) ke svému **úložišti objektů blob v Azure**.
26. V okně **Nová propojená služba (Azure Blob Storage)** proveďte následující kroky:

    1. Jako **Název** zadejte **AzureStorageLinkedService**.
    2. Jako **Název účtu úložiště** vyberte svůj účet služby Azure Storage.
    3. Otestujte připojení a pak klikněte na **Dokončit**.

27. V okně **nastavit vlastnosti** potvrďte, že je pro **propojenou službu** vybraná možnost **AzureStorageLinkedService** . Pak vyberte **Dokončit**.
28. Přejít na kartu **připojení** SinkDataset a proveďte následující kroky:
    1. Do pole **cesta k souboru** zadejte **adftutorial/incrementalcopy**. **adftutorial** je název kontejneru objektů blob a **incrementalcopy** je název složky. Tento fragment kódu předpokládá, že ve svém úložišti objektů blob máte kontejner objektů blob s názvem adftutorial. Pokud tento kontejner neexistuje, vytvořte ho nebo použijte název existujícího kontejneru. Azure Data Factory automaticky vytvoří výstupní složku **incrementalcopy**, pokud neexistuje. Můžete také použít tlačítko **Procházet** u možnosti **Cesta k souboru** a přejít ke složce v kontejneru objektů blob.
    2. V části **soubor** **cesta k souboru** vyberte **Přidat dynamický obsah [ALT + P]** a potom `@CONCAT('Incremental-', pipeline().RunId, '.txt')` v otevřeném okně zadejte. Pak vyberte **Dokončit**. Název souboru se vygeneruje dynamicky pomocí tohoto výrazu. Každé spuštění kanálu má jedinečné ID. Aktivita kopírování používá ID spuštění k vygenerování názvu souboru.

28. Přepněte na editor **kanálu** kliknutím na kartu kanálu v horní části nebo kliknutím na název kanálu ve stromovém zobrazení vlevo.
29. V sadě nástrojů **Aktivity** rozbalte **Obecné** a přetáhněte aktivitu **Uložená procedura** ze sady nástrojů **Aktivity** na plochu návrháře kanálu. **Připojte** zelený výstup (Úspěch) aktivity **Kopírování** k aktivitě **Uložená procedura**.

24. Vyberte v návrháři kanálu **aktivitu Uložená procedura** a změňte její název na **StoredProceduretoWriteWatermarkActivity**.

25. Přepněte na kartu **účet SQL** a jako **propojená služba** vyberte **AzureSqlDatabaseLinkedService** .

26. Přepněte na kartu **Uložená procedura** a proveďte následující kroky:

    1. Jako **název uložené procedury** vyberte **usp_write_watermark**.
    2. Pokud chcete zadat hodnoty parametrů uložené procedury, klikněte na **Importovat parametr** a zadejte následující hodnoty parametrů:

        | Název | Typ | Hodnota |
        | ---- | ---- | ----- |
        | LastModifiedtime | DateTime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | Řetězec | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

        ![Aktivita Uložená procedura – nastavení uložené procedury](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. Pokud chcete ověřit nastavení kanálu, klikněte na **Ověřit** na panelu nástrojů. Ověřte, že se nezobrazí žádné chyby ověření. Pokud chcete okno **Sestava ověření kanálu** zavřít, klikněte na >>.   

28. Pomocí tlačítka **Publikovat vše** publikujte entity (propojené služby, datové sady a kanály) do služby Azure Data Factory. Počkejte, dokud se nezobrazí zpráva o úspěšném publikování.


## <a name="trigger-a-pipeline-run"></a>Aktivace spuštění kanálu
1. Klikněte na tlačítko **Přidat aktivační událost** na panelu nástrojů a pak klikněte na **aktivovat nyní**.

2. V okně **Spuštění kanálu** vyberte **Dokončit**.

## <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1. Vlevo přepněte na kartu **Monitorování**. Zobrazí se stav běhu aktivovaného ruční triggerem. Pomocí odkazů ve sloupci **název kanálu** můžete zobrazit podrobnosti o spuštění a znovu spustit kanál.

2. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte odkaz pod sloupcem **název kanálu** . Chcete-li zobrazit podrobnosti o spuštění aktivity, vyberte odkaz **Podrobnosti** (ikona brýlí) ve sloupci **název aktivity** . Výběrem možnosti **všechny spuštěné kanály** v horní části přejdete zpátky k zobrazení spuštění kanálu. Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**.


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

Vložte nová data do databáze (úložiště zdrojů dat).

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
```

Aktualizovaná data ve vaší databázi jsou:

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

1. Přepněte na kartu **Upravit** . Pokud není otevřen v návrháři, klikněte na kanál ve stromovém zobrazení.

2. Klikněte na tlačítko **Přidat aktivační událost** na panelu nástrojů a pak klikněte na **aktivovat nyní**.


## <a name="monitor-the-second-pipeline-run"></a>Monitorování druhého spuštění kanálu

1. Vlevo přepněte na kartu **Monitorování**. Zobrazí se stav běhu aktivovaného ruční triggerem. Pomocí odkazů ve sloupci **název kanálu** můžete zobrazit podrobnosti o aktivitách a znovu spustit kanál.

2. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte odkaz pod sloupcem **název kanálu** . Chcete-li zobrazit podrobnosti o spuštění aktivity, vyberte odkaz **Podrobnosti** (ikona brýlí) ve sloupci **název aktivity** . Výběrem možnosti **všechny spuštěné kanály** v horní části přejdete zpátky k zobrazení spuštění kanálu. Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**.


## <a name="verify-the-second-output"></a>Ověření druhého výstupu

1. V úložišti objektů blob uvidíte, že byl vytvořen další soubor. V tomto kurzu se tento nový jmenuje `Incremental-<GUID>.txt`. Otevřete tento soubor a uvidíte, že obsahuje dva řádky záznamů.

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

V tomto kurzu kanál zkopíroval data z jedné tabulky v SQL Database do úložiště objektů BLOB. Přejděte k následujícímu kurzu, kde se dozvíte, jak kopírovat data z několika tabulek v databázi SQL Server do SQL Database.

> [!div class="nextstepaction"]
>[Přírůstkové načtení dat z více tabulek v SQL Serveru do Azure SQL Database](tutorial-incremental-copy-multiple-tables-portal.md)
