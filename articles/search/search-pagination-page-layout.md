---
title: Jak pracovat s výsledky hledání
titleSuffix: Azure Cognitive Search
description: Struktura a řazení výsledků hledání, získání počtu dokumentů a přidání navigace obsahu do výsledků hledání v Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: e583cedc04113615c50cc9906cbd11a99ff48683
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421715"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Jak pracovat s výsledky hledání v Azure Kognitivní hledání

Tento článek vysvětluje, jak získat odpověď na dotaz, který se vrátí s celkovým počtem vyhovujících dokumentů, stránkovaných výsledků, seřazených výsledků a podmínek zvýrazněných na začátku.

Struktura odpovědi je určena parametry v dotazu: [vyhledat dokument](/rest/api/searchservice/Search-Documents) v REST API nebo v sadě .NET SDK [searchResults](/dotnet/api/azure.search.documents.models.searchresults-1) .

## <a name="result-composition"></a>Složení výsledku

I když se dokument vyhledávání může skládat z velkého počtu polí, obvykle se pro reprezentaci každého dokumentu v sadě výsledků vyžaduje jenom pár. V poli žádost o dotaz připojit `$select=<field list>` k Určete, která pole se zobrazí v odpovědi. Pole musí **být v indexu tak, aby** bylo možné zahrnout do výsledku. 

Mezi pole, která fungují nejlépe, patří ta, která se liší od dokumentů, a poskytují dostatečné informace pro pozvání reakce na pozvánku v rámci uživatele. Na webu elektronického obchodování může být název produktu, popis, značka, barva, velikost, cena a hodnocení. V části pohostines-Sample-index je předdefinovaná ukázka, může to být pole v následujícím příkladu:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Pokud chcete zahrnout soubory obrázků ve výsledku, jako je například fotografie nebo logo produktu, uložte je mimo Azure Kognitivní hledání, ale do indexu zahrňte pole, které odkazuje na adresu URL obrázku v dokumentu hledání. Ukázky indexů, které podporují obrázky ve výsledcích, zahrnují ukázku **realestate-Sample-US** , kterou najdete v tomto [rychlém](search-create-app-portal.md)startu, a [ukázkovou aplikaci pro demonstrační úlohy v New Yorku](https://aka.ms/azjobsdemo).

## <a name="paging-results"></a>Výsledky stránkování

Ve výchozím nastavení hledá vyhledávací modul až na prvních 50 shod, které jsou určeny skóre vyhledávání, pokud je dotaz fulltextovým vyhledáváním, nebo v libovolném pořadí pro přesné dotazy shody.

Chcete-li vrátit jiný počet vyhovujících dokumentů, `$top` přidejte `$skip` parametry a do žádosti o dotaz. Následující seznam vysvětluje logiku.

+ Přidejte `$count=true` , chcete-li získat celkový počet vyhovujících dokumentů v rámci indexu.

+ Vrátí první sadu 15 odpovídajících dokumentů a celkový počet shod: `GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Vrátí druhou sadu a přeskočí prvních 15 k získání následujících 15: `$top=15&$skip=15` . Totéž udělejte u třetí sady 15: `$top=15&$skip=30`

V případě změny podkladového indexu není zaručeno, že výsledky stránkovaných dotazů nebudou stabilní. Stránkování změní hodnotu `$skip` pro každou stránku, ale každý dotaz je nezávislý a pracuje s aktuálním zobrazením dat v indexu v době dotazu (jinými slovy, není ukládání do mezipaměti nebo snímku výsledků, jako jsou například ty, které se nacházejí v databázi pro obecné účely).
 
Následuje příklad, jak můžete získat duplicity. Předpokládat index se čtyřmi dokumenty:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
{ "id": "4", "rating": 1 }
```
 
Nyní předpokládejme, že výsledky byly vráceny dvakrát v čase seřazené podle hodnocení. Tento dotaz provedete tak, aby získal první stránku výsledků: `$top=2&$skip=0&$orderby=rating desc` a vygeneroval následující výsledky:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
```
 
Ve službě se předpokládá, že se do indexu přidá pátý dokument mezi voláními dotazu: `{ "id": "5", "rating": 4 }` .  Krátce potom spustíte dotaz k načtení druhé stránky: `$top=2&$skip=2&$orderby=rating desc` a získáte tyto výsledky:

```text
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
```
 
Všimněte si, že dokument 2 je načten dvakrát. Je to proto, že nový dokument 5 má větší hodnotu hodnocení, takže seřadí před dokument 2 a na první stránce. I když toto chování může být neočekávané, je typický způsob, jakým se chová vyhledávací web.

## <a name="ordering-results"></a>Řazení výsledků

U fulltextových vyhledávacích dotazů jsou výsledky automaticky seřazené podle skóre hledání, které se počítá na základě četnosti a blízkosti v dokumentu, s vyšším skóre, které se týkají dokumentů s více nebo silnějšími shodami s hledaným termínem. 

Hledání skóre vyjadřuje obecnou představu o závažnosti a odráží sílu porovnávání ve srovnání s ostatními dokumenty ve stejné sadě výsledků. Skóre nejsou vždy konzistentní od jednoho dotazu k dalšímu, takže při práci s dotazy si můžete všimnout malých nedostatků při řazení dokumentů pro hledání. K tomu může dojít z několika vysvětlení.

| Příčina | Popis |
|-----------|-------------|
| Nestálost dat | Obsah indexu se při přidávání, upravování a odstraňování dokumentů liší. Termínové kmitočty se změní, protože aktualizace indexu jsou zpracovávány v průběhu času, což má vliv na výsledky hledání u vyhovujících dokumentů. |
| Více replik | Pro služby, které používají více replik, jsou dotazy vydávány paralelně pro každou repliku. Statistiky indexu použité k výpočtu skóre vyhledávání se počítají na základě repliky a výsledky se sloučily a seřazeny v odpovědi na dotaz. Repliky jsou většinou zrcadlově navzájem, ale statistiky se mohou lišit v důsledku malých rozdílů ve stavu. Například jedna replika mohla odstranit dokumenty přispívající do jejich statistik, které byly sloučeny mimo jiné repliky. Rozdíly v statistikách pro jednotlivé repliky jsou obvykle patrné v menších indexech. |
| Stejné skóre | Pokud má více dokumentů stejné skóre, může se stát, že se některá z nich zobrazí jako první.  |

### <a name="consistent-ordering"></a>Jednotné řazení

Vzhledem k flexibilnímu řazení výsledků je vhodné prozkoumat další možnosti, pokud je konzistence požadavkem na aplikaci. Nejsnadnější přístup je řazení podle hodnoty pole, jako je například hodnocení nebo datum. U scénářů, ve kterých chcete řadit podle konkrétního pole, jako je například hodnocení nebo datum, můžete explicitně definovat [ `$orderby` výraz](query-odata-filter-orderby-syntax.md), který lze použít pro každé pole, které je indexováno jako **seřaditelné**.

Další možností je použít [vlastní profil vyhodnocování](index-add-scoring-profiles.md). Profily vyhodnocování poskytují větší kontrolu nad hodnocením položek ve výsledcích vyhledávání a schopnost zvyšovat shody nalezené v konkrétních polích. Další logika hodnocení může pomáhat potlačit drobné rozdíly mezi replikami, protože výsledky hledání pro každý dokument jsou dále od sebe. Pro tento přístup doporučujeme použít [algoritmus hodnocení](index-ranking-similarity.md) .

## <a name="hit-highlighting"></a>Zvýrazňování položek

Zvýrazňování přístupů odkazuje na formátování textu (například tučné nebo žluté světla) použité pro odpovídající výrazy ve výsledku, což usnadňuje umístění shody. Pokyny pro zvýraznění přístupů jsou k dispozici v [žádosti o dotaz](/rest/api/searchservice/search-documents). 

Pokud chcete povolit zvýrazňování přístupů, přidejte, `highlight=[comma-delimited list of string fields]` abyste určili, která pole budou používat zvýraznění. Zvýrazňování je užitečné pro delší pole obsahu, jako je pole popisu, kde shoda není okamžitě zřejmá. Jenom definice polí s atributem, které jsou s **možností prohledávání** , jsou způsobilé pro zvýrazňování přístupů.

Ve výchozím nastavení Azure Kognitivní hledání vrátí až pět světel na pole. Toto číslo můžete upravit připojením k poli a pomlčkou, po které následuje celé číslo. Například `highlight=Description-10` vrátí až 10 světel na základě odpovídajícího obsahu v poli Popis.

Formátování se aplikuje na všechny výrazy. Typ formátování je určen značkami `highlightPreTag` a a `highlightPostTag` váš kód zpracovává odpověď (například použití tučného písma nebo žlutého pozadí).

V následujícím příkladu jsou označeny výrazy "oranžovohnědá", "písek", "pláže", "pláž" v rámci pole Popis pro zvýraznění. Dotazy, které spouštějí rozšíření dotazů v modulu, jako je například přibližná a hledání pomocí zástupných znaků, mají omezené podpory pro zvýrazňování přístupů.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2020-06-30 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>Nové chování (od 15. července)

Služby vytvořené po 15. červenci 2020 budou mít k dispozici jiné zvýrazňování. Služby vytvořené před tímto datem se nezmění v chování zvýrazňování. 

Nové chování:

* Vrátí se pouze fráze, které odpovídají úplným dotazům fráze. Dotaz "Super Bowl" bude vracet například takto:

    ```html
    '<em>super bowl</em> is super awesome with a bowl of chips'
    ```
  Všimněte si, že pojem *Bowl čipy* nemá žádné zvýrazňování, protože neodpovídá celé frázi.

Při psaní kódu klienta, který implementuje zvýrazňování přístupů, si pamatujte na tuto změnu. Mějte na paměti, že to nebude mít vliv, pokud vytvoříte úplně novou službu vyhledávání.

## <a name="next-steps"></a>Další kroky

Chcete-li rychle vygenerovat stránku vyhledávání pro klienta, vezměte v úvahu tyto možnosti:

+ [Generátor aplikace](search-create-app-portal.md), na portálu vytvoří stránku HTML s panelem hledání, s vlastností navigace a oblastí výsledků, která obsahuje obrázky.
+ [Vytvoření první aplikace v jazyce C#](tutorial-csharp-create-first-app.md) je kurz, který vytváří funkčního klienta. Vzorový kód ukazuje stránkované dotazy, zvýrazňování přístupů a řazení.

Několik ukázek kódu zahrnuje webové front-endové rozhraní, které můžete najít tady: aplikace pro ukázkovou [úlohu v New Yorku](https://aka.ms/azjobsdemo), [ukázkový kód JavaScriptu s živým ukázkovým webem](https://github.com/liamca/azure-search-javascript-samples)a [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).