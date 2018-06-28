---
title: Načtení dat do Azure SQL Data Warehouse pomocí Azure Data Factory | Microsoft Docs
description: Použití Azure Data Factory ke zkopírování dat do Azure SQL Data Warehouse
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
ms.openlocfilehash: b96483232a1da5ae21e6ba8cbe873d876d38ed11
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050297"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Načtení dat do Azure SQL Data Warehouse pomocí Azure Data Factory

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) je databáze založená na cloudu, Škálováním na více systémů, která je dokáže zpracovávat ohromné objemy dat, relačních i nerelačních. SQL Data Warehouse je postaveno na architektuře massively parallel processing (MPP), která je optimalizovaná pro podnikové datového skladu úlohy. Pružnost cloudu nabízí flexibilitu škálovat úložiště a výpočetní nezávisle.

Začínáme s Azure SQL Data Warehouse je teď jednodušší než kdy při použití Azure Data Factory. Azure Data Factory je plně spravovaná Cloudová datová služba integrace. Službu můžete použít k naplnění SQL Data Warehouse s daty z vašeho stávajícího systému a ušetřit čas při vytváření vlastních analytických řešení.

Azure Data Factory nabízí následující výhody pro načítání dat do Azure SQL Data Warehouse:

* **Snadno nastavit**: intuitivní průvodce krok 5 žádné vyžaduje skriptování.
* **Podpora úložiště bohaté dat**: integrovanou podporu pro bohatou sadu místní a Cloudová datová úložiště. Podrobný seznam najdete v tabulce [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
* **Zabezpečení a dodržování**: Data se přenáší prostřednictvím protokolu HTTPS nebo ExpressRoute. Přítomnosti globální služby zajišťuje vaše data nikdy neopustí hranice zeměpisné.
* **Bezkonkurenční výkonu pomocí funkce PolyBase**: Polybase je nejúčinnější způsob, jak přesunout data do Azure SQL Data Warehouse. Použijte funkci pracovní objektů blob k dosažení vysokého zatížení rychlosti ze všech typů úložišť dat, včetně Azure Blob storage a Data Lake Store. (Polybase podporuje Azure Blob storage a Azure Data Lake Store ve výchozím nastavení.) Podrobnosti najdete v tématu [výkonu kopie aktivity](copy-activity-performance.md).

Tento článek ukazuje, jak používat nástroj Data Factory kopírovat Data k _načtení dat z databáze SQL Azure do Azure SQL Data Warehouse_. Můžete provést podobné kroky ke zkopírování dat od ostatních typů dat úložiště.

> [!NOTE]
> Další informace najdete v tématu [zkopírovat data do nebo z Azure SQL Data Warehouse pomocí Azure Data Factory](connector-azure-sql-data-warehouse.md).
## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Pokud nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/) před zahájením.
* Azure SQL Data Warehouse: Datového skladu obsahuje data, která se zkopíruje přes z databáze SQL. Pokud nemáte Azure SQL Data Warehouse, postupujte podle pokynů v [vytvořit SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Azure SQL Database: V tomto kurzu zkopíruje data z databáze Azure SQL s ukázkovými daty LT společnosti Adventure Works. Databáze SQL můžete vytvořit podle pokynů v [vytvoření Azure SQL database](../sql-database/sql-database-get-started-portal.md). 
* Účet úložiště Azure: úložiště Azure se používá jako _pracovní_ objektů blob v operaci hromadného kopírování. Pokud účet úložiště Azure nemáte, přečtěte si pokyny v tématu [Vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **nový** > **Data + analýzy** > **Data Factory**: 
   
   ![Vytvoření nové datové továrny](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
2. V **nový objekt pro vytváření dat** stránky, zadejte hodnoty pro pole, která jsou zobrazená na následujícím obrázku:
      
   ![Stránka Nová datová továrna](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Název**: Zadejte globálně jedinečný název objektu pro vytváření dat Azure. Pokud se zobrazí chyba "název objektu pro vytváření dat \"LoadSQLDWDemo\" není k dispozici," Zadejte jiný název služby data Factory. Například můžete použít název  _**jméno**_**ADFTutorialDataFactory**. Zkuste vytvořit objekt pro vytváření dat znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné**: Vyberte předplatné Azure, ve kterém se má vytvořit datová továrna. 
    * **Skupina prostředků**: Vyberte existující skupinu prostředků z rozevíracího seznamu, nebo **vytvořit nový** možnost a zadejte název skupiny prostředků. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Verze**: vyberte **V2**.
    * **Umístění**: Vyberte umístění služby data Factory. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat, které jsou používány pro vytváření dat může být v jiných umístěních a oblastech. Tyto úložiště dat patří Azure Data Lake Store, Azure Storage, Azure SQL Database a tak dále.

3. Vyberte **Vytvořit**.
4. Po dokončení vytvoření přejděte do data factory. Zobrazí **Data Factory** domovskou stránku, jak je znázorněno na následujícím obrázku:
   
   ![Domovská stránka objektu pro vytváření dat](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Vyberte **Autor & monitorování** dlaždici spuštění aplikace integraci dat na samostatné kartě.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Načtení dat do Azure SQL Data Warehouse

1. V **Začínáme** vyberte **kopírovat Data** dlaždici spustíte nástroj pro kopírování dat:

   ![Dlaždice nástroje pro kopírování dat](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
2. V **vlastnosti** zadejte **CopyFromSQLToSQLDW** pro **název úlohy** pole a vyberte možnost **Další**:

    ![Stránka Vlastnosti](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

3. V **zdrojového úložiště dat** proveďte následující kroky:

    a. Klikněte na tlačítko **+ vytvořit nové připojení**:

    ![Stránka Zdrojové úložiště dat](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. Vyberte **Azure SQL Database** z galerie a vyberte **pokračovat**. "SQL" můžete zadat do pole hledání k filtrování konektory.

    ![Vyberte databázi Azure SQL](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. V **Nová propojená služba** z rozevíracího seznamu vyberte název serveru a název databáze a zadejte uživatelské jméno a passworkd. Klikněte na tlačítko **testovací připojení** Pokud chcete ověřit nastavení, pak vyberte **Dokončit**.
   
    ![Konfigurace Azure SQL DB](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Vyberte nově vytvořenou propojenou službu jako zdroj a pak klikněte na **Next** (Další).

    ![Výběr zdrojové propojené služby](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

4. V **vyberte tabulky, ze kterého chcete zkopírovat data nebo použít vlastní dotaz** zadejte **SalesLT** vyfiltrujete tabulky. Vyberte **(Vybrat vše)** používat všechny tabulky kopie a poté vyberte **Další**: 

    ![Vyberte zdrojové tabulky](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. V **cílového úložiště dat** proveďte následující kroky:

    a. Klikněte na tlačítko **+ vytvořit nové připojení** přidat připojení

    ![Jímky stránka úložiště dat](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. Vyberte **Azure SQL Data Warehouse** z galerie a vyberte **Další**.

    ![Vyberte datového skladu Azure SQL](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. V **Nová propojená služba** z rozevíracího seznamu vyberte název serveru a název databáze a zadejte uživatelské jméno a passworkd. Klikněte na tlačítko **testovací připojení** Pokud chcete ověřit nastavení, pak vyberte **Dokončit**.
   
    ![Konfigurace datového skladu Azure SQL](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Vyberte nově vytvořenou propojené služby jako jímku a pak klikněte na tlačítko **Další**.

    ![Vyberte jímky propojené služby](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

6. V **mapování tabulky** , zkontrolujte obsah a vyberte **Další**. Mapování u inteligentního tabulky zobrazí. Zdrojové tabulky jsou namapované na cílové tabulky podle názvů tabulek. Pokud zdrojová tabulka neexistuje v cílovém, Azure Data Factory vytvoří cílové tabulky se stejným názvem ve výchozím nastavení. Zdrojová tabulka můžete také mapovat existující cílové tabulky. 

   > [!NOTE]
   > Vytvoření automatického tabulky pro jímku SQL Data Warehouse použitelná v případě systému SQL Server nebo Azure SQL Database je zdrojem. Při kopírování dat z jiného zdrojového úložiště dat, musíte před provedením kopírování dat předem vytvořit schéma v jímce Azure SQL Data Warehouse.

   ![Stránka Mapování tabulek](./media/load-azure-sql-data-warehouse/table-mapping.png)

9. V **schéma mapování** , zkontrolujte obsah a vyberte **Další**. Mapování inteligentního tabulky je založen na názvu sloupce. Pokud necháte Data Factory automaticky vytvářet tabulky, převod typů dat může dojít, když došlo k problémům s kompatibilitou mezi zdrojovým a cílovým úložiště. Pokud konverzi nepodporovaný datový typ mezi zdrojovým a cílovým sloupcem se zobrazí chybová zpráva vedle odpovídající tabulky.

    ![Stránka Mapování schématu](./media/load-azure-sql-data-warehouse/schema-mapping.png)

11. V **nastavení** proveďte následující kroky:

    a. V **pracovní nastavení** klikněte na tlačítko **+ nový** do nové pracovní úložiště. Úložiště se používá pro přípravu dat, než se načte do SQL Data Warehouse pomocí PolyBase. Po dokončení kopírování průběžných dat ve službě Azure Storage je automaticky vyčištěna. 

    ![Konfigurovat pracovní](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. V **Nová propojená služba** , vyberte svůj účet úložiště a vyberte **Dokončit**.
   
    ![Konfigurace úložiště Azure](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. V **upřesňující nastavení** část, zrušte výběr **použít výchozí typ** možnost a pak vyberte **Další**.

    ![Konfigurace funkcí PolyBase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

12. V **Souhrn** , zkontrolujte nastavení a vyberte **Další**:

    ![Stránka souhrnu](./media/load-azure-sql-data-warehouse/summary-page.png)
13. V **stránka nasazení**, vyberte **monitorování** k monitorování kanálu (úlohy):

    ![Stránka Nasazení](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. **Akce** sloupec obsahuje odkazy, chcete-li zobrazit podrobnosti o spuštění aktivity a znovu spusťte kanál: 

    ![Monitorování spuštění kanálu](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
15. Chcete-li zobrazit běh aktivit, které jsou spojené s kanálu spustit, vyberte **zobrazení aktivity spouští** odkaz v **akce** sloupce. Chcete-li přepnout zpět do kanálu spustí zobrazení, vyberte **kanály** odkaz v horní části. Seznam můžete aktualizovat výběrem možnosti **Aktualizovat**. 

    ![Monitorování spuštění aktivit](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

16. Ke sledování provádění podrobnosti pro každou aktivitu kopírování, vyberte **podrobnosti** v části **akce** v aktivitě zobrazení monitorování. Dokáže monitorovat podrobné údaje, jako objem dat zkopíruje ze zdroje jímka, propustnost dat, provádění kroky s odpovídající doba trvání a použít konfigurace:

    ![Podrobnosti o spuštění aktivity monitorování](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Další postup

Přechodu na v následujícím článku na další informace o podpoře Azure SQL Data Warehouse: 

> [!div class="nextstepaction"]
>[Konektor služby Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)
