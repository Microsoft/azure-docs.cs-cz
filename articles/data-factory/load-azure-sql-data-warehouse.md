---
title: Načtení dat do Azure SQL Data Warehouse pomocí Azure Data Factory
description: Použití Azure Data Factory ke kopírování dat do Azure SQL Data Warehouse
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: 538751b1e93dfec66c35ea3768bde603c198df32
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672747"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Načtení dat do Azure SQL Data Warehouse pomocí Azure Data Factory

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) je cloudová, škálovatelná databáze, která dokáže zpracovávat obrovské objemy dat, jak relační, tak i nerelační. SQL Data Warehouse je postaven na architektuře MPP (Theed Parallel Processing), která je optimalizována pro úlohy podnikového datového skladu. Nabízí cloudovou flexibilitu, díky které můžete nezávisle škálovat úložiště a výpočetní výkon.

Začínáme s Azure SQL Data Warehouse je teď při použití Azure Data Factory jednodušší než kdy dřív. Azure Data Factory je plně spravovaná cloudová služba pro integraci dat. Službu můžete použít k naplnění SQL Data Warehouse daty ze stávajícího systému a šetří čas při sestavování analytických řešení.

Azure Data Factory nabízí následující výhody pro načítání dat do Azure SQL Data Warehouse:

* **Snadné nastavení**: intuitivní průvodce 5 kroky bez nutnosti skriptování.
* **Bohatá Podpora úložiště dat**: Integrovaná podpora pro bohatou sadu místních a cloudových úložišť dat. Podrobný seznam najdete v tabulce [podporovaných úložišť dat](copy-activity-overview.md#supported-data-stores-and-formats).
* **Zabezpečení a dodržování předpisů**: data se přenáší přes protokol HTTPS nebo ExpressRoute. Přítomnost globální služby zajišťuje, že vaše data nikdy neopustí zeměpisnou hranici.
* **Neparalelní výkon pomocí základu**: základem je nejúčinnější způsob, jak přesunout data do Azure SQL Data Warehouse. Pomocí funkce pracovního objektu blob můžete dosáhnout vysoké rychlosti zatížení ze všech typů úložišť dat, včetně služby Azure Blob Storage a Data Lake Store. (Základem podporuje službu Azure Blob Storage a Azure Data Lake Store ve výchozím nastavení.) Podrobnosti najdete v tématu o [výkonu aktivity kopírování](copy-activity-performance.md).

V tomto článku se dozvíte, jak pomocí nástroje Data Factory Kopírování dat _načíst data z Azure SQL Database do Azure SQL Data Warehouse_. Můžete postupovat podle podobných kroků a kopírovat data z jiných typů úložišť dat.

> [!NOTE]
> Další informace najdete v tématu [kopírování dat do nebo z Azure SQL Data Warehouse pomocí Azure Data Factory](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Azure SQL Data Warehouse: datový sklad obsahuje data, která se kopírují z databáze SQL. Pokud nemáte Azure SQL Data Warehouse, přečtěte si pokyny v tématu [vytvoření SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Azure SQL Database: v tomto kurzu se kopírují data z Azure SQL Database s ukázkovými daty Adventure Works LT. SQL Database můžete vytvořit podle pokynů v tématu [Vytvoření databáze SQL Azure](../sql-database/sql-database-get-started-portal.md). 
* Účet úložiště Azure: Azure Storage se používá jako _pracovní_ objekt BLOB v operaci hromadného kopírování. Pokud účet úložiště Azure nemáte, přečtěte si pokyny v tématu [Vytvoření účtu úložiště](../storage/common/storage-quickstart-create-account.md).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **vytvořit prostředek** > **data a analýzy** > **Data Factory**: 
   
   ![Výběr datové továrny v podokně Nový](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stránce **Nová datová továrna** zadejte hodnoty pro pole, která jsou zobrazena na následujícím obrázku:
      
   ![Stránka Nová datová továrna](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Název**: zadejte globálně jedinečný název pro objekt pro vytváření dat Azure. Pokud se zobrazí chyba "název objektu pro vytváření dat \"LoadSQLDWDemo\" není k dispozici," zadejte jiný název pro objekt pro vytváření dat. Můžete například použít název _**Your**_ **ADFTutorialDataFactory**. Zkuste vytvořit datovou továrnu znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné**: vyberte předplatné Azure, ve kterém chcete vytvořit datovou továrnu. 
    * **Skupina prostředků**: v rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte možnost **vytvořit novou** a zadejte název skupiny prostředků. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Verze**: vyberte **v2**.
    * **Umístění**: vyberte umístění pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat, která služba Data Factory používá, můžou být v jiných umístěních a oblastech. Mezi Tato úložiště dat patří Azure Data Lake Store, Azure Storage, Azure SQL Database a tak dále.

3. Vyberte **Vytvořit**.
4. Až se vytváření dokončí, přejdete do vaší datové továrny. Zobrazí se Domovská stránka **Data Factory** , jak je znázorněno na následujícím obrázku:
   
   ![Domovská stránka objektu pro vytváření dat](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Kliknutím na dlaždici **Author & monitor** můžete spustit aplikaci pro integraci dat na samostatné kartě.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Načtení dat do Azure SQL Data Warehouse

1. Na stránce **Začínáme** vyberte dlaždici **kopírování dat** pro spuštění nástroje kopírování dat:

   ![Dlaždice nástroje pro kopírování dat](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. Na stránce **vlastnosti** zadejte **CopyFromSQLToSQLDW** pro pole **název úlohy** a vyberte **Další**:

    ![Stránka Vlastnosti](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. Na stránce **zdrojové úložiště dat** proveďte následující kroky:

    a. klikněte na **+ vytvořit nové připojení**:

    ![Stránka Zdrojové úložiště dat](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. V galerii vyberte **Azure SQL Database** a pak vyberte **pokračovat**. Můžete zadat text "SQL" do vyhledávacího pole, chcete-li filtrovat konektory.

    ![Výběr Azure SQL DB](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Na stránce **Nová propojená služba** vyberte v rozevíracím seznamu název serveru a název databáze a zadejte uživatelské jméno a heslo. Kliknutím na **Test připojení** ověřte nastavení a pak vyberte **Dokončit**.
   
    ![Konfigurace Azure SQL DB](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Vyberte nově vytvořenou propojenou službu jako zdroj a pak klikněte na **Next** (Další).

    ![Výběr zdrojové propojené služby](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. V tabulce **Vybrat tabulky, ze kterých se mají kopírovat data, nebo použijte vlastní dotaz** , zadejte **tabulky SalesLT** k filtrování tabulek. Zvolte pole **(Vybrat vše)** , chcete-li použít všechny tabulky pro kopii, a poté vyberte možnost **Další**: 

    ![Vybrat zdrojové tabulky](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. Na stránce **cílové úložiště dat** proveďte následující kroky:

    a. Kliknutím na **+ Create new connection** (+ Vytvořit nové připojení) přidejte připojení.

    ![Stránka úložiště dat jímky](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. V galerii vyberte **Azure SQL Data Warehouse** a **pak vyberte další**.

    ![Výběr Azure SQL DW](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Na stránce **Nová propojená služba** vyberte v rozevíracím seznamu název serveru a název databáze a zadejte uživatelské jméno a heslo. Kliknutím na **Test připojení** ověřte nastavení a pak vyberte **Dokončit**.
   
    ![Konfigurace Azure SQL DW](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Vyberte nově vytvořenou propojenou službu jako jímku a klikněte na **Next** (Další).

    ![Výběr propojené služby jímky](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. Na stránce **mapování tabulek** zkontrolujte obsah a vyberte **Další**. Zobrazí se mapování inteligentní tabulky. Zdrojové tabulky jsou namapovány na cílové tabulky založené na názvech tabulek. Pokud zdrojová tabulka v cíli neexistuje, Azure Data Factory ve výchozím nastavení vytvoří cílovou tabulku se stejným názvem. Zdrojovou tabulku můžete také namapovat na existující cílovou tabulku. 

   > [!NOTE]
   > Automatické vytvoření tabulky pro SQL Data Warehouse jímka se vztahuje, pokud je zdroj SQL Server nebo Azure SQL Database. Pokud kopírujete data z jiného zdrojového úložiště dat, budete muset před spuštěním kopie dat předem vytvořit schéma v Azure SQL Data Warehouse jímky.

   ![Stránka Mapování tabulek](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. Na stránce **mapování schématu** zkontrolujte obsah a vyberte **Další**. Mapování inteligentní tabulky je založené na názvu sloupce. Pokud necháte Data Factory automaticky vytvořit tabulky, převod datového typu se může vyskytnout v případě nekompatibility mezi zdrojovým a cílovým úložištěm. Pokud je mezi zdrojovým a cílovým sloupcem převod nepodporovaného datového typu, zobrazí se vedle odpovídající tabulky chybová zpráva.

    ![Stránka Mapování schématu](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. Na stránce **Nastavení** proveďte následující kroky:

    a. V části **pracovní nastavení** klikněte na **+ Nová** a nové pracovní úložiště. Úložiště se používá pro přípravu dat, než se načte do SQL Data Warehouse pomocí základu. Po dokončení kopírování se automaticky vyčistí dočasná data v Azure Storage. 

    ![Konfigurace přípravy](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. Na stránce **Nová propojená služba** vyberte svůj účet úložiště a pak vyberte **Dokončit**.
   
    ![Konfigurace Azure Storage](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. V části **Upřesnit nastavení** zrušte výběr možnosti **použít výchozí typ** a pak vyberte **Další**.

    ![Konfigurovat základ](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. Na stránce **Souhrn** zkontrolujte nastavení a vyberte **Další**:

    ![Stránka souhrnu](./media/load-azure-sql-data-warehouse/summary-page.png)
1. Na **stránce nasazení**vyberte **monitorovat** a Sledujte kanál (úkol):

    ![Stránka Nasazení](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. Sloupec **Actions (akce** ) obsahuje odkazy na zobrazení podrobností o spuštění aktivit a opětovné spuštění kanálu: 

    ![Monitorování spuštění kanálu](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce** . Pokud chcete přejít zpátky k zobrazení spuštění kanálu, vyberte odkaz **kanály** v horní části. Seznam můžete aktualizovat kliknutím na **Aktualizovat**. 

    ![Monitorování spuštění aktivit](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Chcete-li monitorovat podrobnosti o spuštění každé aktivity kopírování, vyberte odkaz **Podrobnosti** v části **Akce** v zobrazení monitorování aktivit. Můžete monitorovat podrobnosti, jako je objem dat zkopírovaných ze zdroje, do jímky, propustnosti dat, kroky provádění s odpovídající dobou trvání a používané konfigurace:

    ![Podrobnosti o spuštění aktivity monitorování](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Další kroky

V následujícím článku se dozvíte o podpoře Azure SQL Data Warehouse: 

> [!div class="nextstepaction"]
>[Konektor Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)
