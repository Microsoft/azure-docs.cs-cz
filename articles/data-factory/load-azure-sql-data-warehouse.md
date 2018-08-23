---
title: Načtení dat do Azure SQL Data Warehouse pomocí Azure Data Factory | Dokumentace Microsoftu
description: Použití Azure Data Factory pro kopírování dat do Azure SQL Data Warehouse
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
ms.openlocfilehash: 8525dd443e80bb7d67bc48cc007ab1632ee3e611
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42059419"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Načtení dat do Azure SQL Data Warehouse pomocí Azure Data Factory

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) je založené na cloudu, škálovatelná databáze, která dokáže zpracovávat ohromné objemy dat, relačních i nerelačních. SQL Data Warehouse je postavená na architektuře paralelního zpracování (MPP), která je optimalizována pro úlohy datových skladů podnikové. Nabízí cloudovou elasticitu a flexibilně škálovat úložiště a výpočetní nezávisle na sobě.

Začínáme s Azure SQL Data Warehouse je teď snadnější než kdy dřív při použití služby Azure Data Factory. Azure Data Factory je služba pro integraci plně spravovaný cloudový datový. Služby můžete použít k naplnění SQL Data Warehouse s daty ze stávajícího systému a ušetřit čas při vytváření vlastních analytických řešení.

Azure Data Factory nabízí následující výhody pro načítání dat do služby Azure SQL Data Warehouse:

* **Snadné nastavení**: intuitivní Průvodce kroku 5 s žádné požadované skriptování.
* **Podpora store velké množství dat**: integrovanou podporu pro bohatou sadu místní a cloudové datové úložiště. Podrobný seznam najdete v tabulce [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
* **Zabezpečení a dodržování**: Data se přenáší prostřednictvím protokolu HTTPS nebo ExpressRoute. Přítomnost globální služba zajišťuje, že vaše data nikdy neopustí hranice zeměpisné.
* **Bezkonkurenční výkon pomocí PolyBase**: je nejúčinnější způsob pro přesun dat do Azure SQL Data Warehouse Polybase. Použijte funkci pracovního objektu blob k dosažení vysoké rychlosti ze všech typů úložišť dat, včetně Azure Blob storage a Data Lake Store. (Polybase podporuje Azure Blob storage a Azure Data Lake Store ve výchozím nastavení.) Podrobnosti najdete v tématu [výkonu aktivity kopírování](copy-activity-performance.md).

Tento článek ukazuje, jak použít nástroje pro kopírování dat objekt pro vytváření dat do _načtení dat ze služby Azure SQL Database do Azure SQL Data Warehouse_. Můžete použít podobným způsobem kopírovat data z jiných typů úložišť dat.

> [!NOTE]
> Další informace najdete v tématu [kopírování dat do nebo z Azure SQL Data Warehouse pomocí Azure Data Factory](connector-azure-sql-data-warehouse.md).
## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/) předtím, než začnete.
* Azure SQL Data Warehouse: Datový sklad obsahuje data, která se zkopíruje z databáze SQL. Pokud nemáte službu Azure SQL Data Warehouse, přečtěte si pokyny v [vytvořit SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Azure SQL Database: V tomto kurzu kopíruje data ze služby Azure SQL database s ukázkovými daty Adventure Works LT. SQL database můžete vytvořit podle pokynů v [vytvoření Azure SQL database](../sql-database/sql-database-get-started-portal.md). 
* Účet úložiště Azure: Azure Storage se používá jako _pracovní_ objektů blob v operaci hromadného kopírování. Pokud účet úložiště Azure nemáte, přečtěte si pokyny v tématu [Vytvoření účtu úložiště](../storage/common/storage-quickstart-create-account.md).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **nový** > **Data a analýzy** > **služby Data Factory**: 
   
   ![Vytvoření nové datové továrny](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
1. V **nová datová továrna** zadejte hodnoty pro pole, která jsou zobrazena na následujícím obrázku:
      
   ![Stránka Nová datová továrna](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Název**: Zadejte globálně jedinečný název pro službu Azure data factory. Pokud se zobrazí chyba "název objektu pro vytváření dat \"LoadSQLDWDemo\" není k dispozici," Zadejte jiný název datové továrny. Můžete například použít název  _**vaše_jméno**_**ADFTutorialDataFactory**. Zkuste znovu vytvořit datovou továrnu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné**: vyberte své předplatné Azure, ve kterém chcete datovou továrnu vytvořit. 
    * **Skupina prostředků**: z rozevíracího seznamu vyberte existující skupinu prostředků, nebo **vytvořit nový** možnost a zadejte název skupiny prostředků. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Verze**: vyberte **V2**.
    * **Umístění**: Vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat, které jsou používané datovou továrnou můžou být v jiných umístěních a oblastech. Úložiště těchto dat patří Azure Data Lake Store, Azure Storage, Azure SQL Database a tak dále.

1. Vyberte **Vytvořit**.
1. Po vytvoření se, přejděte do služby data factory. Zobrazí **služby Data Factory** domovskou stránku, jak je znázorněno na následujícím obrázku:
   
   ![Domovská stránka datové továrny](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Vyberte **vytvořit a monitorovat** dlaždice aplikace pro integraci dat v na samostatné kartě.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Načtení dat do Azure SQL Data Warehouse

1. V **Začínáme** stránky, vyberte **kopírování dat** dlaždice nástroje pro kopírování dat:

   ![Dlaždice nástroje pro kopírování dat](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. V **vlastnosti** určete, **CopyFromSQLToSQLDW** pro **název úkolu** pole a vyberte **Další**:

    ![Stránka Vlastnosti](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. V **zdrojového úložiště dat** stránce, proveďte následující kroky:

    a. Klikněte na tlačítko **+ vytvořit nové připojení**:

    ![Stránka Zdrojové úložiště dat](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. Vyberte **Azure SQL Database** z galerie a vyberte **pokračovat**. Do vyhledávacího pole filtrovat konektory můžete zadat "SQL".

    ![Výběr Azure SQL DB](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. V **Nová propojená služba** stránce, z rozevíracího seznamu vyberte název serveru a název databáze a zadejte uživatelské jméno a passworkd. Klikněte na tlačítko **Test připojení** Pokud chcete ověřit nastavení, pak vyberte **Dokončit**.
   
    ![Konfigurace Azure SQL DB](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Vyberte nově vytvořenou propojenou službu jako zdroj a pak klikněte na **Next** (Další).

    ![Výběr zdrojové propojené služby](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. V **vyberte tabulky, ze kterého chcete kopírovat data, nebo použijte vlastní dotaz** zadejte **SalesLT** filtrovat tabulky. Zvolte **(Vybrat vše)** používat všechny tabulky pro kopii a potom vyberte **Další**: 

    ![Vyberte zdrojové tabulky](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. V **cílového úložiště dat** stránce, proveďte následující kroky:

    a. Kliknutím na **+ Create new connection** (+ Vytvořit nové připojení) přidejte připojení.

    ![Stránka úložiště dat jímky](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. Vyberte **Azure SQL Data Warehouse** z galerie a vyberte **Další**.

    ![Vyberte Azure SQL data Warehouse](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. V **Nová propojená služba** stránce, z rozevíracího seznamu vyberte název serveru a název databáze a zadejte uživatelské jméno a passworkd. Klikněte na tlačítko **Test připojení** Pokud chcete ověřit nastavení, pak vyberte **Dokončit**.
   
    ![Konfigurovat Azure SQL data Warehouse](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Vyberte nově vytvořenou propojenou službu jako jímku a klikněte na **Next** (Další).

    ![Výběr propojené služby jímky](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. V **mapování tabulky** stránce zkontrolujte obsah a vyberte **Další**. Mapování inteligentní tabulky zobrazí. Do cílových tabulek na základě tabulky názvů jsou mapovány zdrojové tabulky. Pokud zdrojová tabulka neexistuje v cílovém umístění, Azure Data Factory vytvoří cílová tabulka se stejným názvem ve výchozím nastavení. Můžete také namapovat zdrojovou tabulku existující cílová tabulka. 

   > [!NOTE]
   > Vytvoření automatického tabulky pro jímku SQL Data Warehouse platí, pokud SQL Server nebo Azure SQL Database připojený zdroj. Při kopírování dat z jiné zdrojové úložiště dat, musíte před spuštěním kopírování dat předem vytvořit schéma v Azure SQL Data Warehouse jímky.

   ![Stránka Mapování tabulek](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. V **mapování schématu** stránce zkontrolujte obsah a vyberte **Další**. Inteligentní tabulky mapování vychází z názvu sloupce. Pokud necháte služby Data Factory automaticky vytvářet tabulky, převodu typu dat. může nastat, když je nekompatibility mezi zdrojovým a cílovým úložištěm. Pokud dojde nepodporovaný datový typ převodu mezi sloupci zdrojového a cílového, se zobrazí chybová zpráva vedle příslušné tabulky.

    ![Stránka Mapování schématu](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. V **nastavení** stránce, proveďte následující kroky:

    a. V **pracovní nastavení** klikněte na tlačítko **+ nová** na nové pracovní úložiště. Úložiště se používá pro přípravu dat před načtením do SQL Data Warehouse pomocí PolyBase. Po dokončení kopírování dočasné data ve službě Azure Storage je automaticky vyčištěna. 

    ![Konfigurovat pracovní](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. V **Nová propojená služba** stránky, vyberte svůj účet úložiště a vyberte **Dokončit**.
   
    ![Konfigurace služby Azure Storage](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. V **upřesňující nastavení** části, zrušte výběr **výchozího typu použití** možnost a potom vyberte **Další**.

    ![Konfigurace funkce PolyBase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. V **Souhrn** stránky, zkontrolujte nastavení a vyberte **Další**:

    ![Stránka souhrnu](./media/load-azure-sql-data-warehouse/summary-page.png)
1. V **stránku nasazení**vyberte **monitorování** a začněte monitorovat kanál (úlohu):

    ![Stránka Nasazení](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. **Akce** sloupec obsahuje odkazy, chcete-li zobrazit podrobnosti o spuštění aktivit a opětovné spuštění kanálu: 

    ![Monitorování spuštění kanálu](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. Pokud chcete zobrazit spuštění aktivit, které jsou spojeny se spuštěním kanálu, vyberte **zobrazit spuštění aktivit** odkaz v **akce** sloupce. Pokud chcete přepnout zpět na zobrazení spuštění kanálu, vyberte **kanály** odkazu v horní části. Seznam můžete aktualizovat výběrem možnosti **Aktualizovat**. 

    ![Monitorování spuštění aktivit](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Pokud chcete monitorovat spuštění podrobnosti o každé aktivitě kopírování, vyberte **podrobnosti** odkaz v části **akce** v aktivitě zobrazení monitorování. Podobně jako objem dat zkopírovanou ze zdroje do jímky, propustnost dat, provádění kroků s určitou dobu a použít konfigurace, můžete sledovat informace:

    ![Podrobnosti o spuštění aktivit monitorování](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Další postup

Přejděte k další informace o podpoře Azure SQL Data Warehouse v následujícím článku: 

> [!div class="nextstepaction"]
>[Konektor Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)
