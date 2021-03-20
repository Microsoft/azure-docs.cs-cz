---
title: Zvýšení pořadí vyhledávání pomocí profilů vyhodnocování
titleSuffix: Azure Cognitive Search
description: Zvyšte skóre pořadí hledání pro Azure Kognitivní hledání výsledky přidáním profilů vyhodnocování.
manager: nitinme
author: shmed
ms.author: ramero
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 97797e309c32c6ea996d5ae1901b9a266a683173
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537629"
---
# <a name="add-scoring-profiles-to-an-azure-cognitive-search-index"></a>Přidání profilů vyhodnocování do indexu služby Azure Cognitive Search

*Bodování* počítá skóre hledání pro každou položku v sadě výsledků seřazené podle pořadí. Každé položce v sadě výsledků hledání je přiřazeno skóre hledání, seřazené od nejvyšší po nejnižší.

 Azure Kognitivní hledání používá k výpočtu počátečního skóre výchozí bodování, ale tento výpočet můžete přizpůsobit pomocí *profilu vyhodnocování*. Profily vyhodnocování poskytují větší kontrolu nad hodnocením položek ve výsledcích vyhledávání. Například můžete chtít zvýšit množství položek na základě jejich potenciálních výnosů, zvýšit úroveň nových položek nebo možná zvýšit množství položek, které byly v inventáři příliš dlouho.  

 Následující segment videa se rychle přepošle na to, jak profily vyhodnocování fungují v Azure Kognitivní hledání.
 
> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=463&end=970]

## <a name="scoring-profile-definitions"></a>Definice profil bodování

 Profil vyhodnocování je součástí definice indexu, která se skládá z vážených polí, funkcí a parametrů.  

 Chcete-li poskytnout představu o tom, jak profil vyhodnocování vypadá, následující příklad ukazuje jednoduchý profil s názvem geo. Tato jedna zvyšuje položky, které mají hledaný termín v poli **hotely** . Používá také `distance` funkci k upřednostnění položek, které spadají do deseti kilometrů aktuálního umístění. Pokud někdo vyhledá pojem "Inn" a "Inn" se stane součástí názvu hotelu, dokumenty, které obsahují Hotely s "Inn" v rámci poloměru 10 KM v aktuálním umístění, se ve výsledcích hledání zobrazí výše.  


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


 Chcete-li použít tento profil vyhodnocování, je dotaz určen k určení profilu v řetězci dotazu. V níže uvedeném dotazu si všimněte parametru dotazu `scoringProfile=geo` v žádosti.  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2020-06-30 
```  

 Tento dotaz vyhledá pojem ' Inn ' a předá ho do aktuálního umístění. Všimněte si, že tento dotaz obsahuje další parametry, například `scoringParameter` . Parametry dotazu jsou popsány v [dokumentu hledání &#40;Azure Kognitivní hledání REST API&#41;](/rest/api/searchservice/Search-Documents).  

 Kliknutím na [příklad](#bkmk_ex) můžete zkontrolovat podrobnější příklad profilu vyhodnocování.  

## <a name="what-is-default-scoring"></a>Co je výchozí bodování?  
 Bodování počítá skóre hledání pro každou položku v sadě výsledků seřazené podle pořadí. Každé položce v sadě výsledků hledání je přiřazeno skóre hledání, seřazené od nejvyšší po nejnižší. Do aplikace se vrátí položky s vyšším skóre. Ve výchozím nastavení se vrátí nejvyšší 50, ale můžete použít `$top` parametr k vrácení menšího nebo většího počtu položek (až 1000 v jedné odpovědi).  

Skóre hledání je vypočítáno na základě statistických vlastností dat a dotazu. Azure Kognitivní hledání najde dokumenty, které obsahují hledané termíny do řetězce dotazu (v závislosti na `searchMode` ), a upřednostňuje dokumenty, které obsahují mnoho instancí hledaného termínu. Skóre hledání bude ještě vyšší, pokud je v indexu dat zřídka, ale v dokumentu běžné. Základem pro tento přístup k výpočetním významům je známý jako [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) nebo četnost termínů – inverzní frekvence dokumentů.  

 Za předpokladu, že neexistuje žádné vlastní řazení, výsledky se pak seřazené podle skóre vyhledávání, než se vrátí do volající aplikace. Pokud není zadán $top, jsou vráceny 50 položky, které mají nejvyšší skóre hledání.  

 Hodnoty skóre hledání je možné opakovat v rámci sady výsledků dotazu. Můžete mít například 10 položek s skóre 1,2, 20 položek se skóre 1,0 a 20 položek se skóre 0,5. Pokud má více přístupů stejný výsledek hledání, řazení stejných položek skóre není definováno a není stabilní. Spusťte dotaz znovu a můžete se podívat na pozici posunu položek. Vzhledem k tomu, že se dvě položky shodují se stejným skóre, neexistuje žádná záruka, která se zobrazí jako první.  

## <a name="when-to-add-scoring-logic"></a>Kdy přidat logiku bodování 
 Jeden nebo více profilů vyhodnocování byste měli vytvořit v případě, že výchozí chování hodnocení nebude v souladu s vašimi podnikovými záměry dostatečně daleko. Například se můžete rozhodnout, že se má při hledání upřednostňovat nové přidávané položky. Podobně může být pole, které obsahuje ziskové marže, nebo jiné pole, které indikuje potenciální výnosy. Zvýšení počtu přístupů, které přinášejí výhody vašemu podniku, může být důležitým faktorem při rozhodování o používání profilů vyhodnocování.  

 Řazení založené na relevanci se také implementuje prostřednictvím profilů vyhodnocování. Vezměte v úvahu stránky výsledků hledání, které jste použili v minulosti, které vám umožňují řadit podle ceny, data, hodnocení nebo relevance. V Azure Kognitivní hledání se profily vyhodnocování řídí možností "závažnost". Definice relevance je řízena vámi, s predikátem pro obchodní cíle a typem vyhledávacího prostředí, které chcete doručit.  

##  <a name="example"></a><a name="bkmk_ex"></a> Případě  
 Jak bylo uvedeno dříve, přizpůsobené hodnocení je implementováno prostřednictvím jednoho nebo více profilů vyhodnocování definovaných ve schématu indexu.  

 Tento příklad ukazuje schéma indexu se dvěma profily vyhodnocování ( `boostGenre` , `newAndHighlyRated` ). Každý dotaz na tento index, který obsahuje buď profil jako parametr dotazu, použije profil k vyhodnocení sady výsledků dotazu.  

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
 Chcete-li implementovat vlastní chování bodování, přidejte profil vyhodnocování do schématu definujícího index. V rámci indexu můžete mít až 100 profilů vyhodnocování (viz [omezení služby](search-limits-quotas-capacity.md)), ale v libovolném daném dotazu můžete zadat jenom jeden profil v čase.  

 Začněte [šablonou](#bkmk_template) uvedeným v tomto tématu.  

 Zadejte název. Profily vyhodnocování jsou volitelné, ale pokud ho přidáte, název je povinný. Nezapomeňte dodržovat zásady vytváření názvů pro pole (začíná písmenem, vyhněte se speciálním znakům a rezervovaným slovům). Úplný seznam najdete v tématu [pravidla Pojmenování &#40;Azure Kognitivní hledání&#41;](/rest/api/searchservice/naming-rules) .  

 Tělo profilu vyhodnocování je vyrobeno z vážených polí a funkcí.  

|||  
|-|-|  
|**Váhu**|Zadejte páry název-hodnota, které poli přiřadí relativní váhu. V [příkladu](#bkmk_ex)jsou pole albumTitle, Žánr a umělec posílena 1,5, 5 a 2 v uvedeném pořadí. Proč se Žánr zvýšilo, takže je mnohem větší než ostatní? Pokud je vyhledávání provedeno nad daty, která jsou poměrně homogenní (stejně jako v případě "žánru" v rámci `musicstoreindex` ), možná budete potřebovat větší odchylku v relativních závažích. Například v `musicstoreindex` , "Rock" se zobrazuje jako žánr a v přesně frázi popis žánrů. Pokud chcete, aby měl Žánr popis žánru, bude mít pole Žánr mnohem vyšší relativní váhu.|  
|**Functions**|Používá se, pokud jsou pro konkrétní kontexty vyžadovány další výpočty. Platné hodnoty jsou `freshness`, `magnitude`, `distance` nebo `tag`. Každá funkce má parametry, které jsou pro ni jedinečné.<br /><br /> -   `freshness` by měla být použita, pokud chcete zvýšit způsob, jakým je položka nová nebo stará. Tato funkce se dá použít jenom s `datetime` poli (EDM. DataTimeOffset). Všimněte si, že `boostingDuration` atribut se používá pouze s `freshness` funkcí.<br />-   `magnitude` by měla být použita, pokud chcete zvýšit úroveň na základě vysoké nebo nízké číselné hodnoty. Mezi scénáře, které volají tuto funkci, patří zvýšení úrovně zisku za ziskové marže, nejvyšší cena, nejnižší cena nebo počet souborů ke stažení. Tato funkce se dá použít jenom s poli Double a Integer.<br />     V `magnitude` případě funkce můžete změnit rozsah od nejvyšší po nejnižší, pokud chcete inverzní vzorek (například pro zvýšení ceny položek s nižší cenou více než zboží s vyšší cenou). V rámci rozsahu cen od $100 do $1 byste při zvyšování počtu položek snížení ceny nastavili hodnotu `boostingRangeStart` 100 a `boostingRangeEnd` 1.<br />-   `distance` by se měla použít, když chcete zvýšit polohu v blízkosti nebo zeměpisné poloze. Tato funkce se dá použít jenom s `Edm.GeographyPoint` poli.<br />-   `tag` by měla být použita, pokud chcete zvýšit podle značek běžných mezi dokumenty a vyhledávacími dotazy. Tato funkce se dá použít jenom s `Edm.String` `Collection(Edm.String)` poli a.<br /><br /> **Pravidla pro používání funkcí**<br /><br /> Typ funkce ( `freshness` , `magnitude` , `distance` ), `tag` musí být malými písmeny.<br /><br /> Funkce nesmí obsahovat hodnoty null nebo prázdné hodnoty. Konkrétně, pokud zahrnete NázevPole, je nutné jej nastavit na něco.<br /><br /> Funkce se dají použít jenom u filtrovaných polí. Další informace o polích, která se filtrují, najdete v tématu [vytvoření indexu &#40;Azure Kognitivní hledání REST API&#41;](/rest/api/searchservice/create-index) .<br /><br /> Funkce lze použít pouze pro pole, která jsou definována v kolekci pole v indexu.|  

 Po definování indexu Sestavte index tak, že nahrajete schéma indexu, po kterém následují dokumenty. Pokyny k těmto operacím najdete v tématu [vytvoření indexu &#40;azure Kognitivní hledání REST API&#41;](/rest/api/searchservice/create-index) a [Přidání, aktualizace nebo odstranění dokumentů &#40;Azure kognitivní hledání REST API ](/rest/api/searchservice/addupdate-or-delete-documents)&#41;. Po vytvoření indexu byste měli mít funkční profil pro vyhodnocování, který pracuje s vašimi vyhledávacími daty.  

##  <a name="template"></a><a name="bkmk_template"></a> Vzhledu  
 Tato část ukazuje syntaxi a šablonu pro profily vyhodnocování. Popisy atributů najdete v tématu Referenční informace k [atributům indexu](#bkmk_indexref) v následující části.  

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

##  <a name="index-attributes-reference"></a><a name="bkmk_indexref"></a> Reference k atributům indexu  

> [!NOTE]  
>  Funkci bodování lze použít pouze pro pole, která lze filtrovat.  

|Atribut|Popis|  
|---------------|-----------------|  
|`name`|Povinná hodnota. Toto je název profilu vyhodnocování. Řídí se stejnými zásadami pro pojmenování pole. Musí začínat písmenem, nesmí obsahovat tečky, dvojtečky nebo @ symboly a nemůže začínat slovem ' azureSearch ' (rozlišuje velká a malá písmena).|  
|`text`|Obsahuje vlastnost váhy.|  
|`weights`|Nepovinný parametr. Obsahuje páry název-hodnota, které určují název pole a relativní váhu. Relativní váha musí být kladné celé číslo nebo číslo s plovoucí desetinnou čárkou.<br /><br /> Váhy se používají k označení důležitosti jednoho vyhledávacího pole relativně k druhému.|  
|`functions`|Nepovinný parametr. Funkci bodování lze použít pouze pro pole, která lze filtrovat.|  
|`type`|Vyžaduje se pro funkce bodování. Určuje typ funkce, která se má použít. Platné hodnoty zahrnují velikost, aktuálnost, vzdálenost a značku. Do každého profilu vyhodnocování můžete zahrnout více než jednu funkci. Název funkce musí být malými písmeny.|  
|`boost`|Vyžaduje se pro funkce bodování. Kladné číslo, které se používá jako násobitel pro nezpracované skóre. Nemůže být rovna 1.|  
|`fieldname`|Vyžaduje se pro funkce bodování. Funkci bodování lze použít pouze pro pole, která jsou součástí kolekce polí indexu a která jsou filtrovatelné. Kromě toho každý typ funkce zavádí další omezení (aktuálnost se používá s poli DateTime, rozsah s celočíselnými nebo dvojitými poli a vzdáleností s poli umístění). Pro každou definici funkce můžete zadat jenom jedno pole. Chcete-li například použít velikost dvakrát ve stejném profilu, je třeba zahrnout velikost dvou definic, jednu pro každé pole.|  
|`interpolation`|Vyžaduje se pro funkce bodování. Definuje sklon, pro který se zvyšování skóre zvyšuje od začátku rozsahu až po konec rozsahu. Mezi platné hodnoty patří lineární (výchozí), konstanta, kvadratická a logaritmická. Podrobnosti najdete v tématu [Nastavení interpolací](#bkmk_interpolation) .|  
|`magnitude`|Funkce hodnocení velikosti se používá ke změně pořadí na základě rozsahu hodnot číselného pole. Mezi nejběžnější příklady použití patří:<br /><br /> -   **Hodnocení hvězdičkami:** Změňte hodnocení na základě hodnoty v poli hodnocení hvězdičkami. V případě, že jsou relevantní dvě položky, zobrazí se jako první položka s vyšším hodnocením.<br />-   **Okraj:** V případě, že jsou relevantní dva dokumenty, může maloobchodní prodejce chtít nejprve zvýšit dokumenty, které mají vyšší marže.<br />-   **Klikněte na počty:** V případě aplikací, které sledují kliknutí prostřednictvím akcí na produkty nebo stránky, můžete použít velikost ke zvýšení množství položek, které jsou obvykle schopné získat většinu provozu.<br />-   **Počty stahování:** U aplikací, které sledují soubory ke stažení, vám funkce velikost umožňuje zvýšit množství položek, které mají nejvíc souborů ke stažení.|  
|`magnitude` &#124; `boostingRangeStart`|Nastaví počáteční hodnotu rozsahu, jehož velikost je vyhodnocena. Hodnota musí být celé číslo nebo číslo s plovoucí desetinnou čárkou. Pro hodnocení hvězdičkami 1 až 4 by to bylo 1. Pro okraje větší než 50% by to bylo 50.|  
|`magnitude` &#124; `boostingRangeEnd`|Nastaví koncovou hodnotu rozsahu, jehož velikost je vyhodnocena. Hodnota musí být celé číslo nebo číslo s plovoucí desetinnou čárkou. Pro hodnocení hvězdičkami 1 až 4 by to bylo 4.|  
|`magnitude` &#124; `constantBoostBeyondRange`|Platné hodnoty jsou true nebo false (výchozí). Při nastavení na hodnotu true bude úplný nárůst platit i pro dokumenty, které mají hodnotu pro cílové pole, které je vyšší než horní konec rozsahu. Pokud je hodnota false, zvýšení této funkce se nepoužije na dokumenty, které mají hodnotu cílového pole, které spadají mimo rozsah.|  
|`freshness`|Funkce bodování aktuálnosti se používá pro změnu hodnocení skóre pro položky na základě hodnot v `DateTimeOffset` polích. Například položka s novějším datem může být seřazena více než staršími položkami.<br /><br /> Je také možné seřadit položky, jako jsou například události kalendáře s budoucími kalendářními daty, aby se položky blíže k tomuto seznamu mohly v budoucnu seřadit výše než položky.<br /><br /> V aktuální verzi služby bude jeden konec rozsahu pevně stanoven na aktuální čas. Druhý konec je čas v minulosti na základě `boostingDuration` . Pokud chcete v budoucnu zvýšit časový rozsah, použijte záporné `boostingDuration` .<br /><br /> Frekvence, s jakou se zvyšují změny z maximálního a minimálního rozsahu, je určena interpolací použitou pro profil vyhodnocování (viz následující obrázek). Chcete-li obrátit faktor zvýšení úrovně, vyberte faktor zvýšení, který je menší než 1.|  
|`freshness` &#124; `boostingDuration`|Nastaví období vypršení platnosti, po kterém se u konkrétního dokumentu zastaví zvýšení úrovně. Syntaxe a příklady najdete v tématu [set boostingDuration](#bkmk_boostdur) v následující části.|  
|`distance`|Funkce bodování vzdálenosti se používá k ovlivnění skóre dokumentů na základě toho, jak blízko nebo daleko jsou relativní vzhledem k geografickému umístění odkazu. Referenční umístění je zadáno jako součást dotazu v parametru (pomocí `scoringParameterquery` Možnosti řetězec) jako argument Lon, lat.|  
|`distance` &#124; `referencePointParameter`|Parametr, který se má předat v dotazech, který se má použít jako referenční umístění `scoringParameter` je parametr dotazu. Popisy parametrů dotazů naleznete v tématu [Search documents &#40;Azure Kognitivní hledání REST API&#41;](/rest/api/searchservice/Search-Documents) .|  
|`distance` &#124; `boostingDistance`|Číslo, které označuje vzdálenost v kilometrech od místa odkazu, kde končí rozsah zvyšování úrovně.|  
|`tag`|Funkce bodování značek se používá k ovlivnění skóre dokumentů na základě značek v dokumentech a vyhledávacích dotazech. Dokumenty, které mají Tagy společné s vyhledávacím dotazem, se budou zvyšovat. Značky pro vyhledávací dotaz jsou k dispozici jako parametr bodování v každé žádosti o vyhledávání (pomocí `scoringParameterquery` Možnosti řetězec).|  
|`tag` &#124; `tagsParameter`|Parametr, který se má předat v dotazech k určení značek pro konkrétní požadavek. `scoringParameter` je parametr dotazu. Popisy parametrů dotazů naleznete v tématu [Search documents &#40;Azure Kognitivní hledání REST API&#41;](/rest/api/searchservice/Search-Documents) .|  
|`functionAggregation`|Nepovinný parametr. Platí pouze v případě, že jsou zadány funkce. Platné hodnoty jsou: Sum (výchozí), Average, minim, maximum a firstMatching. Skóre hledání je jediná hodnota, která je vypočítána z více proměnných, včetně více funkcí. Tento atribut určuje, jakým způsobem jsou kombinování všech funkcí zkombinovány do jediného souhrnného zvýšení, které je následně použito pro základní skóre dokumentu. Základní skóre vychází z hodnoty [TF-IDF](http://www.tfidf.com/) vypočítané z dokumentu a vyhledávacího dotazu.|  
|`defaultScoringProfile`|Pokud není zadán žádný profil vyhodnocování, je při spuštění žádosti o vyhledávání použit výchozí bodování (pouze[TF-IDF](http://www.tfidf.com/) ).<br /><br /> Tady můžete nastavit výchozí název profilu vyhodnocování, což způsobí, že Azure Kognitivní hledání používat tento profil, když v žádosti o vyhledávání není zadaný žádný konkrétní profil.|  

##  <a name="set-interpolations"></a><a name="bkmk_interpolation"></a> Nastavit interpoly  
 Interpolace umožňují nastavit tvar sklonu, který se používá pro bodování. Vzhledem k tomu, že je bodování vysoké, je sklon vždy nižší, ale interpolace určuje křivku sklonu dolů. Můžete použít následující interpolace:  

| Interpolace | Description |  
|-|-|  
|`linear`|U položek, které jsou v rozsahu Max a min, se zvýšení použité pro položku provede v nepřetržitém snížení množství. Lineární je výchozí interpolací pro profil vyhodnocování.|  
|`constant`|Pro položky, které jsou v rozsahu od začátku do konce, se pro výsledky řazení použije konstantní zvýšení.|  
|`quadratic`|V porovnání s lineární interpolací, která má neustále se zmenšující zvýšení, se zpočátku zkrátí na menší tempo a pak při blížící se k koncovému rozsahu se zmenší v mnohem větším intervalu. Tato možnost interpolace není povolená ve funkcích bodování značek.|  
|`logarithmic`|V porovnání s lineární interpolací, která má neustále se zmenšující zvýšení, se logaritmická hodnota zpočátku zmenší při větším tempu a potom se blíží koncovému rozsahu, sníží se v mnohem kratším intervalu. Tato možnost interpolace není povolená ve funkcích bodování značek.|  

 ![Konstanta, lineární, kvadratická, log10 –ová čára v grafu](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="set-boostingduration"></a><a name="bkmk_boostdur"></a> Nastavení boostingDuration  
 `boostingDuration` je atributem `freshness` funkce. Použijete ji k nastavení období vypršení platnosti, po kterém se u konkrétního dokumentu zastaví zvyšování úrovně. Chcete-li například zvýšit množství produktů nebo označení na 10 dní propagačního období, zadejte pro tyto dokumenty 10 dní tečku jako "P10D".  

 `boostingDuration` musí být formátován jako hodnota XSD "dayTimeDuration" (omezená podmnožina hodnoty Duration ISO 8601). Vzor pro tuto položku je: "P [nD] [T [nH] [nM] [nS]]".  

 Následující tabulka uvádí několik příkladů.  

|Doba trvání|boostingDuration|  
|--------------|----------------------|  
|1 den|"P1D"|  
|2 dny a 12 hodin|"P2DT12H"|  
|15 minut|"PT15M"|  
|30 dní, 5 hodin, 10 minut a 6,334 sekund|"P30DT5H10M 6.334 S"|  

 Další příklady naleznete v tématu [schéma XML: DataTypes (w3.org Web)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Viz také  

+ [Odkaz na REST API](/rest/api/searchservice/)
+ [Vytvoření rozhraní API pro index](/rest/api/searchservice/create-index)
+ [Sada Azure Kognitivní hledání .NET SDK](/dotnet/api/overview/azure/search?)