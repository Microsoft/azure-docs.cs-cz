---
title: Kopírování dat z úložiště objektů BLOB v Azure do SQL pomocí nástroje pro Kopírování dat
description: Vytvořte Azure Data Factory a pak pomocí nástroje Kopírování dat zkopírujte data z úložiště objektů BLOB v Azure do SQL Database.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 20db863f1e2ae66acada928687b0bcd572f729f9
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103534"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Kopírování dat z úložiště objektů BLOB v Azure do SQL Database pomocí nástroje Kopírování dat

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuální verze](tutorial-copy-data-tool.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V tomto kurzu pomocí webu Azure Portal vytvoříte datovou továrnu. Pak použijete nástroj Kopírování dat k vytvoření kanálu, který kopíruje data ze služby Azure Blob Storage do SQL Database.

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

V tomto kurzu budete provádět následující kroky:
> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet Azure Storage**: jako _zdrojové_ úložiště dat použijte úložiště objektů BLOB. Pokud nemáte účet Azure Storage, přečtěte si pokyny v tématu [Vytvoření účtu úložiště](../storage/common/storage-account-create.md).
* **Azure SQL Database**: jako úložiště dat _jímky_ použijte SQL Database. Pokud nemáte SQL Database, přečtěte si pokyny v tématu [vytvoření SQL Database](../azure-sql/database/single-database-create-quickstart.md).

### <a name="create-a-blob-and-a-sql-table"></a>Vytvoření objektu blob a tabulky SQL

Provedením těchto kroků Připravte úložiště objektů BLOB a SQL Database pro tento kurz.

#### <a name="create-a-source-blob"></a>Vytvoření zdrojového objektu blob

1. Spusťte **Poznámkový blok**. Zkopírujte následující text a uložte ho na disk do souboru **inputEmp.txt**:

   ```text
   FirstName|LastName
   John|Doe
   Jane|Doe
   ```

1. Vytvořte kontejner **adfv2tutorial** a nahrajte do něj soubor inputEmp.txt. K provedení těchto úloh můžete použít Azure Portal nebo různé nástroje, jako je [Průzkumník služby Azure Storage](https://storageexplorer.com/) .

#### <a name="create-a-sink-sql-table"></a>Vytvoření tabulky SQL jímky

1. Pomocí následujícího skriptu SQL vytvořte v SQL Database tabulku s názvem **dbo. EMP** :

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

2. Povolte službám Azure přístup k SQL Serveru. Ověřte, že nastavení **Povolit službám a prostředkům Azure pro přístup k tomuto serveru** je povolené pro váš server, na kterém běží SQL Database. Toto nastavení umožní službě Data Factory zapisovat data do vaší instance databáze. Pokud chcete toto nastavení ověřit a zapnout, přejděte na logické SQL Server > zabezpečení > brány firewall a virtuální sítě > nastavte možnost **Povolit službám a prostředkům Azure přístup k tomuto serveru** na **zapnuto**.

   > [!NOTE]
   > Možnost **Povolit službám a prostředkům Azure přístup k tomuto serveru** umožňuje přístup k síti SQL Server z jakéhokoli prostředku Azure, nikoli jenom těch v rámci vašeho předplatného. Další informace najdete v tématu [pravidla brány firewall pro Azure SQL Server](../azure-sql/database/firewall-configure.md). Místo toho můžete pomocí [soukromých koncových bodů](../private-link/private-endpoint-overview.md) připojit se ke službám Azure PaaS bez použití veřejných IP adres.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte vytvořit data Factory **pro**  >  **integraci** prostředků  >  :

   ![Vytvoření nové datové továrny](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**.

   Název datové továrny musí být _globálně jedinečný_. Možná se zobrazí následující chybová zpráva:

   :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nová chybová zpráva objektu pro vytváření dat pro duplicitní název.":::

   Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. Použijte například název _**vaše_jméno**_**ADFTutorialDataFactory**. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).

1. Vyberte **předplatné** Azure, v rámci kterého se má nová datová továrna vytvořit.

1. U položky **Skupina prostředků** proveďte jeden z následujících kroků:

   a. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

   b. Vyberte **vytvořit novou** a zadejte název skupiny prostředků.

   Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).

1. V části **Verze** vyberte **V2**.

1. V části **umístění** vyberte umístění pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například služby Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používané datovou továrnou můžou být v jiných umístěních a oblastech.

1. Vyberte **Vytvořit**.

1. Po vytvoření se zobrazí domovská stránka **Datová továrna**.

   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Domovská stránka Azure Data Factory s dlaždicí pro monitorování autora &.":::

1. Pokud chcete na samostatné kartě otevřít uživatelské rozhraní služby Azure Data Factory, vyberte dlaždici **Vytvořit a monitorovat**.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Vytvoření kanálu pomocí nástroje pro kopírování dat

1. Na stránce **Začínáme** vyberte dlaždici **Kopírovat data**. Spustí se nástroj pro kopírování dat.

   ![Dlaždice nástroje pro kopírování dat](./media/doc-common-process/get-started-page.png)

1. Na stránce **Vlastnosti** v části **Název úlohy** zadejte **CopyFromBlobToSqlPipeline**. Pak vyberte **Další**. Uživatelské rozhraní služby Data Factory vytvoří kanál se zadaným názvem úlohy.

   ![Vytvoření kanálu](./media/tutorial-copy-data-tool/create-pipeline.png)

1. Na stránce **Source data store** (Zdrojové úložiště dat) proveďte následující kroky:

   a. Kliknutím na **+ vytvořit nové připojení** přidejte připojení.

   b. Z Galerie vyberte **Azure Blob Storage** a pak vyberte **pokračovat**.

   c. Na stránce **Nová propojená služba** vyberte své předplatné Azure a v seznamu **název účtu úložiště** vyberte svůj účet úložiště. Otestujte připojení a pak vyberte **vytvořit**.

   d. Vyberte jako zdroj nově vytvořenou propojenou službu a pak vyberte **Další**.

   ![Výběr zdrojové propojené služby](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Na stránce **Choose the input file or folder** (Zvolit vstupní soubor nebo složku) proveďte následující kroky:

   a. Vyberte **Procházet** a přejděte do složky **adfv2tutorial/Input** , vyberte soubor **inputEmp.txt** a **pak vyberte vybrat.**

   b. Kliknutím na tlačítko **Další** přejděte na další krok.

1. Na stránce **Nastavení formátu souboru** zaškrtněte políčko pro *první řádek jako záhlaví*. Všimněte si, že nástroj automaticky rozpoznává oddělovače sloupců a řádků. Vyberte **Další**. Můžete také zobrazit náhled dat a zobrazit schéma vstupních dat na této stránce.

   ![Nastavení formátu souboru](./media/tutorial-copy-data-tool/file-format-settings-page.png)

1. Na stránce **Destination data store** (Cílové úložiště dat) proveďte následující kroky:

   a. Kliknutím na **+ vytvořit nové připojení** přidejte připojení.

   b. V galerii vyberte **Azure SQL Database** a pak vyberte **pokračovat**.

   c. Na stránce **Nová propojená služba** vyberte v rozevíracím seznamu název serveru a název databáze a zadejte uživatelské jméno a heslo a pak vyberte **vytvořit**.

      ![Konfigurace Azure SQL DB](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

   d. Vyberte nově vytvořenou propojenou službu jako jímku a pak vyberte **Další**.

1. Na stránce **Mapování tabulek** vyberte tabulku **[dbo].[emp]** a pak vyberte **Další**.

1. Na stránce **mapování sloupců** si všimněte, že druhý a třetí sloupec ve vstupním souboru jsou namapovány na sloupce **FirstName** a **LastName** tabulky **EMP** . Upravte mapování tak, aby se zajistilo, že nedochází k žádné chybě, a pak vyberte **Další**.

   ![Stránka mapování sloupců](./media/tutorial-copy-data-tool/column-mapping.png)

1. Na stránce **Settings** (Nastavení) vyberte **Next** (Další).

1. Na stránce **Souhrn** zkontrolujte nastavení a pak vyberte **Další**.

1. Na stránce **Nasazení** vyberte **Monitorovat** a začněte monitorovat kanál (úlohu).

   ![Monitorování kanálu](./media/tutorial-copy-data-tool/monitor-pipeline.png)

1. Na stránce spuštění kanálu vyberte **aktualizovat** , aby se seznam aktualizoval. Pokud chcete zobrazit podrobnosti o spuštění aktivit nebo znovu spustit kanál, vyberte odkaz v části **název kanálu** . 

   ![Spuštění kanálu](./media/tutorial-copy-data-tool/pipeline-run.png)

1. Na stránce spuštění aktivit vyberte odkaz **Podrobnosti** (ikona brýlí) ve sloupci **název aktivity** , kde najdete další podrobnosti o operaci kopírování. Pokud se chcete vrátit do zobrazení spuštění kanálu, vyberte odkaz **všechny spuštění kanálu** v nabídce s popisem cesty. Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**.

   ![Monitorování spuštění aktivit](./media/tutorial-copy-data-tool/activity-monitoring.png)

1. Ověřte, že jsou data do tabulky **dbo. EMP** v SQL Database vložena.

1. Výběrem karty **Autor** na levé straně přepněte do režimu úprav. Pomocí editoru můžete aktualizovat propojené služby, datové sady a kanály vytvořené nástrojem. Podrobnosti o úpravách těchto entit v uživatelském rozhraní služby Data Factory najdete ve [verzi tohoto kurzu pro Azure Portal](tutorial-copy-data-portal.md).

   ![Vybrat kartu autor](./media/tutorial-copy-data-tool/author-tab.png)

## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z úložiště objektů blob do SQL Database. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

Pokud se chcete dozvědět, jak kopírovat data z místního prostředí do cloudu, přejděte k následujícímu kurzu:

>[!div class="nextstepaction"]
>[Kopírování dat z místního prostředí do cloudu](tutorial-hybrid-copy-data-tool.md)
