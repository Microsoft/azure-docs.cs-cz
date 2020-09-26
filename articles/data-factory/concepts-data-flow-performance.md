---
title: Mapování výkonu toku dat a Průvodce optimalizací
description: Přečtěte si o klíčových faktorech, které mají vliv na výkon mapování datových toků v Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 08/12/2020
ms.openlocfilehash: 4a78e966d420591ebe7a9607777158cf17ddf698
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91370874"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Průvodce optimalizací výkonu a ladění toků dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mapování toků dat v Azure Data Factory poskytuje rozhraní bez kódu pro návrh a spouštění transformací dat ve velkém měřítku. Pokud nejste obeznámeni s toky mapování dat, přečtěte si téma [Přehled toku dat mapování](concepts-data-flow-overview.md). V tomto článku se seznámíte s různými způsoby, jak vyladit a optimalizovat toky dat, aby splňovaly srovnávací testy výkonu.

Podívejte se na toto video, kde se dozvíte, že ukázka časování transformuje data pomocí toků dat.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="testing-data-flow-logic"></a>Testování logiky toku dat

Při navrhování a testování toků dat z uživatelského rozhraní ADF vám režim ladění umožňuje interaktivní testování proti aktivnímu clusteru Spark. Díky tomu můžete zobrazovat náhled dat a spouštět toky dat bez čekání na zahřívání clusteru. Další informace naleznete v tématu [režim ladění](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flow-performance"></a>Monitorování výkonu toku dat

Po ověření logiky transformace pomocí režimu ladění spusťte celý tok dat jako aktivitu v kanálu. Toky dat jsou v kanálu v provozu pomocí [aktivity spustit tok dat](control-flow-execute-data-flow-activity.md). Aktivita toku dat má jedinečné prostředí monitorování v porovnání s jinými Azure Data Factory aktivitami, které zobrazují podrobný plán spuštění a profil výkonu logiky transformace. Chcete-li zobrazit podrobné informace o sledování toku dat, klikněte na ikonu brýlí v aktivitě spustit výstup kanálu. Další informace najdete v tématu [monitorování toků dat mapování](concepts-data-flow-monitoring.md).

![Sledování toku dat](media/data-flow/monitoring-details.png "Monitor toku dat 2")

Při monitorování výkonu toku dat existují čtyři možná kritická místa pro hledání:

* Čas spuštění clusteru
* Čtení ze zdroje
* Čas transformace
* Zápis do jímky 

![Sledování toku dat](media/data-flow/monitoring-performance.png "Monitor toku dat 3")

Doba spuštění clusteru je doba potřebná k vystavení clusteru Apache Spark. Tato hodnota je umístěna v pravém horním rohu obrazovky monitorování. Toky dat běží na modelu za běhu, kde každá úloha používá izolovaný cluster. Tento čas zahájení obvykle trvá 3-5 minut. U sekvenčních úloh to můžete zkrátit tím, že zapnete hodnotu TTL (Time to Live). Další informace najdete v tématu [optimalizace Azure Integration runtime](#ir).

Toky dat využívají Optimalizátor Sparku, který mění pořadí a spouští vaši obchodní logiku ve fázích, aby je bylo možné co nejrychleji provést. Pro každou jímku, do které tok dat zapisuje, výstup monitorování vypíše dobu trvání jednotlivých fází transformace spolu s časem potřebným k zápisu dat do jímky. Čas, který je největší, je pravděpodobný kritickým bodem toku dat. Pokud fáze transformace, která využívá největší, obsahuje zdroj, můžete chtít zobrazit další informace o optimalizaci doby čtení. Pokud transformace trvá dlouhou dobu, může být nutné změnit rozdělení nebo zvětšit velikost prostředí Integration runtime. Pokud je doba zpracování jímky velká, možná budete potřebovat škálovat databázi nebo ověřit, že nechcete mít výstup do jednoho souboru.

Po zjištění kritického bodu toku dat můžete zvýšit výkon pomocí níže uvedených strategií optimalizace.

## <a name="optimize-tab"></a>Karta Optimalizace

Karta **optimalizace** obsahuje nastavení pro konfiguraci schématu dělení clusteru Spark. Tato karta existuje v každé transformaci toku dat a určuje, zda chcete znovu rozdělit data **po** dokončení transformace. Úpravy dělení poskytují kontrolu nad distribucí vašich dat mezi výpočetními uzly a optimalizací dat, které mohou mít jak pozitivní, tak negativní dopad na celkový výkon toku dat.

![Snímek obrazovky zobrazuje kartu optimalizace, která zahrnuje možnost oddílu, typ oddílu a počet oddílů.](media/data-flow/optimize.png)

Ve výchozím nastavení je vybrána *možnost použít aktuální rozdělení do oddílů* , která určuje, Azure Data Factory zachovat aktuální výstupní oddíl transformace. Při změně rozdělení dat do oddílů se ve většině scénářů doporučuje *použít aktuální dělení* . Scénáře, ve kterých můžete chtít znovu rozdělit data, patří po agregacích a spojeních, které významně zkosí vaše data nebo využívají zdrojové oddíly databáze SQL.

Chcete-li změnit dělení na jakékoli transformaci, vyberte kartu **optimalizace** a vyberte přepínač **nastavit dělení** . Zobrazí se řada možností pro dělení. Nejlepší způsob dělení se liší v závislosti na vašich datových svazcích, kandidátních klíčích, hodnotách null a mohutnosti. 

> [!IMPORTANT]
> Jeden oddíl kombinuje všechna distribuovaná data do jednoho oddílu. Jedná se o velmi pomalou operaci, která významně ovlivňuje veškerou transformaci a zápisy po celém proudu. Azure Data Factory důrazně doporučuje použití této možnosti, pokud není k dispozici explicitní obchodní důvod.

Následující možnosti dělení jsou k dispozici v každé transformaci:

### <a name="round-robin"></a>Kruhové dotazování 

Kruhové dotazování distribuuje data rovnoměrně napříč oddíly. Pomocí kruhového dotazování nemusíte mít vhodné klíčové kandidáty k implementaci ucelené strategie vytváření oddílů. Můžete nastavit počet fyzických oddílů.

### <a name="hash"></a>Hodnoty hash

Azure Data Factory vytvoří hodnotu hash sloupců pro vytvoření stejnorodých oddílů tak, aby řádky s podobnými hodnotami byly ve stejném oddílu. Když použijete možnost hash, otestujete možnou hodnotu zešikmení oddílu. Můžete nastavit počet fyzických oddílů.

### <a name="dynamic-range"></a>Dynamický rozsah

Dynamický rozsah používá dynamické rozsahy Spark na základě sloupců nebo výrazů, které zadáte. Můžete nastavit počet fyzických oddílů. 

### <a name="fixed-range"></a>Pevný rozsah

Sestavte výraz, který poskytuje pevný rozsah pro hodnoty v rámci sloupců s dělenými daty. Abyste se vyhnuli zkosení oddílu, měli byste před použitím této možnosti dobře pochopit svá data. Hodnoty, které zadáte pro výraz, se používají jako součást funkce oddílu. Můžete nastavit počet fyzických oddílů.

### <a name="key"></a>Klíč

Pokud máte dobré znalosti o mohutnosti vašich dat, může být vytváření oddílů dobrým zvykem. Klíčové vytváření oddílů vytváří oddíly pro každou jedinečnou hodnotu ve sloupci. Počet oddílů nejde nastavit, protože číslo je založené na jedinečných hodnotách v datech.

> [!TIP]
> Ruční nastavení schématu dělení přesadí data a může kompenzovat výhody Optimalizátoru Sparku. Osvědčeným postupem je nenastavit oddíly ručně, pokud nepotřebujete.

## <a name="optimizing-the-azure-integration-runtime"></a><a name="ir"></a> Optimalizace Azure Integration Runtime

Toky dat běží na clusterech Spark, které se provedou v době běhu. V modulu Integration runtime (IR) aktivity se definuje konfigurace pro použitý cluster. Při definování prostředí Integration runtime jsou k dispozici tři požadavky na výkon: typ clusteru, velikost clusteru a doba do provozu.

Další informace o tom, jak vytvořit Integration Runtime, najdete v tématu [Integration runtime v Azure Data Factory](concepts-integration-runtime.md).

### <a name="cluster-type"></a>Typ clusteru

Existují tři dostupné možnosti pro typ přístupnosti clusteru Spark: obecné účely, optimalizace paměti a optimalizované pro výpočty.

Clustery pro **obecné účely** jsou výchozími možnostmi a budou ideální pro většinu úloh toku dat. Ty mají za následek nejlepší rovnováhu mezi výkonem a náklady.

Pokud má tok dat mnoho spojení a hledání, můžete chtít použít **paměťově optimalizovaný** cluster. Paměťově optimalizované clustery můžou ukládat víc dat do paměti a minimalizovat všechny chyby z důvodu nedostatku paměti, které se vám můžou stát. Optimalizovaná paměť má nejvyšší cenový bod na jádro, ale také obvykle vede k většímu počtu úspěšných kanálů. Pokud dojde k chybám při spouštění toků dat, přepněte na paměťově optimalizované Azure IR konfiguraci. 

**Optimalizované výpočty** nejsou ideální pro pracovní postupy ETL a nedoporučují Azure Data Factory tým pro většinu produkčních úloh. Pro jednodušší, nepaměťově náročné transformace dat, jako je filtrování dat nebo přidávání odvozených sloupců, je možné výpočetní clustery, které jsou optimalizované pro výpočty, používat s levnější cenou za jádro.

### <a name="cluster-size"></a>Velikost clusteru

Toky dat distribuují zpracování dat do různých uzlů v clusteru Spark, aby se operace prováděly paralelně. Cluster Spark s více jádry zvyšuje počet uzlů ve výpočetním prostředí. Další uzly zvyšují výpočetní výkon toku dat. Zvýšení velikosti clusteru je často snadný způsob, jak zkrátit dobu zpracování.

Výchozí velikost clusteru je čtyři uzly ovladačů a čtyři pracovní uzly.  Při zpracování více dat se doporučuje větší clustery. Níže jsou uvedeny možné možnosti změny velikosti:

| Jádra pracovního procesu | Jádra ovladače | Celkem jader | Poznámky |
| ------------ | ------------ | ----------- | ----- |
| 4 | 4 | 8 | Není k dispozici pro optimalizaci Compute. |
| 8 | 8 | 16 | |
| 16 | 16 | 32 | |
| 32 | 16 | 48 | |
| 64 | 16 | 80 | |
| 128 | 16 | 144 | |
| 256 | 16 | 272 | |

Ceny datových toků se účtují za Vcore, což znamená, že do této funkce patří i velikost clusteru i doba provádění. Při horizontálním navýšení kapacity se zvýší náklady na cluster za minutu, ale celková doba se sníží.

> [!TIP]
> Existuje strop, jak velká velikost clusteru ovlivňuje výkon toku dat. V závislosti na velikosti dat je bod, ve kterém zvýšení velikosti clusteru přestane zvyšovat výkon. Například pokud máte více uzlů než oddíly dat, přidání dalších uzlů nebude užitečné. Osvědčeným postupem je začít malým a horizontálním škálováním, aby se splnily požadavky na výkon. 

### <a name="time-to-live"></a>Hodnota TTL (Time to Live)

Ve výchozím nastavení každá aktivita toku dat natočí nový cluster na základě konfigurace IR. Doba spuštění clusteru trvá několik minut a zpracování dat nepůjde spustit, dokud nebude dokončeno. Pokud vaše kanály obsahují více **sekvenčních** toků dat, můžete povolit hodnotu TTL (Time to Live). Zadání hodnoty TTL (Time to Live) udržuje cluster aktivní po určitou dobu po dokončení jeho spuštění. Pokud se nová úloha začne používat v čase TTL během času TTL, bude se výrazně snižovat i po opětovném spuštění stávajícího clusteru. Po dokončení druhé úlohy zůstane cluster opět aktivní pro dobu TTL.

V jednom clusteru může běžet jenom jedna úloha. Pokud je k dispozici cluster, ale dva toky dat se spustí, použije se živý cluster jenom s jedním. Druhá úloha si vytočí svůj vlastní izolovaný cluster.

Pokud se většina toků dat spouští paralelně, nedoporučuje se povolit hodnotu TTL. 

> [!NOTE]
> Doba do Live není při použití prostředí Integration runtime pro automatické řešení dostupná.

## <a name="optimizing-sources"></a>Optimalizace zdrojů

U každého zdroje s výjimkou Azure SQL Database se doporučuje jako vybraná hodnota **použít aktuální rozdělení na oddíly** . Při čtení ze všech ostatních zdrojových systémů data toků automaticky rozdělí data rovnoměrně na základě velikosti dat. Vytvoří se nový oddíl pro přibližně každých 128 MB dat. Při zvýšení velikosti dat se zvýší počet oddílů.

Jakékoli vlastní dělení se provede *po* čtení Sparku v datech a negativně ovlivní výkon toku dat. Vzhledem k tomu, že jsou data rovnoměrně rozdělená na čtení, to se nedoporučuje. 

> [!NOTE]
> Rychlost čtení může být omezená propustností vašeho zdrojového systému.

### <a name="azure-sql-database-sources"></a>Azure SQL Database zdroje

Azure SQL Database má jedinečnou možnost dělení s názvem source partitioning. Povolením vytváření oddílů můžete zlepšit dobu čtení ze služby Azure SQL DB povolením paralelních připojení na zdrojovém systému. Zadejte počet oddílů a způsob dělení dat. Použijte sloupec oddílu s vysokou mohutnosti. Můžete také zadat dotaz, který odpovídá schématu dělení zdrojové tabulky.

> [!TIP]
> V případě zdrojového dělení jsou vstupně-výstupní operace SQL Server kritickým bodem. Přidání příliš velkého počtu oddílů může nasycení zdrojové databáze. Při použití této možnosti je ideální obecně čtyři nebo pět oddílů.

![Vytváření oddílů zdrojového kódu](media/data-flow/sourcepart3.png "Vytváření oddílů zdrojového kódu")

#### <a name="isolation-level"></a>Úroveň izolace

Úroveň izolace čtení ve zdrojovém systému Azure SQL má vliv na výkon. Pokud zvolíte číst nepotvrzené, získáte nejrychlejší výkon a znemožníte zámkům v databázi. Další informace o úrovních izolace SQL najdete v tématu [Principy úrovní izolace](https://docs.microsoft.com/sql/connect/jdbc/understanding-isolation-levels?view=sql-server-ver15).

#### <a name="read-using-query"></a>Číst pomocí dotazu

Můžete číst z Azure SQL Database pomocí tabulky nebo dotazu SQL. Pokud spouštíte dotaz SQL, musí být dotaz dokončen, aby bylo možné spustit transformaci. Dotazy SQL mohou být užitečné při přebírání operací, které mohou být spuštěny rychleji, a snížit množství dat čtených z SQL Server, jako jsou příkazy SELECT, WHERE a JOIN. Při vkládání operací přijdete o schopnost sledovat, jestli jsou transformace a jejich výkon, než data přijde do toku dat.

### <a name="azure-synapse-analytics-sources"></a>Zdroje analýzy Azure synapse

Pokud používáte Azure synapse Analytics, nastavení s názvem **Povolit přípravu** existuje v možnostech zdroje. Díky tomu může ADF číst z synapse s využitím [základny](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15), která významně zlepšuje výkon při čtení. Při povolení podkladu je potřeba zadat Blob Storage služby Azure nebo Azure Data Lake Storage Gen2 pracovní umístění v nastavení aktivity toku dat.

![Zapnout pracovní režim](media/data-flow/enable-staging.png "Zapnout pracovní režim")

### <a name="file-based-sources"></a>Souborové zdroje

Zatímco datové toky podporují různé typy souborů, Azure Data Factory doporučuje použít formát Spark-Native Parquet pro optimální dobu čtení a zápisu.

Pokud používáte stejný tok dat v sadě souborů, doporučujeme číst ze složky, pomocí zástupných cest nebo číst ze seznamu souborů. Spuštění jedné aktivity toku dat může zpracovat všechny vaše soubory ve službě Batch. Další informace o tom, jak tato nastavení nastavit, najdete v dokumentaci konektoru, jako je například [Azure Blob Storage](connector-azure-blob-storage.md#source-transformation).

Pokud je to možné, vyhněte se použití aktivity for-each ke spouštění datových toků v sadě souborů. Tím dojde k tomu, že každá iterace pro každý z nich provede vlastní cluster Spark, který není často nutný a může být nákladný. 

## <a name="optimizing-sinks"></a>Optimalizace jímky

Při zapisování toků dat do jímky dojde k jakémukoli vlastnímu dělení hned před zápisem. Podobně jako u zdroje se ve většině případů doporučuje používat jako možnost vybraný oddíl **aktuální dělení na oddíly** . Dělená data budou zapisovat významně rychlejší než nedělená data, i když vaše cíle není rozdělené do oddílů. Níže jsou uvedeny jednotlivé předpoklady pro různé typy jímky. 

### <a name="azure-sql-database-sinks"></a>Azure SQL Database jímky

Při použití Azure SQL Database by výchozí dělení mělo fungovat ve většině případů. Je pravděpodobné, že vaše jímka může mít příliš mnoho oddílů pro zpracování vaší databáze SQL. Pokud k tomu dojde, snižte počet oddílů vydaných jímkou SQL Database.

#### <a name="disabling-indexes-using-a-sql-script"></a>Zakázání indexů pomocí skriptu SQL

Zakázání indexů před zatížením databáze SQL může významně zlepšit výkon zápisu do tabulky. Před zápisem do jímky SQL spusťte následující příkaz.

`ALTER INDEX ALL ON dbo.[Table Name] DISABLE`

Po dokončení zápisu znovu sestavte indexy pomocí následujícího příkazu:

`ALTER INDEX ALL ON dbo.[Table Name] REBUILD`

Je možné je nativně provádět pomocí skriptů před a po SQL v rámci služby Azure SQL DB nebo synapse jímky v mapování datových toků.

![Zakázat indexy](media/data-flow/disable-indexes-sql.png "Zakázat indexy")

> [!WARNING]
> Při zakázání indexů je tok dat efektivně přebírat řízení databáze a dotazy jsou v tuto chvíli pravděpodobně neúspěšné. V důsledku toho se řada úloh ETL aktivuje v noci uprostřed noční, aby se předešlo tomuto konfliktu. Další informace získáte v informacích o [omezeních zakazování indexů](https://docs.microsoft.com/sql/relational-databases/indexes/disable-indexes-and-constraints?view=sql-server-ver15) .

#### <a name="scaling-up-your-database"></a>Škálování databáze

Naplánujte změnu velikosti zdroje a jímky Azure SQL DB a DW před spuštěním kanálu, abyste zvýšili propustnost a minimalizovali omezení Azure, jakmile dosáhnete limitů DTU. Po dokončení spuštění kanálu změňte velikost databází zpět na normální rychlost běhu.

### <a name="azure-synapse-analytics-sinks"></a>Jímky Azure synapse Analytics

Když zapisujete do služby Azure synapse Analytics, ujistěte se, že **možnost Povolit přípravu** je nastavená na hodnotu true. Díky tomu může ADF zapisovat pomocí [základu](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) , který efektivně načte data do hromadného zatížení. Když použijete základnu, budete muset odkazovat na Azure Data Lake Storage Gen2 nebo účet Azure Blob Storage pro přípravu dat.

Kromě základu se stejné osvědčené postupy vztahují i na Azure synapse Analytics jako Azure SQL Database.

### <a name="file-based-sinks"></a>Jímky založené na souborech 

Zatímco datové toky podporují různé typy souborů, Azure Data Factory doporučuje použít formát Spark-Native Parquet pro optimální dobu čtení a zápisu.

Pokud jsou data rovnoměrně distribuována, použijte pro zápis souborů možnost **aktuální rozdělení** do oddílů.

#### <a name="file-name-options"></a>Možnosti názvu souboru

Při psaní souborů můžete zvolit možnosti pojmenování, které mají vliv na výkon.

![Možnosti jímky](media/data-flow/file-sink-settings.png "možnosti jímky")

Výběr **výchozí** možnosti bude zapisovat nejrychlejší. Každý oddíl se bude rovnat souboru s výchozím názvem Spark. To je užitečné, pokud právě čtete ze složky dat.

Nastavením **vzoru** pro pojmenování dojde k přejmenování každého souboru oddílu na více uživatelsky přívětivého názvu. Tato operace proběhne po zápisu a je mírně pomalejší než volba výchozí. Jednotlivé oddíly umožňují jednotlivé oddíly pojmenovat ručně.

Pokud sloupec odpovídá způsobu, jakým chcete data výstupovat, můžete vybrat **jako data ve sloupci**. Tím dojde k přemístění dat a může to mít vliv na výkon, pokud sloupce nejsou rovnoměrně distribuovány.

**Výstup do jednoho souboru** kombinuje všechna data do jednoho oddílu. To vede k dlouhým časem zápisu, zejména pro velké datové sady. Azure Data Factory tým **vysoce doporučuje tuto** možnost Nevybrat, pokud není k dispozici explicitní obchodní důvod.

### <a name="cosmosdb-sinks"></a>CosmosDB jímky

Při psaní do CosmosDB může změna propustnosti a velikosti dávek během provádění toku dat zvýšit výkon. Tyto změny se projeví pouze během spuštění aktivity toku dat a po uzavření se vrátí k původnímu nastavení kolekce. 

**Velikost dávky:** Vypočítá přibližnou velikost řádků dat a ujistěte se, že velikost řádku * velikost dávky je menší než 2 000 000. Pokud je, zvyšte velikost dávky, abyste získali lepší propustnost.

**Propustnost:** Nastavením vyšší propustnosti zadáte rychlejší zápis dokumentů do CosmosDB. Na základě nastavení vysoké propustnosti mějte na paměti vyšší náklady na RU.

**Rozpočet propustnosti zápisu:** Použijte hodnotu, která je menší než celková ru za minutu. Pokud máte tok dat s vysokým počtem oddílů Spark, nastavení propustnosti rozpočtu umožní v těchto oddílech větší rovnováhu.


## <a name="optimizing-transformations"></a>Optimalizace transformací

### <a name="optimizing-joins-exists-and-lookups"></a>Optimalizace spojení, existence a vyhledávání

#### <a name="broadcasting"></a>Vysílací

V spojeních, vyhledávání a existují transformace, pokud je jeden nebo oba datové proudy dostatečně malé, aby se vešly do paměti pracovních uzlů, můžete optimalizovat výkon tím, že povolíte **všesměrové vysílání**. Všesměrové vysílání je při odesílání malých datových snímků všem uzlům v clusteru. To umožňuje, aby modul Spark prováděl spojení bez nutnosti přesměrovat data ve velkém datovém proudu. Ve výchozím nastavení se modul Spark automaticky rozhodne, jestli se má vysílat jedna strana spojení. Pokud znáte vaše příchozí data a víte, že jeden datový proud bude výrazně menší než druhý, můžete vybrat **pevné** vysílání. Pevné vysílání vynutí, aby Spark vysílaly vybraný datový proud. 

Pokud je velikost všesměrového data pro uzel Spark příliš velká, může dojít k chybě z důvodu nedostatku paměti. Aby nedošlo k chybám, používejte **paměťově optimalizované** clustery. Pokud při provádění toku dat dojde k vypršení časového limitu všesměrového vysílání, můžete vypnout optimalizaci vysílání. Výsledkem ale bude pomalejší provádění toků dat.

![Optimalizace transformace JOIN](media/data-flow/joinoptimize.png "Spojit optimalizaci")

#### <a name="cross-joins"></a>Vzájemné spojení

Pokud ve svých podmínkách připojení používáte literálové hodnoty nebo máte více shod na obou stranách spojení, Spark spustí spojení jako vzájemné spojení. Mezi spojením je plný kartézském produkt, který potom odfiltruje Spojené hodnoty. To je výrazně pomalejší než u jiných typů spojení. Ujistěte se, že na obou stranách podmínky připojení máte odkazy na sloupec, abyste se vyhnuli dopadu na výkon.

#### <a name="sorting-before-joins"></a>Řazení před spojením

Na rozdíl od sloučení slučovacích nástrojů v nástrojích, jako je SSIS, transformace spojení není povinná operace sloučení spojení. Klíče JOIN nevyžadují řazení před transformací. Tým Azure Data Factory v mapování toků dat nedoporučuje používat transformace řazení.

### <a name="repartitioning-skewed-data"></a>Přerozdělení zkosených dat

Některé transformace, jako jsou spojení a agregace, rozdělí vaše oddíly dat a občas můžou vést k zkoseným datům. Zkosených dat znamená, že data nejsou rovnoměrně rozložena mezi oddíly. Silně zkosená data mohou vést k pomalejším transformům a zápisům jímky. Kliknutím na transformaci v zobrazení monitorování můžete v jakémkoli okamžiku spuštění toku dat kontrolovat zešikmení vašich dat.

![Zešikmení a špičatost](media/data-flow/skewness-kurtosis.png "Zešikmení a špičatost")

V zobrazení monitorování se zobrazí způsob distribuce dat napříč jednotlivými oddíly spolu se dvěma metrikami, zešikmení a fluktuace. **Asymetrie** je míra, jak asymetrická data jsou a můžou mít kladnou, nulovou, zápornou nebo nedefinovanou hodnotu. Záporné zkosení znamená, že levý konec je delší než vpravo. **Špičatost** je měřítko, zda jsou data příliš zatížená nebo lehká. Hodnoty vysoké fluktuace nejsou vhodné. Ideální rozsahy zešikmení leží mezi-3 a 3 a rozsahy špičatosti jsou menší než 10. Snadný způsob, jak interpretovat tato čísla, je prohlížení grafu oddílů a zobrazení, zda je 1 panel výrazně větší než zbytek.

Pokud vaše data nejsou rovnoměrně rozdělená po transformaci, můžete k jejímu rozdělení použít [kartu optimalizace](#optimize-tab) . Rozmísení dat trvá čas a nemusí zlepšit výkon toku dat.

> [!TIP]
> Pokud data změníte na oddíly, ale budete mít k dispozici transformace, které přerozdělí data, použijte dělení algoritmu hash u sloupce používaného jako klávesa JOIN.

## <a name="using-data-flows-in-pipelines"></a>Používání toků dat v kanálech 

Při sestavování složitých kanálů s více datovými toky může mít logický tok velký dopad na časování a náklady. Tato část se zabývá dopadem různých strategií architektury.

### <a name="executing-data-flows-in-parallel"></a>Paralelní spouštění toků dat

Pokud spouštíte více toků dat paralelně, ADF vytočí samostatné clustery Spark pro každou aktivitu. To umožňuje, aby každá úloha byla izolovaná a spuštěná paralelně, ale povedlo se současně o několik clusterů.

Pokud se vaše toky dat spouštějí paralelně, doporučuje se Nepovolit vlastnost Azure IR Time to Live, protože by to mohlo vést k několika nevyužitým fondům teplého fondu.

> [!TIP]
> Místo spuštění stejného toku dat v rámci pro každou aktivitu můžete připravit data ve službě Data Lake a použít cesty zástupných znaků ke zpracování dat v jednom toku dat.

### <a name="execute-data-flows-sequentially"></a>Postupné provádění toků dat

Pokud spustíte aktivity toku dat v sekvenci, doporučuje se nastavit hodnotu TTL v konfiguraci Azure IR. ADF znovu použije výpočetní prostředky, což vede k rychlejšímu spuštění clusteru. Každá aktivita bude i nadále izolovaná a získá nový kontext Sparku pro každé spuštění.

Spuštění úloh postupně bude trvat delší dobu, než se provede celý konec až do konce, ale poskytuje čisté oddělení logických operací.

### <a name="overloading-a-single-data-flow"></a>Přetížení jednoho toku dat

Pokud vložíte veškerou logiku do jediného toku dat, ADF spustí v rámci jedné instance Spark celou úlohu. I když se to může zdát jako způsob, jak snížit náklady, kombinuje dohromady různé logické toky a může být obtížné monitorovat a ladit. V případě selhání jedné součásti dojde také k selhání všech ostatních částí úlohy. Tým Azure Data Factory doporučuje organizovat toky dat nezávisle na obchodní logice. Pokud se tok dat příliš rozsáhlý, rozdělí se na samostatné komponenty a zjednoduší se monitorování a ladění. I když neexistují žádné pevné omezení počtu transformací v toku dat, což má příliš mnoho, bude úloha složitá.

## <a name="next-steps"></a>Další kroky

Další články toku dat související s výkonem:

- [Aktivita toku dat](control-flow-execute-data-flow-activity.md)
- [Sledování výkonu toku dat](concepts-data-flow-monitoring.md)
