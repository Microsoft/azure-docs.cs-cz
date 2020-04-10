---
title: Jak pracovat s výsledky hledání
titleSuffix: Azure Cognitive Search
description: Strukturujte a seřaďte výsledky hledání, získejte počet dokumentů a přidejte navigaci obsahu k výsledkům hledání ve službě Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 451e83fa6ab547536a4cfd85304930e749a8247f
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998394"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Jak pracovat s výsledky hledání v Azure Cognitive Search

Tento článek vysvětluje, jak získat odpověď na dotaz, který se vrátí s celkovým počtem odpovídajících dokumentů, stránkovanými výsledky, seřazenými výsledky a zvýrazněnými výrazy přístupů.

Struktura odpovědi je určena parametry v dotazu: [Hledat dokument](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) v rozhraní REST API nebo [DocumentSearchResult Class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) v .NET SDK.

## <a name="result-composition"></a>Složení výsledků

Zatímco vyhledávací dokument se může skládat z velkého počtu polí, obvykle je k reprezentaci každého dokumentu v sadě výsledků zapotřebí pouze několik. V žádosti o dotaz `$select=<field list>` přidejte k určení, která pole se zobrazí v odpovědi. Pole musí být přiřazeno jako **Retrievable** v indexu, které má být zahrnuto do výsledku. 

Nejlépe funkční pole zahrnují pole, která kontrastují a rozlišují mezi dokumenty a poskytují dostatečné informace pro pozvání odpovědi po prokliku ze strany uživatele. Na webu elektronického obchodování se může jednat o název produktu, popis, značku, barvu, velikost, cenu a hodnocení. Pro vestavěnou ukázku inkasazního indexu hotels-sample může být pole v následujícím příkladu:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Pokud chcete zahrnout obrazové soubory ve výsledku, jako je například fotografie produktu nebo logo, uložte je mimo Azure Cognitive Search, ale zahrnout pole v indexu odkazovat na adresu URL obrázku ve vyhledávacím dokumentu. Ukázkové indexy, které podporují obrázky ve výsledcích patří **realestate-sample-us** demo, vystupoval v tomto [rychlém startu](search-create-app-portal.md)a [New York City Jobs demo aplikace](https://aka.ms/azjobsdemo).

## <a name="paging-results"></a>Výsledky stránkování

Ve výchozím nastavení vyhledávač vrátí až prvních 50 shod, jak je určeno skóre vyhledávání, pokud je dotaz fulltextové vyhledávání nebo v libovolném pořadí pro přesné shody dotazy.

Chcete-li vrátit jiný počet `$top` odpovídajících dokumentů, přidejte a `$skip` parametry do požadavku na dotaz. Následující seznam vysvětluje logiku.

+ Chcete-li `$count=true` získat celkový počet odpovídajících dokumentů v rámci indexu, chcete-li získat celkový počet odpovídajících dokumentů.

+ Vrátí první sadu 15 odpovídajících dokumentů plus počet celkových shod:`GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Vrátit druhý set, přeskočení prvních 15 získat `$top=15&$skip=15`další 15: . Proveďte totéž pro třetí sadu 15:`$top=15&$skip=30`

Výsledky stránkovaných dotazů není zaručeno, že bude stabilní, pokud se mění základní index. Stránkování změní hodnotu `$skip` pro každou stránku, ale každý dotaz je nezávislý a pracuje na aktuální zobrazení dat, jak existuje v indexu v době dotazu (jinými slovy, neexistuje žádné ukládání do mezipaměti nebo snímek výsledků, jako jsou ty, které se nacházejí v databázi pro obecné účely).
 
Následuje příklad, jak můžete získat duplikáty. Předpokládejme index se čtyřmi dokumenty:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
    { "id": "4", "rating": 1 }
 
Nyní předpokládejme, že chcete výsledky vráceny dva najednou, seřazené podle hodnocení. Tento dotaz byste provedli, abyste získali první stránku s výsledky: `$top=2&$skip=0&$orderby=rating desc`, což vytváří následující výsledky:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
 
Ve službě předpokládejme, že pátý dokument je přidán `{ "id": "5", "rating": 4 }`do indexu mezi volání dotazu: .  Krátce poté spustíte dotaz pro načtení `$top=2&$skip=2&$orderby=rating desc`druhé stránky: a získáte tyto výsledky:

    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
 
Všimněte si, že dokument 2 je načten dvakrát. Je to proto, že nový dokument 5 má větší hodnotu pro hodnocení, takže seřadí před dokumentem 2 a přistane na první stránce. I když toto chování může být neočekávané, je typické pro chování vyhledávače.

## <a name="ordering-results"></a>Řazení výsledků

U fulltextových vyhledávacích dotazů jsou výsledky automaticky seřazeny podle skóre vyhledávání, které se počítá na základě četnosti termínů a blízkosti v dokumentu, přičemž vyšší skóre bude v dokumentech, které mají více nebo silnější shody s vyhledávacím dotazem. 

Skóre vyhledávání vyjadřuje obecný pocit relevance, který odráží sílu shody ve srovnání s jinými dokumenty ve stejné sadě výsledků. Skóre nejsou vždy konzistentní z jednoho dotazu na další, takže při práci s dotazy, můžete si všimnout malé nesrovnalosti v pořadí vyhledávacích dokumentů. Existuje několik vysvětlení, proč k tomu může dojít.

| Příčina | Popis |
|-----------|-------------|
| Volatilita dat | Obsah indexu se při přidávání, úpravách nebo odstraňování dokumentů liší. Frekvence termínů se budou měnit při zpracování aktualizací indexu v průběhu času, což ovlivní skóre vyhledávání odpovídajících dokumentů. |
| Více replik | Pro služby, které používají více replik, dotazy jsou vydávány proti každé replice paralelně. Statistiky indexu použité k výpočtu skóre vyhledávání se počítají na základě repliky, přičemž výsledky jsou sloučeny a seřazeny v odpovědi na dotaz. Repliky jsou většinou zrcadla navzájem, ale statistiky se mohou lišit v důsledku malých rozdílů ve stavu. Jedna replika může mít například odstraněné dokumenty přispívající k jejich statistikám, které byly sloučeny z jiných replik. Obvykle rozdíly ve statistikách na repliku jsou výraznější v menších indexů. |
| Identické skóre | Pokud má více dokumentů stejné skóre, může se zobrazit jako první.  |

### <a name="consistent-ordering"></a>Konzistentní řazení

Vzhledem k ohybu ve výsledcích řazení, můžete chtít prozkoumat další možnosti, pokud konzistence je požadavek aplikace. Nejjednodušším přístupem je řazení podle hodnoty pole, například hodnocení nebo data. Pro scénáře, ve kterých chcete řadit podle určitého pole, například podle hodnocení nebo data, můžete explicitně definovat [ `$orderby` výraz](query-odata-filter-orderby-syntax.md), který lze použít pro libovolné pole, které je indexováno jako **seřaditelné**.

Další možností je použití [vlastního profilu hodnocení](index-add-scoring-profiles.md). Profily hodnocení poskytují větší kontrolu nad pořadím položek ve výsledcích vyhledávání, přičemž je schopna propagovat shody nalezené v konkrétních polích. Další logika hodnocení může pomoci přepsat drobné rozdíly mezi replikami, protože skóre hledání pro každý dokument jsou dále od sebe. Doporučujeme [algoritmus řazení](index-ranking-similarity.md) pro tento přístup.

## <a name="hit-highlighting"></a>Zvýrazňování položek

Zvýraznění zásahu označuje formátování textu (například tučné nebo žluté zvýraznění) použité na odpovídající termín ve výsledku, což usnadňuje zobrazení shody. Pokyny pro zvýraznění přístupů jsou k dispozici v [žádosti o dotaz](https://docs.microsoft.com/rest/api/searchservice/search-documents). Vyhledávač uzavře odpovídající termín ve `highlightPreTag` značkách `highlightPostTag`a , a váš kód zpracovává odpověď (například použití tučnépísmo).

Formátování se použije na dotazy na celý termín. V následujícím příkladu jsou pro zvýraznění označeny výrazy "písčité", "písek", "pláže", "pláž" v poli Popis. Dotazy, které aktivují rozšíření dotazů v modulu, jako je například vyhledávání přibližných a zástupných symbolů, mají omezenou podporu pro zvýraznění přístupů.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2019-05-06 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

> [!IMPORTANT]
> Služby vytvořené po 15.7.2020 budou poskytovat různé zvýraznění zkušenosti. Služby vytvořené před tímto datem se nezmění v jejich chování zvýraznění. S touto změnou budou vráceny pouze fráze, které odpovídají úplné frázi dotaz. Také bude možné určit velikost fragmentu vrácené pro zvýraznění.
>
> Při psaní klientského kódu, který implementuje zvýraznění přístupů, uvědomte si tuto změnu. Všimněte si, že to nebude mít vliv na vás, pokud nevytvoříte zcela novou vyhledávací službu.

## <a name="next-steps"></a>Další kroky

Chcete-li rychle vygenerovat vyhledávací stránku pro klienta, zvažte tyto možnosti:

+ [Generátor aplikací](search-create-app-portal.md)na portálu vytvoří stránku HTML s vyhledávacím panelem, fazetovou navigací a oblastí výsledků, která obsahuje obrázky.
+ [Vytvořte si první aplikaci v C#](tutorial-csharp-create-first-app.md) je kurz, který vytváří funkční klienta. Ukázkový kód ukazuje stránkované dotazy, zvýraznění přístupů a řazení.

Několik ukázek kódu obsahuje webové front-end rozhraní, které najdete zde: [Demo aplikace New York City Jobs](https://aka.ms/azjobsdemo), [ukázkový kód JavaScriptu s živým demo webem](https://github.com/liamca/azure-search-javascript-samples)a [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).