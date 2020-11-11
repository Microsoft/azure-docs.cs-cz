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
ms.openlocfilehash: 4f5d691ef99ac4647d2031d6588d0b3922edd8cf
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94505984"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Bezpečné kopírování dat z úložiště objektů BLOB v Azure do databáze SQL pomocí privátních koncových bodů

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto kurzu vytvoříte datovou továrnu pomocí uživatelského rozhraní služby Azure Data Factory. *Kanál v této datové továrně kopíruje data z úložiště objektů BLOB v Azure do Azure SQL Database (umožňuje přístup jenom k vybraným sítím) pomocí privátních koncových bodů v [Azure Data Factory spravovaných Virtual Network](managed-virtual-network-private-endpoint.md).* Schéma konfigurace v tomto kurzu se vztahuje na kopírování z úložiště dat založeného na souborech do relačního úložiště dat. Seznam úložišť dat podporovaných jako zdroje a jímky najdete v tabulce [podporovaná úložiště dat a formáty](./copy-activity-overview.md) .

> [!NOTE]
> Pokud se službou Data Factory teprve začínáte, přečtěte si téma [Úvod do Azure Data Factory](./introduction.md).

V tomto kurzu provedete následující kroky:

* Vytvoření datové továrny
* Vytvoření kanálu s aktivitou kopírování


## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet služby Azure Storage**. Blob Storage použijete jako *zdrojové* úložiště dat. Pokud účet úložiště nemáte, přečtěte si téma [Vytvoření účtu služby Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal), kde najdete postup jeho vytvoření. *Ujistěte se, že účet úložiště povoluje přístup jenom z vybraných sítí.* 
* **Azure SQL Database**. Tuto databázi použijete jako úložiště dat *jímky*. Pokud službu Azure SQL Database nemáte, přečtěte si téma [Vytvoření databáze SQL](../azure-sql/database/single-database-create-quickstart.md) , kde najdete kroky pro její vytvoření. *Ujistěte se, že účet SQL Database povoluje přístup pouze z vybraných sítí.* 

### <a name="create-a-blob-and-a-sql-table"></a>Vytvoření objektu blob a tabulky SQL

Teď Připravte úložiště objektů BLOB a SQL Database pro kurz provedením následujících kroků.

#### <a name="create-a-source-blob"></a>Vytvoření zdrojového objektu blob

1. Otevřete Poznámkový blok. Zkopírujte následující text a uložte si ho na disk jako soubor **emp.txt** :

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. V úložišti objektů BLOB vytvořte kontejner s názvem **adftutorial** . V tomto kontejneru vytvořte složku **input**. Pak do složky **input** nahrajte soubor **emp.txt**. K provedení těchto úloh použijte Azure Portal nebo nástroj, jako je například [Průzkumník služby Azure Storage](https://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Vytvoření tabulky SQL jímky

Použitím následujícího skriptu SQL si v databázi SQL Database vytvořte tabulku **emp** :

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

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
V tomto kroku vytvoříte datovou továrnu a spustíte uživatelské rozhraní služby Data Factory, ve kterém v této datové továrně vytvoříte kanál.

1. Otevřete Microsoft Edge nebo Google Chrome. V současné době podporuje Data Factory uživatelské rozhraní pouze webové prohlížeče Microsoft Edge a Google Chrome.

1. V nabídce vlevo vyberte **vytvořit**  >  **Analytics**  >  **Data Factory** analýzy prostředků.

1. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**.

   Název objektu pro vytváření dat Azure musí být *globálně jedinečný*. Pokud se zobrazí chybová zpráva s názvem hodnota, zadejte jiný název objektu pro vytváření dat (například yournameADFTutorialDataFactory). Pravidla pro pojmenovávání artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](./naming-rules.md).

1. Vyberte **předplatné** Azure, v rámci kterého chcete datovou továrnu vytvořit.

1. U položky **Skupina prostředků** proveďte jeden z následujících kroků:

    - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.
    - Vyberte **vytvořit novou** a zadejte název skupiny prostředků. 
     
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md). 

1. Jako **Verzi** vyberte **V2**.

1. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používaná datovou továrnou můžou být v jiných oblastech.

1. Vyberte **Vytvořit**.

1. Po dokončení vytváření se zobrazí oznámení v centru oznámení. Vyberte **Přejít k prostředku** a přejít na stránku **Data Factory** .

1. Vyberte **Vytvořit a monitorovat**. Na samostatné kartě se spustí uživatelské rozhraní služby Data Factory.

## <a name="create-an-azure-integration-runtime-in-data-factory-managed-virtual-network"></a>Vytvoření prostředí Azure Integration runtime v Data Factory spravovaném Virtual Network
V tomto kroku vytvoříte prostředí Azure Integration runtime a povolíte Data Factory spravované Virtual Network.

1. Na portálu Data Factory klikněte na **Správa** a vyberte **Nový** a vytvořte nový prostředí Azure Integration runtime.

   ![Snímek obrazovky, který ukazuje vytvoření nového prostředí Azure Integration runtime.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Vyberte Vytvoření prostředí **Azure** Integration runtime.

   ![Snímek obrazovky, který ukazuje nový prostředí Azure Integration runtime.](./media/tutorial-copy-data-portal-private/azure-ir.png)
1. V části **Konfigurace virtuální sítě (Preview)** vyberte **Povolit**.

   ![Snímek obrazovky, který ukazuje povolení nového prostředí Azure Integration runtime.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
1. Vyberte **Vytvořit**.

## <a name="create-a-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte v datové továrně kanál s aktivitou kopírování. Aktivita kopírování kopíruje data z úložiště Blob Storage do databáze SQL Database. V [kurzu Rychlý start](./quickstart-create-data-factory-portal.md) jste vytvořili kanál pomocí tohoto postupu:

1. Vytvoření propojené služby
1. Vytvoření vstupní a výstupní datové sady
1. Vytvoření kanálu

V tomto kurzu začnete vytvořením kanálu. Potom vytvoříte propojené služby a datové sady, které budete potřebovat ke konfiguraci kanálu.

1. Na stránce **Začínáme** vyberte **Vytvořit kanál**.

   ![Snímek obrazovky, který ukazuje vytvoření kanálu.](./media/doc-common-process/get-started-page.png)
1. V podokně vlastnosti kanálu zadejte **CopyPipeline** pro název kanálu.

1. V poli nástroje **aktivity** rozbalte kategorii **přesunout a transformovat** a přetáhněte aktivitu **Kopírovat data** z panelu nástrojů na plochu návrháře kanálu. Jako název zadejte **CopyFromBlobToSql** .

    ![Snímek obrazovky zobrazující aktivitu kopírování](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-a-source"></a>Konfigurace zdroje

>[!TIP]
>V tomto kurzu použijete **klíč účtu** jako typ ověřování pro zdrojové úložiště dat. V případě potřeby můžete také zvolit jiné podporované metody ověřování, jako je **URI SAS** , **instanční objekt** a **spravovaná identita** . Další informace najdete v odpovídajících částech v tématu [kopírování a transformace dat v úložišti objektů BLOB v Azure pomocí Azure Data Factory](./connector-azure-blob-storage.md#linked-service-properties).
>
>K bezpečnému ukládání tajných kódů pro úložiště dat doporučujeme také použití Azure Key Vault. Další informace a ilustrace najdete v tématu [uložení přihlašovacích údajů v Azure Key Vault](./store-credentials-in-key-vault.md).

#### <a name="create-a-source-dataset-and-linked-service"></a>Vytvoření zdrojové datové sady a propojené služby

1. Přejít na kartu **zdroj** . Vyberte **+ Nová** a vytvořte zdrojovou datovou sadu.

1. V dialogovém okně **Nová datová sada** vyberte **Azure Blob Storage** a pak vyberte **pokračovat**. Zdrojová data jsou v úložišti Blob Storage, takže jako zdrojovou datovou sadu vyberete **Azure Blob Storage**.

1. V dialogovém okně **Vybrat formát** vyberte typ formátu dat a pak vyberte **pokračovat**.

1. V dialogovém okně **nastavit vlastnosti** zadejte **SourceBlobDataset** do pole **název**. Zaškrtněte políčko pro **první řádek jako záhlaví**. V textovém poli **propojená služba** vyberte **+ Nová**.

1. V dialogovém okně **Nová propojená služba (Azure Blob Storage)** zadejte **AzureStorageLinkedService** jako **název** a v seznamu **název účtu úložiště** vyberte svůj účet úložiště. 

1. Ujistěte se, že jste povolili **interaktivní vytváření**. Povolení může trvat přibližně jednu minutu.

    ![Snímek obrazovky zobrazující interaktivní vytváření obsahu](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Vyberte **Test připojení**. Měla by selhat, pokud účet úložiště povoluje přístup pouze z **vybraných sítí** a vyžaduje Data Factory k vytvoření privátního koncového bodu, který by měl být před jeho použitím schválen. V chybové zprávě byste měli vidět odkaz pro vytvoření privátního koncového bodu, pomocí kterého můžete vytvořit spravovaný privátní koncový bod. Alternativou je přejít přímo na kartu **Správa** a postupovat podle pokynů v [Další části](#create-a-managed-private-endpoint) a vytvořit spravovaný privátní koncový bod.

   > [!NOTE]
   > Karta **Spravovat** nemusí být k dispozici pro všechny instance služby Data Factory. Pokud ho nevidíte, můžete získat přístup k privátním koncovým bodům výběrem možnosti **vytvořit**  >  **připojení**  >  **soukromý koncový bod**.
1. Nechejte dialogové okno otevřené a pak na svém účtu úložiště.

1. Postupujte podle pokynů v [této části](#approval-of-a-private-link-in-a-storage-account) pro schválení privátního odkazu.

1. Vraťte se do dialogového okna. Znovu vyberte **Test připojení** a vyberte **vytvořit** a nasaďte propojenou službu.

1. Po vytvoření propojené služby se tato propojená služba vrátí zpět na stránku **vlastností set** . Vedle pole **Cesta k souboru** vyberte **Procházet**.

1. Přejít do složky **adftutorial/Input** , vyberte soubor **emp.txt** a pak vyberte **OK**.

1. Vyberte **OK**. Automaticky přejde na stránku kanálu. Na kartě **zdroj** potvrďte, že je vybraná možnost **SourceBlobDataset** . Pokud se na této stránce chcete podívat na náhled dat, vyberte **Náhled dat**.

    ![Snímek obrazovky zobrazující zdrojovou datovou sadu](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Vytvoření spravovaného privátního koncového bodu

Pokud jste nevybrali hypertextový odkaz při testování připojení, postupujte podle cesty. Teď je potřeba vytvořit spravovaný privátní koncový bod, ke kterému se připojíte, abyste se připojili k propojené službě, kterou jste vytvořili.

1. Přejít na kartu **Spravovat** .

   > [!NOTE]
   > Karta **Spravovat** nemusí být k dispozici pro všechny instance Data Factory. Pokud ho nevidíte, můžete získat přístup k privátním koncovým bodům výběrem možnosti **vytvořit**  >  **připojení**  >  **soukromý koncový bod**.

1. Přejít do oddílu **spravované privátní koncové body** .

1. V části **spravované privátní koncové body** vyberte **+ Nový** .

    ![Snímek obrazovky, který zobrazuje nové tlačítko spravované soukromé koncové body.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. V seznamu Vyberte dlaždici **Azure Blob Storage** a vyberte **pokračovat**.

1. Zadejte název účtu úložiště, který jste vytvořili.

1. Vyberte **Vytvořit**.

1. Po několika sekundách byste měli vidět, že vytvoření privátního odkazu vyžaduje schválení.

1. Vyberte privátní koncový bod, který jste vytvořili. Zobrazí se hypertextový odkaz, který vás provede schválením privátního koncového bodu na úrovni účtu úložiště.

    ![Snímek obrazovky, který zobrazuje podokno spravovaného privátního koncového bodu.](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Schválení privátního odkazu v účtu úložiště
1. V účtu úložiště v části **Nastavení** použijte **připojení privátního koncového bodu** .

1. Zaškrtněte políčko u privátního koncového bodu, který jste vytvořili, a vyberte **schválit**.

    ![Snímek obrazovky, který zobrazuje tlačítko pro schválení privátního koncového bodu.](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Přidejte popis a vyberte **Ano**.
1. Vraťte se do části **spravované soukromé koncové body** na kartě **Spravovat** v Data Factory.
1. Po jedné nebo dvou minutách byste měli vidět, že se schválení privátního koncového bodu zobrazí v uživatelském rozhraní Data Factory.


### <a name="configure-a-sink"></a>Konfigurace jímky
>[!TIP]
>V tomto kurzu použijete **ověřování SQL** jako typ ověřování pro úložiště dat jímky. V případě potřeby můžete také zvolit jiné podporované metody ověřování, například **instanční objekt** a **spravovanou identitu** . Další informace najdete v tématu odpovídající části v tématu [kopírování a transformace dat v Azure SQL Database pomocí Azure Data Factory](./connector-azure-sql-database.md#linked-service-properties).
>
>K bezpečnému ukládání tajných kódů pro úložiště dat doporučujeme také použití Azure Key Vault. Další informace a ilustrace najdete v tématu [uložení přihlašovacích údajů v Azure Key Vault](./store-credentials-in-key-vault.md).

#### <a name="create-a-sink-dataset-and-linked-service"></a>Vytvoření datové sady jímky a propojené služby
1. Přejděte na kartu **Jímka** , vyberte **+ Nová** a vytvořte datovou sadu jímky.

1. V dialogovém okně **Nová datová sada** zadejte do vyhledávacího pole **SQL** a vyfiltrujte konektory. Vyberte **Azure SQL Database** a pak vyberte **pokračovat**. V tomto kurzu zkopírujte data do databáze SQL Database.

1. V dialogovém okně **nastavit vlastnosti** zadejte **OutputSqlDataset** do pole **název**. V rozevíracím seznamu **propojená služba** vyberte **+ Nová**. Datová sada musí být přidružená k propojené službě. Propojená služba obsahuje připojovací řetězec, který služba Data Factory používá pro připojení k databázi SQL Database za běhu. Datová sada určuje kontejner, složku a soubor (volitelné), do kterého se data kopírují.

1. V dialogovém okně **Nová propojená služba (Azure SQL Database)** proveďte následující kroky:

    1. Do pole **Název** zadejte **AzureSqlDatabaseLinkedService**.
    1. V rozevíracím seznamu **Název serveru** vyberte příslušný název instance SQL Serveru.
    1. Ujistěte se, že jste povolili **interaktivní vytváření**.
    1. V rozevíracím seznamu **Název databáze** vyberte svoji databázi SQL Database.
    1. Do pole **Uživatelské jméno** zadejte jméno uživatele.
    1. Do pole **Heslo** zadejte heslo pro tohoto uživatele.
    1. Vyberte **Test připojení**. Mělo by být neúspěšné, protože systém SQL Server umožňuje přístup pouze z **vybraných sítí** a vyžaduje Data Factory k vytvoření privátního koncového bodu, který by měl být před použitím schválen. V chybové zprávě byste měli vidět odkaz pro vytvoření privátního koncového bodu, pomocí kterého můžete vytvořit spravovaný privátní koncový bod. Alternativou je přejít přímo na kartu **Správa** a postupovat podle pokynů v další části a vytvořit spravovaný privátní koncový bod.
    1. Nechejte dialogové okno otevřené a pak klikněte na vybraný SQL Server.
    1. Postupujte podle pokynů v [této části](#approval-of-a-private-link-in-sql-server) pro schválení privátního odkazu.
    1. Vraťte se do dialogového okna. Znovu vyberte **Test připojení** a vyberte **vytvořit** a nasaďte propojenou službu.

1. Automaticky přejde do dialogového okna **nastavit vlastnosti** . V části **Tabulka** vyberte **[dbo].[emp]**. Pak vyberte **OK**.

1. V kanálu klikněte na kartu a v **datové sadě jímky** potvrďte, že je vybraná možnost **OutputSqlDataset** .

    ![Snímek obrazovky zobrazující kartu kanálu](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)

Podle [mapování schématu v aktivitě kopírování](./copy-activity-schema-and-type-mapping.md)můžete volitelně namapovat schéma zdroje na odpovídající schéma cílového umístění.

#### <a name="create-a-managed-private-endpoint"></a>Vytvoření spravovaného privátního koncového bodu

Pokud jste nevybrali hypertextový odkaz při testování připojení, postupujte podle cesty. Teď je potřeba vytvořit spravovaný privátní koncový bod, ke kterému se připojíte, abyste se připojili k propojené službě, kterou jste vytvořili.

1. Přejít na kartu **Spravovat** .
1. Přejít do oddílu **spravované privátní koncové body** .
1. V části **spravované privátní koncové body** vyberte **+ Nový** .

    ![Snímek obrazovky, který zobrazuje nové tlačítko spravované soukromé koncové body.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. V seznamu Vyberte dlaždici **Azure SQL Database** a vyberte **pokračovat**.
1. Zadejte název serveru SQL Server, který jste vybrali.
1. Vyberte **Vytvořit**.
1. Po několika sekundách byste měli vidět, že vytvoření privátního odkazu vyžaduje schválení.
1. Vyberte privátní koncový bod, který jste vytvořili. Zobrazí se hypertextový odkaz, který vás provede schválením privátního koncového bodu na úrovni serveru SQL Server.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Schválení privátního odkazu v SQL Server
1. V systému SQL Server, v části **Nastavení** klikněte na **připojení privátního koncového bodu** .
1. Zaškrtněte políčko u privátního koncového bodu, který jste vytvořili, a vyberte **schválit**.
1. Přidejte popis a vyberte **Ano**.
1. Vraťte se do části **spravované soukromé koncové body** na kartě **Spravovat** v Data Factory.
1. Pro zobrazení souhlasu s Vaším privátním koncovým bodem by mělo trvat jednu nebo dvě minuty.

#### <a name="debug-and-publish-the-pipeline"></a>Ladění a publikování kanálu

Před publikováním artefaktů (propojených služeb, datových sad a kanálu) do služby Data Factory nebo vlastního úložiště Gitu Azure Repos můžete kanál odladit.

1. K ladění kanálu vyberte na panelu nástrojů **Ladit**. Na kartě **Výstup** v dolní části okna se zobrazí stav spuštění kanálu.
1. Po úspěšném spuštění kanálu klikněte na horním panelu nástrojů na **publikovat vše**. Tato akce publikuje entity (datové sady a kanály), které jste vytvořili pro Data Factory.
1. Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**. Chcete-li zobrazit zprávy s oznámením, vyberte možnost **Zobrazit oznámení** v pravém horním rohu (tlačítko zvonku).


#### <a name="summary"></a>Shrnutí
Kanál v této ukázce kopíruje data z úložiště objektů blob do SQL Database pomocí privátních koncových bodů v Data Factory spravovaných Virtual Network. Naučili jste se:

* Vytvoření datové továrny
* Vytvoření kanálu s aktivitou kopírování