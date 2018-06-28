---
title: Načtení terabajtů dat do SQL Data Warehouse | Microsoft Docs
description: Ukazuje, jak 1 TB dat je možné načíst do Azure SQL Data Warehouse pomocí Azure Data Factory v části 15 minut
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6a7f31cf541bc1cccd3a5d565a0d3a223ccd3aee
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045163"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>Načtení 1 TB do Azure SQL Data Warehouse pomocí služby Data Factory v části 15 minut
> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [zkopírovat data do nebo z Azure SQL Data Warehouse pomocí služby Data Factory](../connector-azure-sql-data-warehouse.md).


[Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) je databáze založená na cloudu, škálování dokáže zpracovávat ohromné objemy dat, relačních i nerelačních.  Postavená na architektuře massively parallel processing (MPP), SQL Data Warehouse je optimalizovaná pro podnikové datového skladu úlohy.  Pružnost cloudu nabízí flexibilitu škálovat úložiště a výpočetní nezávisle.

Začínáme s Azure SQL Data Warehouse je teď jednodušší než kdy použití **Azure Data Factory**.  Azure Data Factory je plně spravovaná Cloudová datová integrační služby, která slouží k naplnění SQL Data Warehouse s daty z vašeho stávajícího systému a ukládání je hodně času při vyhodnocení SQL Data Warehouse a sestavování analytické údaje řešení. Tady jsou klíčové výhody načítání dat do Azure SQL Data Warehouse pomocí Azure Data Factory:

* **Snadno nastavit**: krok 5 intuitivní průvodce bez potřeby skriptování.
* **Podpora úložiště bohaté dat**: integrovanou podporu pro bohatou sadu místní a Cloudová datová úložiště.
* **Zabezpečení a dodržování**: data se přenáší prostřednictvím protokolu HTTPS nebo ExpressRoute a přítomnost globální služby zajišťuje vaše data nikdy neopustí zeměpisné hranic
* **Bezkonkurenční výkonu pomocí funkce PolyBase** – pomocí Polybase je nejúčinnější způsob, jak přesunout data do Azure SQL Data Warehouse. Používá funkci pracovní objekt blob, můžete dosáhnout vysokého zatížení rychlosti ze všech typů úložišť dat kromě úložiště objektů Blob v Azure, což Polybase podporuje ve výchozím nastavení.

Tento článek ukazuje, jak používat Průvodce kopírováním služby Data Factory k načtení 1 TB dat z Azure Blob Storage do Azure SQL Data Warehouse v části 15 minut, při propustnost přes 1,2 GB/s.

Tento článek obsahuje podrobné pokyny pro přesun dat do Azure SQL Data Warehouse pomocí Průvodce kopírováním.

> [!NOTE]
>  Obecné informace o možnostech objektu pro vytváření dat při přesunu dat do/z Azure SQL Data Warehouse najdete v tématu [přesun dat do a z Azure SQL Data Warehouse pomocí Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) článku.
>
> Můžete také vytvořit pomocí portálu Azure, Visual Studio, prostředí PowerShell, kanály atd. V tématu [kurz: kopírování dat z objektu Blob Azure do Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) rychlé návod s podrobnými pokyny pro používání aktivity kopírování v Azure Data Factory.  
>
>

## <a name="prerequisites"></a>Požadavky
* Azure Blob Storage: Tento experiment používá Azure Blob úložiště (GRS) pro ukládání TPC-H testování datovou sadu.  Pokud nemáte účet úložiště Azure, přečtěte si [postup vytvoření účtu úložiště](../../storage/common/storage-create-storage-account.md#create-a-storage-account).
* [TPC-H](http://www.tpc.org/tpch/) data: budeme používat TPC-H jako testování datovou sadu.  K tomu, budete muset použít `dbgen` z TPC-H nástrojů, který umožňuje generovat datovou sadu.  Můžete buď stáhnout zdrojového kódu pro `dbgen` z [TPC nástroje](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) a zkompilovat sami nebo stáhnout kompilované binárního souboru z [Githubu](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  Spusťte dbgen.exe s následující příkazy ke generování plochého souboru 1 TB pro `lineitem` tabulky šíření mezi 10 souborů:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Nyní zkopírujte soubory do objektu Blob Azure.  Odkazovat na [přesun dat do a ze v místním systému souborů pomocí Azure Data Factory](data-factory-onprem-file-system-connector.md) jak to udělat pomocí kopírování ADF.    
* Azure SQL Data Warehouse: tohoto experimentu načte data do Azure SQL Data Warehouse vytvořené pomocí 6000 Dwu

    Odkazovat na [vytvoření Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) podrobné pokyny o tom, jak vytvořit databázi SQL Data Warehouse.  Chcete-li získat nejlepšího výkonu dosáhnete možné zatížení do SQL Data Warehouse pomocí Polybase, jsme zvolte maximální počet jednotek datového skladu (Dwu) povolen v nastavení výkonu, který je 6000 Dwu.

  > [!NOTE]
  > Při načítání z Azure Blob, načítání výkonu dat je přímo úměrná počtu jednotek Dwu, které nakonfigurujete v SQL Data Warehouse:
  >
  > Načtení 1 TB do 1000 DWU SQL Data Warehouse přebírá načítání 1 TB 87 minut (propustnost ~ 200 MB/s) do 2 000 DWU SQL Data Warehouse trvá 46 minut (propustnost ~ 380 MB/s) načítání 1 TB do 6000 DWU SQL Data Warehouse přebírá 14 minut (propustnost ~1.2 GB/s)
  >
  >

    Chcete-li vytvořit SQL Data Warehouse s 6000 Dwu, posuňte jezdec výkonu úplně napravo:

    ![Výkon posuvníku](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Pro existující databázi, který není konfigurován s 6000 Dwu můžete postupně škálovat ji pomocí portálu Azure.  Přejděte do databáze na portálu Azure a je **škálování** v tlačítko **přehled** panelu vidět na následujícím obrázku:

    ![Stupnice – tlačítko](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Klikněte **škálování** otevřete následující panely, přesuňte jezdec na maximální hodnotu, a klikněte na tlačítko **Uložit** tlačítko.

    ![Dialogové okno škálování](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Tento experiment načte data do Azure SQL Data Warehouse pomocí `xlargerc` Třída prostředků.

    K dosažení nejlepší možné propustnost, je nutné provést pomocí SQL Data Warehouse uživatel patřící do kopie `xlargerc` Třída prostředků.  Zjistěte, jak to provést pomocí následujících [změnit v příkladu třída prostředků uživatele](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* Vytvoření schématu cílové tabulky v databázi Azure SQL Data Warehouse spuštěním následujícího příkazu DDL:

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
O postupech, pomocí požadovaných součástí byla dokončena jsme nyní připraveni ke konfiguraci aktivitou kopírování pomocí Průvodce kopírováním.

## <a name="launch-copy-wizard"></a>Spuštění průvodce kopírováním
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **vytvořit prostředek** v levém horním rohu klikněte na **Intelligence + analýzy**a klikněte na tlačítko **Data Factory**.
3. V **nový objekt pro vytváření dat** podokně:

   1. Zadejte **LoadIntoSQLDWDataFactory** pro **název**.
       Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chyba: **název objektu pro vytváření dat "LoadIntoSQLDWDataFactory" není k dispozici**, změňte název objektu pro vytváření dat (například yournameLoadIntoSQLDWDataFactory) a zkuste to znovu. V tématu [Objekty pro vytváření dat – pravidla pojmenování](data-factory-naming-rules.md) najdete pravidla pojmenování artefaktů služby Data Factory.  
   2. Vyberte své **předplatné** Azure.
   3. Pro skupinu prostředků proveďte jeden z následujících kroků:
      1. Vyberte možnost **Použít existující** a vyberte existující skupinu prostředků.
      2. Vyberte možnost **Vytvořit nový** a zadejte název pro skupinu prostředků.
   4. Vyberte **umístění** pro příslušný objekt pro vytváření dat.
   5. Zaškrtněte políčko **Připnout na řídicí panel** v dolní části okna.  
   6. Klikněte na možnost **Vytvořit**.
4. Po vytvoření se zobrazí okno **Objekt pro vytváření dat**, jak je znázorněno na následujícím obrázku:

   ![Domovská stránka objektu pro vytváření dat](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Na domovské stránce objektu pro vytváření dat klikněte na dlaždici **Kopírovat data**. Spustí se **průvodce kopírováním**.

   > [!NOTE]
   > Pokud zjistíte, že se webový prohlížeč zasekl ve fázi „Autorizace…“, zakažte / zrušte zaškrtnutí políčka **Block third party cookies and site data** (Blokovat data souborů cookie a webů třetích stran) nebo nechte toto nastavení povolené a vytvořte výjimku pro **login.microsoftonline.com**. Potom zkuste průvodce znovu spustit.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Krok 1: Konfigurace plánu pro načítání dat
Prvním krokem je konfigurace dat načítání plánu.  

Na stránce **Vlastnosti**:

1. Zadejte **CopyFromBlobToAzureSqlDataWarehouse** pro **název úlohy**
2. Vyberte **spustit jednou nyní** možnost.   
3. Klikněte na **Další**.  

    ![Průvodce kopírováním – stránky vlastností](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Krok 2: Konfigurace zdroje
Tato část popisuje postup konfigurace zdroj: Azure Blob obsahující TPC 1 TB-H položky na řádku soubory.

1. Vyberte **Azure Blob Storage** jako úložiště dat a klikněte na tlačítko **Další**.

    ![Průvodce kopírováním - vyberte zdroj stránky](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Zadejte informace o připojení pro účet úložiště objektů Blob v Azure a klikněte na tlačítko **Další**.

    ![Průvodce kopírováním - informace o připojení zdroje](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Vyberte **složky** obsahující TPC-H řádek souborů položek a klikněte na tlačítko **Další**.

    ![Průvodce kopírováním – vyberte vstupní složky](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Po kliknutí na tlačítko **Další**, nastavení formátu souboru se rozpoznávají automaticky.  Zkontrolujte, zkontrolujte, zda je tento sloupec oddělovač ' | 'místo výchozí čárkou','.  Klikněte na tlačítko **Další** po mít zobrazení náhledu data.

    ![Průvodce kopírováním – nastavení formátu souboru](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Krok 3: Konfigurace cílového
V této části se dozvíte, jak nakonfigurovat cíl: `lineitem` tabulky v databázi Azure SQL Data Warehouse.

1. Zvolte **Azure SQL Data Warehouse** jako cíl uložení a klikněte na tlačítko **Další**.

    ![Průvodce kopírováním - vyberte cílového úložiště dat](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Zadejte informace o připojení pro Azure SQL Data Warehouse.  Je nutné zadat uživatele, který je členem role `xlargerc` (najdete v článku **požadavky** části Podrobné pokyny) a klikněte na tlačítko **Další**.

    ![Průvodce kopírováním - informace o cílovém připojení](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Vyberte cílové tabulky a klikněte na **Další**.

    ![Zkopírujte - tabulky mapování stránku průvodce](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Schéma mapování stránce, nechte nezaškrtnuté možnost "Použijí mapování sloupce" a klikněte na tlačítko **Další**.

## <a name="step-4-performance-settings"></a>Krok 4: Nastavení výkonu

**Povolit polybase** je ve výchozím nastavení zaškrtnuto.  Klikněte na **Další**.

![Zkopírujte – schéma mapování stránku průvodce](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Krok 5: Nasazení a monitorování zatížení výsledky
1. Klikněte na tlačítko **Dokončit** tlačítko pro nasazení.

    ![Průvodce kopírováním - souhrnná stránka](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Po dokončení nasazení klikněte na tlačítko `Click here to monitor copy pipeline` monitorování kopie spustit průběh. Vyberte kanál kopírování jste vytvořili v **aktivity Windows** seznamu.

    ![Průvodce kopírováním - souhrnná stránka](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Můžete zobrazit podrobnosti o spuštění kopie **aktivity okno Průzkumníka** v pravém panelu, včetně datový svazek číst ze zdroje a zapsána do cílového, doba trvání a průměrná propustnost pro spuštění.

    Jak je vidět na následujícím snímku obrazovky, kopírování 1 TB z Azure Blob Storage do SQL Data Warehouse trvalo 14 minut, efektivně dosahování 1.22 GB/s propustnosti!

    ![Průvodce kopírováním – dialogové okno úspěšně vytvořila.](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Osvědčené postupy
Tady je několik osvědčených postupů pro spuštění databáze Azure SQL Data Warehouse:

* Při načítání do CLUSTEROVANÝ INDEX COLUMNSTORE, použijte větší Třída prostředků.
* Pro efektivnější spojení zvažte použití algoritmu hash distribuce podle vyberte sloupce místo výchozí round robin distribuční.
* Vyšší rychlost zatížení zvažte použití haldy pro přechodný data.
* Vytvoření statistiky po dokončení načítání Azure SQL Data Warehouse.

V tématu [osvědčené postupy pro Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md) podrobnosti.

## <a name="next-steps"></a>Další postup
* [Průvodce kopírováním služby Data Factory](data-factory-copy-wizard.md) – Tento článek obsahuje podrobné informace o Průvodci kopírováním.
* [Zkopírujte aktivity výkonu a vyladění průvodce](data-factory-copy-activity-performance.md) – Tento článek obsahuje referenční měření výkonu a vyladění průvodce.
