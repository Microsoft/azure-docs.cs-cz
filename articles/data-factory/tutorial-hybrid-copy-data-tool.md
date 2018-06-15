---
title: Kopírování místních dat pomocí nástroje pro kopírování dat Azure | Microsoft Docs
description: Vytvořte datovou továrnu Azure a pak pomocí nástroje pro kopírování dat zkopírujte data z místní databáze SQL Serveru do úložiště objektů blob v Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/04/2018
ms.author: jingwang
ms.openlocfilehash: 85b721df1e666903c4966ca240c433ded01c06b7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30171899"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Kopírování dat z místní databáze SQL Serveru do úložiště objektů blob v Azure pomocí nástroje pro kopírování dat
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – Obecně dostupná](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Verze 2 – Preview](tutorial-hybrid-copy-data-tool.md)

V tomto kurzu pomocí webu Azure Portal vytvoříte datovou továrnu. Pak pomocí nástroje pro kopírování dat vytvoříte kanál, který kopírujte data z místní databáze SQL Serveru do úložiště objektů blob v Azure.

> [!NOTE]
> - Pokud se službou Azure Data Factory teprve začínáte, přečtěte si téma [Seznámení se službou Data Factory](introduction.md).
>
> - Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Jestliže používáte verzi 1 služby Data Factory, která je obecně dostupná, podívejte se na téma [Úvod do Data Factory (verze 1)](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

V tomto kurzu budete provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Požadavky
### <a name="azure-subscription"></a>Předplatné Azure
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

### <a name="azure-roles"></a>Role Azure
Pro vytvoření instancí datové továrny musí mít uživatelský účet, který použijete pro přihlášení k Azure, přiřazenou roli *přispěvatel* nebo *vlastník* předplatného Azure nebo musí být jeho *správcem*. 

Pokud chcete zobrazit oprávnění, která v předplatném máte, přejděte na web Azure Portal. V pravém horním rohu vyberte své uživatelské jméno a pak vyberte **Oprávnění**. Pokud máte přístup k několika předplatným, vyberte odpovídající předplatné. Ukázku pokynů pro přidání uživatele k roli najdete v tématu věnovaném [přidávání rolí](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 a 2017
V tomto kurzu použijete místní databázi SQL Serveru jako *zdrojové* úložiště dat. Kanál v datové továrně, který vytvoříte v tomto kurzu, kopíruje data z této místní databáze SQL Serveru (zdroj) do úložiště objektů blob (jímka). Ve své databázi SQL Serveru pak vytvoříte tabulku **emp** a vložíte do ní několik ukázkových záznamů. 

1. Spusťte aplikaci SQL Server Management Studio. Pokud na vašem počítači ještě není nainstalovaná, přejděte na stránku pro [stažení aplikace SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

2. Pomocí svých přihlašovacích údajů se přihlaste ke své instanci SQL Serveru. 

3. Vytvořte ukázkovou databázi. Ve stromovém zobrazení klikněte pravým tlačítkem na **Databáze** a pak vyberte **Nová databáze**. 
 
4. V okně **Nová databáze** zadejte název databáze a pak vyberte **OK**. 

5. Vytvořte tabulku **emp** a vložte do ní nějaká ukázková data spuštěním následujícího skriptu dotazu proti databázi. Ve stromovém zobrazení klikněte pravým tlačítkem na databázi, kterou jste vytvořili, a pak vyberte **Nový dotaz**.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Účet služby Azure Storage
V tomto kurzu použijete účet úložiště Azure (konkrétně úložiště objektů blob) pro obecné účely jako cílové úložiště dat nebo úložiště dat jímky. Pokud účet úložiště pro obecné účely nemáte, přečtěte si téma [Vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account), kde najdete pokyny k jeho vytvoření. Kanál v datové továrně, který vytvoříte v tomto kurzu, kopíruje data z místní databáze SQL Serveru (zdroj) do tohoto úložiště objektů blob (jímka). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Získání názvu a klíče účtu úložiště
V tomto kurzu použijete název a klíč svého účtu úložiště. Název a klíč svého účtu úložiště získáte pomocí následujícího postupu: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého uživatelského jména a hesla Azure. 

2. V levém podokně vyberte **Další služby**. Proveďte filtrování pomocí klíčového slova **úložiště** a pak vyberte **Účty úložiště**.

    ![Vyhledávání účtu úložiště](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. V seznamu účtů úložiště v případě potřeby vyfiltrujte váš účet úložiště. Pak vyberte svůj účet úložiště. 

4. V okně **Účet úložiště** vyberte **Přístupové klíče**.

    ![Přístupové klíče](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. Zkopírujte hodnoty polí **Název účtu úložiště** a **klíč1** a vložte je do Poznámkového bloku nebo jiného editoru pro pozdější použití v rámci kurzu. 

#### <a name="create-the-adftutorial-container"></a>Vytvoření kontejneru adftutorial 
V této části vytvoříte ve svém úložišti objektů blob kontejner objektů blob **adftutorial**. 

1. V okně **Účet úložiště** přepněte na **Přehled** a pak vyberte **Objekty blob**. 

    ![Výběr možnosti Objekty blob](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. V okně **Služba Blob** vyberte **Kontejner**. 

    ![Tlačítko Kontejner](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. V okně **Nový kontejner** do pole **Název** zadejte **adftutorial** a pak vyberte **OK**. 

    ![Nový kontejner](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. V seznamu kontejnerů vyberte **adftutorial**.

    ![Výběr kontejneru](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Okno **Kontejner** pro **adftutorial** nechte otevřené. Použijete ji k ověření výstupu na konci tohoto kurzu. Data Factory v tomto kontejneru vytvoří výstupní složku automaticky, takže ji nemusíte vytvářet.

    ![Okno Kontejner](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **Nový** > **Data a analýzy** > **Datová továrna**. 
   
   ![Vytvoření nové datové továrny](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory-menu.png)
2. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**. 
      
     ![Nová datová továrna](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory.png)
 
   Název datové továrny musí být *globálně jedinečný*. Pokud se u pole s názvem zobrazí následující chybová zpráva, změňte název datové továrny (třeba na váš_název_ADFTutorialDataFactory). Pravidla pro přiřazování názvů artefaktům služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
  
   ![Název nové datové továrny](./media/tutorial-hybrid-copy-data-tool/name-not-available-error.png)
3. Vyberte **předplatné** Azure, v rámci kterého chcete datovou továrnu vytvořit. 
4. U položky **Skupina prostředků** proveďte jeden z následujících kroků:
     
      - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

      - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků. 
         
      Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).
5. V části **Verze** vyberte **V2 (Preview)**.
6. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například služby Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používané datovou továrnou můžou být v jiných umístěních nebo oblastech.
7. Zaškrtněte **Připnout na řídicí panel**. 
8. Vyberte **Vytvořit**.
9. Na řídicím panelu se zobrazí následující dlaždice se stavem **Nasazování datové továrny**:

    ![Dlaždice Nasazování datové továrny](media/tutorial-hybrid-copy-data-tool/deploying-data-factory.png)
10. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.
   
    ![Domovská stránka objektu pro vytváření dat](./media/tutorial-hybrid-copy-data-tool/data-factory-home-page.png)
11. Výběrem dlaždice **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Data Factory. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Vytvoření kanálu pomocí nástroje pro kopírování dat

1. Na stránce **Začínáme** vyberte dlaždici **Kopírovat data**. Spustí se nástroj pro kopírování dat. 

   ![Dlaždice nástroje pro kopírování dat](./media/tutorial-hybrid-copy-data-tool/copy-data-tool-tile.png)
2. Na stránce **Vlastnosti** nástroje pro kopírování dat v části **Název úlohy** zadejte **CopyFromOnPremSqlToAzureBlobPipeline**. Pak vyberte **Další**. Nástroj pro kopírování dat vytvoří kanál s názvem, který zadáte do tohoto pole. 
    
   ![Název úlohy](./media/tutorial-hybrid-copy-data-tool/properties-page.png)
3. Na stránce **Zdrojové úložiště dat** vyberte **SQL Server** a pak vyberte **Další**. Možná se budete muset posunout dolů, aby se v seznamu zobrazil **SQL Server**. 

   ![Výběr SQL Serveru](./media/tutorial-hybrid-copy-data-tool/select-source-data-store.png)
4. V části **Název připojení** zadejte **SqlServerLinkedService**. Vyberte odkaz **Vytvořit prostředí Integration Runtime**. Musíte vytvořit místní prostředí Integration Runtime, stáhnout ho na svůj počítač a zaregistrovat ho ve službě Data Factory. Místní prostředí Integration Runtime kopíruje data mezi vaším místním prostředím a cloudem.

   ![Vytvoření místního prostředí Integration Runtime](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-link.png)
5. V dialogovém okně **Vytvořit prostředí Integration Runtime** v části **Název** zadejte **TutorialIntegrationRuntime**. Potom vyberte **Vytvořit**. 

   ![Název prostředí Integration Runtime](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog.png)
6. Vyberte **Spustit expresní instalaci na tomto počítači**. Tato akce nainstaluje prostředí Integration Runtime na vašem počítači a zaregistruje ho ve službě Data Factory. Případně můžete využít možnost ruční instalace a stáhnout instalační soubor, spustit ho a použít klíč k registraci prostředí Integration Runtime. 

    ![Odkaz Spustit expresní instalaci na tomto počítači](./media/tutorial-hybrid-copy-data-tool/launch-express-setup-link.png)
7. Spusťte staženou aplikaci. V okně se zobrazí stav expresní instalace. 

    ![Stav expresní instalace](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)
8. Ověřte, že je v poli **Prostředí Integration Runtime** vybrané prostředí **TutorialIntegrationRuntime**.

    ![Vybrané prostředí Integration Runtime](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)
9. V části **Zadejte místní databázi SQL Serveru** proveďte následující kroky: 

    a. V části **Název připojení** zadejte **OnPremSqlLinkedService**.

    b. V části **Název serveru** zadejte název vaší instance místního SQL Serveru.

    c. V části **Název databáze** zadejte název vaší místní databáze.

    d. V části **Typ ověřování** vyberte odpovídající typ ověřování.

    e. V části **Uživatelské jméno** zadejte jméno uživatele s přístupem k místnímu SQL Serveru.

    f. Zadejte **heslo** pro tohoto uživatele. 
10. Na stránce **Vyberte tabulky, ze kterých se mají kopírovat data, nebo použijte vlastní dotaz** vyberte v seznamu tabulku **[dbo].[emp]** a vyberte **Další**. 

    ![Výběr tabulky emp](./media/tutorial-hybrid-copy-data-tool/select-emp-table.png)
11. Na stránce **Destination data store** (Cílové úložiště dat) vyberte **Azure Blob Storage** a pak vyberte **Next** (Další).

    ![Výběr služby Blob Storage](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)
12. Na stránce **Zadejte účet služby Azure Blob Storage** proveďte následující kroky: 

    a. V části **Název připojení** zadejte **AzureStorageLinkedService**.

    b. V části **Název účtu úložiště** vyberte z rozevíracího seznamu svůj účet úložiště. 

    c. Vyberte **Další**.

    ![Zadání účtu úložiště](./media/tutorial-hybrid-copy-data-tool/specify-azure-blob-storage-account.png)
13. Na stránce **Zvolte výstupní soubor nebo složku** v části **Cesta ke složce** zadejte **adftutorial/fromonprem**. Kontejner **adftutorial** jste vytvořili jako součást požadavků. Pokud výstupní složka neexistuje, služba Data Factory ji automaticky vytvoří. Můžete také použít tlačítko **Procházet** a přejít k úložišti objektů blob a jeho kontejnerům a složkám. Všimněte si, že ve výchozím nastavení je název výstupního souboru nastavený na **dbo.emp**.
        
    ![Zvolte výstupní soubor nebo složku](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)
14. Na stránce **File format settings** (Nastavení formátu souboru) vyberte **Next** (Další). 

    ![Stránka Nastavení formátu souboru](./media/tutorial-hybrid-copy-data-tool/file-format-settings-page.png)
15. Na stránce **Settings** (Nastavení) vyberte **Next** (Další). 

    ![Stránka Nastavení](./media/tutorial-hybrid-copy-data-tool/settings-page.png)
16. Na stránce **Souhrn** zkontrolujte hodnoty všech nastavení a vyberte **Další**. 

    ![Stránka souhrnu](./media/tutorial-hybrid-copy-data-tool/summary-page.png)
17. Na stránce **Nasazení** vyberte **Monitorovat** a začněte monitorovat vytvořený kanál nebo úlohu.

    ![Stránka Nasazení](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)
18. Na kartě **Monitorování** můžete zobrazit stav kanálu, který jste vytvořili. Pomocí odkazů ve sloupci **Akce** můžete zobrazit spuštění aktivit související se spuštěním kanálu nebo spustit kanál znovu. 

    ![Monitorování spuštění kanálu](./media/tutorial-hybrid-copy-data-tool/monitor-pipeline-runs.png)
19. Vyberte odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce** a zobrazte spuštění aktivit související se spuštěním kanálu. Pokud chcete zobrazit podrobnosti o operaci kopírování, vyberte odkaz **Podrobnosti** (ikona brýlí) ve sloupci **Akce**. Pokud chcete přejít zpátky k zobrazení **Spuštění kanálu**, vyberte **Kanály** v horní části.

    ![Monitorování spuštění aktivit](./media/tutorial-hybrid-copy-data-tool/monitor-activity-runs.png)
20. Ověřte, že složka **fromonprem** kontejneru **adftutorial** obsahuje výstupní soubor. 
 
    ![Výstupní objekt blob](./media/tutorial-hybrid-copy-data-tool/output-blob.png)
21. Výběrem karty **Upravit** na levé straně přepněte do režimu úprav. Pomocí editoru můžete aktualizovat propojené služby, datové sady a kanály vytvořené nástrojem. Vyberte **Kód** a zobrazte kód JSON přidružený k entitě otevřené v editoru. Podrobnosti o úpravách těchto entit v uživatelském rozhraní služby Data Factory najdete ve [verzi tohoto kurzu pro Azure Portal](tutorial-copy-data-portal.md).

    ![Karta Upravit](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z místní databáze SQL Serveru do úložiště objektů blob. Naučili jste se tyto postupy: 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

Seznam úložišť dat podporovaných službou Data Factory najdete v tématu popisujícím [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).

Pokud se chcete dozvědět, jak hromadně kopírovat data ze zdroje do cíle, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Hromadné kopírování dat](tutorial-bulk-copy-portal.md)
