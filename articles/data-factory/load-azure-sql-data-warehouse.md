---
title: Načtení dat do Azure SQL Data Warehouse
description: Kopírování dat do Datového skladu Azure SQL pomocí Azure Data Factory
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/22/2018
ms.openlocfilehash: dc6d8c9da749e7ee60713f4357dc6d46051d89f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131275"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Načtení dat do služby Azure SQL Data Warehouse pomocí služby Azure Data Factory

[Azure SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) je cloudová databáze s horizontálním navýšením kapacity, která je schopná zpracovávat obrovské objemy dat, relační i nerelační. SQL Data Warehouse je postaven na architektuře masivně paralelního zpracování (MPP), která je optimalizovaná pro úlohy podnikového datového skladu. Nabízí elasticitu cloudu s flexibilitou škálování úložiště a výpočtu nezávisle.

Začínáme s Azure SQL Data Warehouse je teď jednodušší než kdy jindy, když používáte Azure Data Factory. Azure Data Factory je plně spravovaná cloudová služba pro integraci dat. Pomocí služby můžete naplnit datový sklad SQL daty z vašeho stávajícího systému a ušetřit čas při vytváření analytických řešení.

Azure Data Factory nabízí následující výhody pro načítání dat do Azure SQL Data Warehouse:

* **Snadné nastavení**: Intuitivní pětistupňový průvodce bez nutnosti skriptování.
* **Bohatá podpora úložiště dat:** Integrovaná podpora pro bohatou sadu místních a cloudových úložišť dat. Podrobný seznam naleznete v tabulce [Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
* **Zabezpečené a kompatibilní**: Data se přenášejí přes HTTPS nebo ExpressRoute. Globální přítomnost služby zajišťuje, že vaše data nikdy neopustí geografickou hranici.
* **Bezkonkurenční výkon pomocí PolyBase**: Polybase je nejúčinnější způsob, jak přesunout data do Azure SQL Data Warehouse. Pomocí funkce pracovního objektu blob můžete dosáhnout vysoké rychlosti načítání ze všech typů úložišť dat, včetně úložiště objektů blob Azure a úložiště datového jezera. (Polybase ve výchozím nastavení podporuje úložiště objektů blob Azure a Azure Data Lake Store.) Podrobnosti naleznete v [tématu Kopírování výkonu aktivity](copy-activity-performance.md).

Tento článek ukazuje, jak pomocí nástroje Data Factory Copy Data Tool _načíst data z Azure SQL Database do Azure SQL Data Warehouse_. Můžete postupovat podle podobných kroků ke kopírování dat z jiných typů úložišť dat.

> [!NOTE]
> Další informace najdete [v tématu Kopírování dat do nebo z Azure SQL Data Warehouse pomocí Azure Data Factory](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
* Azure SQL Data Warehouse: Datový sklad obsahuje data, která jsou zkopírována z databáze SQL. Pokud nemáte Datový sklad Azure SQL, přečtěte si pokyny v [tématu Vytvoření datového skladu SQL](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Azure SQL Database: Tento kurz kopíruje data z databáze Azure SQL s ukázkovými daty Adventure Works LT. Databázi SQL můžete vytvořit podle pokynů v části [Vytvoření databáze Azure SQL](../sql-database/sql-database-get-started-portal.md).
* Účet úložiště Azure: Azure Storage se používá jako _pracovní_ objekt blob v operaci hromadného kopírování. Pokud účet úložiště Azure nemáte, přečtěte si pokyny v tématu [Vytvoření účtu úložiště](../storage/common/storage-account-create.md).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V levé nabídce vyberte **Vytvořit data o prostředku** > **+ Analytics** > **Data Factory**:

   ![Výběr datové továrny v podokně Nový](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stránce **Nová továrna dat** zadejte hodnoty pro pole, která jsou zobrazena na následujícím obrázku:

   ![Stránka Nová datová továrna](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)

    * **Název:** Zadejte globálně jedinečný název pro vaši datovou továrnu Azure. Pokud se zobrazí chyba "Název \"datové továrny LoadSQLDWDemo\" není k dispozici", zadejte jiný název pro datové továrny. Můžete například použít název _**yourname**_**ADFTutorialDataFactory**. Zkuste znovu vytvořit datovou továrnu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné:** Vyberte předplatné Azure, ve kterém chcete vytvořit datové továrny. 
    * **Skupina prostředků**: V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte možnost **Vytvořit nový** a zadejte název skupiny prostředků. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
    * **Verze**: Vyberte **V2**.
    * **Umístění**: Vyberte umístění pro datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat, které jsou používány factory může být v jiných umístěních a oblastech. Mezi tato úložiště dat patří Azure Data Lake Store, Azure Storage, Azure SQL Database a tak dále.

3. Vyberte **Vytvořit**.
4. Po dokončení vytváření přejděte do datové továrny. Zobrazí se domovská stránka **Data Factory,** jak je znázorněno na následujícím obrázku:

   ![Domovská stránka objektu pro vytváření dat](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Výběrem dlaždice **Author & Monitor** (Vytvořit a monitorovat) otevřete na samostatné kartě aplikaci pro integraci dat.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Načtení dat do Azure SQL Data Warehouse

1. Na stránce **Začínáme** vyberte dlaždici **Kopírovat data** a spusťte nástroj Kopírovat data:

   ![Dlaždice nástroje pro kopírování dat](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. Na stránce **Vlastnosti** zadejte pro pole **Název úkolu** **příkaz CopyFromSQLToSQLDW** a vyberte **Další**:

    ![Stránka Vlastnosti](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. Na stránce **Úložiště zdrojových dat** proveďte následující kroky:

    a. klikněte na **+ Vytvořit nové připojení**:

    ![Stránka Zdrojové úložiště dat](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. V galerii vyberte **Azure SQL Database** a vyberte **Pokračovat**. Do vyhledávacího pole můžete zadat "SQL" a filtrovat konektory.

    ![Výběr Azure SQL DB](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Na stránce **Nová propojená služba** vyberte název serveru a název db z rozevíracího seznamu a zadejte uživatelské jméno a heslo. Kliknutím na **Test připojení** ověřte nastavení a pak vyberte **Dokončit**.

    ![Konfigurace Azure SQL DB](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Vyberte nově vytvořenou propojenou službu jako zdroj a pak klikněte na **Next** (Další).

    ![Výběr zdrojové propojené služby](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. V části **Vybrat tabulky, ze kterých chcete data kopírovat nebo použít vlastní stránku dotazu,** zadejte **saleslt** pro filtrování tabulek. Chcete-li použít všechny tabulky pro kopii, zvolte pole **(Vybrat vše)** a pak vyberte **Další**:

    ![Výběr zdrojových tabulek](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. Na stránce **Cílové úložiště dat** proveďte následující kroky:

    a. Kliknutím na **+ Create new connection** (+ Vytvořit nové připojení) přidejte připojení.

    ![Stránka úložiště dat jímky](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. V galerii vyberte **Azure SQL Data Warehouse** a vyberte **Další**.

    ![Výběr Azure SQL DW](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Na stránce **Nová propojená služba** vyberte název serveru a název db z rozevíracího seznamu a zadejte uživatelské jméno a heslo. Kliknutím na **Test připojení** ověřte nastavení a pak vyberte **Dokončit**.

    ![Konfigurace Azure SQL DW](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Vyberte nově vytvořenou propojenou službu jako jímku a klikněte na **Next** (Další).

    ![Výběr propojené služby jímky](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. Na stránce **Mapování tabulky** zkontrolujte obsah a vyberte **Další**. Zobrazí se inteligentní mapování tabulek. Zdrojové tabulky jsou mapovány na cílové tabulky na základě názvů tabulek. Pokud zdrojová tabulka v cílovém umístění neexistuje, Azure Data Factory ve výchozím nastavení vytvoří cílovou tabulku se stejným názvem. Zdrojovou tabulku můžete také namapovat na existující cílovou tabulku.

   > [!NOTE]
   > Automatické vytváření tabulek pro jímky datového skladu SQL platí, když je zdrojem SQL Server nebo Azure SQL Database. Pokud zkopírujete data z jiného zdrojového úložiště dat, budete muset před spuštěním kopie dat předem vytvořit schéma v jímce Azure SQL Data Warehouse.

   ![Stránka Mapování tabulek](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. Na stránce **mapování schématu** zkontrolujte obsah a vyberte **Další**. Inteligentní mapování tabulky je založeno na názvu sloupce. Pokud umožníte Data Factory automaticky vytvořit tabulky, převod typu dat může dojít, pokud existují nekompatibility mezi zdrojové a cílové úložiště. Pokud mezi zdrojovým a cílovým sloupcem došlo k nepodporovanému převodu datového typu, zobrazí se vedle odpovídající tabulky chybová zpráva.

    ![Stránka Mapování schématu](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. Na stránce **Nastavení** proveďte následující kroky:

    a. V části **Pracovní nastavení** klikněte na **+ Nový** do nového pracovního úložiště. Úložiště se používá pro pracovní data před načtením do datového skladu SQL pomocí PolyBase. Po dokončení kopírování se automaticky vyčistí prozatímní data ve službě Azure Storage.

    ![Konfigurace pracovní](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. Na stránce **Nová propojená služba** vyberte účet úložiště a vyberte **Dokončit**.

    ![Konfigurace úložiště Azure](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. V části **Upřesnit nastavení** odznačte výchozí volbu **Použít typ a** pak vyberte **Další**.

    ![Konfigurovat polybase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. Na stránce **Souhrn** zkontrolujte nastavení a vyberte **Další**:

    ![Stránka souhrnu](./media/load-azure-sql-data-warehouse/summary-page.png)
1. Na **stránce Nasazení**vyberte **Sledovat,** chcete-li sledovat kanál (úlohu):

    ![Stránka Nasazení](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. Sloupec **Akce** obsahuje odkazy na zobrazení podrobností o spuštění aktivity a opětovné spuštění kanálu:

    ![Monitorování spuštění kanálu](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. Chcete-li zobrazit spuštění aktivity, které jsou přidruženy ke spuštění kanálu, vyberte odkaz **Zobrazit spuštění aktivity** ve sloupci **Akce.** Chcete-li přepnout zpět do zobrazení spuštění kanálu, vyberte odkaz **Potrubí** v horní části. Seznam můžete aktualizovat kliknutím na **Aktualizovat**.

    ![Monitorování spuštění aktivit](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Chcete-li sledovat podrobnosti spuštění pro každou aktivitu kopírování, vyberte odkaz **Podrobnosti** v části **Akce** v zobrazení sledování aktivity. Můžete sledovat podrobnosti, jako je objem dat zkopírovaných ze zdroje do jímky, propustnost dat, kroky spuštění s odpovídající dobou trvání a použité konfigurace:

    ![Podrobnosti spuštění aktivity monitoru](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Další kroky

Přejdete k následujícímu článku, kde se dozvíte o podpoře Datového skladu Azure SQL:

> [!div class="nextstepaction"]
>[Konektor datového skladu Azure SQL](connector-azure-sql-data-warehouse.md)
