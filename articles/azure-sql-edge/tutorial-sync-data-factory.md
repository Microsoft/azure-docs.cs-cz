---
title: Synchronizace dat z Azure SQL Edge pomocí Azure Data Factory
description: Další informace o synchronizaci dat mezi Azure SQL Edge a Azure Blob Storage
keywords: SQL Edge, synchronizace dat z SQL Edge a SQL Edge Data Factory
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 672c9f0d5403ae27a26d58617dca44f0f1121411
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904166"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>Kurz: synchronizace dat z Edge SQL do úložiště objektů BLOB v Azure pomocí Azure Data Factory

V tomto kurzu použijete Azure Data Factory k přírůstkové synchronizaci dat do služby Azure Blob Storage z tabulky v instanci Azure SQL Edge.

## <a name="before-you-begin"></a>Než začnete

Pokud jste ještě nevytvořili databázi nebo tabulku v nasazení Azure SQL Edge, použijte jednu z těchto metod k jejímu vytvoření:

* K připojení k SQL Edge použijte [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) nebo [Azure Data Studio](/sql/azure-data-studio/download/) . Spusťte skript SQL pro vytvoření databáze a tabulky.
* Vytvořte databázi a tabulku pomocí nástroje [Sqlcmd](/sql/tools/sqlcmd-utility/) přímým připojením k modulu SQL Edge. Další informace najdete v tématu [připojení k databázovému stroji pomocí nástroje Sqlcmd](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Pomocí SQLPackage.exe nasaďte soubor balíčku DAC do kontejneru SQL Edge. Tento proces můžete automatizovat zadáním identifikátoru URI souboru SqlPackage jako součást konfigurace požadovaných vlastností modulu. K nasazení balíčku DAC do SQL Edge můžete také použít přímo Nástroj klienta SqlPackage.exe.

    Informace o tom, jak stáhnout SqlPackage.exe, najdete v tématu [Stažení a instalace SqlPackage](/sql/tools/sqlpackage-download/). Následuje několik ukázkových příkazů pro SqlPackage.exe. Další informace najdete v dokumentaci k SqlPackage.exe.

    **Vytvoření balíčku DAC**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **Použití balíčku DAC**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Vytvoření tabulky a procedury SQL pro ukládání a aktualizaci úrovní meze

Tabulka meze se používá k uložení posledního časového razítka, na které už byla synchronizovaná data s Azure Storage. Uloženou proceduru jazyka Transact-SQL (T-SQL) slouží k aktualizaci tabulky meze po každé synchronizaci.

Spusťte tyto příkazy na instanci SQL Edge:

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

## <a name="create-a-data-factory-pipeline"></a>Vytvoření kanálu Data Factory

V této části vytvoříte kanál Azure Data Factory k synchronizaci dat do služby Azure Blob Storage z tabulky ve službě Azure SQL Edge.

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>Vytvoření datové továrny pomocí Data Factoryho uživatelského rozhraní

Pomocí pokynů v [tomto kurzu](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)Vytvořte datovou továrnu.

### <a name="create-a-data-factory-pipeline"></a>Vytvoření kanálu Data Factory

1. Na stránce **Začínáme** v uživatelském rozhraní Data Factory vyberte **vytvořit kanál**.

    ![Vytvoření kanálu Data Factory](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Na stránce **Obecné** v okně **vlastnosti** pro kanál zadejte **PeriodicSync** pro název.

3. Přidejte aktivitu vyhledávání a získejte tak starou hodnotu meze. V podokně **aktivity** rozbalte **Obecné** a přetáhněte aktivitu **vyhledávání** na plochu návrháře kanálu. Změňte název aktivity na **OldWatermark**.

    ![Přidání starého vyhledávání vodoznaku](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Přepněte na kartu **Nastavení** a pro **zdrojovou datovou sadu**vyberte **Nový** . Nyní vytvoříte datovou sadu, která bude představovat data v tabulce meze. Tato tabulka obsahuje starou mez, která se použila v předchozí operaci kopírování.

5. V okně **Nová datová sada** vyberte **Azure SQL Server**a pak vyberte **pokračovat**.  

6. V okně **nastavit vlastnosti** pro datovou sadu v části **název**zadejte **WatermarkDataset**.

7. V případě **propojené služby**vyberte **Nový**a pak dokončete tyto kroky:

    1. Do **pole název**zadejte **SQLDBEdgeLinkedService**.

    2. V části **název serveru**zadejte podrobnosti serveru SQL Edge.

    3. V seznamu vyberte **název vaší databáze** .

    4. Zadejte své **uživatelské jméno** a **heslo**.

    5. Chcete-li otestovat připojení k instanci SQL Edge, vyberte možnost **Test připojení**.

    6. Vyberte **Vytvořit**.

    ![Vytvoření propojené služby](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Vyberte **OK**.

8. Na kartě **Nastavení** vyberte **Upravit**.

9. Na kartě **připojení** vyberte **[dbo]. [ vodotisk]** pro **tabulku**. Chcete-li zobrazit náhled dat v tabulce, vyberte možnost **Náhled dat**.

10. Přepněte do editoru kanálů tak, že vyberete kartu kanál v horní části nebo vyberete název kanálu ve stromovém zobrazení vlevo. V okně vlastnosti aktivity vyhledávání potvrďte, že je v seznamu **zdrojovou datovou sadu** vybraná možnost **WatermarkDataset** .

11. V podokně **aktivity** rozbalte **Obecné** a přetáhněte další aktivitu **vyhledávání** na plochu návrháře kanálu. Na kartě **Obecné** v okně Vlastnosti nastavte název na **NewWatermark** . Tato aktivita vyhledávání získá novou hodnotu meze z tabulky, která obsahuje zdrojová data, aby se mohla zkopírovat do cíle.

12. V okně Vlastnosti druhé aktivity vyhledávání přepněte na kartu **Nastavení** a výběrem možnosti **nové** Vytvořte datovou sadu, která bude odkazovat na zdrojovou tabulku, která obsahuje novou hodnotu meze.

13. V okně **Nová datová sada** vyberte **instance SQL Edge**a pak vyberte **pokračovat**.

    1. V okně **nastavit vlastnosti** v části **název**zadejte **SourceDataset**. V části **propojená služba**vyberte **SQLDBEdgeLinkedService**.

    2. V části **tabulka**vyberte tabulku, kterou chcete synchronizovat. Můžete také zadat dotaz pro tuto datovou sadu, jak je popsáno dále v tomto kurzu. Dotaz má přednost před tabulkou, kterou zadáte v tomto kroku.

    3. Vyberte **OK**.

14. Přepněte do editoru kanálů tak, že vyberete kartu kanál v horní části nebo vyberete název kanálu ve stromovém zobrazení vlevo. V okně vlastnosti aktivity vyhledávání potvrďte, že je v seznamu **zdrojovou datovou sadu** vybraná možnost **SourceDataset** .

15. V části **použít dotaz**vyberte **dotaz** . Aktualizujte název tabulky v následujícím dotazu a pak zadejte dotaz. Vybíráte pouze maximální hodnotu `timestamp` z tabulky. Nezapomeňte vybrat **pouze první řádek**.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![vybrat dotaz](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. V podokně **aktivity** rozbalte **přesunout & transformovat** a přetáhněte aktivitu **kopírování** z podokna **aktivity** na plochu návrháře. Nastavte název aktivity na **IncrementalCopy**.

17. Propojte obě aktivity vyhledávání s aktivitou kopírování přetažením zeleného tlačítka připojeného k aktivitám vyhledávání na aktivitu kopírování. Až se barva ohraničení aktivity kopírování změní na modrou, uvolněte tlačítko myši.

18. Vyberte aktivitu kopírování a ověřte, že se v okně **Vlastnosti** zobrazí vlastnosti aktivity.

19. V okně **vlastnosti** přepněte na kartu **zdroj** a proveďte tyto kroky:

    1. V poli **zdrojová datová sada** vyberte **SourceDataset**.

    2. V části **použít dotaz**vyberte **dotaz**.

    3. Do pole **dotaz** zadejte dotaz SQL. Tady je ukázkový dotaz:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Na kartě **jímka** vyberte v části **datová sada jímky**možnost **Nový** .

21. V tomto kurzu je úložiště dat jímky úložiště dat úložiště objektů BLOB v Azure. Vyberte **úložiště objektů BLOB v Azure**a potom v okně **Nová datová sada** vyberte **pokračovat** .

22. V okně **Vybrat formát** vyberte formát dat a pak vyberte **pokračovat**.

23. V okně **nastavit vlastnosti** v části **název**zadejte **SinkDataset**. V části **propojená služba**vyberte **Nový**. Nyní vytvoříte připojení (propojená služba) k úložišti objektů BLOB v Azure.

24. V okně **Nová propojená služba (Azure Blob Storage)** proveďte tyto kroky:

    1. Do pole **název** zadejte **AzureStorageLinkedService**.

    2. V části **název účtu úložiště**vyberte účet úložiště Azure pro vaše předplatné Azure.

    3. Otestujte připojení a pak vyberte **Dokončit**.

25. V okně **nastavit vlastnosti** potvrďte, že je v části **propojená služba**vybraná možnost **AzureStorageLinkedService** . Vyberte **vytvořit** a **OK**.

26. Na kartě **jímka** vyberte **Upravit**.

27. V SinkDataset klikněte na kartu **připojení** a proveďte tyto kroky:

    1. V části **cesta k souboru**zadejte *asdedatasync/incrementalcopy*, kde *asdedatasync* je název kontejneru objektů BLOB a *incrementalcopy* je název složky. Pokud tento kontejner neexistuje, vytvořte ho nebo použijte název existujícího kontejneru. Azure Data Factory automaticky vytvoří výstupní složku *incrementalcopy* , pokud neexistuje. Můžete také použít tlačítko **Procházet** u možnosti **Cesta k souboru** a přejít ke složce v kontejneru objektů blob.

    2. V části **soubor** **cesta**k souboru vyberte **Přidat dynamický obsah [ALT + P]** a potom zadejte ** @CONCAT ("přírůstkové,", kanál (). RunId, '. txt ')** v okně, které se otevře. Vyberte **Dokončit**. Název souboru se dynamicky generuje pomocí výrazu. Každé spuštění kanálu má jedinečné ID. Aktivita kopírování používá ID spuštění k vygenerování názvu souboru.

28. Přepněte do editoru kanálů tak, že vyberete kartu kanál v horní části nebo vyberete název kanálu ve stromovém zobrazení vlevo.

29. V podokně **aktivity** rozbalte **Obecné** a přetáhněte aktivitu **uložená procedura** z podokna **aktivity** na plochu návrháře kanálu. Propojte zelenou (úspěšnost) výstup aktivity kopírování s aktivitou uložené procedury.

30. Vyberte v Návrháři kanálu **aktivitu uložená procedura** a změňte její název na **SPtoUpdateWatermarkActivity**.

31. Přepněte na kartu **účet SQL** a v části **propojená služba**vyberte ***QLDBEdgeLinkedService** .

32. Přepněte na kartu **uložená procedura** a proveďte tyto kroky:

    1. V části **název uložené procedury**vyberte **[dbo]. [ usp_write_watermark]**.

    2. Chcete-li zadat hodnoty parametrů uložené procedury, vyberte možnost **importovat parametr** a zadejte tyto hodnoty parametrů:

    |Název|Typ|Hodnota|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@ {Activity (' NewWaterMark '). Output. firstRow. NewWatermarkvalue}|
    |TableName|Řetězec|@ {Activity (' OldWaterMark '). Output. firstRow. TableName}|

33. Pokud chcete ověřit nastavení kanálu, vyberte **ověřit** na panelu nástrojů. Ověřte, že se nezobrazí žádné chyby ověření. Chcete-li zavřít okno **Sestava ověření kanálu** , vyberte **>>** .

34. Publikujte entity (propojené služby, datové sady a kanály) do služby Azure Data Factory tak, že vyberete tlačítko **publikovat vše** . Počkejte, dokud se nezobrazí zpráva s potvrzením, že operace publikování proběhla úspěšně.

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>Aktivace kanálu podle plánu

1. Na panelu nástrojů kanálu vyberte **Přidat Trigger**, vyberte **Nový/upravit**a pak vyberte **Nový**.

2. Pojmenujte aktivační událost **HourlySync**. V části **typ**vyberte **plán**. Nastavte **opakování** na každých 1 hodinu.

3. Vyberte **OK**.

4. Vyberte **Publikovat vše**.

5. Vyberte **aktivovat nyní**.

6. Vlevo přepněte na kartu **Monitorování**. Zobrazí se stav ručně aktivovaného spuštění kanálu. Seznam můžete aktualizovat kliknutím na **Aktualizovat**.

## <a name="next-steps"></a>Další kroky

Kanál Azure Data Factory v tomto kurzu kopíruje data z tabulky v instanci SQL Edge do umístění v úložišti objektů BLOB v Azure každou hodinu. Další informace o používání Data Factory v jiných scénářích najdete v těchto [kurzech](../data-factory/tutorial-copy-data-portal.md).
