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
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 49ba61ba8cf68a39eef21b1939a3e8a6c92f8827
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
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
>
> Tento článek se týká verze 2 služby Azure Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [aktivitu kopírování v Azure Data Factory verze 1](v1/data-factory-data-movement-activities.md).

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
    * **Verze**: vyberte **V2 (Preview)**.
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
3. V **zdrojového úložiště dat** vyberte **Azure SQL Database**a vyberte **Další**:

    ![Stránka Zdrojové úložiště dat](./media/load-azure-sql-data-warehouse/specify-source.png)
4. Na stránce **Zadejte databázi SQL Azure** proveďte následující kroky: 
   1. Jako **Název serveru** vyberte váš server SQL Azure.
   2. Jako **Název databáze** vyberte vaši databázi SQL Azure.
   3. Jako **Uživatelské jméno** zadejte jméno uživatele.
   4. Zadejte heslo uživatele pro **heslo**.
   5. Vyberte **Next** (Další).
   
   ![Zadejte databáze Azure SQL](./media/load-azure-sql-data-warehouse/specify-source-connection.png)
5. V **vyberte tabulky, ze kterého chcete zkopírovat data nebo použít vlastní dotaz** zadejte **SalesLT** vyfiltrujete tabulky. Vyberte **(Vybrat vše)** používat všechny tabulky kopie a poté vyberte **Další**: 

    ![Vyberte zdrojové tabulky](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. V **cílového úložiště dat** vyberte **Azure SQL Data Warehouse**a vyberte **Další**:

    ![Stránka Cílové úložiště dat](./media/load-azure-sql-data-warehouse/specify-sink.png)
7. V **zadejte Azure SQL Data Warehouse** proveďte následující kroky: 

   1. Jako **Název serveru** vyberte váš server SQL Azure.
   2. Vyberte datový sklad SQL Azure pro **název databáze**.
   3. Jako **Uživatelské jméno** zadejte jméno uživatele.
   4. Zadejte heslo uživatele pro **heslo**.
   5. Vyberte **Next** (Další).
   
   ![Zadejte Azure SQL Data Warehouse](./media/load-azure-sql-data-warehouse/specify-sink-connection.png)
8. V **mapování tabulky** , zkontrolujte obsah a vyberte **Další**. Mapování u inteligentního tabulky zobrazí. Zdrojové tabulky jsou namapované na cílové tabulky podle názvů tabulek. Pokud zdrojová tabulka neexistuje v cílovém, Azure Data Factory vytvoří cílové tabulky se stejným názvem ve výchozím nastavení. Zdrojová tabulka můžete také mapovat existující cílové tabulky. 

   > [!NOTE]
   > Vytvoření automatického tabulky pro jímku SQL Data Warehouse použitelná v případě systému SQL Server nebo Azure SQL Database je zdrojem. Při kopírování dat z jiného zdrojového úložiště dat, musíte před provedením kopírování dat předem vytvořit schéma v jímce Azure SQL Data Warehouse.

   ![Stránka Mapování tabulek](./media/load-azure-sql-data-warehouse/specify-table-mapping.png)

9. V **schéma mapování** , zkontrolujte obsah a vyberte **Další**. Mapování inteligentního tabulky je založen na názvu sloupce. Pokud necháte Data Factory automaticky vytvářet tabulky, převod typů dat může dojít, když došlo k problémům s kompatibilitou mezi zdrojovým a cílovým úložiště. Pokud konverzi nepodporovaný datový typ mezi zdrojovým a cílovým sloupcem se zobrazí chybová zpráva vedle odpovídající tabulky.

    ![Stránka Mapování schématu](./media/load-azure-sql-data-warehouse/specify-schema-mapping.png)

11. V **nastavení** vyberte účet úložiště Azure ve **název účtu úložiště** rozevíracího seznamu. Účet se používá pro přípravu dat, než se načte do SQL Data Warehouse pomocí PolyBase. Po dokončení kopírování průběžných dat ve službě Azure Storage je automaticky vyčištěna. V části **upřesňující nastavení**, zrušte výběr **použít výchozí typ** možnost:

    ![Stránka Nastavení](./media/load-azure-sql-data-warehouse/copy-settings.png)
12. V **Souhrn** , zkontrolujte nastavení a vyberte **Další**:

    ![Stránka souhrnu](./media/load-azure-sql-data-warehouse/summary-page.png)
13. V **stránka nasazení**, vyberte **monitorování** k monitorování kanálu (úlohy):

    ![Stránka Nasazení](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. **Akce** sloupec obsahuje odkazy, chcete-li zobrazit podrobnosti o spuštění aktivity a znovu spusťte kanál: 

    ![Monitorování spuštění kanálu](./media/load-azure-sql-data-warehouse/monitor-pipeline-run.png)
15. Chcete-li zobrazit běh aktivit, které jsou spojené s kanálu spustit, vyberte **zobrazení aktivity spouští** odkaz v **akce** sloupce. Jsou 10 kopie aktivity v kanálu a každá aktivita zkopíruje jednu tabulku dat. Chcete-li přepnout zpět do kanálu spustí zobrazení, vyberte **kanály** odkaz v horní části. Seznam můžete aktualizovat výběrem možnosti **Aktualizovat**. 

    ![Monitorování spuštění aktivit](./media/load-azure-sql-data-warehouse/monitor-activity-run.png)

16. Ke sledování provádění podrobnosti pro každou aktivitu kopírování, vyberte **podrobnosti** v části **akce** v aktivitě zobrazení monitorování. Dokáže monitorovat podrobné údaje, jako objem dat zkopíruje ze zdroje jímka, propustnost dat, provádění kroky s odpovídající doba trvání a použít konfigurace:

    ![Podrobnosti o spuštění aktivity monitorování](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Další postup

Přechodu na v následujícím článku na další informace o podpoře Azure SQL Data Warehouse: 

> [!div class="nextstepaction"]
>[Konektor služby Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)