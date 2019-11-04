---
title: Synchronizace dat z Azure SQL Database Edge pomocí Azure Data Factory | Microsoft Docs
description: Další informace o synchronizaci dat mezi Azure SQL Database Edge a úložištěm objektů BLOB v Azure
keywords: Edge SQL Database, synchronizace dat z okraje SQL Database, SQL Database Edge Data Factory
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 0e75da9516303bb4250b6847a4b381d07b3d7dad
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501320"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-using-azure-data-factory"></a>Kurz: synchronizace dat z SQL Database Edge do úložiště objektů BLOB v Azure pomocí Azure Data Factory

V tomto kurzu použijete Azure Data Factory k přírůstkové synchronizaci dat z tabulky v instanci Azure SQL Database Edge do úložiště objektů BLOB v Azure.

## <a name="before-you-begin"></a>Než začnete

Pokud jste ještě nevytvořili databázi nebo tabulku v nasazení Azure SQL Database Edge, použijte k vytvoření jednu z následujících metod:

* Pomocí [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) nebo [Azure Data Studio](/sql/azure-data-studio/download/) se připojte k SQL Database Edge a spusťte skript SQL a vytvořte databázi a tabulku.
* Vytvořte databázi SQL a tabulku pomocí nástroje [Sqlcmd](/sql/tools/sqlcmd-utility/) pomocí přímého připojení k modulu SQL Database Edge. Další informace najdete v tématu [připojení k databázovému stroji pomocí nástroje Sqlcmd](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Pomocí SQLPackage. exe nasaďte soubor DACPAC do kontejneru SQL Database Edge. To může být automatizované zadáním identifikátoru URI souboru SQLPackage jako součást konfigurace požadovaných vlastností nebo přímo pomocí nástroje klienta SqlPackage. exe k nasazení DACPAC pro SQL Database Edge.

    Pokud si chcete stáhnout SqlPackage, přečtěte si téma [Stažení a instalace SqlPackage](/sql/tools/sqlpackage-download/). K dispozici jsou následující ukázkové příkazy pro SqlPackage. exe, ale další informace najdete v dokumentaci k SqlPackage.

    **Vytvořit DACPAC**:

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name> 
    ```

    **Použít DACPAC**:

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Vytvoření tabulky a procedury SQL pro ukládání a aktualizaci úrovní meze

Tabulka meze se používá k uložení posledního časového razítka, na které již byla data synchronizována s úložištěm Azure. Uložená procedura Transact-SQL (T-SQL) se používá k aktualizaci tabulky meze po každé synchronizaci. 

Na instanci SQL Database Edge spusťte následující příkazy:

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp WHERE [TableName] = @TableName
    END
    Go
```

## <a name="create-a-data-factory-workflow"></a>Vytvoření pracovního postupu Data Factory

V této části vytvoříte kanál Azure Data Factory pro synchronizaci dat z tabulky v rámci Azure SQL Database Edge do úložiště objektů BLOB v Azure.

### <a name="create-data-factory-using-the-data-factory-ui"></a>Vytvoření Data Factory pomocí uživatelského rozhraní Data Factory

Pomocí pokynů v tomto [kurzu](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)vytvořte Data Factory.

### <a name="create-a-data-factory-pipeline"></a>Vytvoření kanálu Data Factory

1. Na stránce **Začínáme** v uživatelském rozhraní Data Factory vyberte dlaždici **vytvořit kanál** .

    ![Data Factory – vytvoření kanálu](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Na stránce **Obecné** v okně **vlastnosti** pro kanál zadejte název **PeriodicSync** .

3. Přidejte aktivitu **vyhledávání** a získejte tak starou hodnotu meze. Na **panelu nástrojů aktivity**rozbalte **obecné**a přetáhněte & přetáhněte aktivitu **vyhledávání** na plochu návrháře kanálu. Změňte název aktivity na *OldWatermark*.

    ![staré vyhledávání vodoznaků](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Přepněte na kartu **Nastavení** a vyberte **+ Nová** pro **zdrojovou datovou sadu**. V tomto kroku vytvoříte datovou sadu, která bude reprezentovat data v poli s vodotiskem. Tato tabulka obsahuje starou mez, která se použila v předchozí operaci kopírování.

5. V okně **Nová datová sada** vyberte **Azure SQL Server**a vyberte **pokračovat**.  

6. V okně **nastavit vlastnosti** pro datovou sadu zadejte *WatermarkDataset* pro název.

7. V poli **propojená služba**vyberte **Nový**a proveďte následující kroky:

    1. Jako **název**zadejte *SQLDBEdgeLinkedService* .

    2. Zadejte podrobnosti serveru SQL Database Edge pro **název serveru**.

    3. Zadejte **název vaší databáze** z rozevíracího seznamu.

    4. Zadejte své **uživatelské jméno** a **heslo**.

    5. Chcete-li otestovat připojení k instanci SQL Database Edge, vyberte možnost **Test připojení**.

    6. Vyberte **Create** (Vytvořit).

    ![vytvořit propojenou službu](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Vybrat **OK**

8. Na kartě **Nastavení** vyberte **Upravit**.

9. Na kartě **připojení** vyberte *[dbo]. [ vodotisk]* pro **tabulku**. Chcete-li zobrazit náhled dat v tabulce, vyberte možnost **Náhled dat**.

10. Přepněte do editoru kanálů tak, že vyberete kartu kanál v horní části nebo vyberete název kanálu ve stromovém zobrazení vlevo. V okně vlastnosti **aktivity vyhledávání**potvrďte, že je vybrána možnost **WatermarkDataset** pro pole **zdrojové datové sady** .

11. Na panelu nástrojů **aktivity** rozbalte **Obecné**, přetáhněte a přetáhněte další aktivitu **vyhledávání** na plochu návrháře kanálu a na kartě **Obecné** v okně Vlastnosti nastavte název na **NewWatermark** . Tato aktivita vyhledávání získá hodnotu meze z tabulky se zdrojovými daty, která se mají zkopírovat do cíle.

12. V okně Vlastnosti druhé aktivity **vyhledávání** přepněte na kartu **Nastavení** a výběrem možnosti **nové** Vytvořte datovou sadu, která bude ukazovat na zdrojovou tabulku, která obsahuje novou hodnotu meze.

13. V okně **Nová datová sada** vyberte SQL Database Edge instance a vyberte **pokračovat**.

    1. V okně **nastavit vlastnosti** jako **název**zadejte **SourceDataset** . Jako propojená služba vyberte *SQLDBEdgeLinkedService* .

    2. Vyberte ***tabulku, kterou chcete synchronizovat*** pro tabulku. Můžete také zadat dotaz pro tuto datovou sadu, jak je uvedeno dále v tomto kurzu. Dotaz má přednost před tabulkou, kterou zadáte v tomto kroku.

    3. Vyberte **OK**.

14. Přepněte do editoru kanálů tak, že vyberete kartu kanál v horní části nebo vyberete název kanálu ve stromovém zobrazení vlevo. V okně Vlastnosti aktivity **Vyhledávání** ověřte, že je v poli **Zdrojová datová sada** vybraná datová sada **SourceDataset**.

15. Pro pole **použít dotaz** vyberte **dotaz** a po aktualizaci názvu tabulky v dotazu zadejte následující dotaz: v tabulce vybíráte pouze maximální hodnotu časového razítka. Ujistěte se prosím, že jste taky zkontrolovali **jenom první řádek**.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![vybrat dotaz](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. Na panelu nástrojů **aktivity** rozbalte **Přesunout & transformovat**, přetáhněte aktivitu **kopírování** z panelu nástrojů aktivity a nastavte její název na **IncrementalCopy**.

17. Propojte obě aktivity **vyhledávání** s aktivitou **kopírování** přetažením **zeleného tlačítka** připojeného k aktivitám **vyhledávání** do aktivity **kopírování** . Až se barva ohraničení aktivity kopírování změní na modrou, uvolněte tlačítko myši.

18. Vyberte aktivitu **kopírování** a potvrďte, že se v okně **vlastnosti** zobrazí vlastnosti aktivity.

19. V okně **Vlastnosti** přepněte na kartu **Zdroj** a proveďte následující kroky:

    1. V poli **Zdrojová datová sada** vyberte **SourceDataset**.

    2. V poli **Použít dotaz** vyberte **Dotaz**.

    3. Zadejte dotaz SQL pro pole **dotazu** . Ukázkový dotaz níže

    4. Dotaz SQL:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Přepněte na kartu **jímka** a v poli **datová sada jímky** vyberte **+ Nová** .

21. V tomto kurzu je úložiště dat jímky typu **úložiště objektů BLOB v Azure**. Vyberte **úložiště objektů BLOB v Azure**a v okně **Nová datová sada** vyberte **pokračovat** .

22. V okně **Vybrat formát** vyberte typ formátu dat a vyberte **pokračovat**.

23. V okně **nastavit vlastnosti** jako název zadejte **SinkDataset** . V případě propojené služby vyberte **+ Nová**. V tomto kroku vytvoříte připojení (propojenou službu) ke svému **úložišti objektů blob v Azure**.

24. V okně **Nová propojená služba (Azure Blob Storage)** proveďte následující kroky:

    1. Jako název zadejte *AzureStorageLinkedService* .

    2. Vyberte účet Azure Storage pro **název účtu úložiště** s vaším předplatným Azure.

    3. Otestujte **připojení** a pak vyberte **Dokončit**.

25. V okně **nastavit vlastnosti** potvrďte, že je pro **propojenou službu**vybraná možnost *AzureStorageLinkedService* . Pak vyberte **vytvořit** a **OK**.

26. Na kartě **jímka** vyberte **Upravit**.

27. Přejít na kartu **připojení** *SinkDataset* a proveďte následující kroky:

    1. Do pole **cesta k souboru** zadejte *asdedatasync/incrementalcopy*, kde **adftutorial** je název kontejneru objektů BLOB a **incrementalcopy** je název složky. Pokud tento kontejner neexistuje, vytvořte ho nebo použijte název existujícího kontejneru. Azure Data Factory automaticky vytvoří výstupní složku *incrementalcopy*, pokud neexistuje. Můžete také použít tlačítko **Procházet** u možnosti **Cesta k souboru** a přejít ke složce v kontejneru objektů blob.

    2. V části **soubor** **cesta k souboru** vyberte **Přidat dynamický obsah [ALT + P]** a pak *Zadejte @CONCAT(přírůstkové –, kanál (). RunId, '. txt ')* v otevřeném okně. Pak vyberte **Dokončit**. Název souboru se vygeneruje dynamicky pomocí tohoto výrazu. Každé spuštění kanálu má jedinečné ID. Aktivita kopírování používá ID spuštění k vygenerování názvu souboru.

28. Přepněte do editoru **kanálů** tak, že vyberete kartu kanál v horní části nebo vyberete název kanálu ve stromovém zobrazení vlevo.

29. V sadě nástrojů **Aktivity** rozbalte **Obecné** a přetáhněte aktivitu **Uložená procedura** ze sady nástrojů **Aktivity** na plochu návrháře kanálu. **Připojte** zelený výstup (Úspěch) aktivity **Kopírování** k aktivitě **Uložená procedura**.

30. Vyberte v Návrháři kanálu **aktivitu uložená procedura** a změňte její název na *SPtoUpdateWatermarkActivity*.

31. Přepněte na kartu **účet SQL** a jako **propojená služba**vyberte *SQLDBEdgeLinkedService* .

32. Přepněte na kartu **Uložená procedura** a proveďte následující kroky:

    1. Jako **název uložené procedury**vyberte *[dbo]. [ usp_write_watermark]* .

    2. Chcete-li zadat hodnoty parametrů uložené procedury, vyberte možnost importovat parametr a zadejte následující hodnoty parametrů:

    |Name (Název)|Typ|Hodnota|
    |-----|----|-----|
    |LastModifiedtime|Datum a čas|@ {Activity (' NewWaterMark '). Output. firstRow. NewWatermarkvalue}|
    |TableName|Řetězec|@ {Activity (' OldWaterMark '). Output. firstRow. TableName}|

33. Pokud chcete ověřit nastavení kanálu, vyberte **ověřit** na panelu nástrojů. Ověřte, že se nezobrazí žádné chyby ověření. Chcete-li zavřít okno **Sestava ověření kanálu** , vyberte možnost **>>** .

34. Pomocí tlačítka **Publikovat vše** publikujte entity (propojené služby, datové sady a kanály) do služby Azure Data Factory. Počkejte, dokud se nezobrazí zpráva o úspěšném publikování.

## <a name="trigger-a-pipeline-on-schedule"></a>Aktivace kanálu podle plánu

1. Na panelu nástrojů kanál vyberte **Přidat Trigger**a pak vyberte **Nový/upravit**, vyberte **+ Nový**.

2. Pojmenujte Trigger pomocí *HourlySync*, vyberte **typ** jako plán a nastavte **opakování** na 1 hodinu.

3. Vyberte **OK**.

4. Vyberte **Publikovat vše**.

5. Vyberte **aktivovat nyní**.

6. Vlevo přepněte na kartu **Monitorování**. Zobrazí se stav ručně aktivovaného spuštění kanálu. Kliknutím na tlačítko **aktualizovat** seznam aktualizujte.

## <a name="next-steps"></a>Další kroky

Kanál Azure Data Factory v tomto kurzu kopíruje data z tabulky v instanci SQL Database Edge do umístění v úložišti objektů BLOB v Azure s hodinovou frekvencí. Pokud se chcete dozvědět víc o použití Data Factory ve více scénářích, Projděte si tyto [kurzy](../data-factory/tutorial-copy-data-portal.md).
