---
title: Zvýšení pořadí vyhledávání pomocí profilů hodnocení
titleSuffix: Azure Cognitive Search
description: Přidáním profilů hodnocení zvyšte skóre pořadí vyhledávání pro výsledky Azure Cognitive Search.
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/28/2019
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
ms.openlocfilehash: 516637b812afece1966006ce6d894dd1e32e6293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245457"
---
# <a name="add-scoring-profiles-to-an-azure-cognitive-search-index"></a>Přidání profilů vyhodnocování do indexu služby Azure Cognitive Search

  Hodnocení odkazuje na výpočtu *skóre vyhledávání* pro každou položku vrácenou ve výsledcích hledání. Skóre je indikátorem relevance položek v kontextu aktuální operace vyhledávání. Čím vyšší je skóre, tím je daná položka relevantnější. Ve výsledcích hledání jsou položky seřazeny od nejvyššího k nízkému na základě skóre vyhledávání vypočítaného pro každou položku.  

 Azure Cognitive Search používá výchozí bodování k výpočtu počáteční skóre, ale můžete přizpůsobit výpočet prostřednictvím *profil bodování*. Profily hodnocení poskytují větší kontrolu nad pořadím položek ve výsledcích vyhledávání. Můžete například chtít zvýšit položky na základě jejich potenciálu výnosů, propagovat novější položky nebo možná zvýšit položky, které byly ve skladu příliš dlouho.  

 Profil hodnocení je součástí definice indexu, která se skládá z vážených polí, funkcí a parametrů.  

 Abyste měli představu o tom, jak profil hodnocení vypadá, následující příklad ukazuje jednoduchý profil s názvem "geo". Tento zvýrazní položky, které mají hledaný výraz v poli **hotelName.** Používá také `distance` funkci upřednostnit položky, které jsou do deseti kilometrů od aktuální polohy. Pokud někdo vyhledá termín "hostinec" a "hostinec" je náhodou součástí názvu hotelu, dokumenty, které zahrnují hotely s "hostincí" v okruhu 10 KM od aktuální polohy, se ve výsledcích vyhledávání zobrazí výše.  


```json
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


 Chcete-li použít tento profil hodnocení, je váš dotaz formulován tak, aby určil profil v řetězci dotazu. V níže uvedeném dotazu `scoringProfile=geo` si všimněte parametru dotazu v požadavku.  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2019-05-06 
```  

 Tento dotaz vyhledá termín "hostinec" a předá v aktuálním umístění. Všimněte si, že tento dotaz `scoringParameter`obsahuje další parametry, například . Parametry dotazu jsou popsány v [dokumentech vyhledávání &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).  

 Kliknutím na [Příklad](#bkmk_ex) zkontrolujte podrobnější příklad profilu hodnocení.  

## <a name="what-is-default-scoring"></a>Co je výchozí bodování?  
 Vyhodnocování vypočítá skóre vyhledávání pro každou položku v seřazené sadě výsledků. Každé položce ve výsledkové sadě vyhledávání je přiřazeno skóre vyhledávání a poté se řadí na nejvyšší hodnotu za nejnižší. Položky s vyšším skóre jsou vráceny do aplikace. Ve výchozím nastavení je vráceno prvních 50, ale `$top` parametr můžete použít k vrácení menšího nebo většího počtu položek (až 1000 v jedné odpovědi).  

Skóre hledání se vypočítá na základě statistických vlastností dat a dotazu. Azure Cognitive Search vyhledá dokumenty, které obsahují hledané termíny `searchMode`v řetězci dotazu (některé nebo všechny, v závislosti na ), upřednostňující dokumenty, které obsahují mnoho instancí hledaného výrazu. Skóre vyhledávání se zvýší ještě vyšší, pokud je termín v celém indexu dat vzácný, ale v dokumentu běžný. Základem pro tento přístup k výpočtu relevance se označuje jako [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) nebo termín frekvence inverzní frekvence dokumentu frekvence.  

 Za předpokladu, že neexistuje žádné vlastní řazení, výsledky jsou pak seřazeny podle skóre hledání před jejich vrácením do volající aplikace. Pokud není zadán $top, je vráceno 50 položek s nejvyšším skóre vyhledávání.  

 Hodnoty skóre hledání lze opakovat v celé sadě výsledků. Můžete mít například 10 položek se skóre 1,2, 20 položek se skóre 1,0 a 20 položek se skóre 0,5. Pokud má více přístupů stejné skóre vyhledávání, řazení stejných položek s hodnocením není definováno a není stabilní. Spusťte dotaz znovu a může se zobrazit pozice posunu položek. Vzhledem k tomu, dvě položky s identickým skóre, neexistuje žádná záruka, která z nich se objeví jako první.  

## <a name="when-to-use-custom-scoring"></a>Kdy použít vlastní vyhodnocování  
 Pokud výchozí chování hodnocení neodpovídá dostatečně daleko při plnění obchodních cílů, měli byste vytvořit jeden nebo více profilů hodnocení. Můžete se například rozhodnout, že relevance vyhledávání by měla upřednostňovat nově přidané položky. Podobně můžete mít pole, které obsahuje ziskovou marži, nebo jiné pole označující výnosový potenciál. Zvýšení počet zásahů, které přinášejí výhody vaší firmě, může být důležitým faktorem při rozhodování o použití profilů hodnocení.  

 Pořadí založené na relevanci je také implementováno prostřednictvím profilů hodnocení. Zvažte stránky s výsledky vyhledávání, které jste použili v minulosti a které umožňují řazení podle ceny, data, hodnocení nebo relevance. V Azure Cognitive Search, vyhodnocování profily řídit možnost 'relevance'. Definici relevance řídíte vy, založená na obchodních cílech a typu vyhledávání, které chcete poskytovat.  

##  <a name="example"></a><a name="bkmk_ex"></a>Příklad  
 Jak již bylo uvedeno dříve, vlastní bodování je implementováno prostřednictvím jednoho nebo více profilů hodnocení definovaných ve schématu indexu.  

 Tento příklad ukazuje schéma indexu se dvěma profily hodnocení (`boostGenre`, `newAndHighlyRated`). Jakýkoli dotaz proti tomuto indexu, který obsahuje buď profil jako parametr dotazu, použije profil ke skóre sady výsledků.  

```json
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
 Chcete-li implementovat vlastní hodnocení chování, přidejte profil hodnocení do schématu, který definuje index. V rámci indexu můžete mít až 100 profilů hodnocení (viz [Omezení služeb](search-limits-quotas-capacity.md)), ale v libovolném dotazu můžete zadat pouze jeden profil najednou.  

 Začněte [šablonou](#bkmk_template) uvedenou v tomto tématu.  

 Zadejte název. Profily hodnocení jsou volitelné, ale pokud jej přidáte, bude vyžadován název. Ujistěte se, že postupujte podle zásad pojmenování pro pole (začíná písmenem, vyhýbá se speciální znaky a vyhrazená slova). Úplný seznam najdete [v tématu Pravidla pojmenování &#40;Azure Cognitive Search&#41;.](https://docs.microsoft.com/rest/api/searchservice/naming-rules)  

 Tělo profilu hodnocení je vyrobeno z vážených polí a funkcí.  

|||  
|-|-|  
|**Hmotnosti**|Zadejte dvojice název-hodnota, které přiřazují pole relativní váhu. V [poli Example](#bkmk_ex)jsou pole název alba, žánr a název interpreta posílena 1,5, 5 a 2. Proč je žánr posílen a vyšší než ostatní? Pokud se vyhledávání provádí přes data, která jsou poněkud homogenní (jako je `musicstoreindex`tomu v případě 'žánr' v ), budete potřebovat větší odchylku v relativní váhy. Například v `musicstoreindex`, 'rock' se zobrazí jako žánr a ve stejně formulované žánrové popisy. Pokud chcete, aby žánr převážil nad popisem žánru, pole žánru bude potřebovat mnohem vyšší relativní váhu.|  
|**Functions**|Používá se v případě, že jsou požadovány další výpočty pro konkrétní kontexty. Platné hodnoty `freshness` `magnitude`jsou `distance`, `tag`, a . Každá funkce má parametry, které jsou jedinečné.<br /><br /> -   `freshness`by měl být použit, pokud chcete zvýšit tím, jak nová nebo stará položka je. Tuto funkci lze použít `datetime` pouze s poli (edm. DataTimeOffset). Všimněte `boostingDuration` si, že `freshness` atribut se používá pouze s funkcí.<br />-   `magnitude`by měl být použit, pokud chcete zvýšit na základě toho, jak vysoká nebo nízká číselná hodnota je. Scénáře, které vyžadují tuto funkci, zahrnují zvýšení ziskové marže, nejvyšší ceny, nejnižší ceny nebo počtu stažení. Tuto funkci lze použít pouze s dvojitými a celými poli.<br />     Pro `magnitude` funkci můžete obrátit rozsah, vysoká až nízká, pokud chcete inverzní vzor (například zvýšit nižší ceny položek více než vyšší ceny položek). Vzhledem k tomu, rozsah cen od $ 100 do $ 1, měli byste nastavit `boostingRangeStart` na 100 a `boostingRangeEnd` na 1 zvýšit nižší ceny-položky.<br />-   `distance`by měl být použit, pokud chcete zvýšit o blízkost nebo zeměpisné polohy. Tuto funkci lze použít `Edm.GeographyPoint` pouze s poli.<br />-   `tag`by měl být použit, pokud chcete zvýšit značky společné mezi dokumenty a vyhledávacídotazy. Tuto funkci lze použít `Edm.String` `Collection(Edm.String)` pouze s poli a.<br /><br /> **Pravidla pro používání funkcí**<br /><br /> Typ funkce`freshness` `magnitude`( `distance` `tag` , , ), musí být malá písmena.<br /><br /> Funkce nemohou obsahovat nulové nebo prázdné hodnoty. Konkrétně pokud zahrnete název pole, musíte jej nastavit na něco.<br /><br /> Funkce lze použít pouze pro filtrovatelná pole. Další informace o filtrovatelných polích najdete v tématu Vytvoření indexu &#40;&#41;rozhraní REST API azure [kognitivního vyhledávání.](https://docs.microsoft.com/rest/api/searchservice/create-index)<br /><br /> Funkce lze použít pouze pro pole, která jsou definována v polích kolekce indexu.|  

 Po definování indexu vytvořte index nahráním schématu indexu následovaným dokumenty. Pokyny k těmto operacím najdete [v tématu Vytvoření indexu &#40;rozhraní REST ROZHRANÍ API azure kognitivního vyhledávání a&#41;přidání,](https://docs.microsoft.com/rest/api/searchservice/create-index) [aktualizaci nebo odstranění dokumentů &#40;&#41;rozhraní REST API pro azure cognitive search.](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) Jakmile je index sestaven, měli byste mít funkční profil hodnocení, který pracuje s vyhledávacími daty.  

##  <a name="template"></a><a name="bkmk_template"></a>Šablony  
 Tato část zobrazuje syntaxi a šablonu pro profily hodnocení. Popis atributů naleznete v [odkazu na odkazy na atributy Rejstříku](#bkmk_indexref) v další části.  

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

##  <a name="index-attributes-reference"></a><a name="bkmk_indexref"></a>Odkaz na atributy rejstříku  

> [!NOTE]  
>  Funkci hodnocení lze použít pouze u polí, která lze filtrovat.  

|Atribut|Popis|  
|---------------|-----------------|  
|`name`|Povinná hodnota. Toto je název profilu hodnocení. Řídí se stejnými konvencemi pojmenování pole. Musí začínat písmenem, nesmí obsahovat tečky, dvojtečky nebo symboly @ a nemůže začínat frází "azureSearch" (rozlišují malá a velká písmena).|  
|`text`|Obsahuje vlastnost weights.|  
|`weights`|Nepovinný parametr. Obsahuje dvojice název-hodnota, které každý zadat název pole a relativní hmotnost. Relativní hmotnost musí být kladné celé číslo nebo číslo s plovoucí desetinnou desetinnou desetinnou stou.<br /><br /> Závaží se používají k označení důležitosti jednoho prohledávatelného pole vzhledem k jinému.|  
|`functions`|Nepovinný parametr. Funkci hodnocení lze použít pouze u polí, která lze filtrovat.|  
|`type`|Vyžadováno pro funkce bodování. Označuje typ funkce, která má být používána. Mezi platné hodnoty patří velikost, čerstvost, vzdálenost a značka. Do každého profilu hodnocení můžete zahrnout více funkcí. Název funkce musí být malá písmena.|  
|`boost`|Vyžadováno pro funkce bodování. Kladné číslo použité jako multiplikátor pro nezpracované skóre. Nemůže se rovnat 1.|  
|`fieldname`|Vyžadováno pro funkce bodování. Funkci hodnocení lze použít pouze pro pole, která jsou součástí kolekce polí indexu a která jsou filtrovatelná. Kromě toho každý typ funkce zavádí další omezení (čerstvost se používá s datetime pole, velikost s celá čísla nebo dvojité pole a vzdálenost s umístění polí). Pro každou definici funkce můžete zadat pouze jedno pole. Chcete-li například použít velikost dvakrát ve stejném profilu, je třeba zahrnout dvě definice velikost, jeden pro každé pole.|  
|`interpolation`|Vyžadováno pro funkce bodování. Definuje sklon, pro který se zvyšuje zvýšení skóre od začátku rozsahu až do konce rozsahu. Mezi platné hodnoty patří Lineární (výchozí), Konstanta, Kvadratická a Logaritmická. Podrobnosti najdete [v tématu Nastavení interpolací.](#bkmk_interpolation)|  
|`magnitude`|Funkce hodnocení velikosti se používá ke změně pořadí na základě rozsahu hodnot pro číselné pole. Některé z nejběžnějších příkladů použití jsou:<br /><br /> -   **Hodnocení hvězdičkami:** Změňte hodnocení na základě hodnoty v poli "Hodnocení hvězdičkami". Pokud jsou relevantní dvě položky, zobrazí se nejprve položka s vyšším hodnocením.<br />-   **Marže:** Pokud jsou relevantní dva dokumenty, může maloobchodník chtít nejprve posílit dokumenty, které mají vyšší marže.<br />-   **Počty kliknutí:** U aplikací, které sledují proklikávky akcí na produkty nebo stránky, můžete použít velikost k posílení položek, které mají tendenci získat největší návštěvnost.<br />-   **Počet stažení:** U aplikací, které sledují stahování, umožňuje funkce velikosti propagovat položky, které mají nejvíce stahování.|  
|`magnitude`&#124;`boostingRangeStart`|Nastaví počáteční hodnotu rozsahu, ve kterém je hodnota velikosti skórována. Hodnota musí být celé číslo nebo číslo s plovoucí desetinnou desetinnou desetinnou tísní. Pro hodnocení hvězdičkami 1 až 4 by to bylo 1. Pro marže nad 50% by to bylo 50.|  
|`magnitude`&#124;`boostingRangeEnd`|Nastaví koncovou hodnotu rozsahu, ve kterém je hodnota velikosti hodnocena. Hodnota musí být celé číslo nebo číslo s plovoucí desetinnou desetinnou desetinnou tísní. Pro hodnocení hvězdičkami 1 až 4 by to bylo 4.|  
|`magnitude`&#124;`constantBoostBeyondRange`|Platné hodnoty jsou pravdivé nebo nepravdivé (výchozí). Pokud je nastavena na hodnotu true, úplné zvýšení bude nadále platit pro dokumenty, které mají hodnotu pro cílové pole, které je vyšší než horní konec rozsahu. Pokud false, zvýšení této funkce nebude použita na dokumenty, které mají hodnotu pro cílové pole, které spadá mimo rozsah.|  
|`freshness`|Funkce hodnocení čerstvosti se používá ke změně skóre `DateTimeOffset` hodnocení položek na základě hodnot v polích. Například položku s novějším datem lze seřadit výše než starší položky.<br /><br /> Je také možné seřadit položky, jako jsou události kalendáře s budoucími daty, tak, aby položky blíže k současnosti mohly být v budoucnu zařazeny výše než položky.<br /><br /> V aktuální verzi služby bude jeden konec rozsahu fixován na aktuální čas. Druhý konec je čas v minulosti `boostingDuration`na základě . Chcete-li v budoucnu zvýšit rozsah `boostingDuration`opakování, použijte záporný program .<br /><br /> Rychlost, s jakou se zvýšení mění z maximálního a minimálního rozsahu, je určena interpolací použitou na bodovací profil (viz obrázek níže). Chcete-li zvrátit použitý faktor zesílení, zvolte faktor zvýšení menší než 1.|  
|`freshness`&#124;`boostingDuration`|Nastaví dobu vypršení platnosti, po jejímž uplynutí se propagace zastaví pro určitý dokument. Syntaxi a příklady najdete v tématu [Nastavení boostingDuration](#bkmk_boostdur) v následující části.|  
|`distance`|Funkce vyhodnocování vzdálenosti se používá k ovlivnění skóre dokumentů na základě toho, jak blízko nebo daleko jsou vzhledem k referenční zeměpisné poloze. Umístění odkazu je dáno jako součást dotazu `scoringParameterquery` v parametru (pomocí možnosti řetězce) jako argument lon,lat.|  
|`distance`&#124;`referencePointParameter`|Parametr, který má být předán v dotazech, které mají být používány jako referenční umístění. `scoringParameter`je parametr dotazu. Popis parametrů dotazu najdete v tématu [Search Documents &#40;Azure Cognitive Search REST API&#41;.](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)|  
|`distance`&#124;`boostingDistance`|Číslo, které označuje vzdálenost v kilometrech od referenčního místa, kde končí dosah zesílení.|  
|`tag`|Funkce vyhodnocování značek se používá k ovlivnění skóre dokumentů na základě značek v dokumentech a vyhledávacích dotazech. Dokumenty, které mají značky společné s vyhledávacím dotazem, budou posíleny. Značky pro vyhledávací dotaz jsou k dispozici jako parametr hodnocení `scoringParameterquery` v každém požadavku hledání (pomocí možnosti řetězec).|  
|`tag`&#124;`tagsParameter`|Parametr, který má být předán v dotazech k určení značek pro konkrétní požadavek. `scoringParameter`je parametr dotazu. Popis parametrů dotazu najdete v tématu [Search Documents &#40;Azure Cognitive Search REST API&#41;.](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)|  
|`functionAggregation`|Nepovinný parametr. Platí pouze v případě, že jsou zadány funkce. Platné hodnoty zahrnují: součet (výchozí), průměr, minimum, maximum a firstMatching. Skóre vyhledávání je jedna hodnota, která je vypočítána z více proměnných, včetně více funkcí. Tento atribut označuje, jak jsou zesílení všech funkcí sloučeny do jednoho agregačního zesílení, které se pak použije na skóre základního dokumentu. Základní skóre je založeno na hodnotě [tf-idf](http://www.tfidf.com/) vypočítané z dokumentu a vyhledávacího dotazu.|  
|`defaultScoringProfile`|Při provádění požadavku hledání, pokud není zadán žádný profil hodnocení, pak se použije výchozí bodování (pouze[tf-idf).](http://www.tfidf.com/)<br /><br /> Zde lze nastavit název výchozího profilu hodnocení, což způsobí, že Azure Cognitive Search použije tento profil, když v požadavku na hledání není uveden žádný konkrétní profil.|  

##  <a name="set-interpolations"></a><a name="bkmk_interpolation"></a>Nastavení interpolací  
 Interpolace umožňují nastavit tvar sklonu použitého pro vyhodnocování. Vzhledem k tomu, že vyhodnocování je od shora až nízké, sklon se vždy snižuje, ale interpolace určuje křivku sestupného svahu. Lze použít následující interpolace:  

|||  
|-|-|  
|`linear`|U položek, které jsou v rozsahu max a min, bude zesílení aplikované na položku provedeno v neustále klesajícím množství. Lineární je výchozí interpolace pro profil hodnocení.|  
|`constant`|U položek, které jsou v počátečním a koncovém rozsahu, bude na výsledky hodnocení použito konstantní zesílení.|  
|`quadratic`|Ve srovnání s lineární interpolací, která má neustále klesající zesílení, quadratic bude zpočátku klesat menším tempem a pak, jak se blíží ke koncovému rozsahu, klesá v mnohem vyšším intervalu. Tato možnost interpolace není povolena ve funkcích hodnocení tagů.|  
|`logarithmic`|Ve srovnání s lineární interpolací, která má neustále klesající podporu, bude Logaritmic zpočátku klesat vyšším tempem a pak, jak se blíží ke konečnému rozsahu, klesá v mnohem menším intervalu. Tato možnost interpolace není povolena ve funkcích hodnocení tagů.|  

 ![Konstantní, lineární, kvadratické, log10 čáry na grafu](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="set-boostingduration"></a><a name="bkmk_boostdur"></a>Nastavit boostingDuration  
 `boostingDuration`je atributem `freshness` funkce. Slouží k nastavení období vypršení platnosti, po jehož uplynutí se zvýšení zastaví pro určitý dokument. Chcete-li například zvýšit produktovou řadu nebo značku na 10denní období propagační akce, zadejte pro tyto dokumenty 10denní období jako "P10D".  

 `boostingDuration`musí být formátována jako hodnota XSD "dayTimeDuration" (omezená podmnožina hodnoty trvání ISO 8601). Vzor pro toto je: "P[nD][T[nH][nM][nS]]".  

 Následující tabulka obsahuje několik příkladů.  

|Doba trvání|boostingDoba trvání|  
|--------------|----------------------|  
|1 den|"P1D"|  
|2 dny a 12 hodin|"P2DT12H"|  
|15 minut|"PT15M"|  
|30 dní, 5 hodin, 10 minut a 6,334 sekundy|"P30DT5H10M6.334S"|  

 Další příklady naleznete [v tématu schéma XML: Datové typy (W3.org webu).](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)  

## <a name="see-also"></a>Viz také  
 [Azure Kognitivní vyhledávání REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Vytvoření indexu &#40;rozhraní REST API pro kognitivní vyhledávání Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
 [Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
