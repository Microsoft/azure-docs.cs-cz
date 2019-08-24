---
title: Mapování výkonu toku dat a Průvodce optimalizací v Azure Data Factory | Microsoft Docs
description: Přečtěte si o klíčových faktorech, které mají vliv na výkon toků dat v Azure Data Factory při použití mapování datových toků.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 8eb244a0eff1569ac27feae68104db613373463a
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992356"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Průvodce optimalizací výkonu a ladění toků dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory mapování datových toků poskytuje rozhraní prohlížeče bez kódu pro návrh, nasazení a orchestraci transformací dat ve velkém měřítku.

> [!NOTE]
> Pokud nejste obeznámeni s datovým proudem mapování ADF, přečtěte si článek [Přehled toků dat](concepts-data-flow-overview.md) před čtením tohoto článku.
>

> [!NOTE]
> Když navrhujete a testujete toky dat z uživatelského rozhraní ADF, nezapomeňte zapnout přepínač ladění, abyste mohli spouštět toky dat v reálném čase, aniž byste čekali na zahřívání clusteru.
>

![Tlačítko ladit](media/data-flow/debugb1.png "Ladit")

## <a name="monitor-data-flow-performance"></a>Sledování výkonu toku dat

Při navrhování toků dat mapování v prohlížeči lze každou jednotlivou transformaci otestovat kliknutím na kartu náhled dat v dolním podokně nastavení pro každou transformaci. Dalším krokem, který byste měli provést, je testování toku dat v Návrháři kanálu. Přidejte aktivitu spustit tok dat a použijte tlačítko ladit k otestování výkonu toku dat. V dolním podokně okna kanálu se zobrazí ikona eyeglass v části akce:

![Sledování toku dat](media/data-flow/mon002.png "Monitor toku dat 2")

Po kliknutí na tuto ikonu se zobrazí plán spuštění a následný profil výkonu toku dat. Tyto informace můžete použít k odhadu výkonu toku dat s různými zdroji dat s různou velikostí. Počítejte s tím, že v celkových výpočtech výkonu můžete předpokládat 1 minutu nastavení času spuštění úlohy clusteru a pokud používáte výchozí Azure Integration Runtime, možná budete muset přidat 5 minut času aktivace clusteru.

![Sledování toku dat](media/data-flow/mon003.png "Monitor toku dat 3")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optimalizace pro Azure SQL Database a Azure SQL Data Warehouse

![Zdrojová část](media/data-flow/sourcepart3.png "Zdrojová část")

### <a name="partition-your-source-data"></a>Rozdělit zdrojová data na oddíly

* Přejít na "optimalizovat" a vybrat zdroj. V dotazu nastavte buď konkrétní sloupec tabulky, nebo typ.
* Pokud jste zvolili "sloupec", pak vyberte sloupec partition (oddíl).
* Nastavte také maximální počet připojení k databázi SQL Azure. Můžete vyzkoušet vyšší nastavení, abyste získali paralelní připojení k vaší databázi. V některých případech ale může dojít k rychlejšímu výkonu s omezeným počtem připojení.
* Zdrojové tabulky databáze není nutné rozdělit na oddíly.
* Když nastavíte dotaz ve zdrojové transformaci, který se shoduje se schématem vytváření oddílů vaší databázovou tabulkou, umožníte zdrojovému databázovému stroji využít odstraňování oddílů.
* Pokud váš zdroj ještě není rozdělený na oddíly, ADF bude v prostředí Spark Transforming dál používat dělení dat, a to na základě klíče, který jste vybrali ve zdrojové transformaci.

### <a name="set-batch-size-and-query-on-source"></a>Nastavit velikost dávky a dotaz na zdroj

![Zdroj](media/data-flow/source4.png "Zdroj")

* Nastavením velikosti dávky budete mít k ADF pokyn k ukládání dat v sadách místo řádků po řádku. Je to volitelné nastavení a na výpočetních uzlech, pokud nemají správnou velikost, může dojít k nedostatku prostředků.
* Nastavení dotazu vám umožní filtrovat řádky přímo ve zdroji předtím, než dostanou tok dat pro zpracování, což může urychlit počáteční získání dat.
* Pokud použijete dotaz, můžete přidat volitelné pomocný parametr dotazu pro Azure SQL DB, tj. číst nepotvrzené.

### <a name="set-isolation-level-on-source-transformation-settings-for-sql-datasets"></a>Nastavit úroveň izolace pro nastavení transformace zdroje pro datové sady SQL

* Čtení nepotvrzeného vám poskytne rychlejší výsledky dotazu na transformaci zdroje.

![Úroveň izolace](media/data-flow/isolationlevel.png "Úroveň izolace")

### <a name="set-sink-batch-size"></a>Nastavit velikost dávky jímky

![Jímka](media/data-flow/sink4.png "Jímka")

* Abyste se vyhnuli zpracování datových toků po řádcích, nastavte velikost dávky v nastavení jímky pro Azure SQL DB. Díky tomu bude ADF v dávkách zpracovávat zápisy do databáze v dávkách na základě zadané velikosti.

### <a name="set-partitioning-options-on-your-sink"></a>Nastavení možností dělení na jímku

* I v případě, že vaše data nejsou rozdělená do cílových tabulek Azure SQL DB, navštivte kartu optimalizace a nastavte dělení.
* Velmi často stačí, když zapnete ADF pro použití kruhové dotazování na oddíly pro spouštěcí Clustery Spark, výsledkem je mnohem rychlejší načítání dat namísto vynucení všech připojení z jednoho uzlu nebo oddílu.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Zvětšete velikost modulu COMPUTE v Azure Integration Runtime

![Nový IR](media/data-flow/ir-new.png "Nový IR")

* Zvyšte počet jader, čímž zvýšíte počet uzlů a získáte další výpočetní výkon pro dotazování a zápis do databáze SQL Azure.
* Vyzkoušejte možnosti "optimalizované pro výpočty" a "paměť optimalizované", abyste pro výpočetní uzly použili více prostředků.

### <a name="unit-test-and-performance-test-with-debug"></a>Test jednotek a test výkonnosti pomocí ladění

* Při toku dat testování částí nastavte na ZAPNUTo tlačítko "ladění toku dat".
* V Návrháři toku dat použijte kartu náhled dat pro transformace k zobrazení výsledků logiky transformace.
* Otestujte datové toky z návrháře kanálu tím, že na plátno pro návrh kanálu umístíte aktivitu toku dat a pomocí tlačítka ladit ji otestujete.
* Testování v režimu ladění bude fungovat s živým živým clusterovým prostředím bez nutnosti čekat na zahřívání clusteru za běhu.

### <a name="disable-indexes-on-write"></a>Zakázat indexy při zápisu
* Použijte aktivitu uložené procedury kanálu ADF před aktivitou toku dat, která zakáže indexy na cílových tabulkách, na které se zapisují z jímky.
* Po aktivitě toku dat přidejte další aktivitu uložené procedury, která povoluje tyto indexy.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Zvětšení velikosti databáze SQL Azure
* Naplánujte změnu velikosti zdroje a jímky Azure SQL DB předtím, než svůj kanál spustíte, abyste zvýšili propustnost a minimalizovali omezení Azure, jakmile dosáhnete limitů DTU.
* Po dokončení kanálu můžete své databáze změnit na normální rychlost běhu.

## <a name="optimizing-for-azure-sql-data-warehouse"></a>Optimalizace pro Azure SQL Data Warehouse

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>Použití přípravy k hromadnému načtení dat prostřednictvím základu

* Abyste se vyhnuli zpracování datových toků po řádcích, nastavte možnost "fázování" v nastavení jímky tak, aby ADF mohla využít základnu k zamezení vkládání řádků do datového skladu. To povede k tomu, aby ADF používalo základnu, aby bylo možné data hromadně načíst.
* Při spuštění aktivity toku dat z kanálu se zapnutým příchodem budete muset pro hromadné načítání vybrat umístění úložiště objektů BLOB vašich pracovních dat.

### <a name="increase-the-size-of-your-azure-sql-dw"></a>Zvětšení velikosti datového skladu Azure SQL

* Naplánování změny velikosti zdroje a jímky Azure SQL DW před spuštěním kanálu za účelem zvýšení propustnosti a minimalizaci omezení Azure, jakmile dosáhnete limitů DWU.

* Po dokončení kanálu můžete své databáze změnit na normální rychlost běhu.

## <a name="optimize-for-files"></a>Optimalizovat pro soubory

* Můžete určit, kolik oddílů bude používat ADF. U každé zdrojové & transformaci jímky a také každou jednotlivou transformaci můžete nastavit schéma dělení. U menších souborů může být při výběru možnosti "jeden oddíl" vhodnější pracovat lépe a rychleji než při vytváření oddílů malých souborů v Sparku.
* Pokud nemáte k dispozici dostatek informací o zdrojových datech, můžete vybrat oddíl "kruhové dotazování" a nastavit počet oddílů.
* Pokud prozkoumáte data a zjistíte, že máte sloupce, které mohou být vhodné pro klíče hash, použijte možnost dělení hodnoty hash.
* Při ladění v náhledu dat a ladění kanálu počítejte s tím, že omezení a velikosti vzorkování pro souborové zdroje založené na souborech se vztahují pouze na počet vrácených řádků, nikoli na počet čtených řádků. To je důležité si uvědomit, protože může ovlivnit výkon při spuštění ladění a může způsobit selhání toku.
* Nezapomeňte, že clustery ladění jsou ve výchozím nastavení malé clustery s jedním uzlem, takže používejte dočasné malé soubory pro ladění. Přejděte na nastavení ladění a najeďte na malou podmnožinu dat pomocí dočasného souboru.

![Nastavení ladění](media/data-flow/debugsettings3.png "Nastavení ladění")

### <a name="file-naming-options"></a>Možnosti pojmenovávání souborů

* Výchozí povaha psaní transformovaných dat v datových tocích mapování ADF se zapisuje do datové sady, která má propojenou službu BLOB nebo ADLS. Tuto datovou sadu byste měli nastavit tak, aby odkazovala na složku nebo kontejner, nikoli na pojmenovaný soubor.
* Toky dat používají Azure Databricks Spark ke spuštění, což znamená, že váš výstup bude rozdělen do více souborů na základě výchozího dělení Spark nebo schématu dělení, které jste výslovně zvolili.
* Velmi běžnou operací v datových proudech ADF je volba "výstup do jednoho souboru", takže všechny soubory výstupních součástí budou sloučeny do jednoho výstupního souboru.
* Tato operace ale vyžaduje, aby se výstup snížil na jeden oddíl na jednom uzlu clusteru.
* Mějte na paměti, že při výběru této oblíbené možnosti. Pokud kombinujete mnoho velkých zdrojových souborů do jednoho oddílu výstupního souboru, můžete použít prostředky uzlu clusteru.
* Aby nedošlo k vyčerpání prostředků výpočetních uzlů, můžete zachovat výchozí nebo explicitní schéma dělení v ADF, které optimalizuje výkon, a pak do kanálu přidat následnou aktivitu kopírování, která sloučí všechny soubory součásti z výstupní složky až po novou jednu. souborů. V podstatě Tato technika odděluje akci transformace ze slučování souborů a dosáhne stejného výsledku jako nastavení "výstup do jednoho souboru".

### <a name="looping-through-file-lists"></a>Procházení seznamem souborů

Ve většině případů se toky dat v ADF spustí lépe z kanálu, který umožňuje transformaci zdroje toku dat na více souborů. Jinými slovy, je vhodnější používat zástupné znaky nebo seznamy souborů v toku dat, než můžete iterovat v rozsáhlých seznamech souborů pomocí příkazu ForEach v kanálu a voláním toku dat spouštění v každé iteraci. Proces toku dat se zrychluje tím, že umožní cyklům výskytu v toku dat.

Například pokud mám seznam datových souborů z července 2019, které chci zpracovat ve složce v Blob Storage, bude se více provádět pokus o volání aktivity toku dat z vašeho kanálu a použití zástupného znaku ve zdroji, jako je to :

```DateFiles/*_201907*.txt```

To bude mít lepší výkon než vyhledávání v úložišti objektů BLOB v kanálu, který pak projde všemi odpovídajícími soubory pomocí příkazu ForEach s aktivitou spustit tok dat uvnitř.

## <a name="next-steps"></a>Další postup

Další články toku dat týkající se výkonu:

- [Karta optimalizace toku dat](concepts-data-flow-optimize-tab.md)
- [Aktivita toku dat](control-flow-execute-data-flow-activity.md)
- [Sledování výkonu toku dat](concepts-data-flow-monitoring.md)
