---
title: Kopírování dat pomocí nástroje pro kopírování dat Azure | Dokumentace Microsoftu
description: Vytvořte datovou továrnu Azure a pak pomocí nástroje pro kopírování dat zkopírujte data z úložiště objektů blob v Azure do databáze SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: d2f1d089c6a08a1dc90f82fd9d1c3cb2b6f6dc0a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30171793"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Kopírování dat z úložiště objektů blob v Azure do databáze SQL pomocí nástroje pro kopírování dat
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [Verze 1 – Obecně dostupná](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Verze 2 – Preview](tutorial-copy-data-tool.md)

V tomto kurzu pomocí webu Azure Portal vytvoříte datovou továrnu. Pak pomocí nástroje pro kopírování dat vytvoříte kanál, který kopírujte data z úložiště objektů blob v Azure do databáze SQL. 

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).
>
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, která je obecně dostupná, přečtěte si téma [Začínáme se službou Data Factory verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


V tomto kurzu budete provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet úložiště Azure:** Jako _zdrojové_ úložiště dat použijte úložiště objektů blob. Pokud účet úložiště Azure nemáte, přečtěte si pokyny v tématu [Vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account).
* **Azure SQL Database:** Jako úložiště dat _jímky_ použijte databázi SQL. Pokud databázi SQL nemáte, přečtěte si pokyny v tématu [Vytvoření databáze SQL](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Vytvoření objektu blob a tabulky SQL

Provedením těchto kroků si připravte úložiště objektů blob a databázi SQL pro tento kurz.

#### <a name="create-a-source-blob"></a>Vytvoření zdrojového objektu blob

1. Spusťte **Poznámkový blok**. Zkopírujte následující text a uložte ho na disk do souboru **inputEmp.txt**:

    ```
    John|Doe
    Jane|Doe
    ```

2. Vytvořte kontejner **adfv2tutorial** a nahrajte do něj soubor inputEmp.txt. K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](http://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Vytvoření tabulky SQL jímky

1. Pomocí následujícího skriptu SQL si v databázi SQL vytvořte tabulku **dbo.emp**:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Povolte službám Azure přístup k SQL serveru. Ověřte, že je pro váš server, na kterém běží SQL Server, povolené nastavení **Povolit přístup ke službám Azure**. Toto nastavení umožní službě Data Factory zapisovat data do vaší instance serveru SQL. Toto nastavení můžete zkontrolovat a zapnout podle následujících kroků:

    a. Na levé straně vyberte **Další služby** a pak vyberte **Servery SQL**.

    b. Vyberte svůj server a pak vyberte **NASTAVENÍ** > **Brána firewall**.

    c. Na stránce **Nastavení brány firewall** nastavte možnost **Povolit přístup ke službám Azure** na **ZAPNUTO**.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **Nový** > **Data a analýzy** > **Datová továrna**: 
   
   ![Vytvoření nové datové továrny](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**. 
      
     ![Nová datová továrna](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Název datové továrny musí být _globálně jedinečný_. Možná se zobrazí následující chybová zpráva:
   
   ![Nová datová továrna – chybová zpráva](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. Použijte například název _**vaše_jméno**_**ADFTutorialDataFactory**. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
3. Vyberte **předplatné** Azure, v rámci kterého se má nová datová továrna vytvořit. 
4. U položky **Skupina prostředků** proveďte jeden z následujících kroků:
     
    a. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

    b. Vyberte **Vytvořit novou** a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).

5. V části **Verze** vyberte **V2 (Preview)**.
6. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například služby Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používané datovou továrnou můžou být v jiných umístěních a oblastech.
7. Zaškrtněte **Připnout na řídicí panel**. 
8. Vyberte **Vytvořit**.
9. Na řídicím panelu se na dlaždici **Nasazování datové továrny** zobrazí stav zpracování.

    ![Dlaždice Nasazování datové továrny](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Po vytvoření se zobrazí domovská stránka **Datová továrna**.
   
    ![Domovská stránka objektu pro vytváření dat](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Pokud chcete na samostatné kartě otevřít uživatelské rozhraní služby Azure Data Factory, vyberte dlaždici **Vytvořit a monitorovat**. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Vytvoření kanálu pomocí nástroje pro kopírování dat

1. Na stránce **Začínáme** vyberte dlaždici **Kopírovat data**. Spustí se nástroj pro kopírování dat. 

   ![Dlaždice nástroje pro kopírování dat](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. Na stránce **Vlastnosti** v části **Název úlohy** zadejte **CopyFromBlobToSqlPipeline**. Pak vyberte **Další**. Uživatelské rozhraní služby Data Factory vytvoří kanál se zadaným názvem úlohy. 

    ![Stránka Vlastnosti](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. Na stránce **Source data store** (Zdrojové úložiště dat) vyberte **Azure Blob Storage** a pak vyberte **Next** (Další). Zdrojová data se nacházejí v úložišti objektů blob. 

    ![Stránka Zdrojové úložiště dat](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. Na stránce **Zadejte účet služby Azure Blob Storage** proveďte následující kroky:

    a. V části **Název připojení** zadejte **AzureStorageLinkedService**.

    b. Z rozevíracího seznamu **Název účtu úložiště** vyberte název svého účtu úložiště.

    c. Vyberte **Next** (Další). 

    ![Zadání účtu úložiště](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

    Propojená služba propojuje úložiště dat nebo výpočetní prostředí s datovou továrnou. V tomto případě vytvoříte propojenou službu Storage, která propojí váš účet úložiště s úložištěm dat. Propojená služba obsahuje informace o připojení, které služba Data Factory používá pro připojení k úložišti Blob Storage za běhu. Datová sada určuje kontejner, složku a soubor (volitelné) obsahující zdrojová data. 

5. Na stránce **Zvolte vstupní soubor nebo složku** proveďte následující kroky:
    
    a. Přejděte do složky **adfv2tutorial/input**.

    b. Vyberte soubor **inputEmp.txt**.

    c. Vyberte **Choose** (Zvolit). Případně můžete na soubor **inputEmp.txt** dvakrát kliknout.

    d. Vyberte **Next** (Další). 

    ![Zvolte vstupní soubor nebo složku](./media/tutorial-copy-data-tool/choose-input-file-folder.png)

6. Na stránce **Nastavení formátu souboru** si všimněte, že nástroj automaticky rozpoznává oddělovače sloupců a řádků. Vyberte **Next** (Další). Na této stránce si také můžete prohlédnou data a schéma vstupních dat. 

    ![Nastavení formátu souboru](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. Na stránce **Cílové úložiště dat** vyberte **Azure SQL Database** a pak vyberte **Další**.

    ![Cílové úložiště dat](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. Na stránce **Zadejte databázi SQL Azure** proveďte následující kroky: 

    a. V části **Název připojení** zadejte **AzureSqlDatabaseLinkedService**.

    b. V rozevíracím seznamu **Název serveru** vyberte příslušný název instance SQL Serveru.

    c. V rozevíracím seznamu **Název databáze** vyberte svoji databázi SQL Database.

    d. Do pole **Uživatelské jméno** zadejte jméno uživatele.

    e. Do pole **Heslo** zadejte heslo pro tohoto uživatele.

    f. Vyberte **Next** (Další). 

    ![Zadání databáze SQL](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

    Datová sada musí být přidružená k propojené službě. Propojená služba obsahuje připojovací řetězec, který služba Data Factory používá pro připojení k databázi SQL Database za běhu. Datová sada určuje kontejner, složku a soubor (volitelné), do kterého se data kopírují.

9. Na stránce **Mapování tabulek** vyberte tabulku **[dbo].[emp]** a pak vyberte **Další**. 

    ![Mapování tabulek](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. Na stránce **Mapování schématu** si všimněte mapování prvního a druhého sloupce ve vstupním souboru na sloupce **FirstName** (Jméno) a **LastName** (Příjmení) tabulky **emp**.

    ![Stránka Mapování schématu](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. Na stránce **Settings** (Nastavení) vyberte **Next** (Další). 

    ![Stránka Nastavení](./media/tutorial-copy-data-tool/settings-page.png)
12. Na stránce **Souhrn** zkontrolujte nastavení a pak vyberte **Další**.

    ![Stránka souhrnu](./media/tutorial-copy-data-tool/summary-page.png)
13. Na stránce **Nasazení** vyberte **Monitorovat** a začněte monitorovat kanál (úlohu).

    ![Stránka Nasazení](./media/tutorial-copy-data-tool/deployment-page.png)
14. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. Sloupec **Akce** obsahuje odkazy na zobrazení podrobností o spuštění aktivit a na opětovné spuštění kanálu. Seznam můžete aktualizovat výběrem možnosti **Aktualizovat**. 

    ![Monitorování spuštění kanálu](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Podrobnosti o operaci kopírování zobrazíte výběrem odkazu **Podrobnosti** (ikona brýlí) ve sloupci **Akce**. Pokud chcete přejít zpátky k zobrazení **Spuštění kanálu**, vyberte odkaz **Kanály** v horní části. Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**. 

    ![Monitorování spuštění aktivit](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. Výběrem karty **Upravit** na levé straně přepněte do režimu úprav. Pomocí editoru můžete aktualizovat propojené služby, datové sady a kanály vytvořené nástrojem. Vyberte **Kód** a zobrazte kód JSON pro entitu aktuálně otevřenou v editoru. Podrobnosti o úpravách těchto entit v uživatelském rozhraní služby Data Factory najdete ve [verzi tohoto kurzu pro Azure Portal](tutorial-copy-data-portal.md).

    ![Karta Editor](./media/tutorial-copy-data-tool/edit-tab.png)
17. Ověřte vložení dat do tabulky **emp** ve vaší databázi SQL.

    ![Ověření výstupu SQL](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z úložiště objektů blob do databáze SQL. Naučili jste se tyto postupy: 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

Pokud se chcete dozvědět, jak kopírovat data z místního prostředí do cloudu, přejděte k následujícímu kurzu: 

> [!div class="nextstepaction"]
>[Kopírování dat z místního prostředí do cloudu](tutorial-hybrid-copy-data-tool.md)
