---
title: Znovu sestavte Azure Search index nebo aktualizace vyhledávat obsah | Microsoft Docs
description: Přidat nové prvky, aktualizovat existující prvky nebo dokumenty nebo odstranit zastaralé dokumentů v úplné opětovné sestavení nebo částečné přírůstkové indexování k aktualizaci indexu Azure Search.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: f38054eaf2829149a496f840366b6f2f9e03e12b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Postup opětovné sestavení indexu Azure Search

Opětovné sestavení indexu změny jeho strukturu, změna fyzické výraz indexu ve službě Azure Search. Naopak aktualizovat index je jen obsah aktualizace vybrat nejnovější změny z přispívajících externí zdroj dat. Tento článek obsahuje směr na tom, jak aktualizovat indexy strukturálně i nezávisle.

Oprávnění pro čtení a zápis na úrovni služby jsou požadovány pro aktualizace indexu. Pro opětovné sestavení úplné nebo přírůstkové indexování obsahu, s parametry zadání možností aktualizace prostřednictvím kódu programu, můžete volat rozhraní API .NET nebo REST. 

Obecně platí jsou aktualizace indexu na vyžádání. Ale pro indexy vyplněny konkrétní zdroj [indexery](search-indexer-overview.md), můžete použít předdefinované plánovač. Plánovač tak často, jak každých 15 minut až jakémkoli intervalu a vzor vyžadujete podporuje aktualizace dokumentu. Rychlejší obnovovací frekvence vyžaduje vkládání index aktualizace ručně, případně prostřednictvím dvojitou zápisu na transakce, aktualizace externí zdroj dat a index Azure Search současně.

## <a name="full-rebuilds"></a>Úplné znovu sestaví

Pro mnoho typů aktualizací se vyžaduje úplné opětovné sestavení. Úplné opětovné sestavení odkazuje na odstranění indexu a data a metadata, a pak opětovného vyplnění index z externích zdrojů. Prostřednictvím kódu programu [odstranit](https://docs.microsoft.com/rest/api/searchservice/delete-index), [vytvořit](https://docs.microsoft.com/rest/api/searchservice/create-index), a [znovu načíst](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) index znovu sestavit. 

Po opětovné sestavení, pamatujte, že pokud jste byla testování vzorům dotazů a vyhodnocování profily, můžete očekávat variace ve výsledcích dotazů Pokud došlo ke změně základní obsah.

## <a name="when-to-rebuild"></a>Když pro opětovné sestavení

Plán na úplné časté, znovu sestaví během active vývoje, když index schémata jsou ve stavu toku.

| Úpravy | Stav opětovné sestavení|
|--------------|---------------|
| Název pole, změnit datový typ, nebo jeho [atributy indexu](https://docs.microsoft.com/rest/api/searchservice/create-index) | Změna definice pole obvykle způsobuje opětovné sestavení snížení, s výjimkou těchto [atributy indexu](https://docs.microsoft.com/rest/api/searchservice/create-index): dá načíst, SearchAnalyzer, SynonymMaps. Retrievable, SearchAnalyzer a SynonymMaps atributy můžete přidat na stávající pole bez nutnosti znovu sestavte jeho index.|
| Přidání polí | Žádné explicitní požadavek na opětovné sestavení. Existující indexované dokumenty zadána pro nové pole hodnotu null. Na budoucí nové indexování nahraďte hodnoty ze zdrojových dat hodnoty Null přidal Azure Search. |
| Odstranění pole | Pole nelze odstranit přímo z indexu Azure Search. Místo toho byste měli mít vaší aplikace "odstraněné" pole, které chcete nepoužívejte ji ignorovat. Fyzicky definice pole a obsah zůstat v indexu až po příštím opětovném sestavení indexu pomocí schéma, které vynechá dané oblasti.|

> [!Note]
> Nové vytvoření je také nutný, pokud přejdete vrstev. Pokud se v určitém okamžiku rozhodnete na větší kapacitu, neexistuje žádný místní upgrade. Nová služba musí být vytvořený nový bod kapacitu a indexy musí být založený od začátku na novou službu. 

## <a name="partial-or-incremental-indexing"></a>Částečné nebo přírůstkové indexování

Po indexu v produkčním prostředí přesouvá fokus přírůstkové indexování, obvykle s žádné přerušení discernable služeb. Částečné nebo přírůstkové indexování je zatížení pouze obsah, který synchronizuje obsah index vyhledávání tak, aby odrážela stav obsahu v přispívajících zdroj dat. Při přidání nebo odstranění ve zdrojovém dokumentu je přidat nebo odstranit do indexu. V kódu volání [přidání, aktualizace nebo odstranění dokumentů](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) operaci nebo jeho ekvivalent rozhraní .NET.

> [!Note]
> Při použití indexery, které procházejí externích zdrojů dat, jsou pro přírůstkové indexování využít mechanismy sledování změn ve zdrojových systémech. Pro [úložiště objektů Azure Blob](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), `lastModified` pole se používá. Na [Azure Table storage](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection), `timestamp` slouží ke stejnému účelu. Podobně i [indexer Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) a [indexer Azure Cosmos DB](search-howto-index-cosmosdb.md#indexing-changed-documents) obsahovat pole pro označování řádek aktualizace. Další informace o indexerech najdete v tématu [Indexer přehled](search-indexer-overview.md).

## <a name="scale-out-indexing"></a>Horizontální indexování

Jako růst datové svazky nebo zpracování musí změnit, můžete zjistit, že jednoduché znovu sestaví a novou indexaci úlohy nejsou dost. Jako první krok k splňuje zvýšené požadavky, doporučujeme zvýšit [škálování a kapacitu](search-capacity-planning.md) v rámci existující službu. 

Pokud můžete použít [indexery](search-indexer-overview.md), k dispozici další mechanismy Škálováním na více systémů. Indexery obsahují předdefinované plánovače, který umožňuje balení se indexování v pravidelných intervalech nebo rozšířit zpracování nad rámec okna 24 hodin. Kromě toho při spárovaný s definice zdroje dat, indexery vám pomohou dosáhnout formu paralelismus segmentace dat a použitím plány prováděny paralelně.

### <a name="scheduled-indexing-for-large-data-sets"></a>Naplánované indexování pro velké sady dat

Plánování je důležité mechanismus pro zpracování velkých datových sad a analýzy zpomalit spuštění jako Analýza obrázků v kanálu kognitivní vyhledávání. Indexer zpracování funguje v rámci časového období 24 hodin. Pokud zpracování nepodaří dokončit do 24 hodin, můžete využít pracovat chování plánování indexer. 

Návrh naplánovat indexování spustí v určitých intervalech s úlohou obvykle dokončení před obnovením na dalšího naplánovaného intervalu. Ale pokud zpracování během intervalu nedokončí, indexeru zastaví (z důvodu nedostatku času). Při dalším intervalu obnoví zpracování místo poslední přerušení, s zachování systému dráhu kde k tomu dojde. 

V praxi, pro zatížení index zahrnující několik dní můžete vložit indexeru podle plánu 24 hodin. Během indexování obnoví pro další stint 24 hodin, restartuje v poslední známé funkční dokumentu. Tímto způsobem můžete indexer pracovat skrze nevyřízených položek dokumentu v řadě dnů, dokud se zpracovávají všechny nezpracované dokumenty. Další informace o tento přístup, najdete v části [indexování rozsáhlých datových sad](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Paralelní indexování

Druhý volba je nastavit paralelní indexování strategie. Pro jiné rutiny výpočetně náročné požadavky, například rozpoznávání znaků na naskenované dokumenty v kanálu kognitivní vyhledávání, indexování paralelní indexování strategie může být správný přístup pro tuto konkrétní cíl. V kanálu obohacení kognitivní hledání jsou dlouho spuštěný image analýzy a zpracování přirozeného jazyka. Paralelní indexování na službě, která není současně zpracování požadavků na dotazy, může být vhodným řešením pro práci prostřednictvím rozsáhlý soubor zpomalit zpracování obsahu. 

Strategie pro paralelní zpracování má tyto prvky:

+ Rozdělení zdroje dat mezi několika kontejnerů nebo více virtuálních složek ve stejném kontejneru. 
+ Mapování jednotlivých mini datových sad na [datum zdroj](https://docs.microsoft.com/rest/api/searchservice/create-data-source), spárované do svého vlastního [indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Pro kognitivní vyhledávání odkazovat na stejné [skillset](ref-create-skillset.md) v každé definici indexer.
+ Zápis do stejné cílový index vyhledávání. 
+ Naplánujte všechny indexery ke spuštění ve stejnou dobu.

> [!Note]
> Vyhledávání systému Azure nepodporuje vyhradit repliky nebo oddíly, které mají určité úlohy. Riziko velkou souběžných indexování je nadměrného zatížení systému a na úkor výkonu dotazu. Pokud máte testovacím prostředí, implementujte paralelní indexování existuje nejprve pochopit nevýhody.

### <a name="configure-parallel-indexing"></a>Konfigurovat paralelní indexování

Pro indexery zpracování kapacity volně vychází z jednoho podsystému indexeru pro jednotlivé služby jednotky (SU) používá vaši službu vyhledávání. Více souběžných indexery je možné na služby vyhledávání systému Azure zřídit na úrovně Basic nebo Standard s alespoň dvě repliky. 

1. V [portál Azure](https://portal.azure.com), na řídicím panelu služby vyhledávání **přehled** stránka, zkontrolujte **cenová úroveň** potvrďte zvládne paralelní indexování. Úrovně Basic a Standard nabízí víc replik.

2. V **nastavení** > **škálování**, [zvýšit repliky](search-capacity-planning.md) pro paralelní zpracování: jeden další repliky pro jednotlivá zatížení indexer. Ponechte dostatečný počet existující svazek dotazu. By došlo ke ztrátě dotazu úlohy pro indexování není funkční kompromis.

3. Distribuci dat do několika kontejnerů na úrovni, která mohou být využity indexery Azure Search. To může být více tabulek v databázi SQL Azure, několika kontejnerů v úložišti objektů Blob v Azure nebo více kolekcí. Definujte jeden objekt zdroje dat pro každou tabulku nebo kontejneru.

4. Vytvořte a naplánovat několik indexerů paralelně spustit:

   + Předpokládejme, je služba se šesti repliky. Nakonfigurujte šesti indexery, každé z nich namapované ke zdroji dat obsahující jednu šestinu datové sady pro 6 způsob rozdělení celé datové sady. 

   + Přejděte na každý indexer stejný index. Pro úlohy kognitivní vyhledávání přejděte na každý indexer stejné skillset.

   + V rámci každé definice indexer naplánujte stejného vzoru spuštění provádění. Například `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` vytvoří plán na 2018-05-15 na všechny indexery, spuštěná v intervalech osmi hodin.

V naplánovaném čase všem indexery zahájit provádění, načítání dat, použití enrichments (Pokud jste nakonfigurovali kognitivní vyhledávání kanálu) a zápis do indexu. Služba Azure Search nezamyká index pro aktualizace. Pokud konkrétní zápisu nebude úspěšná na první pokus opakovat se spravují souběžných zápisy.

> [!Note]
> Při zvýšení repliky, zvažte, pokud velikost indexu se promítá do zvýšit výrazně zvýšit počet oddílů. Oddílech se uloží řezy indexované obsahu; Další oddíly, které máte, tím menší je řez, každé z nich má k uložení.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Přehled indexeru](search-indexer-overview.md)
+ [Indexování na portálu](search-import-data-portal.md)
+ [Indexer Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexer databáze Azure Cosmos](search-howto-index-cosmosdb.md)
+ [Indexer pro Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indexer pro Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpečení ve službě Azure Search](search-security-overview.md)