---
title: Mapování toku dat výkonu a ladění, Průvodce ve službě Azure Data Factory | Dokumentace Microsoftu
description: Další informace o klíčových faktorů, které ovlivňují výkon toků dat ve službě Azure Data Factory, při použití mapování datové toky.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: bbbc2bc5c47821469ecf15a27195b1bf0c12e6e5
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190635"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Mapování výkonu toky dat a Průvodce laděním

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory mapování dat toků poskytují rozhraní bez kódu prohlížeče k návrhu, nasazení a orchestrovat transformace dat ve velkém měřítku.

> [!NOTE]
> Pokud nejste obeznámeni s ADF mapování Data proudí obecně platí, přečtěte si téma [přehled toků dat](concepts-data-flow-overview.md) před čtením tohoto článku.
>

> [!NOTE]
> Při navrhování a testování datové toky v uživatelském rozhraní ADF, ujistěte se, že chcete zapnout přepínač ladění tak, aby vaše toky dat v můžete spustit v reálném čase bez čekání na clusteru zahřívání.
>

![Ladění tlačítko](media/data-flow/debugb1.png "ladění")

## <a name="monitor-data-flow-performance"></a>Monitorování výkonu toku dat

Při navrhování data mapování toků v prohlížeči, můžete test jednotek každé jednotlivé transformace po kliknutí na kartě Náhled dat v dolním podokně nastavení pro každý transformace. Dalším krokem, které byste měli podniknout je k testování vašich dat tok end až do konce v Návrháři kanálu. Přidat aktivitu spuštění toku dat a použijte tlačítko ladění k testování výkonu datový tok. V dolním podokně okna kanál zobrazí se ikona eyeglass v části "akce":

![Tok dat monitorování](media/data-flow/mon002.png "předávají Data monitorování 2")

Klepnutí na tuto ikonu se zobrazí plán provádění a následné výkonu profilu datový tok. Tyto informace můžete použít k odhadu výkon vašeho toku dat pro různé velikosti datové zdroje. Mějte na paměti, že 1 minuta čas nastavení spuštění úlohy clusteru, můžete předpokládat výpočtech celkový výkon a pokud použijete výchozí prostředí Azure Integration Runtime, budete muset přidat clusteru nahoru typu číselník také dobu 5 minut.

![Sledování toku dat](media/data-flow/mon003.png "předávají Data monitorování 3")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optimalizace pro Azure SQL Database a Azure SQL Data Warehouse

![Zdroj část](media/data-flow/sourcepart3.png "zdroje část")

### <a name="partition-your-source-data"></a>Rozdělit svá zdrojová data

* Přejít na "Optimalizace" a vybrat možnost "Zdroj". Nastavte buď sloupce konkrétní tabulky, nebo typ v dotazu.
* Pokud jste zvolili "sloupce", pak vyberte sloupec oddílu.
* Nastavuje maximální počet připojení k vaší databázi SQL Azure. Vyšší zkuste získat paralelní připojení k vaší databázi. Někdy však může vést k vyšší výkon s omezený počet připojení.
* Zdrojových tabulkách databáze není nutné k rozdělení na oddíly.
* Nastavení dotazu ve vaší zdrojové transformace, která odpovídá schématu dělení tabulky databáze, vám umožní source databázového stroje využívat eliminace oddílů.
* Pokud váš zdroj není již rozdělená na oddíly, ADF dál používat dělení v prostředí Spark transformace na základě klíče, který zvolíte v transformaci zdroje dat.

### <a name="set-batch-size-and-query-on-source"></a>Nastavte velikost dávky a dotazování na zdroj

![Zdroj](media/data-flow/source4.png "zdroje")

* Nastavení velikosti dávky se dát pokyn ADF k ukládání dat v sadách v paměti namísto řádek po řádku. Je volitelné nastavení a mohou Pokud nejsou správně velká spuštění ve výpočetních uzlech nemá dostatek prostředků.
* Nastavení dotazu vám umožní filtrovat řádky přímo na zdroji před i doručení pro tok dat ke zpracování, které můžete provést počáteční data pořízení rychlejší.
* Pokud použijete dotaz, můžete přidat nepovinný dotaz pomocných parametrů pro vaši službu Azure SQL DB, to znamená READ UNCOMMITTED

### <a name="set-isolation-level-on-source-transformation-settings-for-sql-datasets"></a>Nastavit úroveň izolace na transformaci nastavení zdroje u datových sad SQL

* Nepotvrzené čtení bude poskytovat rychlejší výsledky dotazu na zdroj transformace

![Úroveň izolace](media/data-flow/isolationlevel.png "úroveň izolace")

### <a name="set-sink-batch-size"></a>Nastavte velikost dávky jímky

![Jímka](media/data-flow/sink4.png "jímky")

* Pokud se chcete vyhnout řádek po řádku zpracování datové toky, nastavte v nastavení jímky pro službu Azure SQL DB "velikost dávky". Dozvíte se, že se že zapíše ADF procesu databáze v dávkách, v závislosti na velikosti k dispozici.

### <a name="set-partitioning-options-on-your-sink"></a>Nastavte možnosti pro jímku dělení

* I v případě, že není nutné data rozdělit na oddíly ve vaší cílové tabulky Azure SQL DB, přejděte na kartu Optimalizace a nastavte dělení.
* Velmi často jednoduše oznamující do kruhové dotazování dělení v clusterech Spark provádění výsledky v mnohem rychlejší načítání místo vynucení všechna připojení z jednoho uzlu/oddílu dat pomocí ADF.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Zvětšete velikost výpočetní modul v prostředí Azure Integration Runtime

![Nové prostředí IR](media/data-flow/ir-new.png "nové prostředí IR")

* Zvýšíte počet jader, které zvýšit počet uzlů a poskytneme vám další výpočetní výkon pro dotazování a zapisovat do databáze SQL Azure.
* Vyzkoušejte "– Compute optimalizované" a "Memory Optimized" možnosti použít další prostředky pro vaše výpočetní uzly.

### <a name="unit-test-and-performance-test-with-debug"></a>Testování částí a test výkonnosti s laděním

* Jednotky testování toků dat, při nastavení na tlačítko "Tok Debug" na ON.
* V Návrháři toku dat na kartě Náhled dat na transformace pro zobrazení výsledků svoji logiku transformace.
* Testování částí data toky z Návrháře kanálu tak, že aktivitu toku dat v návrhu kanálu plátno a testovat pomocí tlačítka "Debug".
* Testování v režimu ladění bude fungovat pro aktivní topným zařízením cluster prostředí, aniž byste museli čekat clusteru just-in-time typu číselník up.

### <a name="disable-indexes-on-write"></a>Zakázat indexy pro zápis
* Použijte aktivitu ADF kanálu uložené procedury před vaši aktivitu toku dat, která zakáže indexů na cílové tabulky, které jsou zapisovány do z pro jímku.
* Po vaši aktivitu toku dat přidejte další aktivitu uložené procedury, povolené tyto indexy.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Zvětšete velikost vaší databáze SQL Azure
* Naplánujte změny velikosti zdroje a jímky Azure SQL DB před spuštění, který omezuje svůj kanál pro zvýšení propustnosti a minimalizovat Azure omezování, když dosáhnete počtu jednotek DTU.
* Po dokončení spouštění kanálu můžete změnit velikost vaší databáze zpět do jejich normální spuštění množství.

## <a name="optimizing-for-azure-sql-data-warehouse"></a>Optimalizace pro službu Azure SQL Data Warehouse

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>Načtení dat pomocí Polybase hromadně pomocí pracovní

* Pokud se chcete vyhnout řádek po řádku zpracování datové toky, nastavte možnost "Pracovní" v nastavení jímky tak, aby ADF můžete využít Polybase, aby se zabránilo řádek po řádku vloží do datového skladu. To bude dát pokyn ADF použití technologie Polybase, tak, aby data je možné načíst hromadně.
* Při spouštění aktivit v toku dat z kanálu s pracovní zapnuta, bude nutné vybrat umístění úložiště objektů Blob pro hromadné načítání vaše pracovní data.

### <a name="increase-the-size-of-your-azure-sql-dw"></a>Zvětšete velikost vašeho datového skladu SQL Azure

* Naplánovat, změna velikosti zdroje a jímky Azure SQL data Warehouse předtím, než spustíte svůj kanál pro zvýšení propustnosti a minimalizovat Azure omezování, když dosáhnete limitů DWU.

* Po dokončení spouštění kanálu můžete změnit velikost vaší databáze zpět do jejich normální spuštění množství.

## <a name="optimize-for-files"></a>Optimalizovat pro soubory

* Můžete určit, kolik oddíly, které budou používat ADF. Každá transformace zdroje a jímky, stejně jako každý jednotlivých transformace můžete nastavit schéma vytváření oddílů. Pro zmenšení souborů může se stát, že vyberete "Jeden oddíl" můžete někdy fungovat lépe a rychleji, než s dotazem, Spark pro rozdělení malých souborů.
* Pokud nemáte dostatek informací o svá zdrojová data, můžete "Kruhové dotazování" vytváření oddílů a nastavit počet oddílů.
* Pokud zkoumat data a zjistit, že sloupce, které může být vhodné hash klíče, použijte-the-Hash dělení možnost.

### <a name="file-naming-options"></a>Možnosti pro pojmenování souborů

* Výchozí povaze zápis Transformovaná data v ADF mapování toků dat je k zápisu do datové sady, která obsahuje objekt Blob nebo ADLS propojenou službu. Měli byste nastavit tuto datovou sadu, aby odkazoval na složku nebo kontejneru, není soubor s názvem.
* Explicitně data toky pomocí Azure Databricks Spark pro spouštění, což znamená, že výstup bude rozdělit přes více souborů na základě buď výchozí Spark rozdělení do oddílů nebo dělení schéma, které jste zvolili.
* Velmi běžné operace v tocích ADF dat je zvolit "Výstup do jednoho souboru" tak, aby všechny soubory část výstupu jsou sloučeny do jednoho výstupního souboru.
* Tato operace vyžaduje, že snižuje výstup do jednoho oddílu na jeden uzel clusteru.
* To mějte na paměti při výběru této možnosti Oblíbené. Můžete spustit nemá dostatek prostředků uzlu clusteru, pokud kombinujete mnoha velkých zdrojové soubory do oddílu jeden výstupní soubor.
* Aby se zabránilo vyčerpání prostředků výpočetního uzlu, můžete ponechat výchozí nebo explicitní schéma rozdělení oddílů ve službě ADF, která optimalizuje výkon, a pak přidejte další aktivitu kopírování v kanálu, která sloučí všechny části souborů z výstupní složky do jediného nového soubor. Tato technika v podstatě odděluje akci transformace z sloučení souboru a dosáhne stejného výsledku jako nastavení "výstup do jednoho souboru".

## <a name="next-steps"></a>Další postup
Zobrazit další toku dat články související s výkonem:

- [Karta Optimalizace toku dat](concepts-data-flow-optimize-tab.md)
- [Aktivitu toku dat](control-flow-execute-data-flow-activity.md)
- [Sledování toku dat výkonu](concepts-data-flow-monitoring.md)
