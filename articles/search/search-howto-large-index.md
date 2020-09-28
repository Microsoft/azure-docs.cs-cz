---
title: Indexování velkých datových sad pomocí předdefinovaných indexerů
titleSuffix: Azure Cognitive Search
description: Strategie pro velké indexování dat nebo výpočetně náročné indexování prostřednictvím dávkového režimu, rezdrojů a technik pro plánované, paralelní a distribuované indexování.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 081f073fa4933d67604173d2169a7abdc3ac7c3f
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403564"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Indexování velkých datových sad v Azure Kognitivní hledání

Azure Kognitivní hledání podporuje [dva základní přístupy](search-what-is-data-import.md) k importu dat do indexu vyhledávání: *vkládání* dat do indexu prostřednictvím kódu programu nebo nasměrování služby [Azure kognitivní hledání indexerem](search-indexer-overview.md) na podporovaný zdroj dat, který se v datech *vyžádá* .

Vzhledem k nárůstu nebo zpracování objemů dat se můžete setkat s tím, že jednoduché nebo výchozí strategie indexování už nejsou praktické. Pro Azure Kognitivní hledání je k dispozici několik přístupů k větším datovým sadám, od způsobu struktury požadavků na nahrání dat, k používání indexerem specifického pro plánované a distribuované úlohy.

Stejné techniky platí i pro dlouhotrvající procesy. Konkrétně postup, který je popsaný v [paralelním indexování](#parallel-indexing) , je užitečný pro výpočetně náročné indexování, jako je například analýza obrázku nebo zpracování přirozeného jazyka v [kanálu rozšíření AI](cognitive-search-concept-intro.md).

V následujících částech najdete techniky pro indexování velkých objemů dat pomocí rozhraní push API i indexerů.

## <a name="use-the-push-api"></a>Použití rozhraní API push

Při vkládání dat do indexu pomocí metody [Add documents REST API](/rest/api/searchservice/addupdate-or-delete-documents) nebo [indexu](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index)existuje několik klíčových důležitých informací, které mají vliv na rychlost indexování. Tyto faktory jsou popsány v níže uvedené části a rozsah z nastavení kapacity služby na optimalizace kódu.

Další informace a ukázky kódu, které ilustrují indexování modelu nabízených oznámení, najdete v tématu [kurz: optimalizace propustnosti indexování](tutorial-optimize-indexing-push-api.md).

### <a name="capacity-of-your-service"></a>Kapacita vaší služby

Jako první krok zkontrolujte charakteristiky a [omezení](search-limits-quotas-capacity.md) vrstvy, na které jste službu zřídili. Jedním z klíčových různých faktorů mezi cenovými úrovněmi je velikost a rychlost oddílů, která má přímý vliv na rychlost indexování. Pokud jste službu vyhledávání zřídili na úrovni, která pro úlohu není dostatečná, upgrade na novou úroveň může být nejjednodušší a nejúčinnější řešení pro zvýšení propustnosti indexování.

Až budete s vrstvou spokojeni, může vám další krok zvýšit počet oddílů. Přidělení oddílu se dá po počátečním indexování znovu upravit, aby se snížily celkové náklady na provoz služby.

> [!NOTE]
> Přidání dalších replik může také zvýšit rychlost indexování, ale není zaručeno. Na druhé straně další repliky zvyšují objem dotazů, které vaše služba vyhledávání může zpracovat. Repliky představují také klíčovou komponentu pro získání [smlouvy SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
>
> Než začnete přidávat oddíly a repliky nebo upgradovat na vyšší úroveň, vezměte v úvahu peněžní náklady a čas přidělení. Přidávání oddílů může významně zvýšit rychlost indexování, ale přidání nebo odebrání může trvat až 15 minut až několik hodin. Další informace najdete v dokumentaci k [Nastavení kapacity](search-capacity-planning.md).
>

### <a name="review-index-schema"></a>Kontrola schématu indexu

Schéma indexu hraje důležitou roli při indexování dat. Další pole, která máte, a další vlastnosti, které jste nastavili (například *prohledávatelné*, *plošky*nebo *filtrovatelné*), přispívají ke zvýšení času indexování. Obecně platí, že byste měli vytvořit a zadat jenom pole, která skutečně potřebujete ve vyhledávacím indexu.

> [!NOTE]
> Aby se zachovala velikost dokumentu, vyhněte se přidávání nequeryablech dat do indexu. Image a další binární data se nedají přímo prohledávat a v indexu by se neměly ukládat. Pro integraci nequeryablech dat do výsledků hledání byste měli definovat pole bez možností vyhledávání, které ukládá odkaz na adresu URL do daného prostředku.

### <a name="check-the-batch-size"></a>Zkontroluje velikost dávky.

Jedním z nejjednodušších mechanismů indexování větší sady dat je odeslání více dokumentů nebo záznamů v jednom požadavku. Pokud je celá datová část kratší než 16 MB, může požadavek zpracovat až 1000 dokumentů v operaci hromadného nahrávání. Tato omezení platí bez ohledu na to, zda používáte metodu [Add documents REST API](/rest/api/searchservice/addupdate-or-delete-documents) nebo [index](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index) v sadě .NET SDK. Pro obě rozhraní API byste měli v těle každého požadavku zabalit 1000 dokumentů.

Použití dávek k indexování dokumentů významně vylepšuje výkon při indexování. Stanovení optimální velikosti dávky pro vaše data je klíčovou součástí Optimalizace rychlosti indexování. Optimální velikost dávky ovlivňují tyto dva primární faktory:

+ Schéma indexu
+ Velikost dat

Vzhledem k tomu, že optimální velikost dávky závisí na vašem indexu a datech, nejlepším přístupem je testování různých velikostí dávek a určení toho, jaké jsou výsledky nejrychlejší indexování pro váš scénář. V tomto [kurzu](tutorial-optimize-indexing-push-api.md) najdete vzorový kód pro testování velikosti dávek pomocí sady .NET SDK. 

### <a name="number-of-threadsworkers"></a>Počet vláken/pracovníků

Abyste plně využili výhod indexování Kognitivní hledání Azure, budete pravděpodobně muset použít více vláken k posílání požadavků dávkového indexování do služby současně.  

Optimální počet vláken určuje:

+ Úroveň služby vyhledávání
+ Počet oddílů
+ Velikost vašich dávek
+ Schéma indexu

Tuto ukázku a test můžete upravit pomocí různých počtů vláken, abyste zjistili optimální počet vláken pro váš scénář. Pokud ale máte několik souběžně běžících vláken, měli byste být schopni využít většinu zisků z hlediska efektivity. 

> [!NOTE]
> Když zvýšíte úroveň služby vyhledávání nebo zvětšíte oddíly, měli byste také zvýšit počet souběžných vláken.

Při navýšení požadavků na službu vyhledávání dojde v případě, že se zobrazí [stavové kódy http](/rest/api/searchservice/http-status-codes) indikující, že požadavek nebyl zcela úspěšný. Při indexování jsou dva běžné stavové kódy HTTP:

+ **služba 503 není k dispozici** – Tato chyba znamená, že systém je zatížen velkým zatížením a váš požadavek nejde v tuto chvíli zpracovat.
+ **207 s více stavy** – Tato chyba znamená, že některé dokumenty byly úspěšně dokončeny, ale nejméně jedna se nezdařila.

### <a name="retry-strategy"></a>Strategie opakování

Pokud dojde k selhání, žádosti by se měly opakovat pomocí [exponenciální strategie omezení rychlosti opakování](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

Sada .NET SDK pro Azure Kognitivní hledání automaticky opakuje 503s a další neúspěšné požadavky, ale budete muset implementovat vlastní logiku a zkusit to znovu 207s. K implementaci strategie opakování můžete také použít Open Source nástroje, jako je [Polly](https://github.com/App-vNext/Polly) .

### <a name="network-data-transfer-speeds"></a>Rychlosti přenosu dat v síti

Rychlost přenosu dat v síti může být omezujícím faktorem při indexování dat. Indexování dat z prostředí Azure je snadný způsob, jak urychlit indexování.

## <a name="use-indexers-pull-api"></a>Použití indexerů (rozhraní API pro vyžádání obsahu)

[Indexery](search-indexer-overview.md) se používají k procházení podporovaných zdrojů dat Azure pro prohledávatelný obsah. I když není specificky určená pro indexování ve velkém měřítku, je k dispozici několik možností indexeru, které jsou zvláště užitečné při používání větších datových sad:

+ Plánovače umožňují v pravidelných intervalech vyřídit indexování, takže je můžete v průběhu času rozložit.
+ Naplánované indexování může pokračovat v posledním známém bodu zastavení. Pokud zdroj dat není plně procházen v průběhu 24 hodin, indexer bude pokračovat v indexování dvou dnů na všech místech, kde se nachází na levé straně.
+ Rozdělení dat na menší jednotlivé zdroje dat umožňuje paralelní zpracování. Zdrojová data můžete rozdělit do menších součástí, například do více kontejnerů v úložišti objektů BLOB v Azure, a pak vytvořit odpovídající více [objektů zdroje dat](/rest/api/searchservice/create-data-source) v Azure kognitivní hledání, které je možné indexovat paralelně.

> [!NOTE]
> Indexery jsou specifické pro zdroj dat. použití přístupu indexeru je možné realizovat jenom pro vybrané zdroje dat v Azure: [SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [BLOB Storage](search-howto-indexing-azure-blob-storage.md), [Table Storage](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="check-the-batchsize-argument-on-create-indexer"></a>Podívejte se na argument batchSize u Create indexer.

Podobně jako u rozhraní push API umožňují indexerům nakonfigurovat počet položek na dávku. U indexerů založených na [REST API vytvořit indexer](/rest/api/searchservice/Create-Indexer)můžete nastavit `batchSize` argument pro přizpůsobení tohoto nastavení tak, aby lépe odpovídal charakteristikám vašich dat. 

Výchozí velikosti dávek jsou specifické pro zdroj dat. Azure SQL Database a Azure Cosmos DB mají výchozí velikost dávky 1000. Indexování objektů blob Azure naopak nastavuje velikost dávky na 10 dokumentů v rozpoznávání větší průměrné velikosti dokumentu. 

### <a name="scheduled-indexing"></a>Naplánované indexování

Plánování indexeru je důležitým mechanismem pro zpracování rozsáhlých datových sad a také pomalým procesům, jako je analýza obrázků v kanálu vyhledávání vnímání. Zpracování indexeru funguje v průběhu 24 hodin. Pokud se zpracování neúspěšně dokončí do 24 hodin, chování plánování indexeru může fungovat na vaši výhodu. 

Podle návrhu plánované indexování začíná v určitých intervalech a úloha se obvykle dokončuje před pokračováním v dalším naplánovaném intervalu. Pokud se ale zpracování nedokončilo v intervalu, zastaví se indexer (protože byl mimo čas). V dalším intervalu se zpracování pokračuje tam, kde naposledy skončila, a systém udržuje přehled o tom, kde k tomu dochází. 

V praktických případech se pro zatížení indexů, které pokrývá několik dní, dá indexer vložit do 24 hodin. Když indexování pokračuje za dalších 24 hodin, restartuje se v posledním známém dokumentu. Tímto způsobem může indexer pracovat svým způsobem prostřednictvím nevyřízených položek dokumentů v rámci série dnů, dokud nebudou zpracovány všechny nezpracované dokumenty. Další informace o obecném nastavení plánů najdete v tématu [Vytvoření indexeru REST API](/rest/api/searchservice/Create-Indexer) nebo v tématu [postup plánování indexerů pro Azure kognitivní hledání](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Paralelní indexování

Strategie paralelního indexování je založena na indexování více zdrojů dat v úlohách, kde každá definice zdroje dat určuje podmnožinu dat. 

Pro Neběžné, výpočetní požadavky na indexování, jako je rozpoznávání OCR u naskenovaných dokumentů v kanálu vyhledávání rozpoznávání, analýza obrázků nebo zpracování v přirozeném jazyce – je často nejvhodnějším přístupem k dokončení dlouhotrvajícího procesu v nejkratší době. Pokud můžete eliminovat nebo snižovat požadavky na dotazy, paralelní indexování na službě, která současně nezpracovává dotazy, je nejlepší volbou pro práci prostřednictvím velkého těla pomalého zpracování obsahu. 

Paralelní zpracování má tyto prvky:

+ Rozdělte zdrojová data mezi více kontejnerů nebo více virtuálních složek uvnitř stejného kontejneru. 
+ Namapujte každou Mini datovou sadu na vlastní [zdroj dat](/rest/api/searchservice/create-data-source), který se spáruje s vlastním [indexerem](/rest/api/searchservice/create-indexer).
+ Pro rozpoznávání rozpoznávání se v každé definici indexeru odkázat na stejný [dovednosti](/rest/api/searchservice/create-skillset) .
+ Zapište do stejného cílového vyhledávacího indexu. 
+ Naplánujte, aby se všechny indexery spouštěly ve stejnou dobu.

> [!NOTE]
> V Azure Kognitivní hledání nemůžete přiřadit jednotlivé repliky nebo oddíly k indexování nebo zpracování dotazů. Systém Určuje, jak se prostředky používají. Aby bylo možné pochopit dopad na výkon dotazů, můžete si před přechodem do provozu vyzkoušet paralelní indexování v testovacím prostředí.  

### <a name="how-to-configure-parallel-indexing"></a>Jak nakonfigurovat paralelní indexování

U indexerů se kapacita zpracování volně vychází z jednoho subsystému indexeru pro každou jednotku služby (SU), kterou používá vaše vyhledávací služba. V Azure Kognitivní hledání Services zřízených na úrovni Basic nebo Standard s nejméně dvěma replikami je možné víc souběžných indexerů. 

1. V [Azure Portal](https://portal.azure.com)na stránce **Přehled** řídicího panelu vyhledávací služby zkontrolujte **cenovou úroveň** a potvrďte, že se může přizpůsobit paralelnímu indexování. Úrovně Basic a Standard nabízejí více replik.

2. Můžete spustit libovolný počet indexerů paralelně jako počet jednotek hledání ve vaší službě. V **Nastavení**  >  **škálování**můžete [Zvětšit repliky](search-capacity-planning.md) nebo oddíly pro paralelní zpracování: jednu další repliku nebo oddíl pro každou úlohu indexeru. Ponechte dostatečný počet pro existující svazek dotazu. Omezení zatížení dotazů na indexování není dobré kompromisy.

3. Distribuujte data do více kontejnerů na úrovni, které můžou Azure Kognitivní hledání indexerům dosáhnout. Může se jednat o více tabulek v Azure SQL Database, více kontejnerů v úložišti objektů BLOB v Azure nebo víc kolekcí. Definujte jeden objekt zdroje dat pro každou tabulku nebo kontejner.

4. Vytvoření a naplánování více indexerů, které mají být spuštěny paralelně:

   + Vybere službu se šesti replikami. Nakonfigurujte šest indexerů, každý z nich mapovaných na zdroj dat, který obsahuje jednu šestou datovou sadu, pro 6 rozdělte celou datovou sadu. 

   + Najeďte jednotlivé indexery na stejný index. Pro zátěžové vyhledávání najeďte jednotlivé indexery na stejný dovednosti.

   + V rámci každé definice indexeru Naplánujte stejný vzor spuštění za běhu. Například `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` vytvoří plán na 2018-05-15 u všech indexerů spuštěných ve osmi hodinových intervalech.

V naplánovaném čase začínají všechny indexery, načítání dat, použití rozšíření (Pokud jste nakonfigurovali kanál vyhledávání rozpoznávání) a zapisujete do indexu. Azure Kognitivní hledání nezamkne index pro aktualizace. Souběžné zápisy jsou spravovány s opakováním, pokud konkrétní zápis při prvním pokusu neproběhne úspěšně.

> [!Note]
> Při zvyšování počtu replik zvažte zvýšení počtu oddílů, pokud je velikost indexu rozrůstat, aby se významně zvýšila. Oddíly ukládají řezy indexovaného obsahu; více oddílů, které máte, menší řez každého z nich musí být uložen.

## <a name="see-also"></a>Viz také

+ [Přehled indexeru](search-indexer-overview.md)
+ [Indexování na portálu](search-import-data-portal.md)
+ [Azure SQL Database indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexer pro Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indexer pro Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indexer pro Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpečení v Azure Kognitivní hledání](search-security-overview.md)