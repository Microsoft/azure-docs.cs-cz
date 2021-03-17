---
title: Kopírování dat z SQL Server do úložiště objektů BLOB pomocí Azure Portal
description: Zjistěte, jak kopírovat data z místního úložiště dat do cloudu s využitím místního prostředí Integration Runtime ve službě Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: fa251b80f8e44f48de565685a0841f57f48620f1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724961"
---
# <a name="copy-data-from-a-sql-server-database-to-azure-blob-storage"></a>Kopírování dat z databáze SQL Server do úložiště objektů BLOB v Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V tomto kurzu použijete Azure Data Factory uživatelské rozhraní (UI) k vytvoření kanálu datové továrny, který kopíruje data z databáze SQL Server do úložiště objektů BLOB v Azure. Vytvoříte a použijete místní prostředí Integration Runtime, které přesouvá data mezi místním a cloudovým úložištěm dat.

> [!NOTE]
> Tento článek neposkytuje podrobný úvod do služby Data Factory. Další informace najdete v tématu [Seznámení se službou Data Factory](introduction.md).

V tomto kurzu budete provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvořte místní prostředí Integration Runtime.
> * Vytvoření propojených služeb SQL Server a Azure Storage
> * Vytvoření datových sad SQL Serveru a Azure Blob
> * Vytvoření kanálu s aktivitou kopírování pro přesun dat
> * Zahajte spuštění kanálu.
> * Monitorování spuštění kanálu

## <a name="prerequisites"></a>Požadavky
### <a name="azure-subscription"></a>Předplatné Azure
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

### <a name="azure-roles"></a>Role Azure
Pro vytvoření instancí služby Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, přiřazený k roli *Přispěvatel* nebo *vlastník* nebo musí být *správcem* předplatného Azure.

Pokud chcete zobrazit oprávnění, která v předplatném máte, přejděte na web Azure Portal. V pravém horním rohu vyberte své uživatelské jméno a pak vyberte **Oprávnění**. Pokud máte přístup k několika předplatným, vyberte odpovídající předplatné. Ukázku pokynů pro přidání uživatele k roli najdete v tématu [přiřazení rolí Azure pomocí Azure Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 a 2017
V tomto kurzu použijete databázi SQL Server jako *zdrojové* úložiště dat. Kanál v datové továrně, který vytvoříte v tomto kurzu, kopíruje data z této SQL Server databáze (zdroj) do úložiště objektů BLOB (jímka). Pak vytvoříte tabulku s názvem **EMP** v databázi SQL Server a do tabulky vložíte několik vzorových položek.

1. Spusťte aplikaci SQL Server Management Studio. Pokud na vašem počítači ještě není nainstalovaná, přejděte na stránku pro [stažení aplikace SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

1. Pomocí svých přihlašovacích údajů se přihlaste ke své instanci SQL Serveru.

1. Vytvořte ukázkovou databázi. Ve stromovém zobrazení klikněte pravým tlačítkem na **Databáze** a pak vyberte **Nová databáze**.
1. V okně **Nová databáze** zadejte název databáze a pak vyberte **OK**.

1. Vytvořte tabulku **emp** a vložte do ní nějaká ukázková data spuštěním následujícího skriptu dotazu proti databázi. Ve stromovém zobrazení klikněte pravým tlačítkem na databázi, kterou jste vytvořili, a pak vyberte **Nový dotaz**.

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

### <a name="azure-storage-account"></a>Účet služby Azure Storage
V tomto kurzu použijete účet úložiště Azure (konkrétně úložiště objektů blob) pro obecné účely jako cílové úložiště dat nebo úložiště dat jímky. Pokud nemáte účet úložiště Azure pro obecné účely, přečtěte si téma [Vytvoření účtu úložiště](../storage/common/storage-account-create.md). Kanál v datové továrně, který vytvoříte v tomto kurzu, kopíruje data z databáze SQL Server (zdroj) do úložiště objektů BLOB (jímka). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Získání názvu a klíče účtu úložiště
V tomto kurzu použijete název a klíč svého účtu úložiště. Název a klíč svého účtu úložiště získáte pomocí následujícího postupu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého uživatelského jména a hesla Azure.

1. V levém podokně vyberte **Všechny služby**. Proveďte filtrování pomocí klíčového slova **úložiště** a pak vyberte **Účty úložiště**.

    ![Vyhledávání účtu úložiště](media/doc-common-process/search-storage-account.png)

1. V seznamu účtů úložiště v případě potřeby vyfiltrujte svůj účet úložiště. Pak vyberte svůj účet úložiště.

1. V okně **Účet úložiště** vyberte **Přístupové klíče**.

1. Zkopírujte hodnoty polí **Název účtu úložiště** a **klíč1** a vložte je do Poznámkového bloku nebo jiného editoru pro pozdější použití v rámci kurzu.

#### <a name="create-the-adftutorial-container"></a>Vytvoření kontejneru adftutorial
V této části vytvoříte ve svém úložišti objektů blob kontejner objektů blob **adftutorial**.

1. V okně **účet úložiště** přejít na **Přehled** a pak vyberte **kontejnery**.

    ![Výběr možnosti Objekty blob](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. V okně **kontejnery** vyberte **+ kontejner** a vytvořte nový.

1. V okně **Nový kontejner** v části **Název** zadejte **adftutorial**. Potom vyberte **Vytvořit**.

1. V seznamu kontejnerů vyberte **adftutorial** , který jste právě vytvořili.

1. Nechejte okno **kontejneru** pro **adftutorial** otevřené. Použijete ji k ověření výstupu na konci tohoto kurzu. Data Factory v tomto kontejneru vytvoří výstupní složku automaticky, takže ji nemusíte vytvářet.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
V tomto kroku vytvoříte datovou továrnu a spustíte uživatelské rozhraní služby Data Factory, ve kterém v této datové továrně vytvoříte kanál.

1. Otevřete webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
1. V nabídce vlevo vyberte vytvořit data Factory **pro**  >  **integraci** prostředků  >  :

   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**.

   Název datové továrny musí být *globálně jedinečný*. Pokud se u pole s názvem zobrazí následující chybová zpráva, tak název datové továrny změňte (třeba na vaše_jméno_ADFTutorialDataFactory). Pravidla pro pojmenovávání artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).

   ![Název nové datové továrny](./media/doc-common-process/name-not-available-error.png)

1. Vyberte **předplatné** Azure, v rámci kterého chcete datovou továrnu vytvořit.
1. U položky **Skupina prostředků** proveďte jeden z následujících kroků:

   - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

   - Vyberte **vytvořit novou** a zadejte název skupiny prostředků.
        
     Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).
1. Jako **Verzi** vyberte **V2**.
1. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například služby Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používané datovou továrnou můžou být v jiných oblastech.
1. Vyberte **Vytvořit**.

1. Po dokončení vytváření se zobrazí stránka **Data Factory** , jak je znázorněno na obrázku:

    ![Domovská stránka objektu pro vytváření dat](./media/doc-common-process/data-factory-home-page.png)
1. Výběrem dlaždice **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Data Factory.


## <a name="create-a-pipeline"></a>Vytvoření kanálu

1. Na stránce **Začínáme** vyberte **Vytvořit kanál**. Automaticky se pro vás vytvoří kanál. Kanál se zobrazí ve stromovém zobrazení a otevře se jeho editor.

   ![Stránka Začínáme](./media/doc-common-process/get-started-page.png)

1. Na panelu Obecné v části **vlastnosti** zadejte **SQLServerToBlobPipeline** pro **název**. Pak panel sbalíte kliknutím na ikonu vlastnosti v pravém horním rohu.

1. V poli nástroje **aktivity** rozbalte **přesunout & transformovat**. Přetáhněte aktivitu **Kopírování** na plochu návrháře kanálu. Nastavte název aktivity na **CopySqlServerToAzureBlobActivity**.

1. V okně **Vlastnosti** přejděte na kartu **Zdroj** a vyberte **+ Nový**.

1. V dialogovém okně **Nová datová sada** vyhledejte **SQL Server**. Vyberte **SQL Server** a pak vyberte **pokračovat**.
    ![Nová datová sada SqlServer](./media/tutorial-hybrid-copy-portal/create-sqlserver-dataset.png)

1. V dialogovém okně **nastavit vlastnosti** zadejte do pole **název** hodnotu **SqlServerDataset**. V části **propojená služba** vyberte **+ Nová**. V tomto kroku vytvoříte připojení ke zdrojovému úložišti dat (databáze SQL Serveru).

1. V dialogovém okně **Nová propojená služba** přidejte **název** jako **SqlServerLinkedService**. V části **připojit prostřednictvím prostředí Integration runtime** vyberte **+ Nový**.  V této části vytvoříte místní prostředí Integration Runtime a přidružíte ho k místnímu počítači s databází SQL Serveru. Místní prostředí Integration Runtime je komponenta, která kopíruje data z databáze SQL Serveru na vašem počítači do úložiště objektů blob.

1. V dialogovém okně **nastavení Integration runtime** **Vyberte možnost v místním** prostředí a pak vyberte **pokračovat**.

1. Do pole název zadejte **TutorialIntegrationRuntime**. Potom vyberte **Vytvořit**.

1. Pro nastavení vyberte **kliknutím sem spustíte expresní instalaci pro tento počítač**. Tato akce nainstaluje prostředí Integration Runtime na vašem počítači a zaregistruje ho ve službě Data Factory. Případně můžete využít možnost ruční instalace a stáhnout instalační soubor, spustit ho a použít klíč k registraci prostředí Integration Runtime.
    ![Instalace prostředí Integration Runtime](./media/tutorial-hybrid-copy-portal/intergration-runtime-setup.png)

1. V okně **instalace Integration runtime (v místním prostředí) Express** po dokončení procesu vyberte **Zavřít** .

    ![Expresní instalace prostředí Integration Runtime (v místním prostředí)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. V dialogovém okně **Nová propojená služba (SQL Server)** potvrďte, že je v části **připojení prostřednictvím prostředí Integration runtime** vybraná možnost **TutorialIntegrationRuntime** . Pak proveďte následující kroky:

    a. V části **Název** zadejte **SqlServerLinkedService**.

    b. V části **Název serveru** zadejte název vaší instance SQL Serveru.

    c. V části **Název databáze** zadejte název databáze s tabulkou **emp**.

    d. V části **Typ ověřování** vyberte odpovídající typ ověřování, který má služba Data Factory používat pro připojení k vaší databázi SQL Serveru.

    e. V částech **Uživatelské jméno** a **Heslo** zadejte uživatelské jméno a heslo. V případě potřeby použijte *MyDomain \\ MyUser* jako uživatelské jméno.

    f. Vyberte **Test připojení**. Tento krok potvrzuje, že Data Factory se může připojit k vaší databázi SQL Server pomocí místního prostředí Integration runtime, které jste vytvořili.

    například Pokud chcete propojenou službu uložit, vyberte **vytvořit**.
 
    ![Nová propojená služba (SQL Server)](./media/tutorial-hybrid-copy-portal/new-sqlserver-linked-service.png)

1. Po vytvoření propojené služby se vrátíte zpátky na stránku **vlastností set** pro SqlServerDataset. Postupujte následovně:

    a. Ověřte, že se v části **Propojená služba** zobrazí **SqlServerLinkedService**.

    b. V části **název tabulky** vyberte **[dbo]. [ EMP]**.
    
    c. Vyberte **OK**.

1. Přejděte na kartu s kanálem **SQLServerToBlobPipeline** nebo vyberte kanál **SQLServerToBlobPipeline** ve stromovém zobrazení.

1. Přejděte na kartu **Jímka** v dolní části okna **Vlastnosti** a vyberte **+ Nová**.

1. V dialogovém okně **Nová datová sada** vyberte **Azure Blob Storage**. Potom vyberte **Pokračovat**.

1. V dialogovém okně **Vybrat formát** zvolte typ formátu dat. Potom vyberte **Pokračovat**.

    ![Výběr formátu dat](./media/doc-common-process/select-data-format.png)

1. V dialogovém okně **nastavit vlastnosti** zadejte **AzureBlobDataset** do pole název. Vyberte **+ Nová** vedle textového pole **Propojená služba**.

1. V dialogovém okně **Nová propojená služba (Azure Blob Storage)** jako název zadejte **AzureStorageLinkedService** a v seznamu název **účtu úložiště** vyberte svůj účet úložiště. Otestujte připojení a pak vyberte **vytvořit** a nasaďte propojenou službu.

1. Po vytvoření propojené služby se vrátíte zpátky na stránku **vlastností set** . Vyberte **OK**.

1. Otevřete datovou sadu jímky. Na kartě **Připojení** proveďte následující kroky:

    a. Ověřte, že je v části **Propojená služba** vybraná služba **AzureStorageLinkedService**.

    b. Do pole **cesta k souboru** zadejte **adftutorial/Fromonprem** pro část **kontejneru/adresáře** . Pokud výstupní složka v kontejneru adftutorial neexistuje, služba Data Factory ji automaticky vytvoří.

    c. V části **soubor** vyberte **Přidat dynamický obsah**.
    ![dynamické výraz pro překlad názvu souboru](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Přidejte `@CONCAT(pipeline().RunId, '.txt')` a pak vyberte **Dokončit**. Tato akce přejmenuje soubor pomocí PipelineRunID.txt.

1. Přejděte na kartu s otevřeným kanálem nebo vyberte kanál ve stromovém zobrazení. Ověřte, že je v části **Datová sada jímky** vybraná datová sada **AzureBlobDataset**.

1. Pokud chcete ověřit nastavení kanálu, vyberte **Ověřit** na panelu nástrojů pro kanál. Pokud chcete **výstup ověřování kanálu** zavřít, vyberte **>>** ikonu.
    ![ověřit kanál](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
    

1. Pokud chcete publikovat entity, které jste vytvořili Data Factory, vyberte **publikovat vše**.

1. Počkejte, dokud neuvidíte místní okno **publikování dokončeno** . Chcete-li zjistit stav publikování, vyberte odkaz **Zobrazit oznámení** v horní části okna. Pokud chcete okno oznámení zavřít, vyberte **Zavřít**.


## <a name="trigger-a-pipeline-run"></a>Aktivace spuštění kanálu
Vyberte možnost **Přidat aktivační událost** na panelu nástrojů pro kanál a potom vyberte **aktivovat nyní**.

## <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1. Přejít na kartu **monitorování** . Zobrazí se kanál, který jste ručně aktivovali v předchozím kroku.

1. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte odkaz **SQLServerToBlobPipeline** pod *názvem kanálu*. 
    ![Monitorování spuštění kanálu](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)

1. Na stránce **spuštění aktivit** klikněte na odkaz Podrobnosti (brýlí obrázek) a zobrazte podrobnosti o operaci kopírování. Pokud se chcete vrátit do zobrazení spuštění kanálu, vyberte **všechny spuštěné kanály** v horní části.

## <a name="verify-the-output"></a>Ověření výstupu
Kanál v kontejneru objektů blob `adftutorial` automaticky vytvoří výstupní složku *fromonprem*. Zkontrolujte, že výstupní složka obsahuje soubor *[pipeline().RunId].txt*.


## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného v úložišti objektů blob. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvořte místní prostředí Integration Runtime.
> * Vytvoření propojených služeb SQL Server a Storage
> * Vytvoření datových sad SQL Serveru a úložiště objektů blob
> * Vytvoření kanálu s aktivitou kopírování pro přesun dat
> * Zahajte spuštění kanálu.
> * Monitorování spuštění kanálu

Seznam úložišť dat podporovaných službou Data Factory najdete v tématu popisujícím [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).

Pokud se chcete dozvědět, jak hromadně kopírovat data ze zdroje do cíle, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Hromadné kopírování dat](tutorial-bulk-copy-portal.md)