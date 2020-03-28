---
title: Synchronizace dat z Azure SQL Database Edge pomocí Azure Data Factory | Dokumenty společnosti Microsoft
description: Informace o synchronizaci dat mezi Azure SQL Database Edge a úložištěm objektů blob Azure
keywords: SQL Database Edge,synchronizace dat z okraje databáze SQL, sql database edge data factory
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: e6fd9e6431137708ba93328a8ed1359b93b4ee1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74851685"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>Kurz: Synchronizace dat z SQL Database Edge do úložiště objektů blob Azure pomocí Azure Data Factory

V tomto kurzu budete používat Azure Data Factory k postupné synchronizaci dat do úložiště objektů Blob Azure z tabulky v instanci Azure SQL Database Edge.

## <a name="before-you-begin"></a>Než začnete

Pokud jste ještě nevytvořili databázi nebo tabulku v nasazení Azure SQL Database Edge, vytvořte jednu z těchto metod:

* K připojení k SQL Database Edge použijte [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) nebo Azure Data [Studio.](/sql/azure-data-studio/download/) Spusťte skript SQL a vytvořte databázi a tabulku.
* Vytvořte databázi sql a tabulku pomocí [SQLCMD](/sql/tools/sqlcmd-utility/) přímým připojením k modulu SQL Database Edge. Další informace naleznete [v tématu Připojení k databázovému stroji pomocí sqlcmd](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Pomocí nástroje SQLPackage.exe nasazujte soubor balíčku DAC do kontejneru SQL Database Edge. Tento proces můžete automatizovat zadáním identifikátoru URI souboru SqlPackage jako součásti požadované konfigurace vlastností modulu. Můžete také přímo použít sqlpackage.exe klientský nástroj k nasazení balíčku DAC sql database edge.

    Informace o stažení souboru SqlPackage.exe naleznete v [tématu Stažení a instalace balíčku SQLPackage](/sql/tools/sqlpackage-download/). Následují některé ukázkové příkazy pro sqlpackage.exe. Další informace naleznete v dokumentaci sqlpackage.exe.

    **Vytvoření balíčku DAC**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **Použití balíčku DAC**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Vytvoření tabulky a postupu SQL pro uložení a aktualizaci úrovní vodoznaku

Tabulka vodoznaků se používá k uložení posledního časového razítka, do kterého byla data již synchronizována s Azure Storage. Uložená procedura Transact-SQL (T-SQL) se používá k aktualizaci tabulky vodoznaku po každé synchronizaci.

Spusťte tyto příkazy na instanci SQL Database Edge:

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

## <a name="create-a-data-factory-pipeline"></a>Vytvoření kanálu datové továrny

V této části vytvoříte kanál Azure Data Factory pro synchronizaci dat do úložiště objektů Blob Azure z tabulky v Azure SQL Database Edge.

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>Vytvoření datové továrny pomocí ui datové továrny

Vytvořte továrnu dat podle pokynů v [tomto kurzu](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).

### <a name="create-a-data-factory-pipeline"></a>Vytvoření kanálu datové továrny

1. Na stránce **Začínáme** v uzu Data Factory vyberte **Vytvořit kanál**.

    ![Vytvoření kanálu datové továrny](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Na stránce **Obecné** v okně **Vlastnosti** pro kanál zadejte pro název **PeriodicSync.**

3. Přidejte aktivitu vyhledávání, abyste získali starou hodnotu vodoznaku. V podokně **Aktivity** rozbalte **položku Obecné** a přetáhněte aktivitu **vyhledávání** na povrch návrháře kanálu. Změňte název aktivity na **OldWatermark**.

    ![Přidání starého vyhledávání vodoznaku](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Přepněte na kartu **Nastavení** a vyberte **Nový** pro **zdrojovou datovou sadu**. Nyní vytvoříte datovou sadu představující data v tabulce vodoznaku. Tato tabulka obsahuje starou mez, která se použila v předchozí operaci kopírování.

5. V okně **Nová datová sada** vyberte **Azure SQL Server**a pak vyberte **Pokračovat**.  

6. V okně **Nastavit vlastnosti** datové sady zadejte v části **Název** **vodoznakDataset**.

7. V **případě propojené služby**vyberte **nový**a proveďte tyto kroky:

    1. V části **Název**zadejte **SQLDBEdgeLinkedService**.

    2. V části **Název serveru**zadejte podrobnosti o serveru SQL Database Edge.

    3. V seznamu vyberte **název databáze.**

    4. Zadejte **své uživatelské jméno** a **heslo**.

    5. Chcete-li otestovat připojení k instanci SQL Database Edge, vyberte **možnost Testovat připojení**.

    6. Vyberte **Vytvořit**.

    ![Vytvoření propojené služby](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Vyberte **OK**.

8. Na kartě **Nastavení** vyberte **Upravit**.

9. Na kartě **Připojení** vyberte **možnost [dbo].[ vodoznak]** pro **table**. Pokud chcete zobrazit náhled dat v tabulce, vyberte **Náhled dat**.

10. Přepněte do editoru kanálu výběrem karty kanálu v horní části nebo výběrem názvu kanálu ve stromovém zobrazení vlevo. V okně vlastností aktivity vyhledávání zkontrolujte, zda je v seznamu **Zdrojová datová sada** vybraná volba **VodoznakDataset.**

11. V podokně **Aktivity** rozbalte **položku Obecné** a přetáhněte jinou aktivitu **vyhledávání** na povrch návrháře kanálu. Nastavte název na **NewWatermark** na kartě **Obecné** v okně vlastností. Tato vyhledávací aktivita získá novou hodnotu vodoznaku z tabulky, která obsahuje zdrojová data, aby ji bylo možné zkopírovat do cíle.

12. V okně vlastností pro druhou aktivitu vyhledávání přepněte na kartu **Nastavení** a výběrem možnosti **Nový** vytvořte datovou sadu, která bude ukazovat na zdrojovou tabulku, která obsahuje novou hodnotu vodoznaku.

13. V okně **Nová datová sada** vyberte **instanci SQL Database Edge**a pak vyberte **Pokračovat**.

    1. V okně **Nastavit vlastnosti** v části **Název**zadejte **SourceDataset**. V části **Propojená služba**vyberte **SQLDBEdgeLinkedService**.

    2. V části **Tabulka**vyberte tabulku, kterou chcete synchronizovat. Můžete také zadat dotaz pro tuto datovou sadu, jak je popsáno dále v tomto kurzu. Dotaz má přednost před tabulkou zadanou v tomto kroku.

    3. Vyberte **OK**.

14. Přepněte do editoru kanálu výběrem karty kanálu v horní části nebo výběrem názvu kanálu ve stromovém zobrazení vlevo. V okně vlastností aktivity vyhledávání zkontrolujte, zda je v seznamu **Zdrojová datová sada** vybraná možnost **SourceDataset.**

15. V části **Použít dotaz**vyberte **Dotaz** . Aktualizujte název tabulky v následujícím dotazu a zadejte dotaz. Vybíráte pouze maximální hodnotu `timestamp` z tabulky. Nezapomeňte vybrat **pouze první řádek**.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![vybrat dotaz](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. V podokně **Aktivity** rozbalte **možnost Přesunout & transformace** a přetáhněte aktivitu **Kopírovat** z podokna **Aktivity** na povrch návrháře. Nastavte název aktivity na **IncrementalCopy**.

17. Propojte obě aktivity vyhledávání s aktivitou kopírování přetažením zeleného tlačítka připojeného k aktivitám vyhledávání na aktivitu kopírování. Uvolněte tlačítko myši, když se zobrazí barva ohraničení změny aktivity kopírování na modrou.

18. Vyberte aktivitu kopírování a ověřte, že se v okně **Vlastnosti** zobrazí vlastnosti aktivity.

19. Přepněte na kartu **Zdroj** v okně **Vlastnosti** a proveďte tyto kroky:

    1. V poli **Zdrojová datová sada** vyberte **SourceDataset**.

    2. V části **Použít dotaz**vyberte **Dotaz**.

    3. Do pole **Dotaz** zadejte dotaz SQL. Zde je ukázkový dotaz:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Na kartě **Jímka** vyberte **Nový** v části **Dataset jímky**.

21. V tomto kurzu úložiště dat jímky je úložiště dat úložiště objektů blob Azure. Vyberte **azure blob úložiště**a pak vyberte **pokračovat** v okně **Nová datová sada.**

22. V okně **Vybrat formát** vyberte formát dat a pak vyberte **Pokračovat**.

23. V okně **Nastavit vlastnosti** zadejte v části **Název** **sadu SinkDataset**. V části **Propojená služba**vyberte **Nový**. Teď vytvoříte připojení (propojenou službu) k úložišti objektů Blob Azure.

24. V okně **Nová propojená služba (azure blob storage)** proveďte tyto kroky:

    1. Do pole **Název** zadejte **AzureStorageLinkedService**.

    2. V části **Název účtu úložiště**vyberte účet úložiště Azure pro vaše předplatné Azure.

    3. Otestujte připojení a pak vyberte **Dokončit**.

25. V okně **Nastavit vlastnosti** zkontrolujte, že je v části **Propojená služba**vybraná možnost **AzureStorageLinkedService** . Vyberte **Vytvořit** a **OK**.

26. Na kartě **Dřez** vyberte **Upravit**.

27. Přejděte na kartu **Připojení** v sadě SinkDataset a proveďte tyto kroky:

    1. V části **Cesta k souboru**zadejte *asdedatasync/incrementalcopy*, kde *asdedatasync* je název kontejneru objektu blob a *incrementalcopy* je název složky. Vytvořte kontejner, pokud neexistuje, nebo použijte název existujícího kontejneru. Azure Data Factory automaticky vytvoří přírůstkovou *kopii* výstupní složky, pokud neexistuje. Můžete také použít tlačítko **Procházet** u možnosti **Cesta k souboru** a přejít ke složce v kontejneru objektů blob.

    2. Pro **část Soubor** **cesty k souboru**vyberte **Přidat dynamický obsah [Alt+P]** a zadejte ** @CONCAT('Přírůstkové-', pipeline(). RunId, '.txt')** v okně, které se otevře. Vyberte **Finish** (Dokončit). Název souboru je dynamicky generován výrazem. Každé spuštění kanálu má jedinečné ID. Aktivita kopírování používá ID spuštění k vygenerování názvu souboru.

28. Přepněte do editoru kanálu výběrem karty kanálu v horní části nebo výběrem názvu kanálu ve stromovém zobrazení vlevo.

29. V podokně **Aktivity** rozbalte **položku Obecné** a přetáhněte aktivitu **Uložená procedura** z podokna **Aktivity** na povrch návrháře kanálu. Připojte zelený (úspěch) výstup Copy aktivity uložené procedury aktivity.

30. Vyberte **uloženou aktivitu procedury** v návrháři kanálu a změňte jeho název na **SPtoUpdateWatermarkActivity**.

31. Přepněte na kartu **účet SQL** a vyberte ***QLDBEdgeLinkedService** v části **Propojená služba**.

32. Přepněte na kartu **Uložená procedura** a proveďte tyto kroky:

    1. V části **Název uložené procedury**vyberte **možnost [dbo].[ usp_write_watermark]**.

    2. Chcete-li zadat hodnoty pro parametry uložené procedury, vyberte **importovat parametr** a zadejte tyto hodnoty pro parametry:

    |Name (Název)|Typ|Hodnota|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}|
    |TableName|Řetězec|@{activity('OldWaterMark').output.firstRow.TableName}|

33. Chcete-li ověřit nastavení kanálu, vyberte na panelu nástrojů **možnost Ověřit.** Ověřte, že se nezobrazí žádné chyby ověření. Chcete-li zavřít okno Sestava **>>** ověření **kanálu,** vyberte položku .

34. Publikujte entity (propojené služby, datové sady a kanály) do služby Azure Data Factory výběrem tlačítka **Publikovat vše.** Počkejte, až se zobrazí zpráva potvrzující, že operace publikování byla úspěšná.

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>Aktivace kanálu na základě plánu

1. Na panelu nástrojů kanálu vyberte **Přidat aktivační událost**, vyberte **Nový/Upravit**a pak vyberte **Nový**.

2. Pojmenujte aktivační událost **HourlySync**. V části **Typ**vyberte **Možnost Naplánovat**. Nastavte **opakování** na každou 1 hodinu.

3. Vyberte **OK**.

4. Vyberte **Publikovat vše**.

5. Vyberte **možnost Spustit nyní**.

6. Vlevo přepněte na kartu **Monitorování**. Zobrazí se stav ručně aktivovaného spuštění kanálu. Seznam můžete aktualizovat kliknutím na **Aktualizovat**.

## <a name="next-steps"></a>Další kroky

Kanál Azure Data Factory v tomto kurzu zkopíruje data z tabulky na instanci SQL Database Edge do umístění v úložišti objektů blob Azure jednou za hodinu. Informace o používání data factory v jiných scénářích, najdete v těchto [kurzech](../data-factory/tutorial-copy-data-portal.md).
