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
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: jingwang
ms.openlocfilehash: 1be4769a8a07ac5d4a968ed5aa15ed2e0a2b6db2
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43086822"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Kopírování dat z úložiště objektů blob v Azure do databáze SQL pomocí nástroje pro kopírování dat
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [Verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuální verze](tutorial-copy-data-tool.md)

V tomto kurzu pomocí webu Azure Portal vytvoříte datovou továrnu. Pak pomocí nástroje pro kopírování dat vytvoříte kanál, který kopírujte data z úložiště objektů blob v Azure do databáze SQL. 

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

V tomto kurzu budete provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet úložiště Azure:** Jako _zdrojové_ úložiště dat použijte úložiště objektů blob. Pokud účet úložiště Azure nemáte, přečtěte si pokyny v tématu [Vytvoření účtu úložiště](../storage/common/storage-quickstart-create-account.md).
* **Azure SQL Database:** Jako úložiště dat _jímky_ použijte databázi SQL. Pokud databázi SQL nemáte, přečtěte si pokyny v tématu [Vytvoření databáze SQL](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Vytvoření objektu blob a tabulky SQL

Provedením těchto kroků si připravte úložiště objektů blob a databázi SQL pro tento kurz.

#### <a name="create-a-source-blob"></a>Vytvoření zdrojového objektu blob

1. Spusťte **Poznámkový blok**. Zkopírujte následující text a uložte ho na disk do souboru **inputEmp.txt**:

    ```
    John|Doe
    Jane|Doe
    ```

1. Vytvořte kontejner **adfv2tutorial** a nahrajte do něj soubor inputEmp.txt. K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](http://storageexplorer.com/).

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

1. Povolte službám Azure přístup k SQL serveru. Ověřte, že je pro váš server, na kterém běží SQL Server, povolené nastavení **Povolit přístup ke službám Azure**. Toto nastavení umožní službě Data Factory zapisovat data do vaší instance serveru SQL. Toto nastavení můžete zkontrolovat a zapnout podle následujících kroků:

    a. Na levé straně vyberte **Další služby** a pak vyberte **Servery SQL**.

    b. Vyberte svůj server a pak vyberte **NASTAVENÍ** > **Brána firewall**.

    c. Na stránce **Nastavení brány firewall** nastavte možnost **Povolit přístup ke službám Azure** na **ZAPNUTO**.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **Nový** > **Data a analýzy** > **Datová továrna**: 
   
   ![Vytvoření nové datové továrny](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
1. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**. 
      
     ![Nová datová továrna](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Název datové továrny musí být _globálně jedinečný_. Možná se zobrazí následující chybová zpráva:
   
   ![Nová datová továrna – chybová zpráva](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. Použijte například název _**vaše_jméno**_**ADFTutorialDataFactory**. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
1. Vyberte **předplatné** Azure, v rámci kterého se má nová datová továrna vytvořit. 
1. U položky **Skupina prostředků** proveďte jeden z následujících kroků:
     
    a. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

    b. Vyberte **Vytvořit novou** a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).

1. V části **Verze** vyberte **V2**.
1. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například služby Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používané datovou továrnou můžou být v jiných umístěních a oblastech.
1. Zaškrtněte **Připnout na řídicí panel**. 
1. Vyberte **Vytvořit**.
1. Na řídicím panelu se na dlaždici **Nasazování datové továrny** zobrazí stav zpracování.

    ![Dlaždice Nasazování datové továrny](media/tutorial-copy-data-tool/deploying-data-factory.png)
1. Po vytvoření se zobrazí domovská stránka **Datová továrna**.
   
    ![Domovská stránka datové továrny](./media/tutorial-copy-data-tool/data-factory-home-page.png)
1. Pokud chcete na samostatné kartě otevřít uživatelské rozhraní služby Azure Data Factory, vyberte dlaždici **Vytvořit a monitorovat**. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Vytvoření kanálu pomocí nástroje pro kopírování dat

1. Na stránce **Začínáme** vyberte dlaždici **Kopírovat data**. Spustí se nástroj pro kopírování dat. 

   ![Dlaždice nástroje pro kopírování dat](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
1. Na stránce **Vlastnosti** v části **Název úlohy** zadejte **CopyFromBlobToSqlPipeline**. Pak vyberte **Další**. Uživatelské rozhraní služby Data Factory vytvoří kanál se zadaným názvem úlohy. 

    ![Stránka Vlastnosti](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
1. Na stránce **Source data store** (Zdrojové úložiště dat) proveďte následující kroky:

    a. Kliknutím na **+ Create new connection** (+ Vytvořit nové připojení) přidejte připojení.

    ![Nová zdrojová propojená služba](./media/tutorial-copy-data-tool/new-source-linked-service.png)

    b. Z galerie vyberte **Azure Blob Storage** a pak vyberte **Next** (Další).

    ![Výběr zdroje objektu blob](./media/tutorial-copy-data-tool/select-blob-source.png)

    c. Na stránce **New Linked Service** (Nová propojená služba) vyberte ze seznamu **Storage account name** (Název účtu úložiště) svůj účet úložiště a pak vyberte **Finish** (Dokončit).

    ![Konfigurace úložiště Azure](./media/tutorial-copy-data-tool/configure-azure-storage.png)

    d. Vyberte nově vytvořenou propojenou službu jako zdroj a pak klikněte na **Next** (Další).

    ![Výběr zdrojové propojené služby](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Na stránce **Choose the input file or folder** (Zvolit vstupní soubor nebo složku) proveďte následující kroky:
    
    a. Klikněte na **Browse** (Procházet), přejděte do složky **adfv2tutorial/input**, vyberte soubor **inputEmp.txt** a pak klikněte na **Choose** (Zvolit).

    ![Zvolte vstupní soubor nebo složku](./media/tutorial-copy-data-tool/specify-source-path.png)

    b. Kliknutím na **Next** (Další) přejděte k dalšímu kroku.

1. Na stránce **Nastavení formátu souboru** si všimněte, že nástroj automaticky rozpoznává oddělovače sloupců a řádků. Vyberte **Další**. Na této stránce si také můžete prohlédnou data a schéma vstupních dat. 

    ![Nastavení formátu souboru](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. Na stránce **Destination data store** (Cílové úložiště dat) proveďte následující kroky:

    a. Kliknutím na **+ Create new connection** (+ Vytvořit nové připojení) přidejte připojení.

    ![Nová propojená služba jímky](./media/tutorial-copy-data-tool/new-sink-linked-service.png)

    b. Z galerie vyberte **Azure Blob Storage** a pak vyberte **Next** (Další).

    ![Výběr Azure SQL DB](./media/tutorial-copy-data-tool/select-azure-sql-db.png)

    c. Na stránce **New Linked Service** (Nová propojená služba) vyberte z rozevíracího seznamu název serveru a název databáze, zadejte uživatelské jméno a heslo a vyberte **Finish** (Dokončit).    

    ![Konfigurace Azure SQL DB](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Vyberte nově vytvořenou propojenou službu jako jímku a klikněte na **Next** (Další).

    ![Výběr propojené služby jímky](./media/tutorial-copy-data-tool/select-sink-linked-service.png)

1. Na stránce **Mapování tabulek** vyberte tabulku **[dbo].[emp]** a pak vyberte **Další**. 

    ![Mapování tabulek](./media/tutorial-copy-data-tool/table-mapping.png)
1. Na stránce **Mapování schématu** si všimněte mapování prvního a druhého sloupce ve vstupním souboru na sloupce **FirstName** (Jméno) a **LastName** (Příjmení) tabulky **emp**. Vyberte **Další**.

    ![Stránka Mapování schématu](./media/tutorial-copy-data-tool/schema-mapping.png)
1. Na stránce **Settings** (Nastavení) vyberte **Next** (Další). 
1. Na stránce **Souhrn** zkontrolujte nastavení a pak vyberte **Další**.

    ![Stránka souhrnu](./media/tutorial-copy-data-tool/summary-page.png)
1. Na stránce **Nasazení** vyberte **Monitorovat** a začněte monitorovat kanál (úlohu).

    ![Stránka Nasazení](./media/tutorial-copy-data-tool/deployment-page.png)
1. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. Sloupec **Akce** obsahuje odkazy na zobrazení podrobností o spuštění aktivit a na opětovné spuštění kanálu. Seznam můžete aktualizovat výběrem možnosti **Aktualizovat**. 

    ![Monitorování spuštění kanálu](./media/tutorial-copy-data-tool/pipeline-monitoring.png)
1. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Podrobnosti o operaci kopírování zobrazíte výběrem odkazu **Podrobnosti** (ikona brýlí) ve sloupci **Akce**. Pokud chcete přejít zpátky k zobrazení **Spuštění kanálu**, vyberte odkaz **Kanály** v horní části. Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**. 

    ![Monitorování spuštění aktivit](./media/tutorial-copy-data-tool/activity-monitoring.png)

    ![Podrobnosti o aktivitě kopírování](./media/tutorial-copy-data-tool/copy-execution-details.png)

1. Ověřte vložení dat do tabulky **emp** ve vaší databázi SQL.

    ![Ověření výstupu SQL](./media/tutorial-copy-data-tool/verify-sql-output.png)

1. Výběrem karty **Autor** na levé straně přepněte do režimu úprav. Pomocí editoru můžete aktualizovat propojené služby, datové sady a kanály vytvořené nástrojem. Podrobnosti o úpravách těchto entit v uživatelském rozhraní služby Data Factory najdete ve [verzi tohoto kurzu pro Azure Portal](tutorial-copy-data-portal.md).

## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z úložiště objektů blob do databáze SQL. Naučili jste se tyto postupy: 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

Pokud se chcete dozvědět, jak kopírovat data z místního prostředí do cloudu, přejděte k následujícímu kurzu: 

> [!div class="nextstepaction"]
>[Kopírování dat z místního prostředí do cloudu](tutorial-hybrid-copy-data-tool.md)
