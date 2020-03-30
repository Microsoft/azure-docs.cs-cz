---
title: Indexování velké datové sady pomocí vestavěných indexerů
titleSuffix: Azure Cognitive Search
description: Strategie pro indexování velkých dat nebo výpočtově náročné indexování prostřednictvím dávkového režimu, zdrojů a technik pro naplánované, paralelní a distribuované indexování.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 4ad5e961e390b60784355ff3bc72aca4a2f73e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190958"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Jak indexovat velké datové sady v Azure Cognitive Search

Jak rostou objemy dat nebo se mění potřeby zpracování, můžete zjistit, že jednoduché nebo výchozí strategie indexování již nejsou praktické. Pro Azure Cognitive Search existuje několik přístupů pro přizpůsobení větší datové sady, od způsobu strukturování žádosti o nahrání dat až po použití indexeru specifického pro zdroj pro naplánované a distribuované úlohy.

Stejné techniky platí i pro dlouhotrvající procesy. Zejména kroky popsané v [paralelní indexování](#parallel-indexing) jsou užitečné pro výpočtově náročné indexování, jako je například analýza obrazu nebo zpracování přirozeného jazyka v [kanálu obohacení AI](cognitive-search-concept-intro.md).

Následující části popisují tři techniky pro indexování velkého množství dat.

## <a name="option-1-pass-multiple-documents"></a>Možnost 1: Předání více dokumentů

Jedním z nejjednodušších mechanismů pro indexování větší datové sady je odeslání více dokumentů nebo záznamů v jedné žádosti. Tak dlouho, dokud je celá datová část pod 16 MB, požadavek může zpracovat až 1000 dokumentů v operaci hromadného nahrávání. Tato omezení platí bez ohledu na to, zda používáte rozhraní [API pro čtení dokumentů REST](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) NEBO metodu Index v [sdk](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) .NET. Pro obě rozhraní API by balíček 1000 dokumentů v těle každé žádosti.

Dávkové indexování je implementováno pro jednotlivé požadavky pomocí REST nebo .NET nebo prostřednictvím indexerů. Několik indexerů pracuje pod různými limity. Konkrétně azure blob indexování nastaví velikost dávky na 10 dokumentů jako uznání větší průměrné velikosti dokumentu. Indexery založené na [rozhraní REST ROZHRANÍ API vytvořit indexeru](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)můžete nastavit `BatchSize` argument pro přizpůsobení tohoto nastavení tak, aby lépe odpovídalcharakteristiky charakteristiky dat. 

> [!NOTE]
> Chcete-li zachovat zmenšil velikost dokumentu, vyhněte se přidávání nedotazovatelných dat do indexu. Obrázky a další binární data nelze přímo prohledávat a neměly by být uloženy v indexu. Chcete-li integrovat nedotazovatelná data do výsledků hledání, měli byste definovat pole, které není možné prohledávat, které ukládá odkaz na adresu URL na prostředek.

## <a name="option-2-add-resources"></a>Možnost 2: Přidání zdrojů

Služby, které jsou zřízeny na jedné z [cenových úrovní Standard](search-sku-tier.md) často mají nevyužitou kapacitu pro úložiště i úlohy (dotazy nebo indexování), což zvyšuje [rozdělení a repliky počítá](search-capacity-planning.md) zřejmé řešení pro ubytování větší datové sady. Pro dosažení nejlepších výsledků potřebujete oba prostředky: oddíly pro úložiště a repliky pro práci při přijím dat.

Zvýšení repliky a oddíly jsou fakturovatelné události, které zvyšují náklady, ale pokud jste průběžně indexování pod maximální zatížení, můžete přidat měřítko po dobu trvání procesu indexování a potom upravit úrovně prostředků zpět dolů po indexování je Dokončení.

## <a name="option-3-use-indexers"></a>Možnost 3: Použití indexerů

[Indexery](search-indexer-overview.md) se používají k procházení podporovaných zdrojů dat Azure pro prohledávatelný obsah. I když není speciálně určen pro rozsáhlé indexování, několik možností indexeru jsou zvláště užitečné pro přizpůsobení větší datové sady:

+ Plánovače umožňují parcelovat indexování v pravidelných intervalech, takže je můžete rozložit v průběhu času.
+ Plánované indexování může pokračovat v posledním známém bodu zastavení. Pokud zdroj dat není plně procházeno v rámci 24hodinového okna, indexer bude pokračovat v indexování druhý den kdekoli, kde skončil.
+ Rozdělení dat do menších jednotlivých zdrojů dat umožňuje paralelní zpracování. Zdrojová data můžete rozdělit na menší součásti, například do více kontejnerů v úložišti objektů Blob Azure, a pak vytvořit odpovídající, více [objektů zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source) v Azure Cognitive Search, které lze indexovat paralelně.

> [!NOTE]
> Indexery jsou specifické pro zdroj dat, takže použití přístupu indexeru je životaschopné jenom pro vybrané zdroje dat v Azure: [SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Blob storage](search-howto-indexing-azure-blob-storage.md), Table [storage](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="scheduled-indexing"></a>Plánované indexování

Plánování indexeru je důležitým mechanismem pro zpracování velkých datových sad, stejně jako pomalu běžící procesy, jako je analýza obrázků v kanálu kognitivního vyhledávání. Zpracování indexeru pracuje v rámci 24hodinového okna. Pokud se zpracování nepodaří dokončit do 24 hodin, chování indexeru plánování může pracovat ve svůj prospěch. 

Podle návrhu, plánované indexování začíná v určitých intervalech, s úlohou obvykle dokončení před pokračováním v dalším naplánovaném intervalu. Pokud však zpracování není dokončeno v rámci intervalu, indexer se zastaví (protože vypršel čas). V dalším intervalu zpracování pokračuje tam, kde naposledy skončilo, přičemž systém sleduje, kde k tomu dochází. 

V praxi pro index zatížení zahrnující několik dní, můžete umístit indexer na 24hodinový plán. Při obnovení indexování pro další 24hodinový cyklus se restartuje na poslední známý dobrý dokument. Tímto způsobem indexer může pracovat svou cestu přes nevyřízené položky dokumentu v průběhu řady dní, dokud všechny nezpracované dokumenty jsou zpracovány. Další informace o tomto přístupu najdete [v tématu indexování velkých datových sad v úložišti objektů blob Azure](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Další informace o nastavení plánů obecně naleznete v [tématu Vytvoření rozhraní REST API indexeru](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) nebo v [tématu Jak naplánovat indexery pro Azure Cognitive Search](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Paralelní indexování

Strategie paralelního indexování je založena na indexování více zdrojů dat v unisono, kde každá definice zdroje dat určuje podmnožinu dat. 

Pro nerutinní, výpočtově náročné požadavky na indexování – například rozpoznávání OCR na skenovaných dokumentech v kanálu kognitivního vyhledávání, analýza obrázků nebo zpracování přirozeného jazyka – je strategie paralelního indexování často správným přístupem k dokončení dlouhotrvajícího procesu v co nejkratším čase. Pokud můžete eliminovat nebo snížit požadavky na dotazy, paralelní indexování ve službě, která není současně zpracování dotazů je nejlepší možnost strategie pro práci prostřednictvím velké množství obsahu pomalé zpracování. 

Paralelní zpracování má tyto prvky:

+ Rozdělit zdrojová data mezi více kontejnerů nebo více virtuálních složek uvnitř stejného kontejneru. 
+ Namapujte každou mini datovou sadu na vlastní [zdroj dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source), spárovaný s vlastním [indexerem](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Pro kognitivní vyhledávání, odkazovat na stejné [dovednosti](https://docs.microsoft.com/rest/api/searchservice/create-skillset) v každé definici indexeru.
+ Zapište do stejného indexu hledání cíle. 
+ Naplánujte spuštění všech indexerů současně.

> [!NOTE]
> V Azure Cognitive Search nelze přiřadit jednotlivé repliky nebo oddíly k indexování nebo zpracování dotazů. Systém určuje způsob použití prostředků. Chcete-li pochopit dopad na výkon dotazu, můžete zkusit paralelní indexování v testovacím prostředí před jeho válcování do produkčního prostředí.  

### <a name="how-to-configure-parallel-indexing"></a>Konfigurace paralelního indexování

U indexerů je kapacita zpracování volně založena na jednom subsystému indexeru pro každou servisní jednotku (SU) používanou vyhledávací službou. Více souběžných indexerů je možné ve službách Azure Cognitive Search zřízených na úrovních Basic nebo Standard, které mají alespoň dvě repliky. 

1. Na [portálu Azure](https://portal.azure.com)na stránce **Přehled** řídicího panelu vyhledávací služby zkontrolujte **cenovou úroveň** a potvrďte, že může pojmout paralelní indexování. Úrovně Basic i Standard nabízejí více replik.

2. V **nastavení** > **škálování**, [zvýšit repliky](search-capacity-planning.md) pro paralelní zpracování: jedna další replika pro každou úlohu indexeru. Ponechte dostatečný počet pro existující svazek dotazu. Obětování úloh dotazu pro indexování není dobrým kompromisem.

3. Distribuujte data do více kontejnerů na úrovni, které mohou dosáhnout indexery Azure Cognitive Search. Může se jednalo o více tabulek v Azure SQL Database, více kontejnerů v úložišti objektů blob Azure nebo více kolekcí. Definujte jeden objekt zdroje dat pro každou tabulku nebo kontejner.

4. Vytvořte a naplánujte více indexerů tak, aby běžely paralelně:

   + Předpokládejme službu se šesti replikami. Nakonfigurujte šest indexerů, z nichž každý je mapován na zdroj dat obsahující jednu šestinu sady dat pro šesticestné rozdělení celé sady dat. 

   + Najelete každý indexer na stejný index. Pro úlohy kognitivní vyhledávání, přejděte každý indexer na stejnou sadu dovedností.

   + V rámci každé definice indexeru naplánujte stejný vzor spuštění za běhu. Například `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` vytvoří plán na 2018-05-15 na všechny indexery, spuštěné v osmihodinových intervalech.

V naplánovaném čase všechny indexery zahájit spuštění, načítání dat, použití obohacení (pokud jste nakonfigurovali kanál kognitivní vyhledávání) a zápis do indexu. Azure Cognitive Search neuzamkne index pro aktualizace. Souběžné zápisy jsou spravovány, s opakováním, pokud konkrétní zápis neuspěje na první pokus.

> [!Note]
> Při zvyšování repliky, zvažte zvýšení počtu oddílů, pokud se předpokládá, že velikost indexu výrazně zvýšit. Oddíly ukládají řezy indexovaného obsahu; čím více oddílů máte, tím menší je řez, který každý z nich musí uložit.

## <a name="see-also"></a>Viz také

+ [Přehled indexeru](search-indexer-overview.md)
+ [Indexování na portálu](search-import-data-portal.md)
+ [Indexer databáze Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexer pro Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indexer pro Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indexer pro Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpečení v Azure Cognitive Search](search-security-overview.md)
