---
title: Terabajty dat do SQL Data Warehouse | Dokumentace Microsoftu
description: Ukazuje, jak 1 TB dat je možné načíst do Azure SQL Data Warehouse pomocí Azure Data Factory v oblasti 15 minut
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 145a1d24e877cc4083706310694005c01c8c8fbf
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020145"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>Načtení 1 TB do Azure SQL Data Warehouse pomocí služby Data Factory v oblasti 15 minut
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [kopírování dat do nebo z Azure SQL Data Warehouse pomocí služby Data Factory](../connector-azure-sql-data-warehouse.md).


[Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) je založené na cloudu, horizontální navýšení kapacity databáze, dokáže zpracovávat ohromné objemy dat, relačních i nerelačních.  Postavená na výkonné architektuře paralelního zpracování (MPP) a SQL Data Warehouse je optimalizovaný pro úlohy datových skladů podnikové.  Nabízí cloudovou elasticitu a flexibilně škálovat úložiště a výpočetní nezávisle na sobě.

Začínáme s Azure SQL Data Warehouse je teď snadnější než kdy dřív pomocí **Azure Data Factory**.  Azure Data Factory je služba pro integraci dat plně spravované cloudové, který slouží k naplnění SQL Data Warehouse s daty ze stávajícího systému a ukládání je drahocenný čas při vyhodnocení SQL Data Warehouse a vytváření analýz řešení. Tady jsou klíčové výhody načítání dat do Azure SQL Data Warehouse pomocí Azure Data Factory:

* **Snadné nastavení**: Krok 5 intuitivní průvodce se žádné skripty povinné.
* **Podpora store velké množství dat**: integrovanou podporu pro bohatou sadu místní a cloudové datové úložiště.
* **Zabezpečení a dodržování**: data se přenáší prostřednictvím protokolu HTTPS nebo ExpressRoute a přítomnost globální služba zajišťuje vaše data nikdy neopustí zeměpisných hranic
* **Bezkonkurenční výkon pomocí PolyBase** – je nejúčinnější způsob pro přesun dat do Azure SQL Data Warehouse pomocí Polybase. Pracovní funkce objektů blob můžete dosáhnout vysoké rychlosti ze všech typů úložišť dat kromě úložiště objektů Blob v Azure, které ve výchozím nastavení podporuje Polybase.

V tomto článku se dozvíte, jak používat Průvodce kopírováním služby Data Factory k načtení 1 TB dat z Azure Blob Storage do Azure SQL Data Warehouse v méně než 15 minut, při více než 1,2 GB/s propustnosti.

Tento článek obsahuje podrobné pokyny pro přesouvání dat do Azure SQL Data Warehouse pomocí Průvodce kopírováním.

> [!NOTE]
>  Obecné informace o možnostech služby Data Factory v přesunu dat do a z Azure SQL Data Warehouse najdete v tématu [přesun dat do a z Azure SQL Data Warehouse pomocí Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) článku.
>
> Můžete také vytvořit pomocí webu Azure portal, Visual Studio, PowerShell, kanály atd. Zobrazit [kurzu: Kopírování dat z objektů Blob v Azure do Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) rychlý návod s podrobnými pokyny pro používání aktivita kopírování ve službě Azure Data Factory.  
>
>

## <a name="prerequisites"></a>Požadavky
* Azure Blob Storage: Tento experiment používá úložiště objektů Blob v Azure (GRS) pro ukládání TPC-H testovací datové sady.  Pokud nemáte účet úložiště Azure, přečtěte si [způsob vytvoření účtu úložiště](../../storage/common/storage-quickstart-create-account.md).
* [TPC-H](http://www.tpc.org/tpch/) data: budeme používat TPC-H jako testovací datové sady.  K tomuto účelu, budete muset použít `dbgen` z testu TPC-H toolkit, což pomáhá generovat datové sady.  Můžete si buď stáhnout zdrojový kód pro `dbgen` z [TPC nástroje](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) a kompilaci sami, nebo stáhnout kompilované binární soubor z [Githubu](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  Spustit s následující příkazy ke generování plochého souboru 1 TB pro dbgen.exe `lineitem` tabulky šíření mezi 10 souborů:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Nyní zkopírujte generované soubory do objektů Blob v Azure.  Odkazovat na [přesun dat do a z v místním systému souborů pomocí služby Azure Data Factory](data-factory-onprem-file-system-connector.md) jak to udělat pomocí ADF kopírování.    
* Azure SQL Data Warehouse: Tento experiment načte data do Azure SQL Data Warehouse vytvořenou s 6000 Dwu

    Odkazovat na [vytvořit službu Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) podrobné pokyny o tom, jak vytvořit databázi SQL Data Warehouse.  Pokud chcete získat nejlepší možné zatížení výkonu do SQL Data Warehouse pomocí Polybase, jsme zvolte maximální počet povolený v nastavení výkonu, což je 6 000 jednotek dwu a rychle jednotek datového skladu (Dwu).

  > [!NOTE]
  > Při načítání z objektů Blob v Azure, výkon při načítání dat je přímo úměrný počtu jednotek Dwu je nakonfigurovat ve službě SQL Data Warehouse:
  >
  > Načtení 1 TB do 1 000 DWU SQL Data Warehouse přebírá 87 minut (propustnost přibližně 200 MB/s) načtení 1 TB na 2 000 DWU SQL Data Warehouse přebírá 46 minut (propustnost ~ 380 MB/s) načtení 1 TB do 6000 DWU SQL Data Warehouse přebírá 14 minut (propustnost ~1.2 GB/s)
  >
  >

    Pokud chcete vytvořit SQL Data Warehouse s 6000 Dwu, přesuňte jezdec výkonu úplně vpravo:

    ![Výkon posuvníku](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Pro existující databázi, který není nakonfigurovaný s 6000 Dwu je možné ji škálovat i pomocí webu Azure portal.  Přejděte do databáze na webu Azure portal a je **škálování** tlačítko **přehled** panelu je znázorněno na následujícím obrázku:

    ![Stupnice – tlačítko](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Klikněte na tlačítko **škálování** otevřete panel následující, nastavte posuvník na maximální hodnotu a klikněte na tlačítko **Uložit** tlačítko.

    ![Dialogové okno škálování](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Tento experiment načte data do Azure SQL Data Warehouse pomocí `xlargerc` třídy prostředků.

    K dosažení nejlepší možné propustnost, je třeba provést pomocí SQL Data Warehouse uživatele patřícího k kopírování `xlargerc` třídy prostředků.  Zjistěte, jak to provést pomocí následujících [změnit v příkladu třída prostředků uživatele](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
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
Požadované kroky dokončit jsme se nyní připraveno ke konfiguraci aktivitou kopírování pomocí Průvodce kopírováním.

## <a name="launch-copy-wizard"></a>Spuštění průvodce kopírováním
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **vytvořit prostředek** z levého horního rohu, klikněte na tlačítko **inteligence a analýza**a klikněte na tlačítko **služby Data Factory**.
3. V **nová datová továrna** podokna:

   1. Zadejte **LoadIntoSQLDWDataFactory** pro **název**.
       Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chybová zpráva: **Název objektu pro vytváření dat "LoadIntoSQLDWDataFactory" není k dispozici**, změňte název datové továrny (například yournameLoadIntoSQLDWDataFactory) a zkuste to znovu. V tématu [Objekty pro vytváření dat – pravidla pojmenování](data-factory-naming-rules.md) najdete pravidla pojmenování artefaktů služby Data Factory.  
   2. Vyberte své **předplatné** Azure.
   3. Pro skupinu prostředků proveďte jeden z následujících kroků:
      1. Vyberte možnost **Použít existující** a vyberte existující skupinu prostředků.
      2. Vyberte možnost **Vytvořit nový** a zadejte název pro skupinu prostředků.
   4. Vyberte **umístění** pro příslušný objekt pro vytváření dat.
   5. Zaškrtněte políčko **Připnout na řídicí panel** v dolní části okna.  
   6. Klikněte na možnost **Vytvořit**.
4. Po vytvoření se zobrazí okno **Objekt pro vytváření dat**, jak je znázorněno na následujícím obrázku:

   ![Domovská stránka datové továrny](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Na domovské stránce objektu pro vytváření dat klikněte na dlaždici **Kopírovat data**. Spustí se **průvodce kopírováním**.

   > [!NOTE]
   > Pokud zjistíte, že se webový prohlížeč zasekl ve fázi „Autorizace…“, zakažte / zrušte zaškrtnutí políčka **Block third party cookies and site data** (Blokovat data souborů cookie a webů třetích stran) nebo nechte toto nastavení povolené a vytvořte výjimku pro **login.microsoftonline.com**. Potom zkuste průvodce znovu spustit.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Krok 1: Konfigurace plánu pro načítání dat
Prvním krokem je konfigurace dat načítání plánu.  

Na stránce **Vlastnosti**:

1. Zadejte **CopyFromBlobToAzureSqlDataWarehouse** pro **název úkolu**
2. Vyberte **jednou spustit** možnost.   
3. Klikněte na **Další**.  

    ![Průvodce kopírováním – stránka Vlastnosti](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Krok 2: Konfigurace zdroje
Tato část popisuje postup konfigurace zdroje: Azure Blob obsahující 1 TB TPC-H položky řádku soubory.

1. Vyberte **Azure Blob Storage** data ukládat a klikněte na tlačítko **Další**.

    ![Průvodce kopírováním – Stránka Výběr zdroje](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Zadejte informace o připojení pro účet úložiště objektů Blob v Azure a klikněte na tlačítko **Další**.

    ![Průvodce kopírováním – informace o připojení zdroje](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Zvolte **složky** obsahující TPC-H řádku soubory položek a klikněte na tlačítko **Další**.

    ![Průvodce kopírováním – výběr vstupní složky](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Po kliknutí na tlačítko **Další**, jsou automaticky zjištěny nastavení formátu souboru.  Ujistěte se, že oddělovač sloupců je ' | 'místo výchozí čárkou','.  Klikněte na tlačítko **Další** po mají zobrazení náhledu data.

    ![Průvodce kopírováním – nastavení formátu souboru](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Krok 3: Nakonfigurujte cíl
Tato část ukazuje, jak na cílovém serveru nakonfigurovat: `lineitem` tabulky v databázi Azure SQL Data Warehouse.

1. Zvolte **Azure SQL Data Warehouse** jako cílové úložiště a klikněte na tlačítko **Další**.

    ![Průvodce kopírováním – výběr cílového úložiště dat.](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Zadejte informace o připojení pro Azure SQL Data Warehouse.  Ujistěte se, že zadáte uživatele, který je členem role `xlargerc` (najdete v článku **požadavky** části Podrobné pokyny) a klikněte na tlačítko **Další**.

    ![Průvodce kopírováním – informace o cílové připojení](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Vyberte cílové tabulky a klikněte na tlačítko **Další**.

    ![Kopírování Průvodce – stránka mapování tabulek](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Na stránce mapování schématu, nechte nezaškrtnuté možnost "Použít mapování sloupců" a klikněte na tlačítko **Další**.

## <a name="step-4-performance-settings"></a>Krok 4: Nastavení výkonu

**Povolit polybase** je ve výchozím nastavení zaškrtnuto.  Klikněte na **Další**.

![Kopírování Průvodce – stránka mapování schématu](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Krok 5: Nasadit a monitorovat výsledky zatížení
1. Klikněte na tlačítko **Dokončit** tlačítko k nasazení.

    ![Průvodce kopírováním – stránka souhrnu](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Po dokončení nasazení klikněte na tlačítko `Click here to monitor copy pipeline` monitorovat spuštění průběh kopírování. Vyberte kanál kopírování, kterou jste vytvořili v **aktivity Windows** seznamu.

    ![Průvodce kopírováním – stránka souhrnu](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Můžete zobrazit podrobnosti o spuštění kopii **Průzkumníku okna aktivity** v pravém panelu, jako je objem dat ze zdroje čtení a zápisu do cílové, doba trvání a průměrná propustnost pro spuštění.

    Jak je vidět v následujícím snímku obrazovky kopírování 1 TB z Azure Blob Storage do SQL Data Warehouse trvalo 14 minut, efektivně dosažení 1.22 propustností GB/s.

    ![Průvodce kopírováním – dialogové okno bylo úspěšné.](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Osvědčené postupy
Tady je několik osvědčených postupů pro spuštění vaší databázi Azure SQL Data Warehouse:

* Použijte větší třídu prostředků, při načítání do CLUSTEROVANÉHO indexu COLUMNSTORE.
* Pro efektivnější spojení zvažte použití algoritmu hash distribuce podle vyberte sloupce namísto výchozího round dotazování na distribuci.
* Pro rychlejší a mají zatížení zvažte použití haldy pro přechodná data.
* Vytvoření statistiky po dokončení načítání Azure SQL Data Warehouse.

Zobrazit [osvědčené postupy pro službu Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md) podrobnosti.

## <a name="next-steps"></a>Další postup
* [Průvodce kopírováním služby Data Factory](data-factory-copy-wizard.md) – Tento článek obsahuje podrobné informace o Průvodci kopírováním.
* [Průvodce laděním a výkonem aktivity kopírování](data-factory-copy-activity-performance.md) – Tento článek obsahuje odkaz na měření výkonu a Průvodce laděním.
