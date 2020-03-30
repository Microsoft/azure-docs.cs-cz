---
title: Průvodce mapováním toku dat a laděním
description: Seznamte se s klíčovými faktory, které ovlivňují výkon toků dat mapování v Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: 95a60abef283984d66736358d2d02048f08d700d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246989"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Průvodce mapováním toků dat a laděním

Mapování toků dat v Azure Data Factory poskytuje rozhraní bez kódu pro navrhování, nasazování a orchestraci transformace dat ve velkém měřítku. Pokud nejste obeznámeni s mapováním toků dat, podívejte se na [přehled toku dat mapování](concepts-data-flow-overview.md).

Při navrhování a testování toků dat z adf UX nezapomeňte zapnout režim ladění, abyste spouštěli toky dat v reálném čase, aniž byste čekali, až se cluster zahřeje. Další informace naleznete [v tématu Režim ladění](concepts-data-flow-debug-mode.md).

Toto video ukazuje některé časování vzorků transformující data s datovými toky:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>Sledování výkonu toku dat

Při navrhování mapování datových toků můžete jednotkový test každé transformace kliknutím na kartu náhleddat v konfiguračním panelu. Po ověření logiky otestujte tok dat od konce do konce jako aktivitu v kanálu. Přidejte aktivitu Spustit tok dat a pomocí tlačítka Ladění otestujte výkon toku dat. Chcete-li otevřít plán provádění a profil výkonu toku dat, klikněte na ikonu brýlí v části "akce" ve výstupní záložce vašeho kanálu.

![Monitor toku dat](media/data-flow/mon002.png "Monitor toku dat 2")

 Tyto informace můžete použít k odhadu výkonu toku dat proti různým velikostem zdrojů dat. Další informace naleznete v [tématu Monitorování toků dat mapování](concepts-data-flow-monitoring.md).

![Sledování toku dat](media/data-flow/mon003.png "Monitor toku dat 3")

 Pro spuštění ladění kanálu je pro teplý cluster vyžadována přibližně jedna minuta nastaveného času clusteru v celkovém výpočtu výkonu. Pokud inicializujete výchozí prostředí Azure Integration Runtime, může trvat přibližně 5 minut doba spouštění.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Zvětšení výpočetní velikosti v prostředí Azure Integration Runtime

Prostředí Integration Runtime s více jádry zvyšuje počet uzlů ve výpočetních prostředích Spark a poskytuje větší výpočetní výkon pro čtení, zápis a transformaci dat.
* Pokud chcete, aby byla rychlost zpracování vyšší než vstupní rychlost, zkuste cluster **optimalizovaný pro výpočetní** výkon.
* Pokud chcete uložit do mezipaměti více dat v paměti, zkuste cluster **optimalizovaný pro paměť.** Optimalizovaná paměť má vyšší cenu za jádro než optimalizovaná výpočetní prostředky, ale pravděpodobně povede k rychlejším rychlostem transformace.

![Nové INFRAČERVENÉ](media/data-flow/ir-new.png "Nové INFRAČERVENÉ")

Další informace o tom, jak vytvořit prostředí Integration Runtime, najdete [v tématu Integration Runtime v Azure Data Factory](concepts-integration-runtime.md).

### <a name="increase-the-size-of-your-debug-cluster"></a>Zvětšení velikosti ladicího clusteru

Ve výchozím nastavení zapnutí ladění bude používat výchozí runtime integrace Azure, který se vytvoří automaticky pro každou datovou továrnu. Tento výchozí Azure IR je nastavena pro osm jader, čtyři pro uzel ovladače a čtyři pro pracovní uzel, pomocí obecné výpočetní vlastnosti. Při testování s většími daty můžete zvětšit velikost ladicího clusteru vytvořením azure ir s většími konfiguracemi a zvolit tento nový Azure IR při zapnutí ladění. To bude pokyn ADF použít tento Azure IR pro náhled dat a ladění kanálu s toky dat.

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optimalizace pro Azure SQL Database a Azure SQL Data Warehouse

### <a name="partitioning-on-source"></a>Dělení na zdroji

1. Přejděte na kartu **Optimalizace** a vyberte **Nastavit rozdělení oddílů.**
1. Vyberte **zdroj**.
1. V části **Počet oddílů**nastavte maximální počet připojení k Azure SQL DB. Můžete zkusit vyšší nastavení získat paralelní připojení k databázi. Některé případy však může mít za následek vyšší výkon s omezeným počtem připojení.
1. Vyberte, zda se má rozdělit podle určitého sloupce tabulky nebo dotazu.
1. Pokud jste vybrali **Sloupec**, vyberte sloupec oddílu.
1. Pokud jste vybrali **možnost Dotaz**, zadejte dotaz, který odpovídá schématu dělení databázové tabulky. Tento dotaz umožňuje zdrojový databázový stroj využít odstranění oddílu. Zdrojové databázové tabulky není nutné rozdělit oddíly. Pokud váš zdroj ještě není rozdělen, ADF bude i nadále používat dělení dat v prostředí transformace Spark na základě klíče, který vyberete v transformaci Zdroj.

![Zdrojová část](media/data-flow/sourcepart3.png "Zdrojová část")

> [!NOTE]
> Dobrý průvodce, který vám pomůže vybrat počet oddílů pro váš zdroj, je založen na počtu jader, které jste nastavili pro prostředí Azure Integration Runtime a vynásobte toto číslo pěti. Takže například pokud transformujete řadu souborů ve složkách ADLS a budete využívat 32jádrový Azure IR, počet oddílů, na které byste cílí, je 32 x 5 = 160 oddílů.

### <a name="source-batch-size-input-and-isolation-level"></a>Úroveň velikosti, vstupu a izolace zdrojové dávky

V části **Možnosti zdroje** ve zdrojové transformaci mohou ovlivnit výkon následující nastavení:

* Velikost dávky instruuje adf k ukládání dat v sadách v paměti namísto řádek po řádku. Velikost dávky je volitelné nastavení a může dojít k vyčerpání prostředků na výpočetních uzlech, pokud nejsou správně dimenzovány.
* Nastavení dotazu vám umožní filtrovat řádky u zdroje před jejich doručením do toku dat ke zpracování. To může urychlit počáteční sběr dat. Pokud použijete dotaz, můžete přidat volitelné rady při hledání dotazu pro azure SQL DB, jako je read uncommitted.
* Čtení nepotvrzené poskytne rychlejší výsledky dotazu na transformaci zdroje

![Zdroj](media/data-flow/source4.png "Zdroj")

### <a name="sink-batch-size"></a>Velikost dřezové dávky

Chcete-li se vyhnout zpracování datových toků po řádcích, nastavte **velikost dávky** na kartě Nastavení pro azure SQL DB a azure SQL DW propadů. Pokud je nastavena velikost dávky, adf zpracovává zápisy databáze v dávkách na základě poskytnuté velikosti.

![Jímka](media/data-flow/sink4.png "Jímka")

### <a name="partitioning-on-sink"></a>Dělení na jímce

I v případě, že nemáte data rozdělena v cílových tabulkách, jeho doporučuje, aby vaše data rozdělena do transformace jímky. Dělená data často vede k mnohem rychlejšínačítání přes vynucení všech připojení k použití jednoho uzlu nebo oddílu. Přejděte na kartu Optimalizace v umyvadle a vyberte round *robin* dělení vybrat ideální počet oddílů pro zápis do jímky.

### <a name="disable-indexes-on-write"></a>Zakázat indexy při zápisu

V kanálu přidejte [aktivitu uložené procedury](transform-data-using-stored-procedure.md) před aktivitou toku dat, která zakáže indexy v cílových tabulkách napsaných z vašeho dřezu. Po aktivitě toku dat přidejte další aktivitu uložené procedury, která umožňuje tyto indexy. Nebo využít pre-zpracování a post-zpracování skripty v jímce databáze.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Zvětšení velikosti azure SQL DB a DW

Naplánujte změna velikosti zdroje a uřadněte Azure SQL DB a DW před spuštěním kanálu, abyste zvýšili propustnost a minimalizovali omezení Azure, jakmile dosáhnete limitů DTU. Po dokončení spuštění kanálu změňte velikost databází zpět na normální rychlost spuštění.

* Zdrojová tabulka SQL DB s 887 kB řádky a 74 sloupců do tabulky SQL DB s jedním odvozeným sloupcem transformace trvá asi 3 minend end-to-end pomocí paměti optimalizované 80-core ladění Azure IRs.

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Pouze Azure Synapse SQL DW] Použití pracovního načtení dat hromadně přes Polybase

Chcete-li se vyhnout vložkování řádek po řádku do dw, zkontrolujte **povolit pracovní** nastavení v nastavení jímky tak, aby ADF můžete použít [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). PolyBase umožňuje ADF načíst data hromadně.
* Při spuštění aktivity toku dat z kanálu, budete muset vybrat umístění úložiště blob nebo ADLS Gen2 pro fázi dat během hromadnénačítání.

* Zdroj souboru 421Mb se 74 sloupci do tabulky Synapse a transformace jednoho odvozeného sloupce trvá přibližně 4 minut a používá paměť optimalizované 80jádrové ladění Azure IRs.

## <a name="optimizing-for-files"></a>Optimalizace pro soubory

Při každé transformaci můžete nastavit schéma dělení, které chcete, aby se data použila na kartě Optimalizace. Je vhodné nejprve otestovat jímky založené na souboru zachování výchozí dělení a optimalizace.

* U menších souborů může být výběr méně oddílů někdy fungovat lépe a rychleji než žádat Spark, aby rozdělila vaše malé soubory.
* Pokud nemáte dostatek informací o zdrojových datech, zvolte *Round Robin* dělení a nastavte počet oddílů.
* Pokud vaše data mají sloupce, které mohou být dobré hash klíče, zvolte *Hash dělení*.

* Zdroj souboru s jímkou souborů 421Mb souboru se 74 sloupci a transformace jednoho odvozeného sloupce trvá přibližně 2 minut a trvá přibližně 2 min end pomocí paměti optimalizované 80-core ladění Azure IRs.

Při ladění v náhledu dat a ladění kanálu, omezení a vzorkování velikosti pro zdrojové datové sady založené na souboru platí pouze pro počet řádků vrácených, nikoli počet řádků číst. To může ovlivnit výkon spuštění ladění a případně způsobit selhání toku.
* Ladicí clustery jsou ve výchozím nastavení malé clustery s jedním uzlem a doporučujeme použít ukázkové malé soubory pro ladění. Přejděte na Nastavení ladění a přejděte na malou podmnožinu dat pomocí dočasného souboru.

    ![Nastavení ladění](media/data-flow/debugsettings3.png "Nastavení ladění")

### <a name="file-naming-options"></a>Možnosti pojmenování souborů

Nejběžnější způsob zápisu transformovaných dat v mapování datových toků zápisu blob nebo úložiště souborů ADLS. V jímce je nutné vybrat datovou sadu, která odkazuje na kontejner nebo složku, nikoli na pojmenovaný soubor. Jako mapování toku dat používá Spark pro spuštění, výstup je rozdělen a více souborů na základě schématu dělení.

Společné schéma dělení je zvolit _výstup na jeden soubor_, který sloučí všechny výstupní part soubory do jednoho souboru v jímce. Tato operace vyžaduje, aby se výstup redukuje na jeden oddíl v jednom uzlu clusteru. Pokud kombinujete mnoho velkých zdrojových souborů do jednoho výstupního souboru, můžete do jednoho výstupního souboru spustit prostředky uzlu clusteru.

Chcete-li se vyhnout vyčerpání prostředků výpočetních uzlů, zachovat výchozí optimalizované schéma v toku dat a přidat aktivitu kopírování v kanálu, který sloučí všechny soubory PART z výstupní složky do nového jediného souboru. Tato technika odděluje akci transformace od sloučení souborů a dosahuje stejného výsledku jako nastavení _výstupu na jeden soubor_.

### <a name="looping-through-file-lists"></a>Opakování seznamů souborů

Mapování toku dat bude provádět lépe, když source transformace iterates přes více souborů namísto opakování prostřednictvím for each aktivity. Ve zdrojové transformaci doporučujeme používat zástupné znaky nebo seznamy souborů. Proces toku dat se spustí rychleji tím, že opakování dochází uvnitř clusteru Spark. Další informace naleznete [v tématu Zástupné symboly v transformaci zdroje](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

Například pokud máte seznam datových souborů z července 2019, které chcete zpracovat ve složce v úložišti objektů Blob, níže je zástupný znak, který můžete použít v transformaci zdroj.

```DateFiles/*_201907*.txt```

Pomocí zástupných kódů bude kanál obsahovat pouze jednu aktivitu toku dat. To bude fungovat lépe než vyhledávání proti blob Store, který pak iterates přes všechny odpovídající soubory pomocí ForEach s spustit tok dat aktivity uvnitř.

### <a name="optimizing-for-cosmosdb"></a>Optimalizace pro CosmosDB

Nastavení propustnost a dávkové vlastnosti na jímky CosmosDB se projeví pouze při provádění tohoto toku dat z aktivity toku dat kanálu. Původní nastavení kolekce bude dodržena CosmosDB po spuštění toku dat.

* Velikost dávky: Vypočítejte hrubou velikost řádku dat a ujistěte se, že velikost dávky rowSize * je menší než dva miliony. Pokud ano, zvětšete velikost dávky, abyste získali lepší propustnost
* Propustnost: Nastavte zde vyšší propustnost, aby dokumenty mohly do CosmosDB rychleji psát. Mějte prosím na paměti vyšší náklady na ŽP na základě nastavení vysoké propustnosti.
*   Rozpočet propustnost zápisu: Použijte hodnotu, která je menší než celkové ru za minutu. Pokud máte tok dat s vysokým počtem oddílů Spark, nastavení propustnost rozpočtu umožní větší rovnováhu mezi těmito oddíly.

## <a name="join-performance"></a>Připojit se k výkonu

Správa výkonu spojení v toku dat je velmi běžná operace, kterou budete provádět po celou dobu životnosti transformací dat. V ADF toky dat nevyžadují data, která mají být seřazeny před spojení, protože tyto operace jsou prováděny jako hash spojení v Sparku. Můžete však těžit z lepší výkon s optimalizací "Vysílání" Spojení. Tím se vyhnete zamíchání tím, že zatlačíte obsah obou stran vztahu spojení do uzlu Spark. To funguje dobře pro menší tabulky, které se používají pro vyhledávání odkazů. Větší tabulky, které se nemusí vejít do paměti uzlu, nejsou vhodnými kandidáty pro optimalizaci vysílání.

Další optimalizace spojení je sestavení spojení tak, aby se zabránilo slitovací tendenci implementovat křížová spojení. Pokud například do podmínek spojení zahrnete literálové hodnoty, Spark uvidí, že jako požadavek na provedení úplného kartézového produktu se může nejprve odfiltrovat. Pokud však zajistíte, že máte hodnoty sloupců na obou stranách podmínky spojení, můžete se tomuto kartéznímu produktu vyvolanému jiskrou vyhnout a zlepšit výkon spojení a datových toků.

## <a name="next-steps"></a>Další kroky

Podívejte se na další články o toku dat související s výkonem:

- [Karta Optimalizace toku dat](concepts-data-flow-overview.md#optimize)
- [Aktivita toku dat](control-flow-execute-data-flow-activity.md)
- [Sledování výkonu toku dat](concepts-data-flow-monitoring.md)
