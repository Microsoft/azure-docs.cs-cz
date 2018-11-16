---
title: Znovu sestavit Azure Search index nebo aktualizace prohledávatelného obsahu | Dokumentace Microsoftu
description: Přidat nové prvky, aktualizovat stávající prvky nebo dokumenty nebo odstranit zastaralé dokumentů v úplné opětovné sestavení nebo částečné přírůstkové indexování na aktualizace indexu Azure Search.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: 0b346756349c483dea32ec31827a653bd9b777cf
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705935"
---
# <a name="how-to-scale-out-indexing-in-azure-search"></a>Jak horizontální navýšení kapacity indexování ve službě Azure Search

Jak rozšiřovat objemy dat nebo zpracování potřeby změní, můžete zjistit, jednoduché [znovu sestaví a přeindexování úlohy](search-howto-reindex.md) nejsou dostatečná. 

Jako první krok při plnění větší nároky, doporučujeme, abyste zvýšili [škálování a kapacitu](search-capacity-planning.md) v mezích limitů vaší existující služby. 

Druhý krok, pokud lze použít [indexery](search-indexer-overview.md), přidá mechanismy pro Škálovatelné indexování. Indexery jsou dostupné integrované plánovače, která umožňuje balení si indexování v pravidelných intervalech nebo rozšířit zpracování za období 24 hodin. Kromě toho v kombinaci s definice zdrojů dat, indexery vám pomohou dosáhnout určitou formu paralelismu dělení dat a používání plánů spustit paralelně.

### <a name="scheduled-indexing-for-large-data-sets"></a>Naplánovat indexování pro velké datové sady

Plánování je důležitým mechanismem pro zpracování velkých datových sad a analýz pomalé stejně jako analýzu obrázků v kanálu kognitivního vyhledávání. Indexer zpracování funguje v rámci časového období 24 hodin. Pokud se zpracování nepodaří dokončit během 24 hodin, chování plánování indexeru můžete pracovat s výhodou. 

Záměrné naplánovat indexování spustí v určitých intervalech s úlohou obvykle dokončení před pokračováním v dalšího naplánovaného intervalu. Ale pokud se zpracování nedokončí v rámci intervalu, indexer zastaví (protože mu došla času). Na další interval zpracování bude pokračovat tam, kde poslední skončila, s vedení systému sledovat, kde dochází. 

V praxi, pro načítání indexů zahrnující několik dní můžete indexer do 24 hodin plánu. Názvy při indexování bude pokračovat pro další období 24 hodin, restartuje na poslední známé dobré dokumentu. Tímto způsobem může indexer pracovat skrze nevyřízenou položku dokumentu několik dnů, po kterém se zpracují všechny nezpracované dokumenty. Další informace o tento přístup, najdete v části [indexování velkých datových sad](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Paralelní indexování

Druhá možnost je k nastavení paralelní indexování strategie. Pro jiné rutiny výpočetně náročné požadavky, jako je například optické rozpoznávání znaků na naskenované dokumenty v kanálu kognitivního vyhledávání, indexování paralelní indexování strategie může být ten správný přístup pro tento konkrétní cíl. V rozšíření kanálu kognitivního vyhledávání analýza a zpracování přirozeného jazyka obrázků jsou spuštěna po dlouhou dobu. Paralelní indexování ve službě, která není zároveň zpracování požadavků na dotazy může být vhodným řešením pro práci prostřednictvím rozsáhlý zpomalit zpracování obsahu. 

Strategie pro paralelní zpracování má tyto prvky:

+ Rozdělte zdroje dat napříč více kontejnery nebo více virtuální složky uvnitř stejného kontejneru. 
+ Mapovat každou mini datovou sadu, která [zdroj dat.](https://docs.microsoft.com/rest/api/searchservice/create-data-source), spárované do svého vlastního [indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Pro kognitivního vyhledávání odkazují na stejný [dovednosti](https://docs.microsoft.com/rest/api/searchservice/create-skillset) v každé definice indexeru.
+ Zápis do stejné cílovým indexem vyhledávání. 
+ Naplánujte všechny indexery ke spuštění ve stejnou dobu.

> [!Note]
> Služba Azure Search nepodporuje vyhradíte repliky nebo oddíly pro konkrétní úlohy. Riziko indexování náročné souběžných je nadměrného zatížení systému na úkor výkonu dotazů. Pokud máte testovací prostředí, implementujte paralelní indexování existuje nejprve porozumět nevýhody.

## <a name="configure-parallel-indexing"></a>Konfigurovat paralelní indexování

V případě indexerů kapacitu zpracování je volně na základě jednoho subsystému indexeru pro každou jednotku služby (SU) používá služba search. Několik souběžných indexerů je možné na služeb Azure Search zřízených v úrovních Basic nebo Standard s nejméně dvěma replikami. 

1. V [webu Azure portal](https://portal.azure.com), na řídicím panelu služby search **přehled** stránky, zkontrolujte **cenová úroveň** potvrďte zvládne paralelní indexování. Úrovně Basic a Standard nabízejí víc replik.

2. V **nastavení** > **škálování**, [zvýšení replik](search-capacity-planning.md) pro paralelní zpracování: jeden další repliky pro jednotlivá zatížení indexeru. Ponechte dostatek číslo pro existující množství dotazů. Omezení dotazu úlohy indexování není dobrý kompromis.

3. Distribuujte data do více kontejnerů na úrovni, se kterým dosáhnete indexerů Azure Search. To může být více tabulek v Azure SQL Database, více kontejnerů ve službě Azure Blob storage nebo více kolekcí. Definujte jeden objekt zdroje dat pro jednotlivé tabulky nebo kontejnerů.

4. Vytvoření a naplánování několik indexerů k paralelnímu spuštění:

   + Předpokládejme, služba se šesti replik. Nakonfigurujte šest indexery, každý z nich mapovat na zdroj dat obsahující čtvrtinu, šestinu datové sady pro 6 způsob rozdělení celé datové sady. 

   + Každý indexer odkazují na stejný index. Kognitivní vyhledávání úloh přejděte na stejné dovednosti každý indexer.

   + V rámci každé definice indexeru naplánujte stejný vzor spuštění za běhu. Například `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` vytvoří plán na 2018-05-15 na všechny indexery, spuštěná každých osm hodin.

Všechny indexery v naplánovaném čase zahájit provádění, načítání dat, použití obohacení (Pokud jste nakonfigurovali kanálu kognitivního vyhledávání) a zápis do indexu. Služba Azure Search nepoužívejte zámky index pro aktualizace. Pokud konkrétní zápisu selže na první pokus opakovat se spravují souběžných zápisů.

> [!Note]
> Při zvýšení replik, zvažte zvýšení počtu oddílů, pokud je očekávaná velikost indexu výrazně zvýšit. Oddílech se uloží řezy indexovaného obsahu; Další oddíly máte, tím menší řez, každý z nich má k uložení.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Přehled indexeru](search-indexer-overview.md)
+ [Indexování na portálu](search-import-data-portal.md)
+ [Indexer pro Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexer databáze Azure Cosmos](search-howto-index-cosmosdb.md)
+ [Indexer pro Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indexer pro Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpečení ve službě Azure Search](search-security-overview.md)