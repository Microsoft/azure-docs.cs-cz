---
title: Vytvoření kanálu datové továrny pomocí Azure Portal
description: Tento kurz obsahuje podrobné pokyny k vytvoření datové továrny s kanálem pomocí portálu Azure Portal. Kanál používá aktivitu kopírování ke kopírování dat z úložiště objektů BLOB v Azure do Azure SQL Database.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 12/14/2020
ms.author: jingwang
ms.openlocfilehash: ca3250fb54440d6b68a808a3b1b3800bdcfd14eb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375761"
---
# <a name="copy-data-from-azure-blob-storage-to-a-database-in-azure-sql-database-by-using-azure-data-factory"></a>Kopírování dat z úložiště objektů BLOB v Azure do databáze v Azure SQL Database pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto kurzu vytvoříte datovou továrnu pomocí uživatelského rozhraní služby Azure Data Factory. Kanál v této datové továrně kopíruje data z úložiště objektů BLOB v Azure do databáze v Azure SQL Database. Schéma konfigurace v tomto kurzu se vztahuje na kopírování z úložiště dat založeného na souborech do relačního úložiště dat. Seznam úložišť dat, která jsou podporovaná jako zdroje a jímky, najdete v tabulce [podporovaných úložišť dat](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> Pokud se službou Data Factory teprve začínáte, přečtěte si téma [Úvod do Azure Data Factory](introduction.md).

V tomto kurzu budete provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu s aktivitou kopírování
> * Testovací spuštění kanálu
> * Ruční aktivace kanálu
> * Aktivace kanálu podle plánu
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet služby Azure Storage**. Blob Storage použijete jako *zdrojové* úložiště dat. Pokud účet úložiště nemáte, přečtěte si téma [Vytvoření účtu služby Azure Storage](../storage/common/storage-account-create.md), kde najdete postup jeho vytvoření.
* **Azure SQL Database**. Tuto databázi použijete jako úložiště dat *jímky*. Pokud nemáte databázi v Azure SQL Database, přečtěte si téma [Vytvoření databáze v Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) , kde najdete kroky pro její vytvoření.

### <a name="create-a-blob-and-a-sql-table"></a>Vytvoření objektu blob a tabulky SQL

Teď si připravte úložiště Blob Storage a databázi SQL Database pro tento kurz, a to podle těchto kroků.

#### <a name="create-a-source-blob"></a>Vytvoření zdrojového objektu blob

1. Spusťte Poznámkový blok. Zkopírujte následující text a uložte si ho na disk jako soubor **emp.txt**:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. V úložišti Blob Storage vytvořte kontejner s názvem **adftutorial**. V tomto kontejneru vytvořte složku **input**. Pak do složky **input** nahrajte soubor **emp.txt**. K provedení těchto úloh použijte Azure Portal nebo nástroj, jako je například [Průzkumník služby Azure Storage](https://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Vytvoření tabulky SQL jímky

1. Pomocí následujícího skriptu SQL vytvořte tabulku **dbo. EMP** v databázi:

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

1. Povolte službám Azure přístup k SQL Serveru. Zkontrolujte, že je nastavení **Povolit přístup ke službám Azure** pro váš SQL Server **ZAPNUTÉ**, aby mohla služba Data Factory na tento SQL Server zapisovat data. Pokud chcete toto nastavení ověřit a zapnout, přejděte na logický server SQL > přehled > nastavení brány firewall serveru> nastavte možnost **Povolit přístup ke službám Azure** na **zapnuto**.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
V tomto kroku vytvoříte datovou továrnu a spustíte uživatelské rozhraní služby Data Factory, ve kterém v této datové továrně vytvoříte kanál.

1. Otevřete **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
2. V nabídce vlevo vyberte vytvořit data Factory **pro**  >  **integraci** prostředků  >  .
3. Na stránce **vytvořit data Factory** na kartě **základy** vyberte **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu.
4. U položky **Skupina prostředků** proveďte jeden z následujících kroků:

    a. Z rozevíracího seznamu vyberte existující skupinu prostředků.

    b. Vyberte **vytvořit novou** a zadejte název nové skupiny prostředků.
    
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md). 
5. V části **oblast** vyberte umístění pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používaná datovou továrnou můžou být v jiných oblastech.
6. Do **pole název** zadejte **ADFTutorialDataFactory**.

   Název objektu pro vytváření dat Azure musí být *globálně jedinečný*. Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. (například yournameADFTutorialDataFactory). Pravidla pro pojmenovávání artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).

     ![Nová datová továrna](./media/doc-common-process/name-not-available-error.png)

7. Jako **Verzi** vyberte **V2**.
8. V horní části vyberte kartu **Konfigurace Git** a zaškrtněte políčko **Konfigurovat Git později** .
9. Vyberte možnost **zkontrolovat + vytvořit** a po ověření platnosti vyberte **vytvořit** .
10. Po dokončení vytváření se zobrazí oznámení v centru oznámení. Vyberte **Přejít k prostředku** a přejděte na stránku Datová továrna.
11. Vyberte **vytvořit & monitorování** a spusťte Azure Data Factory uživatelské rozhraní na samostatné kartě.


## <a name="create-a-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte v datové továrně kanál s aktivitou kopírování. Aktivita kopírování kopíruje data z úložiště Blob Storage do databáze SQL Database. V [kurzu Rychlý start](quickstart-create-data-factory-portal.md) jste vytvořili kanál pomocí tohoto postupu:

1. Vytvoření propojené služby
1. Vytvoření vstupní a výstupní datové sady
1. Vytvoření kanálu

V tomto kurzu začnete vytvořením kanálu. Potom vytvoříte propojené služby a datové sady, které budete potřebovat ke konfiguraci kanálu.

1. Na stránce **Začínáme** vyberte **Vytvořit kanál**.

   ![Vytvoření kanálu](./media/doc-common-process/get-started-page.png)

1. Na panelu Obecné v části **vlastnosti** zadejte **CopyPipeline** pro **název**. Pak panel sbalíte kliknutím na ikonu vlastnosti v pravém horním rohu.

1. V poli nástroje **aktivity** rozbalte kategorii **přesunout a transformovat** a přetáhněte **kopírování dat** aktivity z panelu nástrojů na plochu návrháře kanálu. Jako **Název** zadejte **CopyFromBlobToSql**.

    ![Aktivita kopírování](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Konfigurace zdroje

>[!TIP]
>V tomto kurzu použijete *klíč účtu* jako typ ověřování pro zdrojové úložiště dat, ale v případě potřeby můžete vybrat jiné podporované metody ověřování: *URI SAS*,*instanční objekt* a *spravovaná identita* . Podrobnosti najdete v odpovídajících částech [tohoto článku](./connector-azure-blob-storage.md#linked-service-properties) .
>K bezpečnému ukládání tajných kódů pro úložiště dat je také vhodné použít Azure Key Vault. Podrobnější ilustrace najdete v [tomto článku](./store-credentials-in-key-vault.md) .

1. Přejít na kartu **zdroj** . Vyberte **+ Nová** a vytvořte zdrojovou datovou sadu.

1. V dialogovém okně **Nová datová sada** vyberte **Azure Blob Storage** a pak vyberte **pokračovat**. Zdrojová data jsou v úložišti Blob Storage, takže jako zdrojovou datovou sadu vyberete **Azure Blob Storage**.

1. V dialogovém okně **Vybrat formát** zvolte typ formátu dat a pak vyberte **pokračovat**.

1. V dialogovém okně **nastavit vlastnosti** zadejte **SourceBlobDataset** do pole název. Zaškrtněte políčko pro **první řádek jako záhlaví**. V textovém poli **propojená služba** vyberte **+ Nová**.

1. V dialogovém okně **Nová propojená služba (Azure Blob Storage)** jako název zadejte **AzureStorageLinkedService** a v seznamu **název účtu úložiště** vyberte svůj účet úložiště. Otestujte připojení, vyberte **vytvořit** a nasaďte propojenou službu.

1. Po vytvoření propojené služby přejdete zpátky na stránku **Vlastnosti sady** . Vedle pole **Cesta k souboru** vyberte **Procházet**.

1. Přejděte do složky **adftutorial/Input** , vyberte soubor **emp.txt** a pak vyberte **OK**.

1. Vyberte **OK**. Automaticky přejde na stránku kanálu. Na kartě **zdroj** potvrďte, že je vybraná možnost **SourceBlobDataset** . Pokud se na této stránce chcete podívat na náhled dat, vyberte **Náhled dat**.

    ![Zdrojová datová sada](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Konfigurace jímky
>[!TIP]
>V tomto kurzu použijete *ověřování SQL* jako typ ověřování pro úložiště dat jímky, ale v případě potřeby můžete vybrat jiné podporované metody ověřování: *instanční objekt* a *spravovaná identita* . Podrobnosti najdete v odpovídajících částech [tohoto článku](./connector-azure-sql-database.md#linked-service-properties) .
>K bezpečnému ukládání tajných kódů pro úložiště dat je také vhodné použít Azure Key Vault. Podrobnější ilustrace najdete v [tomto článku](./store-credentials-in-key-vault.md) .

1. Přejděte na kartu **Jímka**, vyberte **+ Nová** a vytvořte datovou sadu jímky.

1. V dialogovém okně **Nová datová sada** zadejte "SQL" do vyhledávacího pole, chcete-li filtrovat konektory, vyberte **Azure SQL Database** a pak vyberte **pokračovat**. V tomto kurzu zkopírujte data do databáze SQL Database.

1. V dialogovém okně **nastavit vlastnosti** zadejte **OutputSqlDataset** do pole název. V rozevíracím seznamu **propojená služba** vyberte **+ Nová**. Datová sada musí být přidružená k propojené službě. Propojená služba obsahuje připojovací řetězec, který Data Factory používá pro připojení k SQL Database za běhu. Datová sada určuje kontejner, složku a soubor (volitelné), do kterého se data kopírují.

1. V dialogovém okně **Nová propojená služba (Azure SQL Database)** proveďte následující kroky:

    a. Do pole **Název** zadejte **AzureSqlDatabaseLinkedService**.

    b. V rozevíracím seznamu **Název serveru** vyberte příslušný název instance SQL Serveru.

    c. V části **název databáze** vyberte svou databázi.

    d. Do pole **Uživatelské jméno** zadejte jméno uživatele.

    e. Do pole **Heslo** zadejte heslo pro tohoto uživatele.

    f. Vyberte **Otestovat připojení** a připojení otestujte.

    například Vyberte **vytvořit** a nasaďte propojenou službu.

    ![Uložení nové propojené služby](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Automaticky přejde do dialogového okna **nastavit vlastnosti** . V části **Tabulka** vyberte **[dbo].[emp]**. Pak vyberte **OK**.

1. Přejděte na kartu s kanálem a zkontrolujte, že je v rozevíracím seznamu **Datová sada jímky** vybraná sada **OutputSqlDataset**.

    ![Karta Kanál](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

Podle [mapování schématu v aktivitě kopírování](copy-activity-schema-and-type-mapping.md)můžete volitelně namapovat schéma zdroje na odpovídající schéma cíle.

## <a name="validate-the-pipeline"></a>Ověření kanálu
Vyberte **Ověřit** z panelu nástrojů a kanál ověřte.

Kliknutím na **kód** v pravém horním rohu uvidíte kód JSON přidružený k kanálu.

## <a name="debug-and-publish-the-pipeline"></a>Ladění a publikování kanálu
Před publikováním artefaktů (propojených služeb, datových sad a kanálu) do služby Data Factory nebo vlastního úložiště Gitu Azure Repos můžete kanál odladit.

1. K ladění kanálu vyberte na panelu nástrojů **Ladit**. Na kartě **Výstup** v dolní části okna se zobrazí stav spuštění kanálu.

1. Po úspěšném spuštění kanálu klikněte na horním panelu nástrojů na **publikovat vše**. Touto akcí publikujete vytvořené entity (datové sady a kanály) do služby Data Factory.

1. Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**. Pokud chcete zobrazit zprávy oznámení, klikněte vpravo nahoře (tlačítko zvonečku) na **Zobrazit oznámení**.

## <a name="trigger-the-pipeline-manually"></a>Ruční aktivace kanálu
V tomto kroku ručně aktivujete kanál, který jste publikovali v minulém kroku.

1. Vyberte na panelu nástrojů **Aktivační událost** a potom vyberte **Aktivovat**. Na stránce **spuštění kanálu** vyberte **OK**.  

1. Vlevo přejděte na kartu **Monitorování**. Zobrazí se stav ručně aktivovaného spuštění kanálu. Pomocí odkazů ve sloupci **název kanálu** můžete zobrazit podrobnosti o aktivitách a znovu spustit kanál.

    [![Monitorování spuštění kanálu](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png)](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png#lightbox)

1. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte odkaz **CopyPipeline** pod sloupcem **název kanálu** . V tomto příkladu je k dispozici pouze jedna aktivita, takže se v seznamu zobrazí pouze jedna položka. Pokud chcete zobrazit podrobnosti o operaci kopírování, vyberte odkaz **Podrobnosti** (ikona brýlí) ve sloupci **název aktivity** . Výběrem možnosti **všechny spuštěné kanály** v horní části přejdete zpátky k zobrazení spuštění kanálu. Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**.

    [![Monitorování spuštění aktivit](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png#lightbox)

1. Ověřte, že se do tabulky **EMP** v databázi přidaly další dva řádky.

## <a name="trigger-the-pipeline-on-a-schedule"></a>Aktivace kanálu podle plánu
V tomto kroku vytvoříte pro kanál aktivační událost plánovače. Tato aktivační událost spouští kanál podle zadaného plánu (například každou hodinu nebo každý den). Tady nastavíte, aby se aktivační událost spouštěla každou minutu až do zadané koncové hodnoty DateTime.

1. Vlevo nad kartou monitorování přejděte na kartu **Autor**.

1. Přejděte na kanál, na panelu nástrojů klikněte na **Aktivační událost** a vyberte **Nové/upravit**.

1. V dialogovém okně **Přidat triggery** vyberte **+ Nová** pro **možnost zvolit oblast aktivace** .

1. V okně **Nová aktivační událost** proveďte následující kroky:

    a. Do pole **Název** zadejte **RunEveryMinute**.

    b. Aktualizuje **počáteční datum** pro aktivační událost. Pokud je datum před aktuální hodnotou DateTime, aktivační událost se začne projevit po publikování změny. 

    c. V části **časové pásmo** vyberte rozevírací seznam.

    d. Nastavte **opakování** na **každých 1 minutu (s)**.

    e. Zaškrtněte políčko pro **zadání koncového data** a aktualizujte **konec** části na několik minut po aktuálním datu a času. Aktivační událost se aktivuje pouze po publikování změn. Pokud nakonfigurujete tuto hodnotu jenom na pár minut a nepublikujete ji, neuvidíte Trigger spustit.

    f. Pro možnost **aktivováno** vyberte **Ano**.

    například Vyberte **OK**.

    > [!IMPORTANT]
    > S každým spuštěním kanálu jsou spojené určité náklady, takže nastavte koncové datum správně.

1. Na stránce **Upravit aktivační událost** si přečtěte upozornění a pak vyberte **Uložit**. Kanál v tomto příkladu nepoužívá žádné parametry.

1. Kliknutím na **publikovat vše** publikujte změnu.

1. Vlevo přejděte na kartu **Monitorování**, kde uvidíte aktivovaná spuštění kanálu.

    [![Aktivovaná spuštění kanálu](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png#lightbox)

1. Pokud chcete přepnout ze zobrazení **spuštění kanálu** na zobrazení **spuštění aktivační události** , vyberte na levé straně okna spustit **Trigger** .

1. V seznamu se zobrazí spuštění aktivační události.

1. Ověřte, že se až do uplynutí zadaného koncového času budou do tabulky **emp** vkládat dva řádky za minutu (pro každé spuštění kanálu).

## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti Blob Storage. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu s aktivitou kopírování
> * Testovací spuštění kanálu
> * Ruční aktivace kanálu
> * Aktivace kanálu podle plánu
> * Monitorování spuštění aktivit a kanálu


Pokud se chcete dozvědět, jak kopírovat data z místního prostředí do cloudu, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Kopírování dat z místního prostředí do cloudu](tutorial-hybrid-copy-portal.md)
