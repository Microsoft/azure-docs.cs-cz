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
ms.openlocfilehash: cb99096c1217fca1527b17946dc12390ddf3f62c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654987"
---
# <a name="how-to-scale-out-indexing-in-azure-seearch"></a>Postup škálování indexování v Azure Seearch

Jako růst datové svazky nebo zpracování musí změnit, možná zjistíte, že jednoduché [znovu sestaví nebo přeindexování úlohy](search-howto-reindex.md) nejsou dost. 

Jako první krok k splňuje zvýšené požadavky, doporučujeme zvýšit [škálování a kapacitu](search-capacity-planning.md) v rámci existující službu. 

Druhý krok, pokud lze použít [indexery](search-indexer-overview.md), přidá mechanismy pro proporcionálních indexování. Indexery obsahují předdefinované plánovače, který umožňuje balení se indexování v pravidelných intervalech nebo rozšířit zpracování nad rámec okna 24 hodin. Kromě toho při spárovaný s definice zdroje dat, indexery vám pomohou dosáhnout formu paralelismus segmentace dat a použitím plány prováděny paralelně.

### <a name="scheduled-indexing-for-large-data-sets"></a>Naplánované indexování pro velké sady dat

Plánování je důležité mechanismus pro zpracování velkých datových sad a analýzy zpomalit spuštění jako Analýza obrázků v kanálu kognitivní vyhledávání. Indexer zpracování funguje v rámci časového období 24 hodin. Pokud zpracování nepodaří dokončit do 24 hodin, můžete využít pracovat chování plánování indexer. 

Návrh naplánovat indexování spustí v určitých intervalech s úlohou obvykle dokončení před obnovením na dalšího naplánovaného intervalu. Ale pokud zpracování během intervalu nedokončí, indexeru zastaví (z důvodu nedostatku času). Při dalším intervalu obnoví zpracování místo poslední přerušení, s zachování systému dráhu kde k tomu dojde. 

V praxi, pro zatížení index zahrnující několik dní můžete vložit indexeru podle plánu 24 hodin. Během indexování obnoví pro další stint 24 hodin, restartuje v poslední známé funkční dokumentu. Tímto způsobem můžete indexer pracovat skrze nevyřízených položek dokumentu v řadě dnů, dokud se zpracovávají všechny nezpracované dokumenty. Další informace o tento přístup, najdete v části [indexování rozsáhlých datových sad](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Paralelní indexování

Druhý volba je nastavit paralelní indexování strategie. Pro jiné rutiny výpočetně náročné požadavky, například rozpoznávání znaků na naskenované dokumenty v kanálu kognitivní vyhledávání, indexování paralelní indexování strategie může být správný přístup pro tuto konkrétní cíl. V kanálu obohacení kognitivní hledání jsou dlouho spuštěný image analýzy a zpracování přirozeného jazyka. Paralelní indexování na službě, která není současně zpracování požadavků na dotazy, může být vhodným řešením pro práci prostřednictvím rozsáhlý soubor zpomalit zpracování obsahu. 

Strategie pro paralelní zpracování má tyto prvky:

+ Rozdělení zdroje dat mezi několika kontejnerů nebo více virtuálních složek ve stejném kontejneru. 
+ Mapování jednotlivých mini datových sad na [datum zdroj](https://docs.microsoft.com/rest/api/searchservice/create-data-source), spárované do svého vlastního [indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Pro kognitivní vyhledávání odkazovat na stejné [skillset](https://docs.microsoft.com/rest/api/searchservice/create-skillset) v každé definici indexer.
+ Zápis do stejné cílový index vyhledávání. 
+ Naplánujte všechny indexery ke spuštění ve stejnou dobu.

> [!Note]
> Vyhledávání systému Azure nepodporuje vyhradit repliky nebo oddíly, které mají určité úlohy. Riziko velkou souběžných indexování je nadměrného zatížení systému a na úkor výkonu dotazu. Pokud máte testovacím prostředí, implementujte paralelní indexování existuje nejprve pochopit nevýhody.

## <a name="configure-parallel-indexing"></a>Konfigurovat paralelní indexování

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