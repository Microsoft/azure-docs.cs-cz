---
title: Indexování velkých datových sad pomocí předdefinovaných indexerů
titleSuffix: Azure Cognitive Search
description: Strategie pro velké indexování dat nebo výpočetně náročné indexování prostřednictvím dávkového režimu, rezdrojů a technik pro plánované, paralelní a distribuované indexování.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 4ad5e961e390b60784355ff3bc72aca4a2f73e11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77190958"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Indexování velkých datových sad v Azure Kognitivní hledání

Vzhledem k nárůstu nebo zpracování objemů dat se můžete setkat s tím, že jednoduché nebo výchozí strategie indexování už nejsou praktické. Pro Azure Kognitivní hledání je k dispozici několik přístupů k větším datovým sadám, od způsobu struktury požadavků na nahrání dat, k používání indexerem specifického pro plánované a distribuované úlohy.

Stejné techniky platí i pro dlouhotrvající procesy. Konkrétně postup, který je popsaný v [paralelním indexování](#parallel-indexing) , je užitečný pro výpočetně náročné indexování, jako je například analýza obrázku nebo zpracování přirozeného jazyka v [kanálu rozšíření AI](cognitive-search-concept-intro.md).

Následující části Prozkoumejte tři techniky pro indexování velkých objemů dat.

## <a name="option-1-pass-multiple-documents"></a>Možnost 1: předání více dokumentů

Jedním z nejjednodušších mechanismů indexování větší sady dat je odeslání více dokumentů nebo záznamů v jednom požadavku. Pokud je celá datová část kratší než 16 MB, může požadavek zpracovat až 1000 dokumentů v operaci hromadného nahrávání. Tato omezení platí bez ohledu na to, zda používáte metodu [Add documents REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) nebo [index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) v sadě .NET SDK. Pro obě rozhraní API byste měli v těle každého požadavku zabalit 1000 dokumentů.

Dávkové indexování je implementováno pro jednotlivé požadavky pomocí REST nebo .NET nebo prostřednictvím indexerů. Několik indexerů pracuje v různých omezeních. Indexování objektů BLOB v Azure nastavuje velikost dávky na 10 dokumentů v rozpoznávání větší průměrné velikosti dokumentu. U indexerů založených na [REST API vytvořit indexer](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)můžete nastavit `BatchSize` argument pro přizpůsobení tohoto nastavení tak, aby lépe odpovídal charakteristikám vašich dat. 

> [!NOTE]
> Aby se zachovala velikost dokumentu, vyhněte se přidávání nequeryablech dat do indexu. Image a další binární data se nedají přímo prohledávat a v indexu by se neměly ukládat. Pro integraci nequeryablech dat do výsledků hledání byste měli definovat pole bez možností vyhledávání, které ukládá odkaz na adresu URL do daného prostředku.

## <a name="option-2-add-resources"></a>Možnost 2: Přidání prostředků

Služby, které jsou zřízené na jedné ze [standardních cenových úrovní](search-sku-tier.md) , často využívají kapacitu pro úložiště i úlohy (dotazy nebo indexování), což [zvyšuje počet a velikost replik](search-capacity-planning.md) , které představují zjevné řešení pro využívání větších datových sad. Pro dosažení nejlepších výsledků budete potřebovat oba prostředky: oddíly pro úložiště a repliky pro práci s přijímáním dat.

Zvýšení počtu replik a oddílů jsou Fakturovatelné události, které zvyšují vaše náklady, ale pokud průběžně neprovádíte indexování v rámci maximálního zatížení, můžete přidat škálování po dobu trvání procesu indexování a potom po dokončení indexování upravit úrovně prostředků směrem dolů.

## <a name="option-3-use-indexers"></a>Možnost 3: použití indexerů

[Indexery](search-indexer-overview.md) se používají k procházení podporovaných zdrojů dat Azure pro prohledávatelný obsah. I když není specificky určená pro indexování ve velkém měřítku, je k dispozici několik možností indexeru, které jsou zvláště užitečné při používání větších datových sad:

+ Plánovače umožňují v pravidelných intervalech vyřídit indexování, takže je můžete v průběhu času rozložit.
+ Naplánované indexování může pokračovat v posledním známém bodu zastavení. Pokud zdroj dat není plně procházen v průběhu 24 hodin, indexer bude pokračovat v indexování dvou dnů na všech místech, kde se nachází na levé straně.
+ Rozdělení dat na menší jednotlivé zdroje dat umožňuje paralelní zpracování. Zdrojová data můžete rozdělit do menších součástí, například do více kontejnerů v úložišti objektů BLOB v Azure, a pak vytvořit odpovídající více [objektů zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source) v Azure kognitivní hledání, které je možné indexovat paralelně.

> [!NOTE]
> Indexery jsou specifické pro zdroj dat. použití přístupu indexeru je možné realizovat jenom pro vybrané zdroje dat v Azure: [SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [BLOB Storage](search-howto-indexing-azure-blob-storage.md), [Table Storage](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="scheduled-indexing"></a>Naplánované indexování

Plánování indexeru je důležitým mechanismem pro zpracování rozsáhlých datových sad a také pomalým procesům, jako je analýza obrázků v kanálu vyhledávání vnímání. Zpracování indexeru funguje v průběhu 24 hodin. Pokud se zpracování neúspěšně dokončí do 24 hodin, chování plánování indexeru může fungovat na vaši výhodu. 

Podle návrhu plánované indexování začíná v určitých intervalech a úloha se obvykle dokončuje před pokračováním v dalším naplánovaném intervalu. Pokud se ale zpracování nedokončilo v intervalu, zastaví se indexer (protože byl mimo čas). V dalším intervalu se zpracování pokračuje tam, kde naposledy skončila, a systém udržuje přehled o tom, kde k tomu dochází. 

V praktických případech se pro zatížení indexů, které pokrývá několik dní, dá indexer vložit do 24 hodin. Když indexování pokračuje za dalších 24 hodin, restartuje se v posledním známém dokumentu. Tímto způsobem může indexer pracovat svým způsobem prostřednictvím nevyřízených položek dokumentů v rámci série dnů, dokud nebudou zpracovány všechny nezpracované dokumenty. Další informace o tomto přístupu najdete v tématu [indexování velkých datových sad v úložišti objektů BLOB v Azure](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Další informace o obecném nastavení plánů najdete v tématu [Vytvoření indexeru REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) nebo v tématu [postup plánování indexerů pro Azure kognitivní hledání](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Paralelní indexování

Strategie paralelního indexování je založena na indexování více zdrojů dat v úlohách, kde každá definice zdroje dat určuje podmnožinu dat. 

Pro Neběžné, výpočetní požadavky na indexování, jako je rozpoznávání OCR u naskenovaných dokumentů v kanálu vyhledávání rozpoznávání, analýza obrázků nebo zpracování v přirozeném jazyce – je často nejvhodnějším přístupem k dokončení dlouhotrvajícího procesu v nejkratší době. Pokud můžete eliminovat nebo snižovat požadavky na dotazy, paralelní indexování na službě, která současně nezpracovává dotazy, je nejlepší volbou pro práci prostřednictvím velkého těla pomalého zpracování obsahu. 

Paralelní zpracování má tyto prvky:

+ Rozdělte zdrojová data mezi více kontejnerů nebo více virtuálních složek uvnitř stejného kontejneru. 
+ Namapujte každou Mini datovou sadu na vlastní [zdroj dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source), který se spáruje s vlastním [indexerem](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Pro rozpoznávání rozpoznávání se v každé definici indexeru odkázat na stejný [dovednosti](https://docs.microsoft.com/rest/api/searchservice/create-skillset) .
+ Zapište do stejného cílového vyhledávacího indexu. 
+ Naplánujte, aby se všechny indexery spouštěly ve stejnou dobu.

> [!NOTE]
> V Azure Kognitivní hledání nemůžete přiřadit jednotlivé repliky nebo oddíly k indexování nebo zpracování dotazů. Systém Určuje, jak se prostředky používají. Aby bylo možné pochopit dopad na výkon dotazů, můžete si před přechodem do provozu vyzkoušet paralelní indexování v testovacím prostředí.  

### <a name="how-to-configure-parallel-indexing"></a>Jak nakonfigurovat paralelní indexování

U indexerů se kapacita zpracování volně vychází z jednoho subsystému indexeru pro každou jednotku služby (SU), kterou používá vaše vyhledávací služba. V Azure Kognitivní hledání Services zřízených na úrovni Basic nebo Standard s nejméně dvěma replikami je možné víc souběžných indexerů. 

1. V [Azure Portal](https://portal.azure.com)na stránce **Přehled** řídicího panelu vyhledávací služby zkontrolujte **cenovou úroveň** a potvrďte, že se může přizpůsobit paralelnímu indexování. Úrovně Basic a Standard nabízejí více replik.

2. V **Nastavení** > **škálování** [zvyšte repliky](search-capacity-planning.md) pro paralelní zpracování: jednu další repliku pro každou úlohu indexeru. Ponechte dostatečný počet pro existující svazek dotazu. Omezení zatížení dotazů na indexování není dobré kompromisy.

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
