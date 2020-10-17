---
title: Kopírování místních dat pomocí nástroje Azure Kopírování dat
description: Vytvořte datovou továrnu Azure a pak pomocí nástroje Kopírování dat zkopírujte data z databáze SQL Server do úložiště objektů BLOB v Azure.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 06/09/2020
ms.openlocfilehash: b9bf03110fcb4f0c034c70d5e77e558b949a7825
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147996"
---
# <a name="copy-data-from-a-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Kopírování dat z databáze SQL Server do úložiště objektů BLOB v Azure pomocí nástroje Kopírování dat
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuální verze](tutorial-hybrid-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto kurzu pomocí webu Azure Portal vytvoříte datovou továrnu. Pak použijete nástroj Kopírování dat k vytvoření kanálu, který kopíruje data z databáze SQL Server do úložiště objektů BLOB v Azure.

> [!NOTE]
> - Pokud se službou Azure Data Factory teprve začínáte, přečtěte si téma [Seznámení se službou Data Factory](introduction.md).

V tomto kurzu budete provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Předpoklady
### <a name="azure-subscription"></a>Předplatné Azure
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

### <a name="azure-roles"></a>Role Azure
Pro vytvoření instancí služby Data Factory musí být uživatelský účet, který používáte pro přihlášení k Azure, přiřazený k roli *Přispěvatel* nebo *vlastník* nebo musí být *správcem* předplatného Azure.

Pokud chcete zobrazit oprávnění, která v předplatném máte, přejděte na web Azure Portal. V pravém horním rohu vyberte své uživatelské jméno a pak vyberte **Oprávnění**. Pokud máte přístup k několika předplatným, vyberte odpovídající předplatné. Ukázku pokynů pro přidání uživatele k roli najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 a 2017
V tomto kurzu použijete databázi SQL Server jako *zdrojové* úložiště dat. Kanál v datové továrně, který vytvoříte v tomto kurzu, kopíruje data z této SQL Server databáze (zdroj) do úložiště objektů BLOB (jímka). Pak vytvoříte tabulku s názvem **EMP** v databázi SQL Server a do tabulky vložíte několik vzorových položek.

1. Spusťte aplikaci SQL Server Management Studio. Pokud na vašem počítači ještě není nainstalovaná, přejděte na stránku pro [stažení aplikace SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

1. Pomocí svých přihlašovacích údajů se přihlaste ke své instanci SQL Serveru.

1. Vytvořte ukázkovou databázi. Ve stromovém zobrazení klikněte pravým tlačítkem na **Databáze** a pak vyberte **Nová databáze**.

1. V okně **Nová databáze** zadejte název databáze a pak vyberte **OK**.

1. Vytvořte tabulku **emp** a vložte do ní nějaká ukázková data spuštěním následujícího skriptu dotazu proti databázi. Ve stromovém zobrazení klikněte pravým tlačítkem na databázi, kterou jste vytvořili, a pak vyberte **Nový dotaz**.

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
V tomto kurzu použijete účet úložiště Azure (konkrétně úložiště objektů blob) pro obecné účely jako cílové úložiště dat nebo úložiště dat jímky. Pokud účet úložiště pro obecné účely nemáte, přečtěte si téma [Vytvoření účtu úložiště](../storage/common/storage-account-create.md), kde najdete pokyny k jeho vytvoření. Kanál v datové továrně, který vytvoříte v tomto kurzu, kopíruje data z databáze SQL Server (zdroj) do tohoto úložiště objektů BLOB (jímka). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Získání názvu a klíče účtu úložiště
V tomto kurzu použijete název a klíč svého účtu úložiště. Název a klíč svého účtu úložiště získáte pomocí následujícího postupu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého uživatelského jména a hesla Azure.

1. V levém podokně vyberte **Všechny služby**. Proveďte filtrování pomocí klíčového slova **úložiště** a pak vyberte **Účty úložiště**.

    ![Vyhledávání účtu úložiště](media/doc-common-process/search-storage-account.png)

1. V seznamu účtů úložiště v případě potřeby vyfiltrujte váš účet úložiště. Pak vyberte svůj účet úložiště.

1. V okně **Účet úložiště** vyberte **Přístupové klíče**.


1. Zkopírujte hodnoty polí **Název účtu úložiště** a **klíč1** a vložte je do Poznámkového bloku nebo jiného editoru pro pozdější použití v rámci kurzu.

#### <a name="create-the-adftutorial-container"></a>Vytvoření kontejneru adftutorial
V této části vytvoříte ve svém úložišti objektů blob kontejner objektů blob **adftutorial**.

1. V okně **Účet úložiště** přepněte na **Přehled** a pak vyberte **Objekty blob**.

1. V okně **objekty blob** vyberte **+ kontejner**.

1. V okně **Nový kontejner** v části **název**zadejte **adftutorial**a pak vyberte **OK**.

1. V seznamu kontejnerů vyberte **adftutorial**.


1. Okno **Kontejner** pro **adftutorial** nechte otevřené. Použijete ji k ověření výstupu na konci tohoto kurzu. Data Factory v tomto kontejneru vytvoří výstupní složku automaticky, takže ji nemusíte vytvářet.


## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce na levé straně vyberte **+ vytvořit**  >  **Analytics**  >  **Data Factory**analýzy prostředků.

   ![Vytvoření nové datové továrny](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**.

   Název datové továrny musí být *globálně jedinečný*. Pokud se u pole s názvem zobrazí následující chybová zpráva, tak název datové továrny změňte (třeba na vaše_jméno_ADFTutorialDataFactory). Pravidla pro pojmenovávání artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).

   ![Název nové datové továrny](./media/doc-common-process/name-not-available-error.png)
1. Vyberte **předplatné** Azure, v rámci kterého chcete datovou továrnu vytvořit.
1. U položky **Skupina prostředků** proveďte jeden z následujících kroků:

   - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

   - Vyberte **vytvořit novou**a zadejte název skupiny prostředků. 
        
     Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).
1. Jako **Verzi** vyberte **V2**.
1. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například služby Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používané datovou továrnou můžou být v jiných umístěních nebo oblastech.
1. Vyberte **Vytvořit**.

1. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.

     ![Domovská stránka objektu pro vytváření dat](./media/doc-common-process/data-factory-home-page.png)
1. Výběrem dlaždice **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Data Factory.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Vytvoření kanálu pomocí nástroje pro kopírování dat

1. Na stránce **Začínáme** vyberte dlaždici **Kopírovat data**. Spustí se nástroj pro kopírování dat.

   ![Stránka Začínáme](./media/doc-common-process/get-started-page.png)

1. Na stránce **Vlastnosti** nástroje pro kopírování dat v části **Název úlohy** zadejte **CopyFromOnPremSqlToAzureBlobPipeline**. Pak vyberte **Další**. Nástroj pro kopírování dat vytvoří kanál s názvem, který zadáte do tohoto pole.
  ![Název úlohy](./media/tutorial-hybrid-copy-data-tool/properties-page.png)

1. Na stránce **Source data store** (Zdrojové úložiště dat) klikněte na **Create new connection** (Vytvořit nové připojení).

1. V části **Nová propojená služba**vyhledejte **SQL Server**a pak vyberte **pokračovat**.

1. V dialogovém okně **Nová propojená služba (SQL Server)** do pole **název**zadejte **SqlServerLinkedService**. V části **připojení prostřednictvím prostředí Integration runtime**vyberte **+ Nový** . Musíte vytvořit místní prostředí Integration Runtime, stáhnout ho na svůj počítač a zaregistrovat ho ve službě Data Factory. Místní prostředí Integration Runtime kopíruje data mezi vaším místním prostředím a cloudem.

1. V dialogovém okně **nastavení Integration runtime** vyberte možnost místní **hostování**. Potom vyberte **Pokračovat**.

   ![Vytvoření prostředí Integration Runtime](./media/tutorial-hybrid-copy-data-tool/create-self-hosted-integration-runtime.png)

1. V dialogovém okně **nastavení Integration runtime** do pole **název**zadejte **TutorialIntegrationRuntime**. Potom vyberte **Vytvořit**.

1. V dialogovém okně **nastavení Integration runtime** vyberte **kliknutím sem spustíte expresní instalaci pro tento počítač**. Tato akce nainstaluje prostředí Integration Runtime na vašem počítači a zaregistruje ho ve službě Data Factory. Případně můžete využít možnost ruční instalace a stáhnout instalační soubor, spustit ho a použít klíč k registraci prostředí Integration Runtime.

1. Spusťte staženou aplikaci. V okně se zobrazí stav expresní instalace.

    ![Stav expresní instalace](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)

1. V dialogovém okně **Nová propojená služba (SQL Server)** potvrďte, že je pro pole Integration runtime vybraná možnost **TutorialIntegrationRuntime** . Pak proveďte následující kroky:

    a. V části **Název** zadejte **SqlServerLinkedService**.

    b. V části **Název serveru** zadejte název vaší instance SQL Serveru.

    c. V části **Název databáze** zadejte název vaší místní databáze.

    d. V části **Typ ověřování** vyberte odpovídající typ ověřování.

    e. V části **uživatelské jméno**zadejte jméno uživatele s přístupem k SQL Server.

    f. Zadejte **heslo** pro uživatele.

    například Otestujte připojení a vyberte **Dokončit**.

      ![Vybrané prostředí Integration Runtime](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)

1. Na stránce **zdrojové úložiště dat** vyberte **Další**.

1. Na stránce **Vyberte tabulky, ze kterých se mají kopírovat data, nebo použijte vlastní dotaz** vyberte v seznamu tabulku **[dbo].[emp]** a vyberte **Další**. Můžete vybrat jakoukoli jinou tabulku, která je založená na vaší databázi.

1. Na stránce **Destination data store** (Cílové úložiště dat) vyberte **Create new connection** (Vytvořit nové připojení).


1. V **nové propojené službě**vyhledejte a vyberte **objekt blob Azure**a pak vyberte **pokračovat**.

   ![Výběr služby Blob Storage](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)

1. V dialogovém okně **New Linked Service (Azure Blob Storage)** (Nová propojená služba (Azure Blob Storage)) proveďte následující kroky:

   a. Do **pole název**zadejte **AzureStorageLinkedService**.

   b. V části **Connect via integration runtime** (Připojit prostřednictvím prostředí Integration Runtime) vyberte **TutorialIntegrationRuntime**.

   c. V části **Název účtu úložiště** vyberte z rozevíracího seznamu svůj účet úložiště.

   d. Vyberte **Dokončit**.

1. V dialogu **cílové úložiště dat** se ujistěte, že je vybraná možnost **Azure Blob Storage** . Pak vyberte **Další**.

1. V dialogovém okně **Choose the output file or folder** (Zvolte výstupní soubor nebo složku) v části **Folder path** (Cesta ke složce) zadejte **adftutorial/fromonprem**. Kontejner **adftutorial** jste vytvořili jako součást požadavků. Pokud výstupní složka neexistuje (v tomto případě **fromonprem**), služba Data Factory ji automaticky vytvoří. Pomocí tlačítka **Procházet** můžete také procházet úložiště objektů BLOB a jeho kontejnery/složky. Pokud do pole **File name** (Název souboru) nezadáte žádnou hodnotu, použije se ve výchozím nastavení název ze zdroje (v tomto případě **dbo.emp**).

   ![Zvolte výstupní soubor nebo složku](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)

1. V dialogovém okně **File format settings** (Nastavení formátu souboru) vyberte **Next** (Další).

1. V dialogovém okně **Settings** (Nastavení) vyberte **Next** (Další).

1. V dialogovém okně **Summary** (Souhrn) zkontrolujte hodnoty všech nastavení a vyberte **Next** (Další).

1. Na stránce **Nasazení** vyberte **Monitorovat** a začněte monitorovat kanál (úlohu). 

1. Po dokončení spuštění kanálu můžete zobrazit stav kanálu, který jste vytvořili. 

1. Na stránce spuštění kanálu vyberte **aktualizovat** , aby se seznam aktualizoval. Kliknutím na odkaz **název kanálu** zobrazíte podrobnosti o spuštění aktivit nebo znovu spustíte kanál. 

1. Na stránce spuštění aktivit vyberte odkaz **Podrobnosti** (ikona brýlí) ve sloupci **název aktivity** , kde najdete další podrobnosti o operaci kopírování. Pokud se chcete vrátit do zobrazení spuštění kanálu, vyberte odkaz **všechny spuštění kanálu** v nabídce s popisem cesty. Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**.

1. Ověřte, že složka **fromonprem** kontejneru **adftutorial** obsahuje výstupní soubor.

1. Výběrem karty **Upravit** na levé straně přepněte do režimu úprav. Pomocí editoru můžete aktualizovat propojené služby, datové sady a kanály vytvořené nástrojem. Vyberte **Kód** a zobrazte kód JSON přidružený k entitě otevřené v editoru. Podrobnosti o úpravách těchto entit v uživatelském rozhraní služby Data Factory najdete ve [verzi tohoto kurzu pro Azure Portal](tutorial-copy-data-portal.md).


## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z databáze SQL Server do úložiště objektů BLOB. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

Seznam úložišť dat podporovaných službou Data Factory najdete v tématu popisujícím [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).

Pokud se chcete dozvědět, jak hromadně kopírovat data ze zdroje do cíle, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Hromadné kopírování dat](tutorial-bulk-copy-portal.md)
