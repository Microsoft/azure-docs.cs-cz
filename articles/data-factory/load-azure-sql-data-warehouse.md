---
title: Načtení dat do služby Azure Synapse Analytics
description: Použití Azure Data Factory ke kopírování dat do služby Azure synapse Analytics
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: 12630e5e4b332d875a75c59d2fdafecd23be0b17
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97005420"
---
# <a name="load-data-into-azure-synapse-analytics-by-using-azure-data-factory"></a>Načtení dat do služby Azure synapse Analytics pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) je cloudová, škálovatelná databáze, která dokáže zpracovávat obrovské objemy dat, a to jak v relačních, tak i v nerelačních sítích. Azure synapse Analytics je postavená na architektuře MPP (Theed Parallel Processing), která je optimalizovaná pro úlohy podnikového datového skladu. Nabízí cloudovou flexibilitu, díky které můžete nezávisle škálovat úložiště a výpočetní výkon.

Začínáme s Azure synapse Analytics je teď při použití Azure Data Factory jednodušší než kdy dřív. Azure Data Factory je plně spravovaná cloudová služba pro integraci dat. Službu můžete použít k naplnění analýzy Azure synapse daty ze stávajícího systému a ušetřit čas při vytváření analytických řešení.

Azure Data Factory nabízí následující výhody pro načítání dat do služby Azure synapse Analytics:

* **Snadné nastavení**: intuitivní průvodce 5 kroky bez nutnosti skriptování.
* **Bohatá Podpora úložiště dat**: Integrovaná podpora pro bohatou sadu místních a cloudových úložišť dat. Podrobný seznam najdete v tabulce [podporovaných úložišť dat](copy-activity-overview.md#supported-data-stores-and-formats).
* **Zabezpečení a dodržování předpisů**: data se přenáší přes protokol HTTPS nebo ExpressRoute. Přítomnost globální služby zajišťuje, že vaše data nikdy neopustí zeměpisnou hranici.
* **Neparalelní výkon pomocí základu**: základem je nejúčinnější způsob, jak přesouvat data do Azure synapse Analytics. Pomocí funkce pracovního objektu blob můžete dosáhnout vysoké rychlosti zatížení ze všech typů úložišť dat, včetně služby Azure Blob Storage a Data Lake Store. (Základem podporuje službu Azure Blob Storage a Azure Data Lake Store ve výchozím nastavení.) Podrobnosti najdete v tématu o [výkonu aktivity kopírování](copy-activity-performance.md).

V tomto článku se dozvíte, jak pomocí nástroje Data Factory Kopírování dat _načíst data z Azure SQL Database do služby Azure synapse Analytics_. Můžete postupovat podle podobných kroků a kopírovat data z jiných typů úložišť dat.

> [!NOTE]
> Další informace najdete v tématu [kopírování dat do nebo ze služby Azure synapse Analytics pomocí Azure Data Factory](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Azure synapse Analytics: datový sklad obsahuje data, která se kopírují z databáze SQL. Pokud nemáte Azure synapse Analytics, přečtěte si pokyny v tématu [Vytvoření služby Azure synapse Analytics](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md).
* Azure SQL Database: v tomto kurzu se zkopírují data z ukázkové datové sady Adventure Works LT v Azure SQL Database. Tuto ukázkovou databázi můžete v SQL Database vytvořit podle pokynů v části [vytvoření ukázkové databáze v Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md).
* Účet úložiště Azure: Azure Storage se používá jako _pracovní_ objekt BLOB v operaci hromadného kopírování. Pokud účet úložiště Azure nemáte, přečtěte si pokyny v tématu [Vytvoření účtu úložiště](../storage/common/storage-account-create.md).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **vytvořit prostředek**  >  **data a analýzy**  >  **Data Factory**:

2. Na stránce **Nová datová továrna** zadejte hodnoty pro následující položky:

    * **Název**: jako název zadejte *LoadSQLDWDemo* . Název vaší datové továrny musí být * globálně jedinečný. Pokud se zobrazí chyba "název objektu pro vytváření dat" LoadSQLDWDemo "není k dispozici", zadejte jiný název pro objekt pro vytváření dat. Můžete například použít název _**Your**_**ADFTutorialDataFactory**. Zkuste vytvořit datovou továrnu znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné**: vyberte předplatné Azure, ve kterém chcete vytvořit datovou továrnu. 
    * **Skupina prostředků**: v rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte možnost **vytvořit novou** a zadejte název skupiny prostředků. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
    * **Verze**: Vyberte **V2**.
    * **Umístění**: vyberte umístění pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat, která služba Data Factory používá, můžou být v jiných umístěních a oblastech. Mezi Tato úložiště dat patří Azure Data Lake Store, Azure Storage, Azure SQL Database a tak dále.

3. Vyberte **Vytvořit**.
4. Až se vytváření dokončí, přejdete do vaší datové továrny. Zobrazí se Domovská stránka **Data Factory** , jak je znázorněno na následujícím obrázku:

   ![Domovská stránka objektu pro vytváření dat](./media/doc-common-process/data-factory-home-page.png)

   Výběrem dlaždice **Author & Monitor** (Vytvořit a monitorovat) otevřete na samostatné kartě aplikaci pro integraci dat.

## <a name="load-data-into-azure-synapse-analytics"></a>Načtení dat do služby Azure Synapse Analytics

1. Na stránce **Začínáme** vyberte dlaždici **Kopírovat data**. Spustí se nástroj pro kopírování dat.

2. Na stránce **vlastnosti** zadejte **CopyFromSQLToSQLDW** pro pole **název úlohy** a vyberte **Další**.

    ![Stránka Vlastnosti](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

3. Na stránce **zdrojové úložiště dat** proveďte následující kroky:
    >[!TIP]
    >V tomto kurzu použijete *ověřování SQL* jako typ ověřování pro zdrojové úložiště dat, ale v případě potřeby můžete vybrat jiné podporované metody ověřování:*instanční objekt* a *spravovaná identita* . Podrobnosti najdete v odpovídajících částech [tohoto článku](./connector-azure-sql-database.md#linked-service-properties) .
    >K bezpečnému ukládání tajných kódů pro úložiště dat je také vhodné použít Azure Key Vault. Podrobnější ilustrace najdete v [tomto článku](./store-credentials-in-key-vault.md) .

    a. klikněte na **+ vytvořit nové připojení**.

    b. V galerii vyberte **Azure SQL Database** a pak vyberte **pokračovat**. Můžete zadat text "SQL" do vyhledávacího pole, chcete-li filtrovat konektory.

    ![Výběr Azure SQL DB](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Na stránce **Nová propojená služba** vyberte v rozevíracím seznamu název serveru a název databáze a zadejte uživatelské jméno a heslo. Kliknutím na **Test připojení** ověřte nastavení a pak vyberte **vytvořit**.

    ![Konfigurace Azure SQL DB](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Vyberte nově vytvořenou propojenou službu jako zdroj a pak klikněte na **Next** (Další).

4. V tabulce **Vybrat tabulky, ze kterých se mají kopírovat data, nebo použijte vlastní dotaz** , zadejte **tabulky SalesLT** k filtrování tabulek. Zvolte pole **(Vybrat vše)** , chcete-li použít všechny tabulky pro kopii, a poté vyberte možnost **Další**.

    ![Vybrat zdrojové tabulky](./media/load-azure-sql-data-warehouse/select-source-tables.png)

5. Na stránce **použít filtr** určete nastavení nebo vyberte **Další**.

6. Na stránce **cílové úložiště dat** proveďte následující kroky:
    >[!TIP]
    >V tomto kurzu použijete *ověřování SQL* jako typ ověřování pro cílové úložiště dat, ale v případě potřeby můžete vybrat jiné podporované metody ověřování:*instanční objekt* a *spravovaná identita* . Podrobnosti najdete v odpovídajících částech [tohoto článku](./connector-azure-sql-data-warehouse.md#linked-service-properties) .
    >K bezpečnému ukládání tajných kódů pro úložiště dat je také vhodné použít Azure Key Vault. Podrobnější ilustrace najdete v [tomto článku](./store-credentials-in-key-vault.md) .

    a. Kliknutím na **+ Create new connection** (+ Vytvořit nové připojení) přidejte připojení.

    b. Z Galerie vyberte **Azure synapse Analytics** a vyberte **pokračovat**.

    ![Výběr služby Azure synapse Analytics](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Na stránce **Nová propojená služba** vyberte v rozevíracím seznamu název serveru a název databáze a zadejte uživatelské jméno a heslo. Kliknutím na **Test připojení** ověřte nastavení a pak vyberte **vytvořit**.

    ![Konfigurace Azure synapse Analytics](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Vyberte nově vytvořenou propojenou službu jako jímku a klikněte na **Next** (Další).

7. Na stránce **mapování tabulek** zkontrolujte obsah a vyberte **Další**. Zobrazí se mapování inteligentní tabulky. Zdrojové tabulky jsou namapovány na cílové tabulky založené na názvech tabulek. Pokud zdrojová tabulka v cíli neexistuje, Azure Data Factory ve výchozím nastavení vytvoří cílovou tabulku se stejným názvem. Zdrojovou tabulku můžete také namapovat na existující cílovou tabulku.

   ![Stránka Mapování tabulek](./media/load-azure-sql-data-warehouse/table-mapping.png)

8. Na stránce **mapování sloupců** zkontrolujte obsah a vyberte **Další**. Mapování inteligentní tabulky je založené na názvu sloupce. Pokud necháte Data Factory automaticky vytvořit tabulky, převod datového typu se může vyskytnout v případě nekompatibility mezi zdrojovým a cílovým úložištěm. Pokud je mezi zdrojovým a cílovým sloupcem převod nepodporovaného datového typu, zobrazí se vedle odpovídající tabulky chybová zpráva.

    ![Stránka mapování sloupců](./media/load-azure-sql-data-warehouse/schema-mapping.png)

9. Na stránce **Nastavení** proveďte následující kroky:

    a. V části **pracovní nastavení** klikněte na **+ Nová** a nové pracovní úložiště. Úložiště se používá pro přípravu dat před jejich načtením do Azure synapse Analytics pomocí základu. Po dokončení kopírování se dočasná data v Azure Blob Storage automaticky vyčistí.

    b. Na stránce **Nová propojená služba** vyberte svůj účet úložiště a vyberte **vytvořit** a nasaďte propojenou službu.

    c. V části **Upřesnit nastavení** zrušte výběr možnosti **použít výchozí typ** a pak vyberte **Další**.

    ![Konfigurovat základ](./media/load-azure-sql-data-warehouse/configure-polybase.png)

10. Na stránce **Souhrn** zkontrolujte nastavení a klikněte na tlačítko **Další**.

    ![Stránka souhrnu](./media/load-azure-sql-data-warehouse/summary-page.png)

11. Na stránce **Nasazení** vyberte **Monitorovat** a začněte monitorovat kanál (úlohu). 
 
12. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. Po úspěšném dokončení kanálu vyberte odkaz **CopyFromSQLToSQLDW** pod sloupcem **název kanálu** , abyste zobrazili podrobnosti o spuštění aktivit nebo znovu spustíte kanál.

    [![Monitorování spuštění kanálu](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png#lightbox)

13. Pokud chcete přejít zpátky k zobrazení spuštění kanálu, vyberte odkaz **všechny spuštění kanálu** v horní části. Seznam můžete aktualizovat kliknutím na **Aktualizovat**.

    ![Monitorování spuštění aktivit](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

14. Chcete-li monitorovat podrobnosti o spuštění každé aktivity kopírování, vyberte odkaz **Podrobnosti** (ikona brýlí) v části **název aktivity** v zobrazení spuštění aktivit. Můžete monitorovat podrobnosti, jako je objem dat zkopírovaných ze zdroje, do jímky, propustnosti dat, kroky provádění s odpovídající dobou trvání a používané konfigurace.
    ![Nejprve monitorovat podrobnosti o spuštění aktivit](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png)

    ![Podrobnosti o spuštění aktivity monitorování – sekunda](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png)

## <a name="next-steps"></a>Další kroky

V následujícím článku se dozvíte o podpoře Azure synapse Analytics:

> [!div class="nextstepaction"]
>[Konektor Azure synapse Analytics](connector-azure-sql-data-warehouse.md)