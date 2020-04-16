---
title: Načítání terabajtů dat do datového skladu SQL
description: Ukazuje, jak lze načíst 1 TB dat do Azure SQL Data Warehouse za méně než 15 minut s Azure Data Factory
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419302"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>Načtení 1 TB do datového skladu Azure SQL za méně než 15 minut pomocí Data Factory
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Kopírování dat do nebo z Datového skladu Azure SQL pomocí datového toku](../connector-azure-sql-data-warehouse.md).


[Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) je cloudová databáze s horizontálním navýšením kapacity schopná zpracovávat obrovské objemy dat, relační i nerelační.  Sql Data Warehouse, postavený na architektuře masivně paralelního zpracování (MPP), je optimalizován pro úlohy podnikového datového skladu.  Nabízí elasticitu cloudu s flexibilitou škálování úložiště a výpočtu nezávisle.

Začínáme s Azure SQL Data Warehouse je teď jednodušší než kdy dřív pomocí **Azure Data Factory**.  Azure Data Factory je plně spravovaná cloudová služba pro integraci dat, kterou lze použít k naplnění datového skladu SQL dat dat dat daty z vašeho stávajícího systému a šetří vám cenný čas při vyhodnocování SQL Data Warehouse a vytváření analytických řešení. Tady jsou hlavní výhody načítání dat do Azure SQL Data Warehouse pomocí Azure Data Factory:

* **Snadné nastavení:** 5-krok intuitivní průvodce bez skriptování nutné.
* **Bohatá podpora úložiště dat:** integrovaná podpora pro bohatou sadu místních a cloudových úložišť dat.
* **Zabezpečení a dodržování**předpisů : data se přenášejí přes PROTOKOL HTTPS nebo ExpressRoute a přítomnost globálních služeb zajišťuje, že vaše data nikdy neopustí geografickou hranici
* **Bezkonkurenční výkon pomocí PolyBase** – Použití Polybase je nejúčinnější způsob, jak přesunout data do Azure SQL Data Warehouse. Pomocí funkce pracovní objekt blob můžete dosáhnout vysoké rychlosti načítání ze všech typů úložišť dat kromě úložiště objektů blob Azure, které polybase podporuje ve výchozím nastavení.

Tento článek ukazuje, jak pomocí Průvodce kopírováním datové továrny načíst data o velikosti 1 TB z azure blob storage do Azure SQL Data Warehouse za méně než 15 minut, s propustností přes 1,2 GB/s.

Tento článek obsahuje podrobné pokyny pro přesun dat do datového skladu Azure SQL pomocí Průvodce kopírováním.

> [!NOTE]
>  Obecné informace o možnostech Data Factory při přesouvání dat do nebo z Azure SQL Data Warehouse najdete v článku Přesun dat do a z Azure SQL Data Warehouse pomocí článku [Azure Data Factory.](data-factory-azure-sql-data-warehouse-connector.md)
>
> Můžete také vytvářet kanály pomocí sady Visual Studio, PowerShell atd. Viz [kurz: Kopírování dat z Azure Blob do Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pro rychlý návod s podrobnými pokyny pro použití aktivity kopírování v Azure Data Factory.  
>
>

## <a name="prerequisites"></a>Požadavky
* Azure Blob Storage: tento experiment používá Azure Blob Storage (GRS) pro ukládání tpc-h testování datové sady.  Pokud nemáte účet úložiště Azure, přečtěte si, [jak vytvořit účet úložiště](../../storage/common/storage-account-create.md).
* [Data TPC-H:](http://www.tpc.org/tpch/) jako testovací datovou sadu použijeme TPC-H.  Chcete-li to provést, `dbgen` musíte použít z TPC-H toolkit, který vám pomůže generovat datovou sadu.  Můžete buď stáhnout zdrojový `dbgen` kód z [TPC Tools](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) a zkompilovat sami, nebo stáhnout kompilovaný binární soubor z [GitHubu](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TPCHTools).  Spusťte dbgen.exe s následujícími příkazy `lineitem` pro generování plochého souboru o velikosti 1 TB pro tabulku rozloženou na 10 souborů:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Teď zkopírujte generované soubory do objektu Blob Azure.  Odkazovat na [Přesunout data do a z místního systému souborů pomocí Azure Data Factory,](data-factory-onprem-file-system-connector.md) jak to udělat pomocí ADF Copy.    
* Azure SQL Data Warehouse: tento experiment načítá data do Datového skladu Azure SQL vytvořeného s 6 000 DU

    Podrobné pokyny k vytvoření databáze datového skladu SQL najdete v článku [Vytvoření datového skladu Azure](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) SQL.  Chcete-li získat nejlepší možný výkon zatížení do datového skladu SQL pomocí Polybase, zvolíme maximální počet jednotek datového skladu (DWU) povolený v nastavení Výkon, což je 6 000 DW.

  > [!NOTE]
  > Při načítání z objektu blob Azure je výkon načítání dat přímo úměrný počtu objektů DW, které nakonfigurujete v datovém skladu SQL:
  >
  > Načítání 1 TB do 1 000 datových skladů DWU SQL trvá 87 minut (cca 200 MB/ propustnost) Načítání 1 TB do 2 000 DWU SQL Data Warehouse trvá 46 minut (~ 380 MB/ s propustnost) Načítání 1 TB do 6 000 DWU SQL Data Warehouse trvá 14 minut (~ 1,2 BPs propustnost)
  >
  >

    Chcete-li vytvořit datový sklad SQL s 6 000 dus, přesuňte jezdec Výkon úplně doprava:

    ![Posuvník výkonu](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Pro existující databázi, která není nakonfigurovaná s 6 000 DW, můžete škálovat pomocí portálu Azure.  Přejděte do databáze na webu Azure Portal a v panelu **Přehled** je tlačítko **Měřítko** zobrazené na následujícím obrázku:

    ![Tlačítko Změnit velikost](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Klepnutím na tlačítko **Změnit měřítko** otevřete následující panel, přesuňte jezdec na maximální hodnotu a klepněte na **tlačítko Uložit.**

    ![Dialogové okno Změnit velikost](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Tento experiment načte data do `xlargerc` datového skladu Azure SQL pomocí třídy prostředků.

    Chcete-li dosáhnout co nejlepší propustnost, je třeba `xlargerc` provést kopírování pomocí SQL Data Warehouse uživatele patřící do třídy prostředků.  Zjistěte, jak to udělat v následujících [chcete-li změnit třídu prostředků uživatele příklad](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* Vytvořte schéma cílové tabulky v databázi datového skladu Azure SQL spuštěním následujícího příkazu DDL:

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
  Po dokončení požadovaných kroků jsme nyní připraveni nakonfigurovat aktivitu kopírování pomocí Průvodce kopírováním.

## <a name="launch-copy-wizard"></a>Spuštění průvodce kopírováním
1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Klikněte na **Vytvořit prostředek** v levém horním rohu, klikněte na **Intelligence + analytics**a klikněte na **Data Factory**.
3. V podokně **Nová továrna dat:**

   1. Zadejte **LoadIntoSQLDWDataFactory** pro **název**.
       Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chyba: **Název factory dat "LoadIntoSQLDWDataFactory" není k dispozici**, změňte název datové továrny (například yournameLoadIntoSQLDWDataFactory) a zkuste vytvořit znovu. V tématu [Objekty pro vytváření dat – pravidla pojmenování](data-factory-naming-rules.md) najdete pravidla pojmenování artefaktů služby Data Factory.  
   2. Vyberte **předplatné**Azure .
   3. Pro skupinu prostředků proveďte jeden z následujících kroků:
      1. Vyberte možnost **Použít existující** a vyberte existující skupinu prostředků.
      2. Vyberte možnost **Vytvořit nový** a zadejte název pro skupinu prostředků.
   4. Vyberte **umístění** pro příslušný objekt pro vytváření dat.
   5. Zaškrtněte políčko **Připnout na řídicí panel** v dolní části okna.  
   6. Klikněte na **Vytvořit**.
4. Po dokončení vytváření se zobrazí okno **Data Factory,** jak je znázorněno na následujícím obrázku:

   ![Domovská stránka objektu pro vytváření dat](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Na domovské stránce objektu pro vytváření dat klikněte na dlaždici **Kopírovat data**. Spustí se **průvodce kopírováním**.

   > [!NOTE]
   > Pokud zjistíte, že se webový prohlížeč zasekl ve fázi „Autorizace…“, zakažte / zrušte zaškrtnutí políčka **Block third party cookies and site data** (Blokovat data souborů cookie a webů třetích stran) nebo nechte toto nastavení povolené a vytvořte výjimku pro **login.microsoftonline.com**. Potom zkuste průvodce znovu spustit.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Krok 1: Konfigurace plánu načítání dat
Prvním krokem je konfigurace plánu načítání dat.  

Na stránce **Vlastnosti**:

1. Zadejte **CopyFromBlobToAzureSqlDataWarehouse** pro **název úlohy.**
2. Vyberte **Možnost Spustit jednou.**   
3. Klikněte na **Další**.  

    ![Průvodce kopírováním – stránka Vlastnosti](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Krok 2: Konfigurace zdroje
Tato část ukazuje kroky konfigurace zdroje: Objekt blob Azure obsahující soubory řádkové položky TPC-H o velikosti 1 TB.

1. Vyberte **Azure Blob Storage** jako úložiště dat a klepněte na tlačítko **Další**.

    ![Průvodce kopírováním – vybrat zdrojovou stránku](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Vyplňte informace o připojení pro účet úložiště objektů blob Azure a klepněte na tlačítko **Další**.

    ![Průvodce kopírováním – informace o zdrojovém připojení](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Zvolte **složku** obsahující soubory řádkových položek TPC-H a klepněte na tlačítko **Další**.

    ![Průvodce kopírováním - výběr vstupní složky](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Po kliknutí na **tlačítko Další**se automaticky zjistí nastavení formátu souboru.  Zkontrolujte, zda je oddělovač sloupců "|" namísto výchozí čárky .'.  Po zobrazení náhledu dat klepněte na **tlačítko Další.**

    ![Průvodce kopírováním – nastavení formátu souboru](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Krok 3: Konfigurace cíle
Tato část ukazuje, jak nakonfigurovat cíl: `lineitem` tabulka v databázi Azure SQL Data Warehouse.

1. Zvolte **Azure SQL Data Warehouse** jako cílové úložiště a klikněte na **další**.

    ![Průvodce kopírováním – výběr cílového úložiště dat](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Vyplňte informace o připojení pro Azure SQL Data Warehouse.  Ujistěte se, že jste určili `xlargerc` uživatele, který je členem role (podrobné pokyny naleznete v části **požadavky)** a klepněte na tlačítko **Další**.

    ![Průvodce kopírováním – informace o cílovém připojení](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Zvolte cílovou tabulku a klepněte na tlačítko **Další**.

    ![Průvodce kopírováním – stránka mapování tabulky](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Na stránce mapování schématponechte možnost Použít mapování sloupců nezaškrtnutou a klepněte na tlačítko **Další**.

## <a name="step-4-performance-settings"></a>Krok 4: Nastavení výkonu

**Povolit polybase** je kontrolována ve výchozím nastavení.  Klikněte na **Další**.

![Průvodce kopírováním – stránka mapování schématu](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Krok 5: Nasazení a monitorování výsledků zatížení
1. Chcete-li **nasadit tlačítko Dokončit,** klepněte na tlačítko Dokončit.

    ![Průvodce kopírováním – stránka souhrn](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Po dokončení nasazení můžete `Click here to monitor copy pipeline` klepnutím sledovat průběh spuštění kopírování. Vyberte kanál kopírování, který jste vytvořili v seznamu **Windows aktivity.**

    ![Průvodce kopírováním – stránka souhrn](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Podrobnosti spuštění kopie můžete zobrazit v **Průzkumníku aktivity** v pravém panelu, včetně objemu dat přečteném ze zdroje a zapsaného do cíle, doby trvání a průměrné propustnosti pro spuštění.

    Jak můžete vidět z následujícího snímku obrazovky, kopírování 1 TB z úložiště objektů blob Azure do datového skladu SQL trvalo 14 minut, což efektivně dosáhlo propustnosti 1.22 GB/s!

    ![Průvodce kopírováním – úspěšné dialogové okno](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Osvědčené postupy
Tady je několik doporučených postupů pro spuštění databáze Azure SQL Data Warehouse:

* Při načítání do clusterovaného indexu COLUMNSTORE použijte větší třídu prostředků.
* Pro efektivnější spojení zvažte použití distribuce hash pomocí vybraného sloupce namísto výchozího rozdělení kruhového dotazování.
* Pro vyšší rychlosti načítání zvažte použití haldy pro přechodná data.
* Po dokončení načítání datového skladu Azure SQL vytvořte statistiky.

Podrobnosti najdete [v tématu Doporučené postupy pro Azure SQL Data Warehouse.](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md)

## <a name="next-steps"></a>Další kroky
* [Průvodce kopírováním do datové továrny](data-factory-copy-wizard.md) – tento článek obsahuje podrobnosti o Průvodci kopírováním.
* [Kopírovat aktivity výkon a ladění průvodce](data-factory-copy-activity-performance.md) - Tento článek obsahuje referenční měření výkonu a ladění průvodce.
