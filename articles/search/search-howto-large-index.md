---
title: Index velké datové sady pomocí integrovaných indexerů – Azure Search
description: Úvod do strategií pro velké objemy dat indexování nebo výpočetně náročné indexování prostřednictvím režimu služby batch, prostředky a techniky pro naplánované, paralelní a distribuovaná indexování.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2f3d08a32384cea815f096f51b24eea596d0d118
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742231"
---
# <a name="how-to-index-large-data-sets-in-azure-search"></a>Jak indexovat rozsáhlým datovým sadám ve službě Azure Search

Jak rozšiřovat objemy dat nebo zpracování potřeby změní, můžete zjistit, že výchozí indexování strategie už nejsou praktické. Pro službu Azure Search existuje několik přístupů pro naplnění větších datových sad, od vytvoření struktury žádost o nahrání dat, k použití indexeru pro konkrétní zdroj pro plánované a distribuovaných úloh.

Stejné postupy pro velká data platí také pro dlouho běžící procesy. Konkrétně se podle kroků popsaných v [paralelní indexování](#parallel-indexing) jsou užitečné pro výpočetně náročné indexování, jako je například Analýza obrázku nebo ve zpracování přirozeného jazyka [kognitivního vyhledávání kanály](cognitive-search-concept-intro.md).

## <a name="batch-indexing"></a>Indexování služby batch

Jedním z nejjednodušší mechanismy pro indexování větší datové sady je odeslat více dokumenty nebo záznamy v jedné žádosti. Za předpokladu celé datové části je mladší 16 MB, žádost o dokáže zpracovat až 1 000 dokumentů v operaci hromadného nahrání. Za předpokladu, že [přidat nebo aktualizovat dokumentů REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents), 1000 dokumentů v textu požadavku by balíček.

Indexování služby batch je implementována pro jednotlivé požadavky pomocí REST nebo .NET, nebo pomocí indexerů. Několik indexerů pracovat v rámci různá omezení. Konkrétně indexování objektů Blob v Azure nastaví velikost dávky v 10 dokumenty, abychom ocenili větší velikost běžného dokumentu. Pro indexery na základě [vytvořit Indexer REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer ), můžete nastavit `BatchSize` argument k přizpůsobení tohoto nastavení můžete lépe odpovídala vlastnosti vaše data. 

> [!NOTE]
> Omezit velikost dokumentu, nezapomeňte vyloučit bez možnosti dotazování dat z požadavku. Obrázky a další binární data nejsou přímo prohledávatelný a neměl by být uložen v indexu. K integraci dat bez možnosti dotazování na výsledky hledání, byste měli definovat neprohledávatelném pole uchovávající odkaz URL na prostředek.

## <a name="add-resources"></a>Přidat prostředky

Služby, které jsou zřízené v některém z [standardní cenové úrovně](search-sku-tier.md) často nevyužité kapacity úložiště a úlohy (dotazy nebo indexování), díky čemuž je [zvýšení počtu oddílů a replik ](search-capacity-planning.md) zřejmé řešení pro naplnění větších datových sad. Nejlepších výsledků dosáhnete, je nutné, aby oba prostředky: oddílů pro úložiště a repliky pro příjem pracovní data.

Rostoucí repliky a oddíly, které jsou účtovaných událostí, které zvyšují náklady, ale pokud jsou neustále indexování maximálního zatížení, můžete přidat škálování po dobu trvání procesu indexování a potom upravte nižší úrovně prostředků po dokončení indexování bylo dokončeno.

## <a name="use-indexers"></a>Použití indexerů

[Indexery](search-indexer-overview.md) se používají k procházejí externí zdroje dat pro prohledávatelný obsah. Určený konkrétně zkoumaly pro indexování ve velkém měřítku, několik funkcí indexeru jsou zvláště užitečné pro naplnění větších datových sad:

+ Plánovači umožňují balení si indexování v pravidelných intervalech, aby vám ho rozdělit navýšení kapacity v čase.
+ Naplánované indexování můžete pokračovat v posledním bodem známé zastavení. Pokud zdroj dat není plně Procházet v rámci časového období 24 hodin, indexer bude pokračovat, indexování dne, dva v všude, kde přestalo.
+ Dělení dat do menších jednotlivých datových zdrojů umožňuje paralelní zpracování. Můžete velké datové sady rozdělit na menší datové sady a pak vytvořte více definice zdrojů dat, které může být indexován paralelně.

> [!NOTE]
> Indexery jsou zdrojový konkrétní data, takže použití přístup indexeru je pouze přijatelné pro vybrané zdroje dat v Azure: [SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [úložiště objektů Blob](search-howto-indexing-azure-blob-storage.md), [Table storage](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

## <a name="scheduled-indexing"></a>Naplánovat indexování

Indexer plánování je důležitým mechanismem pro zpracování velkých datových sad, jakož i pomalé procesy, jako je analýza obrázků v kanálu kognitivního vyhledávání. Indexer zpracování funguje v rámci časového období 24 hodin. Pokud se zpracování nepodaří dokončit během 24 hodin, chování plánování indexeru můžete pracovat s výhodou. 

Záměrné naplánovat indexování spustí v určitých intervalech s úlohou obvykle dokončení před pokračováním v dalšího naplánovaného intervalu. Ale pokud se zpracování nedokončí v rámci intervalu, indexer zastaví (protože mu došla času). Na další interval zpracování bude pokračovat tam, kde poslední skončila, s vedení systému sledovat, kde dochází. 

V praxi, pro načítání indexů zahrnující několik dní můžete indexer do 24 hodin plánu. Názvy při indexování bude pokračovat na další cyklus 24 hodin, restartuje na poslední známé dobré dokumentu. Tímto způsobem může indexer pracovat skrze nevyřízenou položku dokumentu několik dnů, po kterém se zpracují všechny nezpracované dokumenty. Další informace o tento přístup, najdete v části [indexování velké datové sady ve službě Azure Blob storage](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Další informace o nastavení plánů obecně naleznete v tématu [vytvořit Indexer REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer#request-syntax).

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Paralelní indexování

Paralelní indexování strategie vychází z více zdrojů dat v článku, kde každá definice zdroje dat určuje podmnožinu dat indexování. 

Pro jiné rutiny, výpočetně náročné požadavky indexování – například optické rozpoznávání znaků na naskenované dokumenty v kanálu kognitivního vyhledávání, Analýza obrázků nebo zpracování přirozeného jazyka – paralelní indexování strategie je často správný přístup pro dokončení dlouho běžící proces v nejkratší době. Pokud se můžete vyloučit nebo omezit požadavků na dotazy, paralelní indexování ve službě, která není zároveň zpracování dotazů je nejvhodnější strategie pro práci prostřednictvím rozsáhlý zpomalit zpracování obsahu. 

Paralelní zpracování má tyto prvky:

+ Rozdělit zdroje dat napříč více kontejnery nebo více virtuální složky uvnitř stejného kontejneru. 
+ Mapovat každou mini datové sady do svého vlastního [zdroj dat.](https://docs.microsoft.com/rest/api/searchservice/create-data-source), spárované do svého vlastního [indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Pro kognitivního vyhledávání odkazují na stejný [dovednosti](https://docs.microsoft.com/rest/api/searchservice/create-skillset) v každé definice indexeru.
+ Zápis do stejné cílovým indexem vyhledávání. 
+ Naplánujte všechny indexery ke spuštění ve stejnou dobu.

> [!NOTE]
> Služba Azure Search nepodporuje vyhradíte repliky nebo oddíly pro konkrétní úlohy. Riziko indexování náročné souběžných je nadměrného zatížení systému na úkor výkonu dotazů. Pokud máte testovací prostředí, implementujte paralelní indexování existuje nejprve porozumět nevýhody.

### <a name="how-to-configure-parallel-indexing"></a>Jak nakonfigurovat paralelní indexování

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