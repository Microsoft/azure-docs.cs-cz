---
title: Vytvoření kanálu Azure Data Factory pomocí privátních koncových bodů
description: Tento kurz obsahuje podrobné pokyny k vytvoření datové továrny s kanálem pomocí portálu Azure Portal. Kanál používá aktivitu kopírování ke kopírování dat z úložiště objektů BLOB v Azure do databáze SQL Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.author: jingwang
ms.openlocfilehash: 9458c89922fab8450b7cb8e202491d4c47e250e8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537630"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Bezpečné kopírování dat z úložiště objektů BLOB v Azure do databáze SQL pomocí privátních koncových bodů

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto kurzu vytvoříte datovou továrnu pomocí uživatelského rozhraní služby Azure Data Factory. **Kanál v této datové továrně kopíruje data z úložiště objektů BLOB v Azure do služby Azure SQL Database (povoluje přístup pouze k vybraným sítím) pomocí privátních koncových bodů v [Azure Data Factory spravovaných Virtual Network](managed-virtual-network-private-endpoint.md).** Schéma konfigurace v tomto kurzu se vztahuje na kopírování z úložiště dat založeného na souborech do relačního úložiště dat. Seznam úložišť dat, která jsou podporovaná jako zdroje a jímky, najdete v tabulce [podporovaných úložišť dat](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).

> [!NOTE]
>
> - Pokud se službou Data Factory teprve začínáte, přečtěte si téma [Úvod do Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction).

V tomto kurzu budete provádět následující kroky:

> * Vytvoření datové továrny
> * Vytvoření kanálu s aktivitou kopírování


## <a name="prerequisites"></a>Předpoklady
* **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet služby Azure Storage**. Blob Storage použijete jako *zdrojové* úložiště dat. Pokud účet úložiště nemáte, přečtěte si téma [Vytvoření účtu služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal), kde najdete postup jeho vytvoření. **Ujistěte se, že účet úložiště povoluje přístup jenom z vybraných sítí.** 
* **Azure SQL Database**. Tuto databázi použijete jako úložiště dat *jímky*. Pokud službu Azure SQL Database nemáte, přečtěte si téma [Vytvoření databáze SQL](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) , kde najdete kroky pro její vytvoření. **Ujistěte se, že účet Azure SQL Database povoluje přístup jenom z vybraných sítí.** 

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

1. Použitím následujícího skriptu SQL si v databázi SQL Database vytvořte tabulku **emp**:

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

1. Povolte službám Azure přístup k SQL Serveru. Zkontrolujte, že je nastavení **Povolit přístup ke službám Azure** pro váš SQL Server **ZAPNUTÉ**, aby mohla služba Data Factory na tento SQL Server zapisovat data. Pokud chcete toto nastavení ověřit a zapnout, přejděte na Azure SQL Server > přehled > nastavení brány firewall serveru> nastavte možnost **Povolit přístup ke službám Azure** na **zapnuto**.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
V tomto kroku vytvoříte datovou továrnu a spustíte uživatelské rozhraní služby Data Factory, ve kterém v této datové továrně vytvoříte kanál.

1. Otevřete **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.


2. V nabídce vlevo vyberte **vytvořit**  >  **Analytics**  >  **Data Factory**analýzy prostředků.

3. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**.

   Název objektu pro vytváření dat Azure musí být *globálně jedinečný*. Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. (například yournameADFTutorialDataFactory). Pravidla pro pojmenovávání artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](https://docs.microsoft.com/azure/data-factory/naming-rules).

4. Vyberte **předplatné** Azure, v rámci kterého chcete datovou továrnu vytvořit.

5. U položky **Skupina prostředků** proveďte jeden z následujících kroků:

    a. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

    b. Vyberte **vytvořit novou**a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/overview). 

6. Jako **Verzi** vyberte **V2**.

7. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používaná datovou továrnou můžou být v jiných oblastech.


8. Vyberte **Vytvořit**.


9. Po dokončení vytváření se zobrazí oznámení v centru oznámení. Vyberte **Přejít k prostředku** a přejděte na stránku Datová továrna.

10. Vyberte **Vytvořit a monitorovat**. Na samostatné kartě se spustí uživatelské rozhraní služby Data Factory.

## <a name="create-an-azure-integration-runtime-in-adf-managed-virtual-network"></a>Vytvoření Azure Integration Runtime ve spravovaném ADF Virtual Network
V tomto kroku vytvoříte Azure Integration Runtime a povolíte spravované Virtual Network.

1. V portálu ADF přejděte na **Spravovat centrum** a klikněte na **Nový** a vytvořte nový Azure Integration runtime.
   ![Vytvořit nový Azure Integration Runtime](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
2. Vyberte, chcete-li vytvořit službu Azure * * Integration Runtime.
   ![Nový Azure Integration Runtime](./media/tutorial-copy-data-portal-private/azure-ir.png)
3. Povolte **Virtual Network**.
   ![Nový Azure Integration Runtime](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
4. Vyberte **Vytvořit**.

## <a name="create-a-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte v datové továrně kanál s aktivitou kopírování. Aktivita kopírování kopíruje data z úložiště Blob Storage do databáze SQL Database. V [kurzu Rychlý start](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) jste vytvořili kanál pomocí tohoto postupu:

1. Vytvoření propojené služby
1. Vytvoření vstupní a výstupní datové sady
1. Vytvoření kanálu

V tomto kurzu začnete vytvořením kanálu. Potom vytvoříte propojené služby a datové sady, které budete potřebovat ke konfiguraci kanálu.

1. Na stránce **Začínáme** vyberte **Vytvořit kanál**.

   ![Vytvoření kanálu](./media/doc-common-process/get-started-page.png)
1. V podokně **vlastnosti** kanálu zadejte **CopyPipeline** pro **název** kanálu.

1. V poli nástroje **aktivity** rozbalte kategorii **přesunout a transformovat** a přetáhněte **kopírování dat** aktivity z panelu nástrojů na plochu návrháře kanálu. Jako **Název** zadejte **CopyFromBlobToSql**.

    ![Aktivita kopírování](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Konfigurace zdroje

>[!TIP]
>V tomto kurzu použijete *klíč účtu* jako typ ověřování pro zdrojové úložiště dat, ale v případě potřeby můžete vybrat jiné podporované metody ověřování: *URI SAS*,*instanční objekt* a *spravovaná identita* . Podrobnosti najdete v odpovídajících částech [tohoto článku](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#linked-service-properties) .
>K bezpečnému ukládání tajných kódů pro úložiště dat je také vhodné použít Azure Key Vault. Podrobnější ilustrace najdete v [tomto článku](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) .

#### <a name="create-source-dataset-and-linked-service"></a>Vytvoření zdrojové datové sady a propojené služby

1. Pokud chcete vytvořit zdrojovou datovou sadu, přejít na kartu **zdroj** . Vyberte **+ Nová** .

1. V dialogovém okně **Nová datová sada** vyberte **Azure Blob Storage**a pak vyberte **pokračovat**. Zdrojová data jsou v úložišti Blob Storage, takže jako zdrojovou datovou sadu vyberete **Azure Blob Storage**.

1. V dialogovém okně **Vybrat formát** zvolte typ formátu dat a pak vyberte **pokračovat**.

1. V dialogovém okně **nastavit vlastnosti** zadejte **SourceBlobDataset** do pole název. Zaškrtněte políčko pro **první řádek jako záhlaví**. V textovém poli **propojená služba** vyberte **+ Nová**.

1. V dialogovém okně **Nová propojená služba (Azure Blob Storage)** jako název zadejte **AzureStorageLinkedService** a v seznamu **název účtu úložiště** vyberte svůj účet úložiště. 

1. Ujistěte se, že jste povolili **interaktivní vytváření**. Povolení může trvat přibližně 1 minutu.

    ![Interaktivní vytváření](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Vyberte **Test připojení**, pokud účet úložiště povoluje přístup jenom z vybrané sítě a vyžaduje Azure Data Factory k vytvoření privátního koncového bodu, který by měl být před jeho použitím schválen. V chybové zprávě byste měli vidět odkaz pro vytvoření **privátního koncového bodu** , pomocí kterého můžete vytvořit spravovaný privátní koncový bod. *Alternativou je přejít přímo na kartu Správa a postupovat podle pokynů v [následující části](#create-a-managed-private-endpoint) a vytvořit spravovaný privátní koncový bod.*
> [!NOTE]
> Karta spravovat nemusí být k dispozici pro všechny instance služby Data Factory. Pokud ho nevidíte, můžete i nadále přistupovat k privátním koncovým bodům prostřednictvím karty**Autor**– >**připojení**-->**privátního koncového bodu**.
1. Nechejte dialogové okno otevřené a pak klikněte na účet úložiště, který jste vybrali výše.

1. Postupujte podle pokynů v [této části](#approval-of-a-private-link-in-storage-account) pro schválení privátního odkazu.

1. Vraťte se do dialogového okna. **Otestujte připojení** znovu a vyberte **vytvořit** a nasaďte propojenou službu.

1. Po vytvoření propojené služby přejdete zpátky na stránku **Vlastnosti sady** . Vedle pole **Cesta k souboru** vyberte **Procházet**.

1. Přejděte do složky **adftutorial/Input** , vyberte soubor **emp.txt** a pak vyberte **OK**.

1. Vyberte **OK**. Automaticky přejde na stránku kanálu. Na kartě **zdroj** potvrďte, že je vybraná možnost **SourceBlobDataset** . Pokud se na této stránce chcete podívat na náhled dat, vyberte **Náhled dat**.

    ![Zdrojová datová sada](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Vytvoření spravovaného privátního koncového bodu

Pokud jste nepoužili odkaz na hypertextový odkaz při testování výše uvedeného připojení, použijte následující cestu. Teď je potřeba vytvořit spravovaný privátní koncový bod, ke kterému se připojíte přes propojenou službu vytvořenou výše.

1. Přejít na kartu spravovat.
> [!NOTE]
> Karta spravovat nemusí být k dispozici pro všechny instance služby Data Factory. Pokud ho nevidíte, můžete i nadále přistupovat k privátním koncovým bodům prostřednictvím karty**Autor**– >**připojení**-->**privátního koncového bodu**.

1. Přejít do oddílu spravované privátní koncové body.

1. V části spravované privátní koncové body vyberte **+ Nový** .

    ![Nový spravovaný privátní koncový bod](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. V seznamu Vyberte dlaždici Azure Blob Storage a vyberte **pokračovat**.

1. Zadejte název účtu úložiště, který jste vytvořili výše.

1. Vyberte **Vytvořit**.

1. Měli byste vidět, že se po chvíli čekání na vytvoření privátního odkazu vyžaduje schválení.

1. Vyberte privátní koncový bod, který jste vytvořili výše. Zobrazí se hypertextový odkaz, který vás provede schválením privátního koncového bodu na úrovni účtu úložiště.

    ![Správa privátního koncového bodu](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-storage-account"></a>Schválení privátního odkazu v účtu úložiště
1. V účtu úložiště v části Nastavení klikněte na **připojení privátního koncového bodu** .

1. Zaznačte si privátní koncový bod, který jste vytvořili, a vyberte **schválit**.

    ![Schválit soukromý koncový bod](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Přidejte popis a klikněte na **Ano** .
1. Vraťte se do části **spravované soukromé koncové body** na kartě **Spravovat** v Azure Data Factory.
1. Získání schválení privátního koncového bodu v Azure Data Factory uživatelském rozhraní by mělo trvat přibližně 1-2 minut.


### <a name="configure-sink"></a>Konfigurace jímky
>[!TIP]
>V tomto kurzu použijete *ověřování SQL* jako typ ověřování pro úložiště dat jímky, ale v případě potřeby můžete vybrat jiné podporované metody ověřování: *instanční objekt* a *spravovaná identita* . Podrobnosti najdete v odpovídajících částech [tohoto článku](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties) .
>K bezpečnému ukládání tajných kódů pro úložiště dat je také vhodné použít Azure Key Vault. Podrobnější ilustrace najdete v [tomto článku](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) .

#### <a name="create-sink-dataset-and-linked-service"></a>Vytvoření datové sady jímky a propojené služby
1. Přejděte na kartu **Jímka**, vyberte **+ Nová** a vytvořte datovou sadu jímky.

1. V dialogovém okně **Nová datová sada** zadejte "SQL" do vyhledávacího pole, chcete-li filtrovat konektory, vyberte **Azure SQL Database**a pak vyberte **pokračovat**. V tomto kurzu zkopírujte data do databáze SQL Database.

1. V dialogovém okně **nastavit vlastnosti** zadejte **OutputSqlDataset** do pole název. V rozevíracím seznamu **propojená služba** vyberte **+ Nová**. Datová sada musí být přidružená k propojené službě. Propojená služba obsahuje připojovací řetězec, který služba Data Factory používá pro připojení k databázi SQL Database za běhu. Datová sada určuje kontejner, složku a soubor (volitelné), do kterého se data kopírují.

1. V dialogovém okně **Nová propojená služba (Azure SQL Database)** proveďte následující kroky:

    1. Do pole **Název** zadejte **AzureSqlDatabaseLinkedService**.
    1. V rozevíracím seznamu **Název serveru** vyberte příslušný název instance SQL Serveru.
    1. Ujistěte se, že jste povolili **interaktivní vytváření**.
    1. V rozevíracím seznamu **Název databáze** vyberte svoji databázi SQL Database.
    1. Do pole **Uživatelské jméno** zadejte jméno uživatele.
    1. Do pole **Heslo** zadejte heslo pro tohoto uživatele.
    1. Vyberte **Test připojení**. Mělo by být neúspěšné, protože SQL Server umožňuje přístup pouze z vybraných sítí a vyžaduje Azure Data Factory k vytvoření privátního koncového bodu, který by měl být před použitím schválen. V chybové zprávě byste měli vidět odkaz pro vytvoření **privátního koncového bodu** , pomocí kterého můžete vytvořit spravovaný privátní koncový bod. *Alternativou je přejít přímo na kartu Správa a postupovat podle pokynů v následující části a vytvořit spravovaný privátní koncový bod.*
    1. Nechejte dialogové okno otevřené a pak na SQL serveru, který jste vybrali výše.    
    1. Postupujte podle pokynů v [této části](#approval-of-a-private-link-in-sql-server) pro schválení privátního odkazu.
    1. Vraťte se do dialogového okna. **Otestujte připojení** znovu a vyberte **vytvořit** a nasaďte propojenou službu.

1. Automaticky přejde do dialogového okna **nastavit vlastnosti** . V části **Tabulka** vyberte **[dbo].[emp]**. Pak vyberte **OK**.

1. Přejděte na kartu s kanálem a zkontrolujte, že je v rozevíracím seznamu **Datová sada jímky** vybraná sada **OutputSqlDataset**.

    ![Karta Kanál](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)       

Podle [mapování schématu v aktivitě kopírování](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping)můžete volitelně namapovat schéma zdroje na odpovídající schéma cíle.

#### <a name="create-a-managed-private-endpoint"></a>Vytvoření spravovaného privátního koncového bodu

Pokud jste nepoužili odkaz na hypertextový odkaz při testování výše uvedeného připojení, použijte následující cestu. Teď je potřeba vytvořit spravovaný privátní koncový bod, ke kterému se připojíte přes propojenou službu vytvořenou výše.

1. Přejít na kartu spravovat.
1. Přejít do oddílu spravované privátní koncové body.
1. V části spravované privátní koncové body vyberte **+ Nový** .

    ![Nový spravovaný privátní koncový bod](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. V seznamu Vyberte dlaždici Azure SQL Database a vyberte **pokračovat**.
1. Zadejte název SQL serveru, který jste vybrali výše.
1. Vyberte **Vytvořit**.
1. Měli byste vidět, že se po chvíli čekání na vytvoření privátního odkazu vyžaduje schválení.
1. Vyberte privátní koncový bod, který jste vytvořili výše. Zobrazí se hypertextový odkaz, který vás provede schválením privátního koncového bodu na úrovni serveru SQL Server.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Schválení privátního odkazu v SQL serveru
1. V systému SQL Server v části nastavení v části Nastavení klikněte na **připojení privátního koncového bodu** .
1. Zaznačte si privátní koncový bod, který jste vytvořili, a vyberte **schválit**.
1. Přidejte popis a klikněte na **Ano**.
1. Vraťte se do části **spravované soukromé koncové body** na kartě **Spravovat** v Azure Data Factory.
1. Pro získání souhlasu s vaším soukromým koncovým bodem by mělo trvat přibližně 1-2 minut.

#### <a name="debug-and-publish-the-pipeline"></a>Ladění a publikování kanálu

Před publikováním artefaktů (propojených služeb, datových sad a kanálu) do služby Data Factory nebo vlastního úložiště Gitu Azure Repos můžete kanál odladit.

1. K ladění kanálu vyberte na panelu nástrojů **Ladit**. Na kartě **Výstup** v dolní části okna se zobrazí stav spuštění kanálu.
2. Po úspěšném spuštění kanálu klikněte na horním panelu nástrojů na **publikovat vše**. Touto akcí publikujete vytvořené entity (datové sady a kanály) do služby Data Factory.
3. Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**. Chcete-li zobrazit oznamovací zprávy, klikněte na tlačítko **Zobrazit oznámení** vpravo nahoře (tlačítko zvonku).


#### <a name="summary"></a>Souhrn
Kanál v této ukázce kopíruje data z úložiště objektů blob do služby Azure SQL DB pomocí privátního koncového bodu ve spravovaném Virtual Network. Naučili jste se:

> * Vytvoření datové továrny
> * Vytvoření kanálu s aktivitou kopírování

