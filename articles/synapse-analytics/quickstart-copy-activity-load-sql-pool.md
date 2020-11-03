---
title: Rychlý Start k načtení dat do fondu SQL pomocí aktivity kopírování
description: Načtení dat do fondu SQL pomocí Azure synapse Analytics
services: synapse-analytics
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: synapse-analytics
ms.topic: quickstart
ms.custom: seo-lt-2019
ms.date: 11/02/2020
ms.openlocfilehash: 12b5530ccf154220b11f9d1286d629caf2209475
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280887"
---
# <a name="quickstart-load-data-into-sql-pool-using-copy-activity"></a>Rychlý Start: načtení dat do fondu SQL pomocí aktivity kopírování

Azure synapse Analytics nabízí různé analytické moduly, které vám pomůžou ingestovat, transformovat, modelovat a analyzovat vaše data. Fond SQL nabízí výpočetní a úložné funkce založené na T-SQL. Po vytvoření fondu SQL ve vašem pracovním prostoru synapse se dají data načíst, namodelovat, zpracovávat a doručovat pro rychlejší analytické poznatky.

V tomto rychlém startu se dozvíte, jak *načíst data z Azure SQL Database do služby Azure synapse Analytics*. Můžete postupovat podle podobných kroků a kopírovat data z jiných typů úložišť dat. A podobný tok se vztahuje také na kopírování dat mezi jiným zdrojem a jímkou.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Pracovní prostor Azure synapse: Vytvořte pracovní prostor synapse pomocí Azure Portal podle pokynů v tématu [rychlý Start: vytvoření pracovního prostoru synapse](quickstart-create-workspace.md).
* Azure SQL Database: v tomto kurzu se zkopírují data z ukázkové datové sady Adventure Works LT v Azure SQL Database. Tuto ukázkovou databázi můžete v SQL Database vytvořit podle pokynů v části [vytvoření ukázkové databáze v Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md). Můžete také použít další úložiště dat podle podobných kroků.
* Účet služby Azure Storage: Azure Storage slouží jako *pracovní* oblast v operaci kopírování. Pokud účet úložiště Azure nemáte, přečtěte si pokyny v tématu [Vytvoření účtu úložiště](../storage/common/storage-account-create.md).
* Azure synapse Analytics: jako úložiště dat jímky použijete fond SQL. Pokud nemáte instanci Azure synapse Analytics, přečtěte si téma [Vytvoření fondu SQL](quickstart-create-sql-pool-portal.md) , kde najdete kroky pro jeho vytvoření.

### <a name="navigate-to-the-synapse-studio"></a>Přejít na synapse Studio

Po vytvoření pracovního prostoru Azure synapse máte dva způsoby, jak otevřít synapse Studio:

* Otevřete pracovní prostor synapse ve [Azure Portal](https://ms.portal.azure.com/#home). V horní části přehledu vyberte **Spustit synapse Studio**.
* Otevřete [Azure synapse Analytics](https://web.azuresynapse.net/) a přihlaste se ke svému pracovnímu prostoru.

V tomto rychlém startu používáme jako příklad pracovní prostor s názvem "adftest2020". Automaticky vás přejde na domovskou stránku synapse studia.

![Domovská stránka synapse Studio](media/doc-common-process/synapse-studio-home.png)

## <a name="create-linked-services"></a>Vytvoření propojených služeb

V Azure synapse Analytics je propojená služba, kde můžete definovat informace o připojení k ostatním službám. V této části vytvoříte následující dva druhy propojených služeb: Azure SQL Database a Azure Data Lake Storage Gen2 propojených služeb.

1. Na domovské stránce synapse Studio v levém navigačním panelu vyberte kartu **Spravovat** .
1. V části externí připojení vyberte propojené služby.
  
   ![Vytvoření nové propojené služby](media/doc-common-process/new-linked-service.png)

1. Chcete-li přidat propojenou službu, vyberte možnost **Nový**.
1. V galerii vyberte **Azure SQL Database** a pak vyberte **pokračovat**. Můžete zadat text "SQL" do vyhledávacího pole, chcete-li filtrovat konektory.

   ![Vytvořit novou propojenou službu Azure SQL Database](media/quickstart-copy-activity-load-sql-pool/new-azure-sql-linked-service.png)

1. Na stránce Nová propojená služba vyberte v rozevíracím seznamu název serveru a název databáze a zadejte uživatelské jméno a heslo. Kliknutím na **Test připojení** ověřte nastavení a pak vyberte **vytvořit**.

   ![Konfigurace propojené služby Azure SQL Database](media/quickstart-copy-activity-load-sql-pool/azure-sql-linked-service-configuration.png)

1. Opakujte kroky 3-4, ale z Galerie vyberte **Azure Data Lake Storage Gen2** místo toho. Na stránce Nová propojená služba vyberte v rozevíracím seznamu název svého účtu úložiště. Kliknutím na **Test připojení** ověřte nastavení a pak vyberte **vytvořit**. 

   ![Konfigurace Azure Data Lake Storage Gen2](media/quickstart-copy-activity-load-sql-pool/adls-gen2-linked-service-configuration.png)
 
## <a name="create-a-pipeline"></a>Vytvoření kanálu

Kanál obsahuje logický tok pro spuštění sady aktivit. V této části vytvoříte kanál s aktivitou kopírování, která ingestuje data z Azure SQL Database do fondu SQL.

1. Přejít na kartu **integrace** . Vyberte ikonu plus vedle záhlaví kanály a vyberte kanál.

   ![Vytvořit nový kanál](media/doc-common-process/new-pipeline.png)

1. V části *přesunout a transformovat* v podokně *aktivity* přetáhněte **Kopírovat data** na plátno kanálu.
1. Vyberte aktivitu kopírování a přejít na kartu zdroj. Pokud chcete vytvořit novou zdrojovou datovou sadu, vyberte **Nový** .

   ![Vytvoření zdrojové datové sady](media/quickstart-copy-activity-load-sql-pool/new-source-dataset.png)

1. Jako úložiště dat vyberte **Azure SQL Database** a vyberte **pokračovat**.
1. V podokně *nastavit vlastnosti* vyberte propojenou službu Azure SQL Database, kterou jste vytvořili v předchozím kroku. 
1. V části název tabulky vyberte ukázkovou tabulku, kterou chcete použít při následující aktivitě kopírování. V tomto rychlém startu používáme jako příklad tabulku tabulky SalesLT. Customer. 

   ![Nastavení vlastností zdrojové datové sady](media/quickstart-copy-activity-load-sql-pool/source-dataset-properties.png)
1. Po dokončení vyberte **OK** .
1. Vyberte aktivitu kopírování a pak přejít na kartu jímka. Vyberte **Nový** , chcete-li vytvořit novou datovou sadu jímky.
1. Jako úložiště dat vyberte **fond SQL Analytics** a vyberte **pokračovat**.
1. V podokně  **nastavit vlastnosti** vyberte fond SQL Analytics, který jste vytvořili v předchozím kroku. Pokud zapisujete do existující tabulky, v rozevíracím seznamu *název tabulky* vyberte. V opačném případě se podívejte na Upravit a zadejte název nové tabulky. Po dokončení vyberte **OK** .
1. V části nastavení datové sady jímky povolte **automaticky vytvořit tabulku** v poli možnost tabulky.

   ![Povolit automatické vytváření](media/quickstart-copy-activity-load-sql-pool/auto-create-table.png)

1. Na stránce **Nastavení** zaškrtněte políčko **Povolit přípravu**. Tato možnost se používá, pokud zdrojová data nejsou kompatibilní se základem. V části **pracovní nastavení** vyberte propojenou službu Azure Data Lake Storage Gen2, kterou jste vytvořili v předchozím kroku jako pracovní úložiště. 

    Úložiště se používá pro přípravu dat před jejich načtením do Azure synapse Analytics pomocí základu. Po dokončení kopírování se automaticky vyčistí dočasná data v Azure Data Lake Storage Gen2.

   ![Zapnout pracovní režim](media/quickstart-copy-activity-load-sql-pool/staging-linked-service.png)

1. Pokud chcete kanál ověřit, vyberte **ověřit** na panelu nástrojů. Na pravé straně stránky se zobrazí výsledek výstupu ověření kanálu. 

## <a name="debug-and-publish-the-pipeline"></a>Ladění a publikování kanálu

Po dokončení konfigurace kanálu můžete spustit ladění před publikováním artefaktů, abyste ověřili, jestli je vše správné.

1. K ladění kanálu vyberte na panelu nástrojů **Ladit**. Na kartě **Výstup** v dolní části okna se zobrazí stav spuštění kanálu. 

   ![Ladění kanálu](media/quickstart-copy-activity-load-sql-pool/debugging-result.png)

1. Po úspěšném spuštění kanálu klikněte na horním panelu nástrojů na **publikovat vše**. Tato akce publikuje entity (datové sady a kanály), které jste vytvořili ve službě synapse Analytics.
1. Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**. Pokud chcete zobrazit oznamovací zprávy, vyberte v pravém horním rohu tlačítko zvonku. 


## <a name="trigger-and-monitor-the-pipeline"></a>Aktivace a monitorování kanálu

V této části ručně aktivujete kanál publikovaný v předchozím kroku. 

1. Vyberte **Přidat aktivační událost** na panelu nástrojů a pak vyberte **aktivovat nyní**. Na stránce **spuštění kanálu** vyberte **OK**.  
1. Přejít na kartu **monitorování** umístěnou na levém bočním panelu. Zobrazí se stav ručně aktivovaného spuštění kanálu. 
1. Po úspěšném dokončení kanálu vyberte odkaz pod sloupcem **název kanálu** a zobrazte podrobnosti o spuštění aktivit nebo spusťte kanál znovu. V tomto příkladu je k dispozici pouze jedna aktivita, takže se v seznamu zobrazí pouze jedna položka. 
1. Pokud chcete zobrazit podrobnosti o operaci kopírování, vyberte odkaz **Podrobnosti** (ikona brýlí) ve sloupci **název aktivity** . Můžete monitorovat podrobnosti, jako je objem dat zkopírovaných ze zdroje, do jímky, propustnosti dat, kroky provádění s odpovídající dobou trvání a používané konfigurace.

   ![Podrobnosti o aktivitách](media/quickstart-copy-activity-load-sql-pool/activity-details.png)

1. Pokud chcete přejít zpátky k zobrazení spuštění kanálu, vyberte odkaz **všechny spuštění kanálu** v horní části. Seznam můžete aktualizovat kliknutím na **Aktualizovat**.
1. Ověřte, že vaše data jsou ve fondu SQL správně napsaná.


## <a name="next-steps"></a>Další kroky

V následujícím článku se dozvíte o podpoře Azure synapse Analytics:

> [!div class="nextstepaction"]
> [Kanál a aktivity](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 
>  [Přehled](https://docs.microsoft.com/azure/data-factory/connector-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 
>  konektoru [Aktivita kopírování](https://docs.microsoft.com/azure/data-factory/copy-activity-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)