---
title: Načtení terabajtů dat do SQL Data Warehouse
description: Ukazuje, jak může být 1 TB dat načteno do Azure SQL Data Warehouse za 15 minut s Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0bef6b5e87e7f0964989db371014c305b97f1d12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81419302"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>Načtěte 1 TB do Azure SQL Data Warehouse za 15 minut s Data Factory
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [kopírování dat do nebo z Azure SQL Data Warehouse pomocí Data Factory](../connector-azure-sql-data-warehouse.md).


[Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) je cloudová, škálovatelná databáze, která dokáže zpracovávat obrovské objemy dat, jak relační, tak i nerelační.  SQL Data Warehouse je pro úlohy podnikového datového skladu postavená na architektuře MPP (hromadně paralelní zpracování).  Nabízí cloudovou flexibilitu, díky které můžete nezávisle škálovat úložiště a výpočetní výkon.

Seznámení s Azure SQL Data Warehouse je teď v **Azure Data Factory**jednodušší než kdy dřív.  Azure Data Factory je plně spravovaná cloudová služba pro integraci dat, která se dá použít k naplnění SQL Data Warehouse s daty ze stávajícího systému a šetří vám tak cenné časy při vyhodnocování SQL Data Warehouse a vytváření analytických řešení. Tady jsou klíčové výhody načítání dat do Azure SQL Data Warehouse pomocí Azure Data Factory:

* **Snadné nastavení**: 5 – intuitivní průvodce bez nutnosti skriptování.
* **Bohatá Podpora úložiště dat**: Integrovaná podpora pro bohatou sadu místních a cloudových úložišť dat.
* **Zabezpečení a dodržování předpisů**: data se přenáší přes protokol HTTPS nebo ExpressRoute a přítomnost globální služby zajišťuje, že vaše data nikdy neopustí zeměpisnou hranici.
* **Neparalelní výkon pomocí základu** použití základny je nejúčinnější způsob, jak přesunout data do Azure SQL Data Warehouse. Pomocí funkce pracovního objektu blob můžete dosáhnout vysoké rychlosti načítání ze všech typů úložišť dat kromě úložiště objektů BLOB v Azure, které základem podporuje standardně.

V tomto článku se dozvíte, jak pomocí Průvodce kopírováním Data Factory načíst data o 1 TB z Azure Blob Storage do Azure SQL Data Warehouse během 15 minut při překročení propustnosti 1,2 GB/s.

Tento článek poskytuje podrobné pokyny pro přesouvání dat do Azure SQL Data Warehouse pomocí Průvodce kopírováním.

> [!NOTE]
>  Obecné informace o funkcích Data Factory při přesunu dat do a z Azure SQL Data Warehouse najdete v tématu [přesun dat do a z Azure SQL Data Warehouse pomocí Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) článku.
>
> Kanály můžete vytvářet také pomocí sady Visual Studio, PowerShellu atd. Podrobný návod k použití aktivity kopírování v Azure Data Factory najdete v tématu [kurz: kopírování dat z objektu blob Azure do Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .  
>
>

## <a name="prerequisites"></a>Požadavky
* Azure Blob Storage: Tento experiment používá Azure Blob Storage (GRS) pro ukládání testovací datové sady TPC-H.  Pokud nemáte účet úložiště Azure, přečtěte si, [jak vytvořit účet úložiště](../../storage/common/storage-account-create.md).
* [TPC-h](http://www.tpc.org/tpch/) data: používáme TPC-H jako testovací datovou sadu.  K tomu je nutné použít `dbgen` z TPC-H Toolkit, který vám pomůže vygenerovat datovou sadu.  Můžete si buď stáhnout zdrojový kód `dbgen` z [nástrojů TPC](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) a zkompilovat ho sami, nebo stáhnout zkompilovaný binární soubor z [GitHubu](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TPCHTools).  Spusťte dbgen. exe s následujícími příkazy pro vygenerování plochého souboru 1 TB `lineitem` pro rozložení tabulky mezi 10 soubory:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Nyní zkopírujte generované soubory do objektu blob Azure.  Informace o tom, jak to udělat pomocí kopírování ADF, najdete [v tématu přesun dat do a z místního systému souborů pomocí Azure Data Factory](data-factory-onprem-file-system-connector.md) .    
* Azure SQL Data Warehouse: Tento experiment načte data do Azure SQL Data Warehouse vytvořených pomocí 6 000 DWU

    Podrobné pokyny k vytvoření databáze SQL Data Warehouse najdete v tématu věnovaném [vytvoření Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) .  Abychom dosáhli nejlepšího možného zatížení při SQL Data Warehouse používáním základny, zvolíme v nastavení výkonu maximální počet jednotek datového skladu (DWU), což je 6 000 DWU.

  > [!NOTE]
  > Při načítání z objektu blob Azure je výkon načítání dat přímo úměrný počtu DWU, které nakonfigurujete na SQL Data Warehouse:
  >
  > Načtení 1 TB do 1 000 DWU SQL Data Warehouse trvá 87 minut (~ 200 MB/s propustnost) načítání 1 TB do 2 000 DWU SQL Data Warehouse trvá 46 minut (přibližně MB/s propustnost) načítání 1 TB do 380 DWU SQL Data Warehouse trvá 14 minut (propustnost 6 000 GB/s).
  >
  >

    Chcete-li vytvořit SQL Data Warehouse s 6 000 DWU, přesuňte posuvník výkon úplně vpravo:

    ![Posuvník výkonu](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Pro existující databázi, která není nakonfigurovaná s 6 000 DWU, můžete ji škálovat pomocí Azure Portal.  Přejděte do databáze v Azure Portal a na panelu **Přehled** se zobrazí tlačítko **škálování** , které vidíte na následujícím obrázku:

    ![Tlačítko škálovat](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Kliknutím na tlačítko **škálovat** otevřete následující panel, přesunete posuvník na maximální hodnotu a kliknete na tlačítko **Uložit** .

    ![Dialogové okno škála](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Tento experiment načte data do Azure SQL Data Warehouse pomocí `xlargerc` třídy prostředků.

    Chcete-li dosáhnout nejlepší možné propustnosti, je třeba provést kopírování pomocí SQL Data Warehouse uživatele, `xlargerc` který patří do třídy prostředků.  Přečtěte si, jak postupovat podle následujícího postupu: [Změna třídy prostředků uživatele](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* Spuštěním následujícího příkazu DDL vytvořte v databázi Azure SQL Data Warehouse schéma cílové tabulky:

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
  Po dokončení požadovaných součástí jsme teď připraveni ke konfiguraci aktivity kopírování pomocí Průvodce kopírováním.

## <a name="launch-copy-wizard"></a>Spuštění průvodce kopírováním
1. Přihlaste se k [Azure Portal](https://portal.azure.com).
2. V levém horním rohu klikněte na **vytvořit prostředek** , klikněte na **Intelligence + Analytics**a pak klikněte na **Data Factory**.
3. V podokně **Nová datová továrna** :

   1. Jako **název**zadejte **LoadIntoSQLDWDataFactory** .
       Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chyba: **název objektu pro vytváření dat "LoadIntoSQLDWDataFactory" není k dispozici**, změňte název objektu pro vytváření dat (třeba na yournameLoadIntoSQLDWDataFactory) a zkuste to znovu. V tématu [Objekty pro vytváření dat – pravidla pojmenování](data-factory-naming-rules.md) najdete pravidla pojmenování artefaktů služby Data Factory.  
   2. Vyberte své **předplatné**Azure.
   3. Pro skupinu prostředků proveďte jeden z následujících kroků:
      1. Vyberte možnost **Použít existující** a vyberte existující skupinu prostředků.
      2. Vyberte možnost **Vytvořit nový** a zadejte název pro skupinu prostředků.
   4. Vyberte **umístění** pro příslušný objekt pro vytváření dat.
   5. Zaškrtněte políčko **Připnout na řídicí panel** v dolní části okna.  
   6. Klikněte na **Vytvořit**.
4. Po dokončení vytváření se zobrazí okno **Data Factory** , jak je znázorněno na následujícím obrázku:

   ![Domovská stránka objektu pro vytváření dat](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Na domovské stránce objektu pro vytváření dat klikněte na dlaždici **Kopírovat data**. Spustí se **průvodce kopírováním**.

   > [!NOTE]
   > Pokud zjistíte, že se webový prohlížeč zasekl ve fázi „Autorizace…“, zakažte / zrušte zaškrtnutí políčka **Block third party cookies and site data** (Blokovat data souborů cookie a webů třetích stran) nebo nechte toto nastavení povolené a vytvořte výjimku pro **login.microsoftonline.com**. Potom zkuste průvodce znovu spustit.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Krok 1: Konfigurace plánu načítání dat
Prvním krokem je konfigurace plánu načítání dat.  

Na stránce **Vlastnosti**:

1. Jako **název úlohy** zadejte **CopyFromBlobToAzureSqlDataWarehouse** .
2. Vyberte možnost **spustit hned** .   
3. Klikněte na **Další**.  

    ![Průvodce kopírováním – stránka vlastností](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Krok 2: Konfigurace zdroje
V této části se dozvíte, jak nakonfigurovat zdroj: Azure BLOB obsahující soubory položek řádků TPC-H 1 TB.

1. Jako úložiště dat vyberte **Azure Blob Storage** a klikněte na **Další**.

    ![Průvodce kopírováním – výběr zdrojové stránky](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Vyplňte informace o připojení pro účet úložiště objektů BLOB v Azure a klikněte na **Další**.

    ![Průvodce kopírováním – informace o zdrojovém připojení](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Vyberte **složku** obsahující soubory položky TPC-H a klikněte na tlačítko **Další**.

    ![Průvodce kopírováním-výběr vstupní složky](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Po kliknutí na tlačítko **Další**se automaticky zjistí nastavení formátu souboru.  Zaškrtněte, pokud chcete, aby byl Oddělovač sloupců | místo výchozí čárky (,).  Až budou data zobrazená, klikněte na **Další** .

    ![Průvodce kopírováním – nastavení formátu souboru](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Krok 3: Konfigurace cíle
V této části se dozvíte, jak nakonfigurovat cíl `lineitem` : tabulka v databázi Azure SQL Data Warehouse.

1. Jako cílové úložiště vyberte **Azure SQL Data Warehouse** a klikněte na **Další**.

    ![Průvodce kopírováním – výběr cílového úložiště dat](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Zadejte informace o připojení pro Azure SQL Data Warehouse.  Ujistěte se, že jste zadali uživatele, který je členem role `xlargerc` (podrobné pokyny najdete v části **požadavky** ), a klikněte na **Další**.

    ![Průvodce kopírováním – informace o cílovém připojení](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Vyberte cílovou tabulku a klikněte na **Další**.

    ![Průvodce kopírováním – stránka mapování tabulek](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Na stránce mapování schématu ponechte nezaškrtnuté políčko použít mapování sloupců a klikněte na **Další**.

## <a name="step-4-performance-settings"></a>Krok 4: nastavení výkonu

Ve výchozím nastavení je zaškrtnuto políčko **povoleno** .  Klikněte na **Další**.

![Průvodce kopírováním – stránka mapování schématu](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Krok 5: nasazení a sledování výsledků načítání
1. Kliknutím na tlačítko **Dokončit** nasadíte.

    ![Průvodce kopírováním – stránka souhrnu](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Po dokončení nasazení kliknutím `Click here to monitor copy pipeline` můžete monitorovat průběh kopírování. Vyberte kanál pro kopírování, který jste vytvořili v seznamu **okna aktivit** .

    ![Průvodce kopírováním – stránka souhrnu](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Podrobnosti o spuštění kopírování můžete zobrazit v **Průzkumníku okna aktivity** v pravém panelu, včetně objemu dat načtených ze zdroje a zapsaných do cíle, doby trvání a průměrné propustnosti pro běh.

    Jak vidíte na následujícím snímku obrazovky, kopírováním 1 TB z Azure Blob Storage do SQL Data Warehouse trvalo 14 minut a efektivně dosáhnete propustnosti 1,22 GB/s.

    ![Průvodce kopírováním – dialogové okno úspěšné](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Osvědčené postupy
Tady je několik osvědčených postupů pro spuštění databáze Azure SQL Data Warehouse:

* Při načítání do CLUSTEROVANÉHO indexu COLUMNSTORE použijte větší třídu prostředků.
* Pro efektivnější spojení zvažte použití distribuce algoritmu hash pomocí sloupce Select namísto výchozí distribuce kruhové dotazování.
* Pro rychlejší rychlost zatížení zvažte použití haldy pro přechodná data.
* Po dokončení načítání Azure SQL Data Warehouse vytvořit statistiku.

Podrobnosti najdete v tématu [osvědčené postupy pro Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md) .

## <a name="next-steps"></a>Další kroky
* [Průvodce kopírováním Data Factory](data-factory-copy-wizard.md) – Tento článek poskytuje podrobné informace o Průvodci kopírováním.
* [Průvodce laděním a výkonem aktivity kopírování](data-factory-copy-activity-performance.md) – Tento článek obsahuje referenční materiály pro měření výkonu a ladění.
