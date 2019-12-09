---
title: Mapování výkonu toku dat a Průvodce optimalizací
description: Přečtěte si o klíčových faktorech, které mají vliv na výkon mapování datových toků v Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: fb2a11850370766ab174c67dd122f33879fb432a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928537"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Průvodce optimalizací výkonu a ladění toků dat

Mapování toků dat v Azure Data Factory poskytuje rozhraní bez kódu pro návrh, nasazení a orchestraci transformací dat ve velkém měřítku. Pokud nejste obeznámeni s toky mapování dat, přečtěte si téma [Přehled toku dat mapování](concepts-data-flow-overview.md).

Při navrhování a testování toků dat z uživatelského prostředí ADF nezapomeňte přepnout na režim ladění, abyste mohli provádět toky dat v reálném čase, aniž byste čekali na zahřívání clusteru. Další informace naleznete v tématu [režim ladění](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flow-performance"></a>Monitorování výkonu toku dat

Při návrhu toků mapování dat můžete každou transformaci otestovat tak, že na panelu Konfigurace kliknete na kartu náhled dat. Po ověření logiky otestujte tok dat od začátku po aktivitu v kanálu. Přidejte aktivitu spustit tok dat a použijte tlačítko ladit k otestování výkonu toku dat. Pokud chcete otevřít plán spuštění a profil výkonu vašeho toku dat, klikněte na ikonu brýlí v části akce na kartě výstup vašeho kanálu.

![Sledování toku dat](media/data-flow/mon002.png "Monitor toku dat 2")

 Tyto informace můžete použít k odhadu výkonu toku dat proti zdrojům dat s různou velikostí. Další informace najdete v tématu [monitorování toků dat mapování](concepts-data-flow-monitoring.md).

![Sledování toku dat](media/data-flow/mon003.png "Monitor toku dat 3")

 V případě spuštění ladění kanálu se v případě teplého clusteru vyžaduje asi jedna minuta času nastavení clusteru v celkových výpočtech výkonu. Pokud inicializujete výchozí Azure Integration Runtime, může to trvat přibližně 5 minut.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Zvýšení výpočetní velikosti v Azure Integration Runtime

Integration Runtime s více jádry zvyšuje počet uzlů ve výpočetním prostředí Spark a poskytuje větší výpočetní výkon pro čtení, zápis a transformaci dat.
* Vyzkoušejte **COMPUTE optimalizovaný** cluster, pokud chcete, aby byl rychlost zpracování vyšší než zadaná rychlost.
* Vyzkoušení **paměťově optimalizovaného** clusteru, pokud chcete uložit do mezipaměti více dat v paměti.

![Nový IR](media/data-flow/ir-new.png "Nový IR")

Další informace o tom, jak vytvořit Integration Runtime, najdete v tématu [Integration runtime v Azure Data Factory](concepts-integration-runtime.md).

### <a name="increase-the-size-of-your-debug-cluster"></a>Zvětšit velikost clusteru ladění

Při zapnutí ladění se ve výchozím nastavení použije výchozí prostředí Azure Integration runtime, které se vytvoří automaticky pro každou datovou továrnu. Tato výchozí Azure IR je nastavená na osm jader, čtyři pro uzel ovladače a čtyři pro pracovní uzel pomocí obecných výpočetních vlastností. Při testování s většími objemy dat můžete zvětšit velikost clusteru ladění tím, že vytvoříte Azure IR s větší konfigurací a zvolíte tuto novou Azure IR při přepnutí na ladění. To vám podá pokyn ADF k použití tohoto Azure IR pro náhled dat a ladění kanálu pomocí toků dat.

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optimalizace pro Azure SQL Database a Azure SQL Data Warehouse

### <a name="partitioning-on-source"></a>Vytváření oddílů na zdroji

1. Přejít na kartu **optimalizace** a vybrat **nastavit dělení**
1. Vyberte **zdroj**.
1. V části **počet oddílů**nastavte maximální počet připojení k databázi SQL Azure. Můžete vyzkoušet vyšší nastavení, abyste získali paralelní připojení k vaší databázi. V některých případech ale může dojít k rychlejšímu výkonu s omezeným počtem připojení.
1. Vyberte, jestli se má rozdělit na oddíly podle konkrétního sloupce tabulky nebo dotazu.
1. Pokud jste vybrali **sloupec**, vyberte sloupec partition (oddíl).
1. Pokud jste vybrali **dotaz**, zadejte dotaz, který odpovídá schématu dělení vaší databázové tabulky. Tento dotaz umožňuje zdrojovému databázovému stroji využít odstraňování oddílů. Zdrojové tabulky databáze není nutné rozdělit na oddíly. Pokud váš zdroj ještě není rozdělený na oddíly, ADF bude v prostředí Spark Transforming dál používat dělení dat, a to na základě klíče, který jste vybrali ve zdrojové transformaci.

![Zdrojová část](media/data-flow/sourcepart3.png "Zdrojová část")

### <a name="source-batch-size-input-and-isolation-level"></a>Velikost, vstup a úroveň izolace zdrojové dávky

V části **Možnosti zdroje** ve zdrojové transformaci můžou mít následující nastavení vliv na výkon:

* Velikost dávky nastaví ADF k ukládání dat v sadách místo řádků po řádku. Velikost dávky je volitelné nastavení a na výpočetních uzlech, pokud nemají správnou velikost, můžou být vycházející prostředky.
* Nastavení dotazu vám umožní filtrovat řádky ve zdroji předtím, než dorazí do toku dat ke zpracování. Díky tomu může být počáteční získání dat rychlejší. Pokud použijete dotaz, můžete přidat volitelné pomocný parametr dotazu pro databázi SQL Azure, například nepotvrzené čtení.
* Čtení nepotvrzeného vám poskytne rychlejší výsledky dotazu na transformaci zdroje.

![Zdroj](media/data-flow/source4.png "Zdroj")

### <a name="sink-batch-size"></a>Velikost dávky jímky

Abyste se vyhnuli zpracování datových toků po řádcích, nastavte **velikost dávky** na kartě nastavení pro Azure SQL DB a jímky Azure SQL DW. Pokud je nastavena velikost dávky, vytvoří ADF v dávkách zápisy do dávek v závislosti na zadané velikosti.

![jímka](media/data-flow/sink4.png "Jímka")

### <a name="partitioning-on-sink"></a>Dělení na jímku

I když vaše data v cílových tabulkách nejsou rozdělená do oddílů, doporučuje se, aby data byla rozdělená do oddílů v transformaci jímky. Data dělená do oddílů často načítají mnohem rychlejší načítání přes vynucení všech připojení, aby používaly jeden uzel nebo oddíl. Přejít na kartu optimalizace jímky a výběrem možnosti *kruhového dotazování* na oddíly vyberte ideální počet oddílů, které chcete zapisovat do jímky.

### <a name="disable-indexes-on-write"></a>Zakázat indexy při zápisu

V kanálu přidejte [aktivitu uložené procedury](transform-data-using-stored-procedure.md) do aktivity toku dat, která zakáže indexy na cílových tabulkách napsaných z jímky. Po aktivitě toku dat přidejte další aktivitu uložené procedury, která povoluje tyto indexy.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Zvětšete velikost vaší databáze SQL Azure a DW.

Naplánujte změnu velikosti zdroje a jímky Azure SQL DB a DW před spuštěním kanálu, abyste zvýšili propustnost a minimalizovali omezení Azure, jakmile dosáhnete limitů DTU. Po dokončení spuštění kanálu změňte velikost databází zpět na normální rychlost běhu.

### <a name="azure-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Jenom Azure SQL DW] Použití přípravy k hromadnému načtení dat prostřednictvím základu

Pokud chcete do datové sady DW vyhnout vkládání řádků, zaškrtněte v nastavení jímky **Povolit přípravu** , aby ADF mohl použít [základ](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). Základem může být, že ADF načte data hromadně.
* Při provádění aktivity toku dat z kanálu musíte vybrat objekt BLOB nebo ADLS Gen2 umístění úložiště pro přípravu dat během hromadného načítání.

## <a name="optimizing-for-files"></a>Optimalizace pro soubory

V každé transformaci můžete nastavit schéma dělení, které má Datová továrna použít na kartě optimalizace.
* V případě menších souborů můžete najít, že výběr *jednoho oddílu* může někdy fungovat lépe a rychleji než vyžádat Spark, aby rozdělil vaše malé soubory.
* Pokud nemáte dostatek informací o zdrojových datech, vyberte možnost *kruhové dotazování* na oddíly a nastavte počet oddílů.
* Pokud vaše data obsahují sloupce, které mohou být vhodnými klíči hash, vyberte možnost *dělení hodnoty hash*.

Při ladění v náhledu dat a při ladění kanálu se limity a velikosti vzorkování pro zdrojové datové sady na základě souborů vztahují pouze na počet vrácených řádků, nikoli na počet čtených řádků. To může mít vliv na výkon při spuštění ladění a může způsobit selhání toku.
* Clustery ladění jsou ve výchozím nastavení malými clustery s jedním uzlem a doporučujeme pro ladění použít ukázkové malé soubory. Přejděte na nastavení ladění a najeďte na malou podmnožinu dat pomocí dočasného souboru.

    ![Nastavení ladění](media/data-flow/debugsettings3.png "Nastavení ladění")

### <a name="file-naming-options"></a>Možnosti pojmenovávání souborů

Nejběžnější způsob, jak zapsat transformovaná data v části mapování toků dat pro zápis do objektů BLOB nebo ADLS úložiště souborů V jímky musíte vybrat datovou sadu, která odkazuje na kontejner nebo složku, nikoli na pojmenovaný soubor. Protože mapování toku dat používá Spark ke spuštění, váš výstup je rozdělen do více souborů na základě schématu vytváření oddílů.

Běžným schématem vytváření oddílů je výběr _výstupu do jediného souboru_, který sloučí všechny soubory výstupních součástí do jednoho souboru v jímky. Tato operace vyžaduje, aby se výstup snížil na jeden oddíl na jednom uzlu clusteru. Pokud kombinujete mnoho velkých zdrojových souborů do jednoho výstupního souboru, můžete použít prostředky uzlu clusteru.

Aby se zabránilo vyčerpání prostředků výpočetních uzlů, ponechte výchozí optimalizované schéma v toku dat a přidejte do kanálu aktivitu kopírování, která sloučí všechny soubory součástí z výstupní složky do nového jediného souboru. Tato technika odděluje akci transformace ze slučování souborů a dosáhne stejného výsledku jako nastavení _výstupu do jediného souboru_.

### <a name="looping-through-file-lists"></a>Procházení seznamem souborů

Tok dat mapování bude proveden lépe, pokud zdrojová transformace prochází přes více souborů místo smyček prostřednictvím smyčky pro každou aktivitu. Ve zdrojové transformaci doporučujeme používat zástupné znaky nebo seznamy souborů. Proces toku dat se zrychluje tím, že umožňuje opakování smyčky v rámci clusteru Spark. Další informace najdete v tématu [zástupné znaky v transformaci zdroje](data-flow-source.md#file-based-source-options).

Pokud máte například seznam datových souborů z července 2019, které chcete zpracovat ve složce v Blob Storage, níže je zástupný znak, který můžete použít ve své zdrojové transformaci.

```DateFiles/*_201907*.txt```

Když použijete zástupné znaky, kanál bude obsahovat jenom jednu aktivitu toku dat. To bude mít lepší výkon než vyhledávání v úložišti objektů blob, které pak projde všemi odpovídajícími soubory pomocí příkazu ForEach s aktivitou spustit tok dat uvnitř.

### <a name="optimizing-for-cosmosdb"></a>Optimalizace pro CosmosDB

Nastavení propustnosti a vlastností dávky u jímky CosmosDB se projeví pouze během provádění tohoto toku dat z aktivity toku dat kanálu. Po spuštění toku dat budou v CosmosDB dodržena původní nastavení kolekce.

* Velikost dávky: Vypočítejte přibližnou velikost řádků dat a ujistěte se, že velikost dávky rowSize * je menší než 2 000 000. Pokud je, zvyšte velikost dávky, abyste získali lepší propustnost.
* Througput: tady nastavte vyšší propustnost, aby bylo možné dokumentům zapisovat rychleji na CosmosDB. Na základě nastavení vysoké propustnosti Prosím mějte na paměti vyšší náklady na RU.
*   Rozpočet propustnosti zápisu: použijte hodnotu, která je menší než celková ru za minutu. Pokud máte tok dat s vysokým počtem Spark partitiongs, nastavení propustnosti rozpočtu umožníte větší rovnováhu mezi těmito oddíly.

## <a name="next-steps"></a>Další kroky

Další články toku dat související s výkonem:

- [Karta optimalizace toku dat](concepts-data-flow-overview.md#optimize)
- [Aktivita toku dat](control-flow-execute-data-flow-activity.md)
- [Sledování výkonu toku dat](concepts-data-flow-monitoring.md)
