---
title: Přidání profilů vyhodnocování do indexu vyhledávání – Azure Search
description: Zvýšení skóre vyhledávání pořadí pro Azure Search výsledky hledání tak, že přidáte bodovací profily.
ms.date: 01/31/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: eae7de00294a6a09cb7f942d11ee2391710fc55f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008486"
---
# <a name="add-scoring-profiles-to-an-azure-search-index"></a>Přidání profilů vyhodnocování do indexu Azure Search

  Vyhodnocování odkazuje na výpočet *skóre vyhledávání* pro každou položku, vrátila ve výsledcích hledání. Skóre je indikátorem položky podle relevance v kontextu aktuální operace vyhledávání. Vyšší ohodnocení, více odpovídající položky. Ve výsledcích hledání jsou položky seřazené od velmi nízká, podle skóre vyhledávání pro každou položku pořadí.  

 Služba Azure Search používá výchozí vyhodnocování pro výpočet počáteční skóre, ale můžete přizpůsobit výpočtu prostřednictvím *bodovací profil*. Profily vyhodnocování poskytují větší kontrolu nad pořadí položek ve výsledcích hledání. Můžete například zvýšit položky podle jejich potenciál výnosů, podporovat novější položky nebo možná zvýšení položky, které byly v inventáři příliš dlouhý.  

 Bodovací profil je součástí definice indexu, tvořené váženým pole, funkce a parametry.  

 Abyste získali představu, jak vypadá bodovací profil, následující příklad ukazuje jednoduchý profil s názvem "geograficky". Ten zvyšuje položek, které mají hledaný termín v **hotelName** pole. Využívá také `distance` funkce upřednostnit položky, které jsou v rámci 10 kilometrů aktuálního umístění. Pokud někdo vyhledá výraz "DIČ" a "DIČ" se stane jako součást Název hotelu, zobrazí se dokumenty, které zahrnují hotels s "DIČ" okruhu 10 KM umístění aktuální vyšší ve výsledcích hledání.  


```  
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
    },
    "functions": [
      {  
        "type": "distance",
        "boost": 5,
        "fieldName": "location",
        "interpolation": "logarithmic",
        "distance": {
          "referencePointParameter": "currentLocation",
          "boostingDistance": 10
        }                        
      }                                      
    ]                     
  }            
]
```  


 Chcete-li použít tato bodovací profil, je dotaz formulovat k určení profilu v řetězci dotazu. V dotazu níže, Všimněte si, že parametr dotazu `scoringProfile=geo` v požadavku.  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2017-11-11  
```  

 Tento dotaz vyhledá výraz "DIČ" a předává v aktuálním umístění. Všimněte si, že tento dotaz obsahuje dalších parametrů, jako například `scoringParameter`. Parametry dotazu jsou popsány v [vyhledávání dokumentů &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).  

 Klikněte na tlačítko [příklad](#bkmk_ex) zobrazíte podrobnější příklad bodovací profil.  

## <a name="what-is-default-scoring"></a>Co je vyhodnocování výchozí?  
 Vyhodnocování vypočítá skóre vyhledávání pro každou položku v sadě řadit seřazený výsledek. Každá položka v sadě výsledků vyhledávání je přiřazena skóre vyhledávání, pak seřazené nejvyšší k nejnižší. Položky s vyšší skóre jsou vráceny do aplikace. Ve výchozím nastavení, budou vráceny horní 50, ale můžete použít `$top` parametr a vrátit tak menší nebo větší počet položek (až 1000 v jednu odpověď).  

Skóre vyhledávání se počítá podle statistické vlastnosti dat a dotazu. Služba Azure Search Vyhledá dokumenty, které zahrnují podmínky vyhledávání v řetězci dotazu (některé nebo všechny, v závislosti na `searchMode`), elegantní dokumenty, které obsahují velký počet instancí hledaný termín. Skóre vyhledávání přejde ještě vyšší, pokud výraz není obvyklé napříč souhrnu dat, ale je běžné v rámci dokumentu. Základ pro tento přístup k výpočetní závažnosti se označuje jako [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) nebo frekvence frekvence inverzní dokumentu termín.  

 Za předpokladu, že není žádná vlastní řazení, výsledky se pak seřazené podle skóre vyhledávání před jejich vrácením volající aplikaci. Pokud není zadán $top, budou vráceny 50 položek, které mají nejvyšší skóre vyhledávání.  

 Hodnoty skóre vyhledávání lze opakovat v rámci sadu výsledků dotazu. Například můžete mít 10 položek se skóre 1.2, 20 položek se skóre 1.0 a 20 položek se skóre 0,5. Pokud více přístupů stejné skóre vyhledávání, řazení stejné Vyhodnocená položky není definována a není stabilní. Spusťte dotaz znovu a může se zobrazit položky posunutí pozice. Zadaný dvě položky se shodné skóre, neexistuje žádná záruka, která se zobrazí jako první.  

## <a name="when-to-use-custom-scoring"></a>Kdy použít vlastní bodování  
 Pokud výchozí chování řazení není daleko dostatek na splnění obchodních cílů, měli byste vytvořit jeden nebo více profilů vyhodnocování. Například můžete rozhodnout, že hledání podle relevance měli upřednostnit nově přidaných položkách. Podobně může mít pole, které obsahuje zisková marže nebo jiné pole, která potenciál. Zvýšení skóre přístupů, které přináší výhody pro vaši firmu, mohou být důležitým faktorem při rozhodování o použití profilů vyhodnocování.  

 Na základě důležitosti řazení se také implementuje pomocí bodovací profily. Vezměte v úvahu Hledat výsledky na stránkách, které jste použili v minulosti, které vám umožňují seřadit podle cen, data, hodnocení nebo podle relevance. Profily vyhodnocování ve službě Azure Search jednotka možnost "relevance". Definice podle relevance se řídí tím můžete na obchodní cíle a typu rozhraní pro hledání, který má být dodána zabezpečuje pomocí vhodné.  

##  <a name="bkmk_ex"></a> Příklad  
 Jak bylo uvedeno dříve, přizpůsobené vyhodnocování se implementuje pomocí jednoho nebo více profilů vyhodnocování definovaný ve schématu indexu.  

 Tento příklad ukazuje schématu indexu s dva profily vyhodnocování (`boostGenre`, `newAndHighlyRated`). Jakýkoli dotaz proti tento index, který obsahuje buď profil jako parametr dotazu bude použít profil ke stanovení skóre pro sadu výsledků dotazu.  

```  
{  
  "name": "musicstoreindex",  
  "fields": [  
    { "name": "key", "type": "Edm.String", "key": true },  
    { "name": "albumTitle", "type": "Edm.String" },  
    { "name": "albumUrl", "type": "Edm.String", "filterable": false },  
    { "name": "genre", "type": "Edm.String" },  
    { "name": "genreDescription", "type": "Edm.String", "filterable": false },  
    { "name": "artistName", "type": "Edm.String" },  
    { "name": "orderableOnline", "type": "Edm.Boolean" },  
    { "name": "rating", "type": "Edm.Int32" },  
    { "name": "tags", "type": "Collection(Edm.String)" },  
    { "name": "price", "type": "Edm.Double", "filterable": false },  
    { "name": "margin", "type": "Edm.Int32", "retrievable": false },  
    { "name": "inventory", "type": "Edm.Int32" },  
    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "boostGenre",  
      "text": {  
        "weights": {  
          "albumTitle": 1.5,  
          "genre": 5,  
          "artistName": 2  
        }  
      }  
    },  
    {  
      "name": "newAndHighlyRated",  
      "functions": [  
        {  
          "type": "freshness",  
          "fieldName": "lastUpdated",  
          "boost": 10,  
          "interpolation": "quadratic",  
          "freshness": {  
            "boostingDuration": "P365D"  
          }  
        },  
        {
          "type": "magnitude",  
          "fieldName": "rating",  
          "boost": 10,  
          "interpolation": "linear",  
          "magnitude": {  
            "boostingRangeStart": 1,  
            "boostingRangeEnd": 5,  
            "constantBoostBeyondRange": false  
          }  
        }  
      ]  
    }  
  ],  
  "suggesters": [  
    {  
      "name": "sg",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="workflow"></a>Pracovní postup  
 K implementaci vlastních bodování chování, přidáte bodovací profil na schéma definující index. Můžete mít až 100 bodovací profily v indexu (naleznete v tématu [omezení služby](search-limits-quotas-capacity.md)), ale můžete nastavit jenom jeden profil v době v jakékoli daný dotaz.  

 Začněte [šablony](#bkmk_template) uvedené v tomto tématu.  

 Zadejte název. Bodovací profily jsou volitelné, ale pokud chcete přidat jednu, název je povinný. Dodržujte konvence pro pole (začíná písmenem, zabraňuje speciální znaky a vyhrazených slov). Zobrazit [pravidla pojmenování &#40;Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/naming-rules) úplný seznam.  

 Tělo bodovací profil je vytvořen z vážený polí a funkcí.  

|||  
|-|-|  
|**Váhy**|Zadejte páry název hodnota, které přiřaďte relativní váhu pole. V [příklad](#bkmk_ex), jsou pole albumTitle žánr a artistName Posílený 1.5, 5 a 2 v uvedeném pořadí. Proč je rozšířením podle tematických boosted mnohem vyšší než ostatní? Pokud se provádí vyhledávání dat, který je o něco homogenní (jako je tomu u "žánr" v `musicstoreindex`), můžete potřebovat větší odchylku v relativní váhy. Například v `musicstoreindex`, "rock" zobrazí jako obou žánr a v popisech žánr identicky obsahuje jiné spojení. Pokud chcete žánr převažují nad žánr popis, bude nutné pole žánr mnohem vyšší relativní váhu.|  
|**Functions**|Při další výpočty jsou požadovány pro konkrétní kontexty. Platné hodnoty jsou `freshness`, `magnitude`, `distance`, a `tag`. Každá funkce má parametry, které jsou jedinečné pro jeho.<br /><br /> -   `freshness` Pokud chcete zvýšit jak nového nebo starého položky je. Tuto funkci jde použít jenom s `datetime` pole (edm. DataTimeOffset). Poznámka: `boostingDuration` atribut se používá jenom s `freshness` funkce.<br />-   `magnitude` by měl použít, pokud chcete zvýšit na základě jak vysoká nebo Nízká je číselná hodnota. Scénáře, které volají pro tuto funkci zahrnují zvýšení skóre zisková marže nejvyšší cenu, nejnižší cenu, nebo o počtu stažení. Tuto funkci jde použít jenom s poli double a celé číslo.<br />     Pro `magnitude` funkce, lze zrušit rozsahu, vysokou a nízkou, pokud chcete, aby inverzní vzor (například k položkám boost nízkonákladových více než vyšší ceny položek). Zadaný rozsah ceny ze 100 USD $ 1, byste měli nastavit `boostingRangeStart` na 100 a `boostingRangeEnd` v 1 a zvýšit tak levnějších položek.<br />-   `distance` by měl použít, pokud chcete zvýšit blízkých výrazů nebo zeměpisné polohy. Tuto funkci jde použít jenom s `Edm.GeographyPoint` pole.<br />-   `tag` by měl použít, pokud chcete zvýšit podle klíčových slov v běžných mezi dokumenty a vyhledávací dotazy. Tuto funkci jde použít jenom s `Edm.String` a `Collection(Edm.String)` pole.<br /><br /> **Pravidla pro používání funkce**<br /><br /> Typ funkce (`freshness`, `magnitude`, `distance`), `tag` musí obsahovat malá písmena.<br /><br /> Funkce nesmí obsahovat hodnoty null ani prázdné hodnoty. Konkrétně Pokud zahrnete fieldname, musíte ho nastavit.<br /><br /> Funkce lze použít pouze filtrovatelná pole. Zobrazit [Create Index &#40;rozhraní REST API služby Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/create-index) Další informace o filtrovatelná pole.<br /><br /> Funkce lze použít pouze u polí, které jsou definovány v kolekci polí v indexu.|  

 Po definování indexu sestavení indexu tak, že nahrajete schéma indexu, za nímž následuje dokumenty. Zobrazit [Create Index &#40;rozhraní REST API služby Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/create-index) a [přidání, aktualizace nebo odstranění dokumentů &#40;rozhraní REST API služby Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) pokyny týkající se těchto operací. Jakmile je sestavená index, byste měli mít funkční bodovací profil, který funguje s vašimi daty vyhledávání.  

##  <a name="bkmk_template"></a> Šablony  
 Tato část popisuje syntaxe a šablony pro bodovací profily. Odkazovat na [indexu referenční dokumentace k atributům](#bkmk_indexref) v další části Popis atributů.  

```  
. . .   
"scoringProfiles": [  
  {   
    "name": "name of scoring profile",   
    "text": (optional, only applies to searchable fields) {   
      "weights": {   
        "searchable_field_name": relative_weight_value (positive #'s),   
        ...   
      }   
    },   
    "functions": (optional) [  
      {   
        "type": "magnitude | freshness | distance | tag",   
        "boost": # (positive number used as multiplier for raw score != 1),   
        "fieldName": "...",   
        "interpolation": "constant | linear (default) | quadratic | logarithmic",   

        "magnitude": {
          "boostingRangeStart": #,   
          "boostingRangeEnd": #,   
          "constantBoostBeyondRange": true | false (default)
        }  

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...",   
. . .  
```  

##  <a name="bkmk_indexref"></a> Referenční dokumentace k atributům indexu  

> [!NOTE]  
>  Vyhodnocení funkce lze použít pouze u polí, které se dají filtrovat.  

|Atribut|Popis|  
|---------------|-----------------|  
|`Name`|Povinná hodnota. Toto je název bodovací profil. To se řídí stejnými zásadami vytváření názvů pole. Musí začínat písmenem, nesmí obsahovat tečky, dvojtečky nebo @ symbolů a nesmí začínat frázi "azureSearch" (malá a velká písmena).|  
|`Text`|Obsahuje vlastnost váhy.|  
|`Weights`|Volitelné. Dvojice název hodnota, která určuje název pole a relativní důležitost. Relativní váha musí být kladné celé číslo nebo číslo s plovoucí desetinnou čárkou. Maximální hodnota je typu int32. Hodnota MaxValue.<br /><br /> Můžete zadat název pole bez odpovídající váhu. Váhy slouží k označení důležitosti jedno pole vzhledem k jiné.|  
|`Functions`|Volitelné. Všimněte si, že funkci bodování lze použít pouze u polí, které se dají filtrovat.|  
|`Type`|Vyžaduje se pro funkce bodování. Určuje typ funkce používat. Platné hodnoty jsou velikost, aktuálnosti, distance a značky. V každé bodovací profil může obsahovat více než jednu funkci. Název funkce musí obsahovat malá písmena.|  
|`Boost`|Vyžaduje se pro funkce bodování. Kladné číslo použije jako násobitel základního skóre. Nemůže být rovna 1.|  
|`Fieldname`|Vyžaduje se pro funkce bodování. Vyhodnocení funkce lze použít pouze na pole, které jsou součástí kolekce pole index a, která se dají filtrovat. Každý typ funkce navíc zavádí další omezení (aktuálnosti se používá s pole typu datetime velikosti se celé číslo a dvojitá pole a vzdálenost s poli umístění). Můžete zadat pouze jedno pole za definici funkce. Například pokud chcete použít velikost dvakrát v jednom profilu, je třeba zahrnout dvě definice velikost, jeden pro každé pole.|  
|`Interpolation`|Vyžaduje se pro funkce bodování. Definuje sklon pro kterou zvyšování skóre od začátku rozsahu na konec rozsahu. Platné hodnoty jsou lineární (výchozí), konstanta, kvadratické a logaritmických. Zobrazit [nastavit interpolace](#bkmk_interpolation) podrobnosti.|  
|`magnitude`|Velikost vyhodnocení funkce se používají pro úpravu pořadí na základě rozsahu hodnot pro číselné pole. Zde jsou některé z nejběžnějších příklady použití tohoto:<br /><br /> -   **Hodnocení hvězdičkami:** Příkaz ALTER vyhodnocování na základě hodnoty v rámci pole "Hvězda hodnocení". Při dvou položek jsou relevantní, se zobrazí první položku s vyšší hodnocení.<br />-   **Okraj:** Když dva dokumenty jsou relevantní, maloobchodní prodejce, který staví na zvýšení dokumentů, které mají vyšší okraje nejprve.<br />-   **Klikněte na tlačítko počty:** Pro aplikace, které sledují proklikejte akce, které produkty nebo stránky, můžete použít velikost na boost položky, které mají tendenci získat nejvíce přenosů.<br />-   **Stáhněte si počty:** Pro aplikace, sledovat soubory ke stažení, zvýšíte umožňuje funkce velikost položek, které mají největší soubory ke stažení.|  
|`magnitude` &#124; `boostingRangeStart`|Nastaví počáteční hodnotu rozsahu, který velikost magnitudu. Hodnota musí být celé číslo nebo číslo s plovoucí desetinnou čárkou. U hodnocení hvězdičkami 1 až 4 by to bylo 1. Pro rozsahy nad 50 % by to bylo 50.|  
|`magnitude` &#124; `boostingRangeEnd`|Nastaví koncovou hodnotu rozsahu, který velikost magnitudu. Hodnota musí být celé číslo nebo číslo s plovoucí desetinnou čárkou. U hodnocení hvězdičkami 1 až 4 by to bylo 4.|  
|`magnitude` &#124; `constantBoostBeyondRange`|Platné hodnoty jsou true nebo false (výchozí). Pokud je nastavena na hodnotu true, bude se skóre budou i nadále platí pro dokumenty s hodnotou cílového pole, která je vyšší než horní konec rozsahu. Pokud je hodnota NEPRAVDA, nárůst této funkce se nepoužije pro dokumenty s hodnotou cílového pole, která spadá mimo rozsah.|  
|`freshness`|Aktuálnost vyhodnocení funkce se používá k Změna pořadí skóre pro položky na základě hodnot v `DateTimeOffset` pole. Položka s novější datum například lze seřadit vyšší než starší položky.<br /><br /> Všimněte si, že je také možné řadit různé věci, třeba události kalendáře s budoucím datem tak, aby položky blíže až po současnost lze seřadit vyšší než položky dále v budoucnu.<br /><br /> V aktuálním vydání služby opravíme v jednom konci rozsahu aktuální čas. Druhém konci je čas v minulosti na základě `boostingDuration`. Pro zvýšení rozsahu čas v budoucnosti, použít zápornou `boostingDuration`.<br /><br /> Rychlost zvýšení úrovně se změní z maximální a minimální rozsah určíte výběrem interpolace použitý pro bodovací profil (viz následující obrázek). Pokud chcete obrátit zvýšení skóre faktor, zvolte faktor zesílení menší než 1.|  
|`freshness` &#124; `boostingDuration`|Nastaví konec platnosti, po kterém se u konkrétního dokumentu přestane zvyšovat skóre. Zobrazit [nastavit boostingDuration](#bkmk_boostdur) v následující části Syntaxe a příkladů.|  
|`distance`|Vzdálenost, o kterou funkci bodování slouží k mít vliv na skóre dokumenty podle toho, jak zavřít nebo daleko jsou od referenční geografické polohy. Odkaz na umístění je uveden jako součást dotazu v parametru (pomocí `scoringParameterquery` řetězec možnost) jako fyzický pevný disk, lat argument.|  
|`distance` &#124; `referencePointParameter`|Parametr má být předán do dotazů jako referenční umístění. `scoringParameter` je parametr dotazu. Zobrazit [vyhledávání dokumentů &#40;rozhraní REST API služby Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) popisy parametrů dotazu.|  
|`distance` &#124; `boostingDistance`|Číslo, která určuje vzdálenost v kilometrech od referenčního místa, kde končí oblast zvýšení skóre.|  
|`tag`|Značka vyhodnocení funkce se používá k bodování dokumentů na základě značek v dokumentech a dotazech. Dokumenty, které mají společné s vyhledávací dotaz značek se dá zvýšit. Značky pro vyhledávací dotaz je zadat jako parametr vyhodnocení v každé žádosti o hledání (pomocí `scoringParameterquery` řetězec možnost).|  
|`tag` &#124; `tagsParameter`|Parametr má být předán v dotazech a bude určovat značky pro konkrétní žádost. `scoringParameter` je parametr dotazu. Zobrazit [vyhledávání dokumentů &#40;rozhraní REST API služby Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) popisy parametrů dotazu.|  
|`functionAggregation`|Volitelné. Platí, pouze když jsou zadané funkce. Platné hodnoty jsou: (výchozí) součet, průměr, minimum, maximum a firstMatching. Skóre vyhledávání je jednu hodnotu, která je vypočítán z více proměnných, včetně více funkcí. Tento atribut určuje, jak zvýšit zvyšuje všechny funkce jsou zkombinované do jedné agregace, který poté platí pro základní dokumentu skóre. Základní hodnocení je založeno na [tf-idf](http://www.tfidf.com/) hodnoty vypočítané z dokumentu a vyhledávací dotaz.|  
|`defaultScoringProfile`|Při provádění požadavku hledání, pokud žádné bodovací profil je zadán, pak se používá výchozí vyhodnocování ([tf-idf](http://www.tfidf.com/) pouze).<br /><br /> Výchozí vyhodnocování název profilu můžete nastavit tady způsobuje Azure Search pro tento profil používat, když žádné konkrétní profil je uveden v požadavku hledání.|  

##  <a name="bkmk_interpolation"></a> Sada interpolace  
 Interpolace umožňují nastavit tvar sklon použít pro vyhodnocování. Protože vyhodnocování je vysoké na nízkou prioritu, sklon se vždy snižuje, ale interpolace určuje křivky klesající sklon. Je možné použít následující interpolace:  

|||  
|-|-|  
|`Linear`|U položek, které spadají do rozsahu maximální a minimální se provede v rámci doby neustále klesající boost použitý pro položku. Lineární je výchozí interpolace pro bodovací profil.|  
|`Constant`|U položek, které jsou v rámci počáteční a koncovou rozsah konstantní boost uplatní na řazení výsledků.|  
|`Quadratic`|Porovnání lineární interpolace, který má neustále klesající boost kvadratické se nejprve sníží menší tempem a potom jako se blíží konec rozsahu, sníží v mnohem vyšší intervalech. Tato možnost umožňuje stručně popsat není povolena ve značce vyhodnocení funkce.|  
|`Logarithmic`|Porovnání lineární interpolace, který má neustále klesající boost logaritmických se nejprve sníží tempem vyšší a potom jako se blíží konec rozsahu, sníží v mnohem menší intervalech. Tato možnost umožňuje stručně popsat není povolena ve značce vyhodnocení funkce.|  

 ![LOG10 – konstanta, lineární, kvadratické, řádky v grafu](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="bkmk_boostdur"></a> Sada boostingDuration  
 `boostingDuration` je atribut `freshness` funkce. Můžete ji použít k nastavení vypršení období, po které zvýšení skóre u konkrétního dokumentu přestane. Například pro zvýšení produktová řada nebo značku pro propagační 10 dní, zadali byste 10 dnů jako "P10D" pro tyto dokumenty.  

 `boostingDuration` musí být naformátovaná jako hodnota "dayTimeDuration" XSD (omezená podmnožina hodnotu doby trvání ISO 8601). Tento vzor je: "P[nD][T[nH][nM][nS]]".  

 Následující tabulka obsahuje několik příkladů.  

|Doba trvání|boostingDuration|  
|--------------|----------------------|  
|1 den|"P1D"|  
|2 dnů až 12 hodin|"P2DT12H"|  
|15 minut|"PT15M"|  
|30 dní, 5 hodin, 10 minut, a 6.334 sekund|"P30DT5H10M6.334S"|  

 Další příklady najdete v tématu [XML schématu: Datové typy (adrese W3.org webu)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Další informace najdete v tématech  
 [Rozhraní REST služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/)   
 [Vytvořit Index &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
 [Služba Azure Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
