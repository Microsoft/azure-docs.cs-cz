---
title: Odkaz na rozhraní API pro vyhledávání pro místní firmy bingu v7
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje technické podrobnosti o objektech odpovědí a parametrech dotazu a záhlavích, které ovlivňují výsledky hledání.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: db764a73aa1bb18ef2fc0f8f6e5ffe8fd60d388c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74075698"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Odkaz na rozhraní API pro vyhledávání v místní chod služby Bing u 7

Rozhraní API pro vyhledávání místních firem odesílá vyhledávací dotaz do bingu, aby získalo výsledky, které zahrnují restaurace, hotely nebo jiné místní firmy. U míst může dotaz zadat název místní firmy nebo kategorie (například restaurace v mém okolí). Mezi výsledky entit patří osoby, místa nebo věci. V této souvislosti jsou podnikatelské subjekty, státy, země/oblasti atd.  

Tato část obsahuje technické podrobnosti o objektech odpovědí a parametrech dotazu a záhlavích, které ovlivňují výsledky hledání. Příklady, které ukazují, jak provádět požadavky, naleznete [v tématu Místní firma Hledat C # rychlý start](quickstarts/local-quickstart.md) nebo Místní firma Hledat Java rychlý [start](quickstarts/local-search-java-quickstart.md). 
  
Informace o záhlavích, které by měly obsahovat požadavky, naleznete v [tématu Záhlaví](#headers).  
  
Informace o parametrech dotazu, které by požadavky měly obsahovat, naleznete v [tématu Parametry dotazu](#query-parameters).  
  
Informace o objektech JSON, které odpověď obsahuje, naleznete v tématu [Response objects](#response-objects).

Informace o povoleném použití a zobrazení výsledků naleznete v tématu [Požadavky na použití a zobrazení](use-display-requirements.md).


  
## <a name="endpoint"></a>Koncový bod  
Chcete-li požádat o výsledky místní firmy, odešlete požadavek GET na adrese: 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
Požadavek musí používat protokol HTTPS.  
  
> [!NOTE]
> Maximální délka adresy URL je 2 048 znaků. Chcete-li zajistit, aby délka adresy URL nepřekročila limit, měla by být maximální délka parametrů dotazu menší než 1 500 znaků. Pokud adresa URL překročí 2 048 znaků, vrátí server 404 Nebyl nalezen.  
  
  
## <a name="headers"></a>Hlavičky  
Následují záhlaví, které může požadavek a odpověď obsahovat.  
  
|Hlavička|Popis|  
|------------|-----------------|  
|Accept|Nepovinná hlavička požadavku.<br /><br /> Výchozí typ média je application/json. Chcete-li určit, že odpověď používá [JSON-LD](https://json-ld.org/), nastavte hlavičku Accept na aplikaci/ld+json.|  
|<a name="acceptlanguage" />Accept-Language|Nepovinná hlavička požadavku.<br /><br /> Čárkami oddělený seznam jazyků pro řetězce uživatelského rozhraní. Seznam je v sestupném pořadí podle priority. Další informace včetně očekávaného formátu najdete v [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Tato hlavička a parametr dotazu [setLang](#setlang) se vzájemně vylučují &mdash; nezadávejte obojí.<br /><br /> Pokud tuto hlavičku nastavíte, musíte zadat také parametr dotazu cc. K určení trhu, pro který se mají vracet výsledky, Bing použije první podporovaný jazyk, který najde v seznamu, a zkombinuje ho s hodnotou parametru `cc`. Pokud seznam jazyků podporovaný jazyk neobsahuje, Bing najde nejbližší jazyk a trh, který požadavek podporuje, nebo pro výsledky použije agregovaný nebo výchozí trh. Pokud chcete zjistit, který trh Bing použil, podívejte se do hlavičky BingAPIs-Market.<br /><br /> Tuto hlavičku a parametr dotazu `cc` použijte jenom v případě, že zadáte více jazyků. Jinak použijte parametry dotazu [mkt](#mkt) a [setLang](#setlang).<br /><br /> Řetězec uživatelského rozhraní je řetězec, který se používá jako popisek v uživatelském rozhraní. V objektech odpovědí JSON je několik řetězců uživatelského rozhraní. Zadaný jazyk použijí všechny odkazy na vlastnosti Bing.com v objektech odpovědi.|  
|<a name="market" />BingAPIs-Market|Hlavička odpovědi.<br /><br /> Trh používaný požadavkem. Forma je \<kódJazyka\>-\<kódZemě\>. Například cs-CZ.|  
|<a name="traceid" />BingAPIs-TraceId|Hlavička odpovědi.<br /><br /> ID položky protokolu obsahující podrobnosti požadavku. Pokud dojde k chybě, toto ID zachyťte. Pokud problém nedokážete určit a vyřešit, uveďte toto ID spolu s dalšími informacemi, které poskytnete týmu podpory.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Povinná hlavička požadavku.<br /><br /> Klíč předplatného, který jste dostali při registraci k této službě v [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Nepovinná hlavička požadavku<br /><br /> Ve výchozím nastavení Bing vrátí obsah uložený v mezipaměti, pokud je k dispozici. Abyste Bingu zabránili ve vrácení obsahu uloženého v mezipaměti, hlavičku Pragma nastavte na hodnotu no-cache (například Pragma: no-cache).
|<a name="useragent" />User-Agent|Nepovinná hlavička požadavku.<br /><br /> Uživatelský agent, ze kterého požadavek pochází. Bing používá uživatelského agenta k poskytnutí optimalizovaného prostředí pro mobilní uživatele. I když je tato hlavička nepovinná, doporučujeme ji vždy zadat.<br /><br /> Uživatelský agent by měl být stejný řetězec, který odesílá kterýkoli běžně používaný prohlížeč. Informace o uživatelských agentech najdete v [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Následují příklady řetězců uživatelského agenta.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Nepovinná hlavička požadavku a odpovědi<br /><br /> Bing tuto hlavičku používá k tomu, aby uživatelům poskytoval konzistentní chování napříč voláními rozhraní API Bingu. Bing často testuje nové funkce a vylepšení a ID klienta používá jako klíč pro přiřazení provozu v různých testovacích verzích. Pokud nepoužíváte stejné ID klienta pro uživatele napříč více požadavky, pak může Bing uživatele přiřadit k více konfliktním testovacím verzím. Přiřazení k více konfliktním testovacím verzím může vést k nekonzistentnímu prostředí pro uživatele. Pokud třeba druhý požadavek má jiné přiřazení k testovací verzi než prví, může být prostředí neočekávané. Bing také může ID klienta použít pro přizpůsobení webových výsledků historii hledání daného ID klienta. Uživatel tak získá bohatší prostředí.<br /><br /> Bing také tuto hlavičku používá ke zlepšování hodnocení výsledků díky analýze aktivity generované tímto ID klienta. Zlepšení relevance pomáhá zlepšit kvalitu výsledků poskytovaných rozhraním API Bingu a stejně tak umožňuje vyšší míru prokliků uživatele rozhraní API.<br /><br /> **DŮLEŽITÉ:** I když je tato hlavička nepovinná, měli byste ji považovat za povinnou. Zachování ID klienta mezi více požadavky pro stejnou kombinaci koncového uživatele a zařízení umožňuje 1) aby uživatel rozhraní API získával konzistentní uživatelské prostředí a 2) vyšší míry prokliků díky kvalitnějším výsledkům z rozhraní API Bingu.<br /><br /> Následují základní pravidla používání, která se na tuto hlavičku vztahují.<br /><ul><li>Každý uživatel, který používá vaši aplikaci na zařízení, musí mít jedinečné ID klienta vygenerované Bingem.<br /><br/>Pokud tuto hlavičku do požadavku nezahrnete, Bing vygeneruje ID a vrátí ho v hlavičce odpovědi X-MSEdge ClientID. Tuto hlavičku byste v požadavku zahrnout NEMĚLI jenom v případě, kdy uživatel aplikaci na zařízení používá poprvé.<br /><br/></li><li>Použijte ID klienta pro každý požadavek rozhraní API Bingu, který vaše aplikace provede pro tohoto uživatele na zařízení.<br /><br/></li><li>**POZOR:** Musíte zajistit, aby toto ID klienta nebylo možné propojit s žádnými autentickými informacemi o uživatelském účtu.</li><br/><li>ID klienta zachovejte. Pokud chcete zachovat ID v aplikaci prohlížeče, použijte trvalý soubor cookie HTTP, aby se zaručilo, že se ID použije ve všech relacích. Nepoužívejte soubor cookie relace. Pro jiné aplikace, jako jsou mobilní aplikace, použijte k zachování ID trvalé úložiště zařízení.<br /><br/>Když uživatel aplikaci na zařízení příště použije, získejte ID klienta, které jste zachovali.</li></ul><br /> **POZNÁMKA:** Odpovědi Bingu tuto hlavičku mohou nebo nemusí obsahovat. Pokud odpověď tuto hlavičku obsahuje, ID klienta zachyťte a použijte pro všechny následné požadavky Bingu pro uživatele na tomto zařízení.<br /><br /> **POZNÁMKA:** Pokud zahrnete X-MSEdge-ClientID, nesmíte do požadavku zahrnout soubory cookie.|  
|<a name="clientip" />X-MSEdge-ClientIP|Nepovinná hlavička požadavku.<br /><br /> Adresa IPv4 nebo IPv6 klientského zařízení. IP adresa se používá ke zjištění polohy uživatele. Bing informace o poloze používá k určení chování bezpečného hledání.<br /><br /> **POZNÁMKA:** I když je tato hlavička nepovinná, doporučujeme ji vždy zadat, stejně jako hlavičku X-Search-Location.<br /><br /> Neprovádějte obfuskaci adresy (například změnou posledního oktetu na 0). Obfuskace adresy vede k tomu, že poloha nebude blízko skutečné polohy zařízení. Bing pak může dodávat chybné výsledky.|  
|<a name="location" />X-Search-Location|Nepovinná hlavička požadavku.<br /><br /> Středníky oddělený seznam párů klíč/hodnota, které popisují zeměpisnou polohu klienta. Bing informace o poloze používá k určení chování bezpečného hledání a vracení relevantního místního obsahu. Pár klíč/hodnota zadejte jako \<klíč\>:\<hodnota\>. Následují klíče, které se používají k určení polohy uživatele.<br /><br /><ul><li>lat&mdash;Zeměpisná šířka polohy klienta ve stupních. Zeměpisná šířka musí být větší nebo rovná -90,0 a menší nebo rovná +90,0. Záporné hodnoty značí jižní šířku a kladné hodnoty značí severní šířku.<br /><br /></li><li>dlouhá&mdash;zeměpisná šířka umístění klienta ve stupních. Zeměpisná délka musí být větší nebo rovná -180,0 a menší nebo rovná +180,0. Záporné hodnoty značí západní délku a kladné hodnoty značí východní délku.<br /><br /></li><li>re&mdash; Poloměr v metrech, který určuje horizontální přesnost souřadnic. Předejte hodnotu vrácenou službou zjišťování polohy zařízení. Typické hodnoty můžou být 22 m pro GPS/Wi-Fi, 380 m pro triangulaci mobilních vysílačů a 18 000 m pro reverzní vyhledávání IP adresy.<br /><br /></li><li>ts&mdash; Časové razítko UTC UNIX o tom, kdy byl klient v místě. (Časové razítko UNIX je počet sekund od 1. ledna 1970.)<br /><br /></li><li>head &mdash; Nepovinné. Relativní směr pohybu klienta. Zadejte směr pohybu ve stupních od 0 do 360 ve směru hodinových ručiček vzhledem k severu. Tento klíč zadejte jenom tehdy, když je klíč `sp` nenulový.<br /><br /></li><li>sp&mdash; Horizontální rychlost (rychlost) v metrech za sekundu, po které klientské zařízení cestuje.<br /><br /></li><li>alt&mdash; Nadmořská výška klientského zařízení v metrech.<br /><br /></li><li>are &mdash; Nepovinné. Poloměr v metrech, který určuje svislou přesnost souřadnic. Poloměr výchozí 50 kilometrů. Tento klíč zadejte jenom tehdy, když zadáte klíč `alt`.<br /><br /></li></ul> **POZNÁMKA:** I když jsou tyto klíče volitelné, čím více informací poskytnete, tím přesnější jsou výsledky umístění.<br /><br /> **POZNÁMKA:** Doporučujeme vždy zadat zeměpisnou polohu uživatele. Poskytnutí polohy je zvlášť důležité, pokud IP adresa klienta přesně neodráží fyzickou polohu uživatele (třeba pokud klient používá síť VPN). Pro dosažení optimálních výsledků byste měli zahrnout tuto hlavičku i hlavičku X-MSEdge ClientIP, minimálně ale aspoň tuto hlavičku.|

> [!NOTE] 
> Nezapomínejte, že podmínky použití vyžadují dodržování příslušných zákonů, včetně těch týkajících se použití těchto hlaviček. Například v určitých jurisdikcích, třeba v Evropě, se před umístěním určitých sledovacích zařízení do zařízení uživatelů požaduje získání souhlasu uživatele.
  

## <a name="query-parameters"></a>Parametry dotazu  
Požadavek může obsahovat následující parametry dotazu. Požadované parametry naleznete ve sloupci Povinné. Parametry dotazu je nutné kódovat adresy URL.  
  
  
|Name (Název)|Hodnota|Typ|Požaduje se|  
|----------|-----------|----------|--------------|
|<a name="count" />count|Počet výsledků, které mají být vráceny, `offset` počínaje indexem určeným parametrem.|Řetězec|Ne|   
|<a name="localCategories" />localCategories|Seznam možností, které definují vyhledávání podle obchodní kategorie.  Viz [Hledání místních obchodních kategorií](local-categories.md)|Řetězec|Ne|  
|<a name="mkt" />mkt|Trh, odkud pochází výsledky. <br /><br />Seznam možných tržních hodnot naleznete v tématu Kódy trhu.<br /><br /> **POZNÁMKA:** Rozhraní LOCAL Business Search API v současné době podporuje pouze en-us trh a jazyk.<br /><br />|Řetězec|Ano|
|<a name="offset"/>Posun|Index pro spuštění výsledků `count` určených parametrem.|Integer|Ne|  
|<a name="query" />Q|Hledaný výraz uživatele.|Řetězec|Ne|  
|<a name="responseformat" />responseFormat|Typ média, který má být pro odpověď používán. Následují možné hodnoty bez rozlišování velkých a malých písmen.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Výchozí hodnota je JSON. Informace o objektech JSON, které odpověď obsahuje, naleznete v [tématu Response Objects](#response-objects).<br /><br />  Pokud zadáte JsonLd, tělo odpovědi obsahuje objekty JSON-LD, které obsahují výsledky hledání. Informace o JSON-LD naleznete v tématu [JSON-LD](https://json-ld.org/).|Řetězec|Ne|  
|<a name="safesearch" />safeSearch|Filtr, který slouží k filtrování obsahu pro dospělé. Následují možné hodnoty filtru s rozlišováním velkých a malých písmen.<br /><ul><li>Off&mdash;Vrátit webové stránky s textem pro dospělé, obrázky nebo videa.<br /><br/></li><li>Moderovat&mdash;návrat webové stránky s textem pro dospělé, ale ne obrázky nebo videa pro dospělé.<br /><br/></li><li>Strict&mdash;Nevracet webové stránky s textem pro dospělé, obrázky nebo videa.</li></ul><br /> Výchozí hodnota je Moderate.<br /><br /> **POZNÁMKA:** Pokud požadavek pochází z trhu, který vyžaduje `safeSearch` zásady pro dospělé Bingu, který je nastaven na strict, Bing ignoruje hodnotu `safeSearch` a používá Strict.<br/><br/>**POZNÁMKA:** Pokud použijete operátor dotazu `site:`, je možné, že odpověď bude zahrnovat obsah pro dospělé bez ohledu na nastavení parametru dotazu `safeSearch`. `site:` použijte jenom v případě, že znáte obsah příslušného webu a váš scénář podporuje možnost zobrazení obsahu pro dospělé. |Řetězec|Ne|  
|<a name="setlang" />setLang|Jazyk, který se má použít pro řetězce uživatelského rozhraní. Jazyk zadejte pomocí dvoupísmenného kódu jazyka ISO 639-1. Například kód jazyka pro češtinu je CS. Výchozí hodnota je EN (angličtina).<br /><br /> I když je jazyk volitelný, měli byste ho vždy zadat. Obvykle se `setLang` nastavuje na stejný jazyk, jaký určuje parametr `mkt`, pokud uživatel nechce řetězce uživatelského rozhraní zobrazené v jiném jazyce.<br /><br /> Tento parametr a hlavička [Accept-Language](#acceptlanguage) se vzájemně vylučují &mdash; nezadávejte obojí.<br /><br /> Řetězec uživatelského rozhraní je řetězec, který se používá jako popisek v uživatelském rozhraní. V objektech odpovědí JSON je několik řetězců uživatelského rozhraní. Zadaný jazyk použijí také všechny odkazy na vlastnosti Bing.com v objektech odpovědi.|Řetězec|Ne| 


## <a name="response-objects"></a>Objekty odpovědí  
Následují objekty odpovědi JSON, které může odpověď obsahovat. Pokud je požadavek úspěšný, objekt nejvyšší úrovně v odpovědi je [Objekt SearchResponse.](#searchresponse) Pokud se požadavek nezdaří, objekt nejvyšší úrovně je objekt [ErrorResponse.](#errorresponse)


|Objekt|Popis|  
|------------|-----------------|  
|[Místo](#place)|Definuje informace o místní firmě, jako je restaurace nebo hotel.|  

  
### <a name="error"></a>Chyba  
Definuje chybu, ke které došlo.  
  
|Element|Popis|Typ|  
|-------------|-----------------|----------|  
|<a name="error-code" />Kód|Kód chyby, který identifikuje kategorii chyby. Seznam možných kódů naleznete v [tématu Chybové kódy](#error-codes).|Řetězec|  
|<a name="error-message" />Zprávu|Popis chyby.|Řetězec|  
|<a name="error-moredetails" />vícePodrobnosti|Popis, který poskytuje další informace o chybě.|Řetězec|  
|<a name="error-parameter" />Parametr|Parametr dotazu v požadavku, který způsobil chybu.|Řetězec|  
|<a name="error-subcode" />subkód|Kód chyby, který identifikuje chybu. Například pokud `code` je InvalidRequest, `subCode` může být ParametrInvalid nebo ParameterInvalidValue. |Řetězec|  
|<a name="error-value" />value|Hodnota parametru dotazu, která nebyla platná.|Řetězec|  
  

### <a name="errorresponse"></a>Chybová odpověď  
Objekt nejvyšší úrovně, který odpověď obsahuje při selhání požadavku.  
  
|Name (Název)|Hodnota|Typ|  
|----------|-----------|----------|  
|_type|Nápověda k typu.|Řetězec|  
|<a name="errors" />Chyby|Seznam chyb, které popisují důvody, proč se požadavek nezdařil.|[Chyba](#error)[]|  

  
  
### <a name="license"></a>Licence  
Definuje licenci, pod kterou může být text nebo fotografie použita.  
  
|Name (Název)|Hodnota|Typ|  
|----------|-----------|----------|  
|jméno|Název licence.|Řetězec|  
|url|Adresa URL webu, kde může uživatel získat další informace o licenci.<br /><br /> Pomocí názvu a adresy URL vytvořte hypertextový odkaz.|Řetězec|  


### <a name="link"></a>Odkaz  
Definuje součásti hypertextového odkazu.  
  
|Name (Název)|Hodnota|Typ|  
|----------|-----------|----------|  
|_type|Nápověda k typu.|Řetězec|  
|text|Text zobrazení.|Řetězec|  
|url|Adresa URL. Pomocí adresy URL a zobrazeného textu vytvořte hypertextový odkaz.|Řetězec|  
  


  
### <a name="organization"></a>Organizace  
Definuje vydavatele.  
  
Upozorňujeme, že vydavatel může uvést své jméno, web nebo obojí.  
  
|Name (Název)|Hodnota|Typ|  
|----------|-----------|----------|  
|jméno|Jméno vydavatele.|Řetězec|  
|url|Adresa URL webu vydavatele.<br /><br /> Upozorňujeme, že vydavatel nemusí poskytovat web.|Řetězec|  
  
  

### <a name="place"></a>Místo  
Definuje informace o místní firmě, například o restauraci nebo hotelu.  
  
|Name (Název)|Hodnota|Typ|  
|----------|-----------|----------|  
|_type|Nápověda k typu, která může být nastavena na jednu z následujících možností:<br /><br /><ul><li>Hotel</li><li>Místní podnikání<br /></li><li>Restaurace</ul><li>|Řetězec|  
|adresa|Poštovní adresa místa, kde se účetní jednotka nachází.|Poštovní adresa|  
|entityPresentationInfo|Další informace o entitě, jako jsou rady, které můžete použít k určení typu entity. Například, ať už je to restaurace nebo hotel. Pole `entityScenario` je nastaveno na Položku list.|EntityPresentationInfo|  
|jméno|Název entity.|Řetězec|  
|Telefonní|Telefonní číslo subjektu.|Řetězec|  
|url|Adresa URL webu entity.<br /><br /> Pomocí této adresy URL spolu s názvem entity vytvořte hypertextový odkaz, který po kliknutí přenese uživatele na web entity.|Řetězec|  
|webSearchUrl|Adresa URL výsledku vyhledávání bingu pro toto místo.|Řetězec| 
  
  
### <a name="querycontext"></a>Kontext dotazu  
Definuje kontext dotazu, který bing použil pro požadavek.  
  
|Element|Popis|Typ|  
|-------------|-----------------|----------|  
|adultIntent|Logická hodnota, která označuje, zda zadaný dotaz má záměr dospělého. Hodnota je **true,** pokud dotaz má záměr dospělého; jinak **false**.|Logická hodnota|  
|změnaOverrideQuery|Řetězec dotazu použít k vynucení Bing použít původní řetězec. Pokud se například řetězec dotazu *po větru zmenšuje*, bude řetězec přepsání dotazu *+saling downwind*. Nezapomeňte zakódovat řetězec dotazu, jehož výsledkem je *%2Bsaling+downwind*.<br /><br /> Toto pole je zahrnuto pouze v případě, že původní řetězec dotazu obsahuje pravopisnou chybu.|Řetězec|  
|alteredQuery|Řetězec dotazu používaný bingem k provedení dotazu. Bing používá změněný řetězec dotazu, pokud původní řetězec dotazu obsahoval pravopisné chyby. Pokud je `saling downwind`například řetězec dotazu , bude `sailing downwind`změněný řetězec dotazu .<br /><br /> Toto pole je zahrnuto pouze v případě, že původní řetězec dotazu obsahuje pravopisnou chybu.|Řetězec|  
|askUserForLocation|Logická hodnota, která označuje, zda Bing vyžaduje umístění uživatele poskytnout přesné výsledky. Pokud jste zadali umístění uživatele pomocí hlaviček [X-MSEdge-ClientIP](#clientip) a [X-Search-Location,](#location) můžete toto pole ignorovat.<br /><br /> U dotazů s vědomím umístění, například "dnešní počasí" nebo "restaurace v mém okolí", které potřebují umístění uživatele poskytnout přesné výsledky, toto pole je nastavena na **hodnotu true**.<br /><br /> U dotazů s informacemi o umístění, které obsahují umístění (například "Počasí v Seattlu"), je toto pole nastaveno na **hodnotu false**. Toto pole je také nastaveno na **hodnotu false** pro dotazy, které nejsou informovány o umístění, například "nejprodávanější".|Logická hodnota|  
|originalQuery|Řetězec dotazu, jak je zadán v požadavku.|Řetězec|  

### <a name="identifiable"></a>Identifikovatelné

|Name (Název)|Hodnota|Typ|  
|-------------|-----------------|----------|
|id|Identifikátor prostředku|Řetězec|
 
### <a name="rankinggroup"></a>Skupina hodnocení
Definuje skupinu výsledků hledání, například hlavní linku.

|Name (Název)|Hodnota|Typ|  
|-------------|-----------------|----------|
|Položky|Seznam výsledků hledání, které se mají zobrazit ve skupině.|Položka hodnocení|

### <a name="rankingitem"></a>Položka hodnocení
Definuje položku výsledku hledání, která se má zobrazit.

|Name (Název)|Hodnota|Typ|  
|-------------|-----------------|----------|
|resultIndex|Nulový index položky v odpovědi k zobrazení. Pokud položka toto pole neobsahuje, zobrazte všechny položky v odpovědi. Můžete například zobrazit všechny zpravodajské články v odpovědi na zprávy.|Integer|
|type odpovědi|Odpověď, která obsahuje položku, která má být zobrazena. Například Novinky.<br /><br />Pomocí typu vyhledejte odpověď v objektu SearchResponse. Typ je název pole SearchResponse.<br /><br /> Typ odpovědi však použijte pouze v případě, že tento objekt obsahuje pole hodnoty. v opačném případě jej ignorujte.|Řetězec|
|textalIndex|Index odpovědi v textalAnswers zobrazit.| Nepodepsané celé číslo|
|value|ID, které identifikuje buď odpověď k zobrazení, nebo položku odpovědi k zobrazení. Pokud ID identifikuje odpověď, zobrazte všechny položky odpovědi.|Identifikovatelné|

### <a name="rankingresponse"></a>RankingResponse  
Definuje, kde na stránce s výsledky hledání má být obsah stránky umístěn a v jakém pořadí.  
  
|Name (Název)|Hodnota|  
|----------|-----------|  
|<a name="ranking-mainline" />hlavní trať|Výsledky hledání, které se mají zobrazit na hlavní spojce.|  
|<a name="ranking-pole" />Pól|Výsledky vyhledávání, které by měly být poskytnuty nejviditelnější ošetření (například zobrazeno nad hlavní a postranní panel).|  
|<a name="ranking-sidebar" />Sidebar|Výsledky hledání, které se mají zobrazit na postranním panelu.| 

### <a name="searchresponse"></a>SearchResponse  
Definuje objekt nejvyšší úrovně, který odpověď obsahuje, když je požadavek úspěšný.  
  
Všimněte si, že pokud služba má podezření na útok odmítnutí služby, požadavek bude úspěšný (stavový kód HTTP je 200 OK); tělo odpovědi však bude prázdné.  
  
|Name (Název)|Hodnota|Typ|  
|----------|-----------|----------|  
|_type|Nápověda k typu, která je nastavena na SearchResponse.|Řetězec|  
|Místa|Seznam entit, které jsou relevantní pro vyhledávací dotaz.|Objekt JSON|  
|queryContext|Objekt, který obsahuje řetězec dotazu, který bing použil pro požadavek.<br /><br /> Tento objekt obsahuje řetězec dotazu zadaný uživatelem. Může také obsahovat změněný řetězec dotazu, který Bing použil pro dotaz, pokud řetězec dotazu obsahoval pravopisnou chybu.|[Kontext dotazu](#querycontext)|  


## <a name="error-codes"></a>Kódy chyb

Následují možné stavové kódy HTTP, které požadavek vrátí.  
  
|Stavový kód|Popis|  
|-----------------|-----------------|  
|200|Úspěch|  
|400|Jeden z parametrů dotazu chybí nebo je neplatný.|  
|401|Klíč předplatného chybí nebo není platný.|  
|403|Uživatel je ověřen (například použili platný klíč předplatného), ale nemají oprávnění k požadovanému prostředku.<br /><br /> Bing může také vrátit tento stav, pokud volající překročil své dotazy za měsíc kvótu.|  
|410|Požadavek používá protokol HTTP namísto protokolu HTTPS. Protokol HTTPS je jediný podporovaný protokol.|  
|429|Volající překročil svou kvótu dotazů za sekundu.|  
|500|Neočekávaná chyba serveru.|

Pokud se požadavek nezdaří, odpověď obsahuje objekt [ErrorResponse,](#errorresponse) který obsahuje seznam objektů [Error,](#error) které popisují, co způsobilo chybu. Pokud chyba souvisí s parametrem, `parameter` pole identifikuje parametr, který je problém. A pokud chyba souvisí s hodnotou parametru, `value` pole identifikuje hodnotu, která není platná.

```json
{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidRequest", 
      "subCode": "ParameterMissing", 
      "message": "Required parameter is missing.", 
      "parameter": "q" 
    }
  ]
}

{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidAuthorization", 
      "subCode": "AuthorizationMissing", 
      "message": "Authorization is required.", 
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

Následují možné kódy chyb a hodnoty kódu dílčí chyby.

|kód|Dílčí kód|Popis
|-|-|-
|Chyba serveru|Neočekávaná chyba<br/>Chyba zdroje<br/>Není implementováno|Stavový kód HTTP je 500.
|Neplatný požadavek|ParametrMissing ParametrMissing ParametrMissing ParametrMissing<br/>ParametrInvalidValue<br/>HttpNotAllowed<br/>Blokované|Bing vrátí InvalidRequest vždy, když žádná část požadavku není platná. Například chybí požadovaný parametr nebo hodnota parametru není platná.<br/><br/>Pokud je chyba ParametrMissing nebo ParameterInvalidValue, je stavový kód HTTP 400.<br/><br/>Pokud použijete protokol HTTP místo HTTPS, Bing vrátí HttpNotAllowed a stavový kód HTTP je 410.
|RateLimit překročena|Žádné podkódy|Bing vrátí RateLimitExceeded vždy, když překročíte vaše dotazy za sekundu (QPS) nebo dotazy za měsíc (QPM) kvóta.<br/><br/>Pokud překročíte QPS, Bing vrátí stavový kód HTTP 429 a pokud překročíte QPM, Bing vrátí 403.
|Neplatná autorizace|AutorizaceChybí<br/>Redundance autorizace|Bing vrátí InvalidAuthorization, když Bing nemůže ověřit volajícího. Například `Ocp-Apim-Subscription-Key` záhlaví chybí nebo klíč předplatného není platný.<br/><br/>K redundanci dochází, pokud zadáte více než jednu metodu ověřování.<br/><br/>Pokud je chyba InvalidAuthorization, stavový kód HTTP je 401.
|Nedostatečná autorizace|Autorizace zakázána.<br/>Platnost oprávnění vypršela.|Bing vrátí InsufficientAuthorization, pokud volající nemá oprávnění k přístupu k prostředku. Tato situace může nastat, pokud byl zakázán klíč předplatného nebo vypršela jeho platnost. <br/><br/>Pokud je chyba InsufficientAuthorization, stavový kód HTTP je 403.

## <a name="next-steps"></a>Další kroky
- [Rychlý start hledání místní firmy](quickstarts/local-quickstart.md)
- [Rychlý start java vyhledávání místních společností](quickstarts/local-search-java-quickstart.md)
- [Rychlý start uzlu místního obchodního hledání](quickstarts/local-search-node-quickstart.md)
- [Rychlý start pythonu pro místní obchodní vyhledávání](quickstarts/local-search-python-quickstart.md)
