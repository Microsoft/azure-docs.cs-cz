---
title: Kopírování dat z SQL Serveru do úložiště objektů blob pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z místního úložiště dat do cloudu s využitím místního prostředí Integration Runtime ve službě Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/11/2018
ms.author: jingwang
ms.openlocfilehash: f408d24a5957061bf03d340a555b87bdc6b2aacc
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44304131"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Kopírování dat z místní databáze SQL Serveru do úložiště objektů blob v Azure
V tomto kurzu pomocí uživatelského rozhraní služby Azure Data Factory vytvoříte kanál datové továrny, který kopíruje data z místní databáze SQL Serveru do úložiště objektů blob v Azure. Vytvoříte a použijete místní prostředí Integration Runtime, které přesouvá data mezi místním a cloudovým úložištěm dat.

> [!NOTE]
> Tento článek neposkytuje podrobný úvod do služby Data Factory. Další informace najdete v tématu [Seznámení se službou Data Factory](introduction.md). 

V tomto kurzu budete provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvořte místní prostředí Integration Runtime.
> * Vytvoření propojených služeb SQL Server a Azure Storage 
> * Vytvoření datových sad SQL Serveru a Azure Blob
> * Vytvoření kanálu s aktivitou kopírování pro přesun dat
> * Zahájení spuštění kanálu
> * Monitorování spuštění kanálu

## <a name="prerequisites"></a>Požadavky
### <a name="azure-subscription"></a>Předplatné Azure
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

### <a name="azure-roles"></a>Role Azure
Abyste mohli vytvořit instance datové továrny, musí mít uživatelský účet, který použijete pro přihlášení k Azure, přiřazenou roli *Přispěvatel* nebo *Vlastník* předplatného Azure nebo musí být jeho *správcem*. 

Pokud chcete zobrazit oprávnění, která v předplatném máte, přejděte na web Azure Portal. V pravém horním rohu vyberte své uživatelské jméno a pak vyberte **Oprávnění**. Pokud máte přístup k několika předplatným, vyberte odpovídající předplatné. Ukázkové pokyny pro přidání uživatele k roli najdete v článku o [správě přístupu pomocí RBAC a webu Azure Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 a 2017
V tomto kurzu použijete místní databázi SQL Serveru jako *zdrojové* úložiště dat. Kanál v datové továrně, který vytvoříte v tomto kurzu, kopíruje data z této místní databáze SQL Serveru (zdroj) do úložiště objektů blob (jímka). Ve své databázi SQL Serveru pak vytvoříte tabulku **emp** a vložíte do ní několik ukázkových záznamů. 

1. Spusťte aplikaci SQL Server Management Studio. Pokud na vašem počítači ještě není nainstalovaná, přejděte na stránku pro [stažení aplikace SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

1. Pomocí svých přihlašovacích údajů se přihlaste ke své instanci SQL Serveru. 

1. Vytvořte ukázkovou databázi. Ve stromovém zobrazení klikněte pravým tlačítkem na **Databáze** a pak vyberte **Nová databáze**. 
1. V okně **Nová databáze** zadejte název databáze a pak vyberte **OK**. 

1. Vytvořte tabulku **emp** a vložte do ní nějaká ukázková data spuštěním následujícího skriptu dotazu proti databázi:

   ```
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

1. Ve stromovém zobrazení klikněte pravým tlačítkem na databázi, kterou jste vytvořili, a pak vyberte **Nový dotaz**.

### <a name="azure-storage-account"></a>Účet služby Azure Storage
V tomto kurzu použijete účet úložiště Azure (konkrétně úložiště objektů blob) pro obecné účely jako cílové úložiště dat nebo úložiště dat jímky. Pokud nemáte účet úložiště Azure pro obecné účely, přečtěte si téma [Vytvoření účtu úložiště](../storage/common/storage-quickstart-create-account.md). Kanál v datové továrně, který vytvoříte v tomto kurzu, kopíruje data z místní databáze SQL Serveru (zdroj) do úložiště objektů blob (jímka). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Získání názvu a klíče účtu úložiště
V tomto kurzu použijete název a klíč svého účtu úložiště. Název a klíč svého účtu úložiště získáte pomocí následujícího postupu: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého uživatelského jména a hesla Azure. 

1. V levém podokně vyberte **Další služby**. Proveďte filtrování pomocí klíčového slova **úložiště** a pak vyberte **Účty úložiště**.

    ![Vyhledávání účtu úložiště](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

1. V seznamu účtů úložiště v případě potřeby vyfiltrujte váš účet úložiště. Pak vyberte svůj účet úložiště. 

1. V okně **Účet úložiště** vyberte **Přístupové klíče**.

    ![Přístupové klíče](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

1. Zkopírujte hodnoty polí **Název účtu úložiště** a **klíč1** a vložte je do Poznámkového bloku nebo jiného editoru pro pozdější použití v rámci kurzu. 

#### <a name="create-the-adftutorial-container"></a>Vytvoření kontejneru adftutorial 
V této části vytvoříte ve svém úložišti objektů blob kontejner objektů blob **adftutorial**. 

1. V okně **Účet úložiště** přejděte do části **Přehled** a pak vyberte **Objekty blob**. 

    ![Výběr možnosti Objekty blob](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. V okně **Blob service** vyberte **Kontejner**. 

    ![Tlačítko Kontejner](media/tutorial-hybrid-copy-powershell/add-container-button.png)

1. V okně **Nový kontejner** v části **Název** zadejte **adftutorial**. Pak vyberte **OK**. 

    ![Okno Nový kontejner](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. V seznamu kontejnerů vyberte **adftutorial**.

    ![Výběr kontejneru](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

1. Okno **Kontejner** pro **adftutorial** nechte otevřené. Použijete ji k ověření výstupu na konci tohoto kurzu. Data Factory v tomto kontejneru vytvoří výstupní složku automaticky, takže ji nemusíte vytvářet.

    ![Okno Kontejner](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Vytvoření datové továrny
V tomto kroku vytvoříte datovou továrnu a spustíte uživatelské rozhraní služby Data Factory, ve kterém v této datové továrně vytvoříte kanál. 

1. Otevřete webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
1. V nabídce vlevo vyberte **Nový** > **Data + analýzy** > **Datová továrna**.
   
   ![Vytvoření nové datové továrny](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
1. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**. 
   
     ![Stránka Nová datová továrna](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)

Název datové továrny musí být *globálně jedinečný*. Pokud se u pole s názvem zobrazí následující chybová zpráva, změňte název datové továrny (třeba na váš_název_ADFTutorialDataFactory). Pravidla pro přiřazování názvů artefaktům služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).

   ![Název nové datové továrny](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
1. Vyberte **předplatné** Azure, v rámci kterého chcete datovou továrnu vytvořit.
1. U položky **Skupina prostředků** proveďte jeden z následujících kroků:
   
      - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

      - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.
        
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).
1. Jako **Verzi** vyberte **V2**.
1. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například služby Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používané datovou továrnou můžou být v jiných oblastech.
1. Zaškrtněte **Připnout na řídicí panel**. 
1. Vyberte **Vytvořit**.
1. Na řídicím panelu se zobrazí následující dlaždice se stavem **Nasazování datové továrny**:

    ![Dlaždice Nasazování datové továrny](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
1. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku:
   
    ![Domovská stránka datové továrny](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
1. Výběrem dlaždice **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Data Factory. 


## <a name="create-a-pipeline"></a>Vytvoření kanálu

1. Na stránce **Pusťme se do toho** vyberte **Vytvořit kanál**. Automaticky se pro vás vytvoří kanál. Kanál se zobrazí ve stromovém zobrazení a otevře se jeho editor. 

   ![Stránka Začínáme](./media/tutorial-hybrid-copy-portal/get-started-page.png)

1. Do pole **Název** na kartě **Obecné** v dolní části okna **Vlastnosti** zadejte **SQLServerToBlobPipeline**.

   ![Název kanálu](./media/tutorial-hybrid-copy-portal/pipeline-name.png)

1. Na panelu nástrojů **Aktivity** rozbalte **Tok dat**. Přetáhněte aktivitu **Kopírování** na plochu návrháře kanálu. Nastavte název aktivity na **CopySqlServerToAzureBlobActivity**.

   ![Název aktivity](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)

1. V okně **Vlastnosti** přejděte na kartu **Zdroj** a vyberte **+ Nový**.

   ![Karta Zdroj](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)

1. V okně **Nová datová sada** vyhledejte **SQL Server**. Vyberte **SQL Server** a pak **Dokončit**. Zobrazí se nová karta s názvem **SqlServerTable1**. Datová sada **SqlServerTable1** se také zobrazí ve stromovém zobrazení na levé straně. 

   ![Výběr SQL Serveru](./media/tutorial-hybrid-copy-portal/select-sql-server.png)

1. Do pole **Název** na kartě **Obecné** v dolní části okna **Vlastnosti** zadejte **SqlServerDataset**.

   ![Název zdrojové datové sady](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)

1. Přejděte na kartu **Připojení** a vyberte **+ Nové**. V tomto kroku vytvoříte připojení ke zdrojovému úložišti dat (databáze SQL Serveru). 

   ![Připojení ke zdrojové datové sadě](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)

1. V okně **New Linked Service** (Nová propojená služba) přidejte **Name** (Název) jako **SqlServerLinkedService**. V části **Connect via integration runtime** (Připojit prostřednictvím prostředí Integration Runtime) vyberte **New** (Nový). V této části vytvoříte místní prostředí Integration Runtime a přidružíte ho k místnímu počítači s databází SQL Serveru. Místní prostředí Integration Runtime je komponenta, která kopíruje data z databáze SQL Serveru na vašem počítači do úložiště objektů blob. 

   ![Nové prostředí Integration Runtime](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)

1. V okně **Instalace prostředí Integration Runtime** vyberte **Privátní síť** a pak **Další**. 

   ![Výběr privátní sítě](./media/tutorial-hybrid-copy-portal/select-private-network.png)

1. Zadejte název prostředí Integration Runtime a vyberte **Další**.

    ![Název prostředí Integration Runtime](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)

1. V části **Možnost 1: Expresní instalace** vyberte **Kliknutím sem spustíte expresní instalaci pro tento počítač**. 

    ![Odkaz na expresní instalaci](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)

1. V okně **Expresní instalace prostředí Integration Runtime (v místním prostředí)** vyberte **Zavřít**. 

    ![Expresní instalace prostředí Integration Runtime (v místním prostředí)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. V okně **New Linked Service** (Nová propojená služba) zkontrolujte, jestli je v seznamu **Connect via integration runtime** (Připojit prostřednictvím Integration Runtime) vybraný výše vytvořený **Integration Runtime**. 

    ![](./media/tutorial-hybrid-copy-portal/select-integration-runtime.png)

1. V okně **Nová propojená služba** proveďte následující kroky:

    a. V části **Název** zadejte **SqlServerLinkedService**.

    b. Ověřte, že se v části **Připojení prostřednictvím prostředí Integration Runtime** zobrazí místní prostředí Integration Runtime, které jste vytvořili dříve.

    c. V části **Název serveru** zadejte název vaší instance SQL Serveru. 

    d. V části **Název databáze** zadejte název databáze s tabulkou **emp**.

    e. V části **Typ ověřování** vyberte odpovídající typ ověřování, který má služba Data Factory používat pro připojení k vaší databázi SQL Serveru.

    f. V částech **Uživatelské jméno** a **Heslo** zadejte uživatelské jméno a heslo. Pokud v názvu uživatelského účtu nebo serveru potřebujete použít zpětné lomítko (\\), vložte před něj řídicí znak (\\). Použijte například *mydomain\\\\myuser*.

    g. Vyberte **Test připojení**. Provedením tohoto kroku potvrdíte, že se služba Data Factory může připojit k vaší databázi SQL Serveru pomocí místního prostředí Integration Runtime, které jste vytvořili.

    h. Pokud chcete propojenou službu uložit, vyberte **Dokončit**.

       

1. Měli byste se vrátit do okna s otevřenou zdrojovou datovou sadou. Na kartě **Připojení** v okně **Vlastnosti** proveďte následující kroky: 

    a. Ověřte, že se v části **Propojená služba** zobrazí **SqlServerLinkedService**.

    b. V rozevíracím seznamu **Tabulka** vyberte **[dbo].[emp]**.

    ![Informace o připojení zdrojové datové sady](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)

1. Přejděte na kartu s kanálem **SQLServerToBlobPipeline** nebo vyberte kanál **SQLServerToBlobPipeline** ve stromovém zobrazení. 

    ![Karta Kanál](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)

1. Přejděte na kartu **Jímka** v dolní části okna **Vlastnosti** a vyberte **+ Nová**. 

    ![Karta Jímka](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)

1. V okně **Nová datová sada** vyberte **Azure Blob Storage**. Pak vyberte **Dokončit**. Otevře se nová karta pro tuto datovou sadu. Datová sada se zobrazí také ve stromovém zobrazení. 

    ![Výběr služby Blob Storage](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)

1. V části **Název** zadejte **AzureBlobDataset**.

    ![Název datové sady jímky](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)

1. Přejděte na kartu **Připojení** v dolní části okna **Vlastnosti**. Vedle možnosti **Propojená služba** vyberte **+ Nová**. 

    ![Tlačítko Nová propojená služba](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)

1. V okně **Nová propojená služba** proveďte následující kroky:

    a. Do pole **Název** zadejte **AzureStorageLinkedService**.

    b. V rozevíracím seznamu **Název účtu úložiště** vyberte svůj účet úložiště.

    c. Pokud chcete otestovat připojení k vašemu účtu úložiště, vyberte **Test připojení**.

    d. Vyberte **Uložit**.

    ![Nastavení propojené služby Storage](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 

1. Měli byste se vrátit do okna s otevřenou datovou sadou jímky. Na kartě **Připojení** proveďte následující kroky: 

    a. Ověřte, že je v části **Propojená služba** vybraná služba **AzureStorageLinkedService**.

    b. Jako část **složka**/ **Adresář** v **Cestě k souboru** zadejte **adftutorial/fromonprem**. Pokud výstupní složka v kontejneru adftutorial neexistuje, služba Data Factory ji automaticky vytvoří.

    c. Jako část **název souboru** v **Cestě k souboru** vyberte **Přidat dynamický obsah**.   

    ![hodnota názvu dynamického souboru](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Přidejte `@CONCAT(pipeline().RunId, '.txt')`, vyberte **Dokončit**. Tím se soubor přejmenuje na PipelineRunID.txt. 

    ![dynamické výraz pro překlad názvu souboru](./media/tutorial-hybrid-copy-portal/add-dynamic-file-name.png)

    ![Připojení k datové sadě jímky](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)

1. Přejděte na kartu s otevřeným kanálem nebo vyberte kanál ve stromovém zobrazení. Ověřte, že je v části **Datová sada jímky** vybraná datová sada **AzureBlobDataset**. 

    ![Vybraná datová sada jímky](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)

1. Pokud chcete ověřit nastavení kanálu, vyberte **Ověřit** na panelu nástrojů pro kanál. Pokud chcete **Sestavu ověření kanálu** zavřít, vyberte **Zavřít**. 

    ![Ověření kanálu](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)

1. Pokud chcete vytvořené entity publikovat do služby Data Factory, vyberte **Publikovat vše**.

    ![Tlačítko Publikovat](./media/tutorial-hybrid-copy-portal/publish-button.png)

1. Počkejte, dokud se nezobrazí překryvné okno **Publikování proběhlo úspěšně**. Pokud chcete zkontrolovat stav publikování, vyberte odkaz **Zobrazit oznámení** na levé straně. Pokud chcete okno oznámení zavřít, vyberte **Zavřít**. 

    ![Publikování proběhlo úspěšně](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)


## <a name="trigger-a-pipeline-run"></a>Aktivace spuštění kanálu
Na panelu nástrojů pro kanál vyberte **Aktivační událost** a pak vyberte **Aktivovat**.

![Aktivace spuštění kanálu](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1. Přejděte na kartu **Monitorování**. Zobrazí se kanál, který jste ručně aktivovali v předchozím kroku. 

    ![Monitorování spuštění kanálu](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
1. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Zobrazí se pouze spuštění aktivit, protože kanál obsahuje pouze jednu aktivitu. Pokud chcete zobrazit podrobnosti o operaci kopírování, vyberte odkaz **Podrobnosti** (ikona brýlí) ve sloupci **Akce**. Pokud chcete přejít zpátky k zobrazení **Spuštění kanálu**, vyberte **Kanály** v horní části.

    ![Monitorování spuštění aktivit](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>Ověření výstupu
Kanál v kontejneru objektů blob `adftutorial` automaticky vytvoří výstupní složku *fromonprem*. Zkontrolujte, že výstupní složka obsahuje soubor *[pipeline().RunId].txt*. 

![potvrzení názvu výstupního souboru](./media/tutorial-hybrid-copy-portal/sink-output.png)


## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného v úložišti objektů blob. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvořte místní prostředí Integration Runtime.
> * Vytvoření propojených služeb SQL Server a Storage 
> * Vytvoření datových sad SQL Serveru a úložiště objektů blob
> * Vytvoření kanálu s aktivitou kopírování pro přesun dat
> * Zahájení spuštění kanálu
> * Monitorování spuštění kanálu

Seznam úložišť dat podporovaných službou Data Factory najdete v tématu popisujícím [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).

Pokud se chcete dozvědět, jak hromadně kopírovat data ze zdroje do cíle, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Hromadné kopírování dat](tutorial-bulk-copy-portal.md)
