---
title: Referenční informace o rozhraní API pro vyhledávání místních obchodních v7 Bingu
titleSuffix: Azure Cognitive Services
description: Tento článek poskytuje technické podrobnosti o objektech odpovědi a parametry dotazu a záhlaví, které mají vliv na výsledky hledání.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: dbd4f32e77dc8d386067987a0ab0436a7875c15e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095386"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Referenční informace o rozhraní API pro vyhledávání místních obchodních v7 Bingu

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Rozhraní API pro místní vyhledávání v obchodu pošle vyhledávací dotaz do Bingu a získá výsledky, které zahrnují restaurace, hotely nebo jiné místní firmy. V případě míst může dotaz zadat název místní firmy nebo kategorii (například restaurace v blízkosti mě). Mezi výsledky entit patří osoby, místa nebo věci. Místo v tomto kontextu patří obchodní entity, státy, země/oblasti atd.  

Tato část poskytuje technické podrobnosti o objektech odpovědi a parametry dotazu a záhlaví, které mají vliv na výsledky hledání. Příklady, které ukazují, jak vytvářet požadavky, najdete v článku [rychlý Start pro místní vyhledávání v jazyce C#](quickstarts/local-quickstart.md) nebo rychlý [Start pro místní vyhledávání v jazyce Java](quickstarts/local-search-java-quickstart.md). 
  
Informace o hlavičkách, které by měly požadavky zahrnovat, najdete v části [Headers](#headers).  
  
Informace o parametrech dotazu, které by měly požadavky zahrnovat, najdete v tématu [parametry dotazu](#query-parameters).  
  
Informace o objektech JSON, které odpověď obsahuje, naleznete v tématu [objekty odpovědi](#response-objects).

Informace o povoleném použití a zobrazení výsledků najdete v tématu [použití a zobrazení požadavků](use-display-requirements.md).


  
## <a name="endpoint"></a>Koncový bod  
Pokud chcete požádat o výsledky místního podnikání, odešlete požadavek GET na: 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
Tento požadavek musí používat protokol HTTPS.  
  
> [!NOTE]
> Maximální délka adresy URL je 2 048 znaků. Aby se zajistilo, že délka vaší adresy URL nepřekračuje limit, maximální délka parametrů dotazu by měla být kratší než 1 500 znaků. Pokud adresa URL překračuje 2 048 znaků, server nenajde hodnotu 404.  
  
  
## <a name="headers"></a>Hlavičky  
Následují hlavičky, které může obsahovat požadavek a odpověď.  
  
|Záhlaví|Description|  
|------------|-----------------|  
|Přijmout|Nepovinná hlavička požadavku.<br /><br /> Výchozí typ média je Application/JSON. Chcete-li určit, že odpověď používá [JSON-ld](https://json-ld.org/), nastavte hlavičku Accept na Application/ld + JSON.|  
|<a name="acceptlanguage"></a>Accept-Language|Nepovinná hlavička požadavku.<br /><br /> Čárkami oddělený seznam jazyků pro řetězce uživatelského rozhraní. Seznam je v sestupném pořadí podle priority. Další informace včetně očekávaného formátu najdete v [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Tato hlavička a parametr dotazu [setLang](#setlang) se vzájemně vylučují &mdash; nezadávejte obojí.<br /><br /> Pokud tuto hlavičku nastavíte, musíte zadat také parametr dotazu cc. K určení trhu, pro který se mají vracet výsledky, Bing použije první podporovaný jazyk, který najde v seznamu, a zkombinuje ho s hodnotou parametru `cc`. Pokud seznam jazyků podporovaný jazyk neobsahuje, Bing najde nejbližší jazyk a trh, který požadavek podporuje, nebo pro výsledky použije agregovaný nebo výchozí trh. Pokud chcete zjistit, který trh Bing použil, podívejte se do hlavičky BingAPIs-Market.<br /><br /> Tuto hlavičku a parametr dotazu `cc` použijte jenom v případě, že zadáte více jazyků. Jinak použijte parametry dotazu [mkt](#mkt) a [setLang](#setlang).<br /><br /> Řetězec uživatelského rozhraní je řetězec, který se používá jako popisek v uživatelském rozhraní. V objektech odpovědí JSON je několik řetězců uživatelského rozhraní. Zadaný jazyk použijí všechny odkazy na vlastnosti Bing.com v objektech odpovědi.|  
|<a name="market"></a>BingAPIs-Market|Hlavička odpovědi.<br /><br /> Trh používaný požadavkem. Formulář je \<languageCode\> - \<countryCode\> . Například cs-CZ.|  
|<a name="traceid"></a>BingAPIs-TraceId|Hlavička odpovědi.<br /><br /> ID položky protokolu obsahující podrobnosti požadavku. Pokud dojde k chybě, toto ID zachyťte. Pokud problém nedokážete určit a vyřešit, uveďte toto ID spolu s dalšími informacemi, které poskytnete týmu podpory.|  
|<a name="subscriptionkey"></a>Ocp-Apim-Subscription-Key|Povinná hlavička požadavku.<br /><br /> Klíč předplatného, který jste dostali při registraci k této službě v [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma"></a>Pragma|Nepovinná hlavička požadavku<br /><br /> Ve výchozím nastavení Bing vrátí obsah uložený v mezipaměti, pokud je k dispozici. Abyste Bingu zabránili ve vrácení obsahu uloženého v mezipaměti, hlavičku Pragma nastavte na hodnotu no-cache (například Pragma: no-cache).
|<a name="useragent"></a>User-Agent|Nepovinná hlavička požadavku.<br /><br /> Uživatelský agent, ze kterého požadavek pochází. Bing používá uživatelského agenta k poskytnutí optimalizovaného prostředí pro mobilní uživatele. I když je tato hlavička nepovinná, doporučujeme ji vždy zadat.<br /><br /> Uživatelský agent by měl být stejný řetězec, který odesílá kterýkoli běžně používaný prohlížeč. Informace o uživatelských agentech najdete v [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Následují příklady řetězců uživatelského agenta.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid"></a>X-MSEdge-ClientID|Nepovinná hlavička požadavku a odpovědi<br /><br /> Bing tuto hlavičku používá k tomu, aby uživatelům poskytoval konzistentní chování napříč voláními rozhraní API Bingu. Bing často testuje nové funkce a vylepšení a ID klienta používá jako klíč pro přiřazení provozu v různých testovacích verzích. Pokud nepoužíváte stejné ID klienta pro uživatele napříč více požadavky, pak může Bing uživatele přiřadit k více konfliktním testovacím verzím. Přiřazení k více konfliktním testovacím verzím může vést k nekonzistentnímu prostředí pro uživatele. Pokud třeba druhý požadavek má jiné přiřazení k testovací verzi než prví, může být prostředí neočekávané. Bing také může ID klienta použít pro přizpůsobení webových výsledků historii hledání daného ID klienta. Uživatel tak získá bohatší prostředí.<br /><br /> Bing také tuto hlavičku používá ke zlepšování hodnocení výsledků díky analýze aktivity generované tímto ID klienta. Zlepšení relevance pomáhá zlepšit kvalitu výsledků poskytovaných rozhraním API Bingu a stejně tak umožňuje vyšší míru prokliků uživatele rozhraní API.<br /><br /> **DŮLEŽITÉ:** I když je tato hlavička nepovinná, měli byste ji považovat za povinnou. Zachování ID klienta mezi více požadavky pro stejnou kombinaci koncového uživatele a zařízení umožňuje 1) aby uživatel rozhraní API získával konzistentní uživatelské prostředí a 2) vyšší míry prokliků díky kvalitnějším výsledkům z rozhraní API Bingu.<br /><br /> Následují základní pravidla používání, která se na tuto hlavičku vztahují.<br /><ul><li>Každý uživatel, který používá vaši aplikaci na zařízení, musí mít jedinečné ID klienta vygenerované Bingem.<br /><br/>Pokud tuto hlavičku do požadavku nezahrnete, Bing vygeneruje ID a vrátí ho v hlavičce odpovědi X-MSEdge ClientID. Tuto hlavičku byste v požadavku zahrnout NEMĚLI jenom v případě, kdy uživatel aplikaci na zařízení používá poprvé.<br /><br/></li><li>Použijte ID klienta pro každý požadavek rozhraní API Bingu, který vaše aplikace provede pro tohoto uživatele na zařízení.<br /><br/></li><li>**Pozornost:** Je nutné zajistit, aby toto ID klienta nebylo propojeno s žádnými informacemi o ověřujícím uživatelském účtu.</li><br/><li>ID klienta zachovejte. Pokud chcete zachovat ID v aplikaci prohlížeče, použijte trvalý soubor cookie HTTP, aby se zaručilo, že se ID použije ve všech relacích. Nepoužívejte soubor cookie relace. Pro jiné aplikace, jako jsou mobilní aplikace, použijte k zachování ID trvalé úložiště zařízení.<br /><br/>Když uživatel aplikaci na zařízení příště použije, získejte ID klienta, které jste zachovali.</li></ul><br /> **POZNÁMKA:** Odpovědi Bingu tuto hlavičku mohou nebo nemusí obsahovat. Pokud odpověď tuto hlavičku obsahuje, ID klienta zachyťte a použijte pro všechny následné požadavky Bingu pro uživatele na tomto zařízení.<br /><br /> **POZNÁMKA:** Pokud zahrnete X-MSEdge-ClientID, nesmíte do požadavku zahrnout soubory cookie.|  
|<a name="clientip"></a>X-MSEdge-ClientIP|Nepovinná hlavička požadavku.<br /><br /> Adresa IPv4 nebo IPv6 klientského zařízení. IP adresa se používá ke zjištění polohy uživatele. Bing informace o poloze používá k určení chování bezpečného hledání.<br /><br /> **POZNÁMKA:** I když je tato hlavička nepovinná, doporučujeme ji vždy zadat, stejně jako hlavičku X-Search-Location.<br /><br /> Neprovádějte obfuskaci adresy (například změnou posledního oktetu na 0). Obfuskace adresy vede k tomu, že poloha nebude blízko skutečné polohy zařízení. Bing pak může dodávat chybné výsledky.|  
|<a name="location"></a>X-Search-Location|Nepovinná hlavička požadavku.<br /><br /> Středníky oddělený seznam párů klíč/hodnota, které popisují zeměpisnou polohu klienta. Bing informace o poloze používá k určení chování bezpečného hledání a vracení relevantního místního obsahu. Zadejte dvojici klíč/hodnota jako \<key\> : \<value\> . Následují klíče, které se používají k určení polohy uživatele.<br /><br /><ul><li>lat &mdash; Zeměpisná šířka umístění klienta ve stupních. Zeměpisná šířka musí být větší nebo rovná -90,0 a menší nebo rovná +90,0. Záporné hodnoty značí jižní šířku a kladné hodnoty značí severní šířku.<br /><br /></li><li>dlouhá &mdash; Zeměpisná délka umístění klienta ve stupních. Zeměpisná délka musí být větší nebo rovná -180,0 a menší nebo rovná +180,0. Záporné hodnoty značí západní délku a kladné hodnoty značí východní délku.<br /><br /></li><li>proveďte znovu &mdash; poloměr, v měřičích, který určuje vodorovnou přesnost souřadnic. Předejte hodnotu vrácenou službou zjišťování polohy zařízení. Typické hodnoty můžou být 22 m pro GPS/Wi-Fi, 380 m pro triangulaci mobilních vysílačů a 18 000 m pro reverzní vyhledávání IP adresy.<br /><br /></li><li>TS – &mdash; časové razítko systému UNIX pro čas, kdy byl klient v umístění. (Časové razítko UNIX je počet sekund od 1. ledna 1970.)<br /><br /></li><li>head &mdash; Nepovinné. Relativní směr pohybu klienta. Zadejte směr pohybu ve stupních od 0 do 360 ve směru hodinových ručiček vzhledem k severu. Tento klíč zadejte jenom tehdy, když je klíč `sp` nenulový.<br /><br /></li><li>SP &mdash; horizontální rychlost (rychlost) v metrech za sekundu, na kterou je klientské zařízení na cestách.<br /><br /></li><li>ALT + &mdash; výšce klientského zařízení v měřičích.<br /><br /></li><li>are &mdash; Nepovinné. Poloměr v metrech, který určuje svislou přesnost souřadnic. Výchozí poloměr je 50 kilometrů. Tento klíč zadejte jenom tehdy, když zadáte klíč `alt`.<br /><br /></li></ul> **Poznámka:** I když jsou tyto klíče volitelné, další informace, které poskytnete, jsou přesnější.<br /><br /> **Poznámka:** Doporučujeme vždy zadat zeměpisnou polohu uživatele. Poskytnutí polohy je zvlášť důležité, pokud IP adresa klienta přesně neodráží fyzickou polohu uživatele (třeba pokud klient používá síť VPN). Pro dosažení optimálních výsledků byste měli zahrnout tuto hlavičku i hlavičku X-MSEdge ClientIP, minimálně ale aspoň tuto hlavičku.|

> [!NOTE] 
> Nezapomínejte, že podmínky použití vyžadují dodržování příslušných zákonů, včetně těch týkajících se použití těchto hlaviček. Například v určitých jurisdikcích, třeba v Evropě, se před umístěním určitých sledovacích zařízení do zařízení uživatelů požaduje získání souhlasu uživatele.
  

## <a name="query-parameters"></a>Parametry dotazů  
Požadavek může obsahovat následující parametry dotazu. Požadované parametry najdete v požadovaném sloupci. Je nutné zadat adresu URL pro kódování parametrů dotazu.  
  
  
|Name|Hodnota|Typ|Vyžadováno|  
|----------|-----------|----------|--------------|
|<a name="count"></a>výpočtu|Počet výsledků, které se mají vrátit, počínaje indexem určeného `offset` parametrem.|Řetězec|No|   
|<a name="localCategories"></a>localCategories|Seznam možností definujících hledání podle obchodních kategorií.  Zobrazit [Hledání kategorií místních obchodů](local-categories.md)|Řetězec|No|  
|<a name="mkt"></a>mkt|Trh, odkud pochází výsledky. <br /><br />Seznam možných hodnot trhu najdete v tématu kódy trhů.<br /><br /> **Poznámka:** Rozhraní API pro místní vyhledávání v současnosti podporuje jenom trh a jazyk EN-US.<br /><br />|Řetězec|Yes|
|<a name="offset"></a>polohy|Index pro zahájení výsledků zadaných `count` parametrem.|Integer|No|  
|<a name="query"></a>č|Hledaný termín uživatele.|Řetězec|No|  
|<a name="responseformat"></a>responseFormat|Typ média, který se má použít pro odpověď Níže jsou uvedené možné hodnoty nerozlišující malá a velká písmena.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Výchozí hodnota je JSON. Informace o objektech JSON, které odpověď obsahuje, naleznete v tématu [objekty Response](#response-objects).<br /><br />  Pokud zadáte JsonLd, tělo odpovědi zahrnuje objekty JSON-LD, které obsahují výsledky hledání. Informace o formátu JSON-LD najdete v tématu [JSON-ld](https://json-ld.org/).|Řetězec|No|  
|<a name="safesearch"></a>safeSearch|Filtr, který slouží k filtrování obsahu pro dospělé. Následují možné hodnoty filtru s rozlišováním velkých a malých písmen.<br /><ul><li>Vypínejte &mdash; webové stránky s použitím textu, obrázků a videí pro dospělé.<br /><br/></li><li>Mírné &mdash; vrácení webových stránek s textem pro dospělé, ale ne pro dospělé obrázky nebo videa.<br /><br/></li><li>Strict &mdash; nevrací webové stránky s použitím textu, obrázků a videí pro dospělé.</li></ul><br /> Výchozí hodnota je Moderate.<br /><br /> **Poznámka:** Pokud žádost pochází z trhu, ve kterém zásada pro dospělé Bingu vyžaduje, aby `safeSearch` byla nastavená na Strict, Bing ignoruje tuto `safeSearch` hodnotu a použije Strict.<br/><br/>**POZNÁMKA:** Pokud použijete operátor dotazu `site:`, je možné, že odpověď bude zahrnovat obsah pro dospělé bez ohledu na nastavení parametru dotazu `safeSearch`. `site:` použijte jenom v případě, že znáte obsah příslušného webu a váš scénář podporuje možnost zobrazení obsahu pro dospělé. |Řetězec|No|  
|<a name="setlang"></a>setLang|Jazyk, který se má použít pro řetězce uživatelského rozhraní. Jazyk zadejte pomocí dvoupísmenného kódu jazyka ISO 639-1. Například kód jazyka pro češtinu je CS. Výchozí hodnota je EN (angličtina).<br /><br /> I když je jazyk volitelný, měli byste ho vždy zadat. Obvykle se `setLang` nastavuje na stejný jazyk, jaký určuje parametr `mkt`, pokud uživatel nechce řetězce uživatelského rozhraní zobrazené v jiném jazyce.<br /><br /> Tento parametr a hlavička [Accept-Language](#acceptlanguage) se vzájemně vylučují &mdash; nezadávejte obojí.<br /><br /> Řetězec uživatelského rozhraní je řetězec, který se používá jako popisek v uživatelském rozhraní. V objektech odpovědí JSON je několik řetězců uživatelského rozhraní. Zadaný jazyk použijí také všechny odkazy na vlastnosti Bing.com v objektech odpovědi.|Řetězec|No| 


## <a name="response-objects"></a>Objekty Response  
Níže jsou uvedené objekty odpovědi JSON, které může odpověď zahrnovat. Pokud je požadavek úspěšný, objekt nejvyšší úrovně v odpovědi je objekt [SearchResponse](#searchresponse) . Pokud se požadavek nezdařil, objekt nejvyšší úrovně je objekt [ErrorResponse](#errorresponse) .


|Objekt|Description|  
|------------|-----------------|  
|[Místa](#place)|Definuje informace o místních firmách, jako je restaurace nebo Hotel.|  

  
### <a name="error"></a>Chybová  
Definuje chybu, ke které došlo.  
  
|Element|Popis|Typ|  
|-------------|-----------------|----------|  
|<a name="error-code"></a>znakovou|Kód chyby, který identifikuje kategorii chyby. Seznam možných kódů naleznete v tématu [kódy chyb](#error-codes).|Řetězec|  
|<a name="error-message"></a>Zpráva|Popis chyby.|Řetězec|  
|<a name="error-moredetails"></a>moreDetails|Popis, který poskytuje další informace o chybě.|Řetězec|  
|<a name="error-parameter"></a>ukazatele|Parametr dotazu v žádosti, která způsobila chybu.|Řetězec|  
|<a name="error-subcode"></a>Podřízeného kódu|Kód chyby, který identifikuje chybu. Například pokud `code` je InvalidRequest, `subCode` může být ParameterInvalid nebo ParameterInvalidValue. |Řetězec|  
|<a name="error-value"></a>osa|Hodnota parametru dotazu, která není platná.|Řetězec|  
  

### <a name="errorresponse"></a>ErrorResponse  
Objekt nejvyšší úrovně, který odpověď zahrnuje v případě, že se požadavek nezdařil.  
  
|Name|Hodnota|Typ|  
|----------|-----------|----------|  
|_type|Zadejte pomocný parametr.|Řetězec|  
|<a name="errors"></a>vyskytl|Seznam chyb popisujících důvody, proč se žádost nezdařila.|[Chyba](#error)[]|  

  
  
### <a name="license"></a>Licence  
Definuje licenci, za kterou se dá text nebo fotka použít.  
  
|Name|Hodnota|Typ|  
|----------|-----------|----------|  
|name|Název licence.|Řetězec|  
|url|Adresa URL webu, kde může uživatel získat další informace o licenci.<br /><br /> K vytvoření hypertextového odkazu použijte název a adresu URL.|Řetězec|  


### <a name="link"></a>Odkaz  
Definuje součásti hypertextového odkazu.  
  
|Name|Hodnota|Typ|  
|----------|-----------|----------|  
|_type|Zadejte pomocný parametr.|Řetězec|  
|text|Zobrazený text|Řetězec|  
|url|ADRESA URL. Hypertextový odkaz vytvoříte pomocí adresy URL a zobrazovaného textu.|Řetězec|  
  


  
### <a name="organization"></a>Organizace  
Definuje vydavatele.  
  
Všimněte si, že Vydavatel může poskytnout svůj název nebo web nebo obojí.  
  
|Name|Hodnota|Typ|  
|----------|-----------|----------|  
|name|Název vydavatele|Řetězec|  
|url|Adresa URL webu vydavatele<br /><br /> Všimněte si, že vydavatel nemusí poskytovat Web.|Řetězec|  
  
  

### <a name="place"></a>Místo  
Definuje informace o místních firmách, jako je restaurace nebo Hotel.  
  
|Name|Hodnota|Typ|  
|----------|-----------|----------|  
|_type|Zadejte pomocný parametr typu, který může být nastaven na jednu z následujících možností:<br /><br /><ul><li>Hotel</li><li>LocalBusiness<br /></li><li>Restaurací</ul><li>|Řetězec|  
|adresa|Poštovní adresa místa, kde se entita nachází.|PostalAddress|  
|entityPresentationInfo|Další informace o entitě, jako jsou například tipy, které můžete použít k určení typu entity. Například bez ohledu na to, jestli se jedná o restaurace nebo Hotel. `entityScenario`Pole je nastaveno na ListItem.|EntityPresentationInfo|  
|name|Název entity|Řetězec|  
|Link|Telefonní číslo entity|Řetězec|  
|url|Adresa URL webu entity<br /><br /> Pomocí této adresy URL spolu s názvem entity můžete vytvořit hypertextový odkaz, který po kliknutí přijme uživatele na web entity.|Řetězec|  
|webSearchUrl|Adresa URL výsledků hledání Bingu pro toto místo|Řetězec| 
  
  
### <a name="querycontext"></a>QueryContext  
Definuje kontext dotazu, který Bing použil pro požadavek.  
  
|Element|Popis|Typ|  
|-------------|-----------------|----------|  
|adultIntent|Logická hodnota, která označuje, zda má zadaný dotaz záměr k dospělému. Hodnota je **true** , pokud má dotaz záměr k dospělému; v opačném případě **false** .|Logická hodnota|  
|alterationOverrideQuery|Řetězec dotazu, který se má použít k vynucení použití původního řetězce v Bingu. Například pokud je řetězec dotazu *Saling downwind* , řetězec přepisu řetězce dotazu bude *+ Saling downwind* . Nezapomeňte zakódovat řetězec dotazu, který má za následek *% 2Bsaling + downwind* .<br /><br /> Toto pole je zahrnuto pouze v případě, že původní řetězec dotazu obsahuje pravopisnou chybu.|Řetězec|  
|alteredQuery|Řetězec dotazu, který Bing používá k provedení dotazu. Bing používá změněný řetězec dotazu, pokud původní řetězec dotazu obsahoval pravopisné chyby. Například pokud je řetězec dotazu `saling downwind` , změněný řetězec dotazu bude `sailing downwind` .<br /><br /> Toto pole je zahrnuto pouze v případě, že původní řetězec dotazu obsahuje pravopisnou chybu.|Řetězec|  
|askUserForLocation|Logická hodnota, která určuje, zda Bing vyžaduje umístění uživatele, aby poskytovalo přesné výsledky. Pokud jste zadali umístění uživatele pomocí hlaviček [x-MSEdge-IP adresa klienta](#clientip) a [x-Search-Location](#location) , můžete toto pole ignorovat.<br /><br /> Pro dotazy na umístění, jako je "dnešní počasí" nebo "Restaurace v mém okolí", které potřebují umístění uživatele, aby poskytovaly přesné výsledky, je toto pole nastaveno na **hodnotu true** .<br /><br /> Pro dotazy na umístění, které zahrnují umístění (například "povětrnostní počasí"), je toto pole nastaveno na **hodnotu NEPRAVDA** . Toto pole je také nastaveno na **hodnotu false** u dotazů, které nejsou v umístění, například "nejlepší prodejci".|Logická hodnota|  
|originalQuery|Řetězec dotazu, jak je uveden v požadavku.|Řetězec|  

### <a name="identifiable"></a>Identifikovatelné údaje

|Name|Hodnota|Typ|  
|-------------|-----------------|----------|
|id|Identifikátor prostředku|Řetězec|
 
### <a name="rankinggroup"></a>Klasifikace
Definuje skupinu výsledků hledání, jako je například hlavní.

|Name|Hodnota|Typ|  
|-------------|-----------------|----------|
|položek|Seznam výsledků hledání, které se mají zobrazit ve skupině|RankingItem|

### <a name="rankingitem"></a>RankingItem
Definuje položku výsledku hledání, která se má zobrazit.

|Name|Hodnota|Typ|  
|-------------|-----------------|----------|
|resultIndex|Index položky vycházející z nuly v odpovědi, která se má zobrazit Pokud tato položka neobsahuje toto pole, zobrazí všechny položky v odpovědi. Můžete například zobrazit všechny články v odpovědi na zprávy.|Integer|
|answerType|Odpověď obsahující položku, která se má zobrazit Například zprávy.<br /><br />Pomocí typu vyhledejte odpověď v objektu SearchResponse. Typ je název pole SearchResponse.<br /><br /> Typ odpovědi však použijte pouze v případě, že tento objekt obsahuje pole hodnota. v opačném případě tuto chybu ignorujte.|Řetězec|
|textualIndex|Index odpovědi v textualAnswers, který se má zobrazit| Celé číslo bez znaménka|
|hodnota|ID, které identifikuje odpověď pro zobrazení nebo položku odpovědi, která se má zobrazit Pokud ID identifikuje odpověď, zobrazí všechny položky odpovědi.|Identifikovatelné údaje|

### <a name="rankingresponse"></a>RankingResponse  
Definuje, kde má být obsah stránky výsledků hledání umístěn a v jakém pořadí.  
  
|Name|Hodnota|  
|----------|-----------|  
|<a name="ranking-mainline"></a>hlavní|Výsledky hledání, které se mají zobrazit v hlavní|  
|<a name="ranking-pole"></a>prut|Výsledky hledání, které by měly být uváděny jako nejužitečnější způsob zpracování (například zobrazené nad hlavní a postranní panel).|  
|<a name="ranking-sidebar"></a>stín|Výsledky hledání, které se mají zobrazit na bočním panelu| 

### <a name="searchresponse"></a>SearchResponse  
Definuje objekt nejvyšší úrovně, který odpověď zahrnuje, když je požadavek úspěšný.  
  
Všimněte si, že pokud by služba způsobila útok DoS (Denial of Service), požadavek bude úspěšný (kód stavu HTTP je 200 OK); tělo odpovědi však bude prázdné.  
  
|Name|Hodnota|Typ|  
|----------|-----------|----------|  
|_type|Zadejte pomocný parametr typu, který je nastavený na SearchResponse.|Řetězec|  
|zadá|Seznam entit, které jsou relevantní pro vyhledávací dotaz.|Objekt JSON|  
|queryContext|Objekt, který obsahuje řetězec dotazu, který Bing použil pro požadavek.<br /><br /> Tento objekt obsahuje řetězec dotazu zadaný uživatelem. Může také obsahovat změněný řetězec dotazu, který Bing použil pro dotaz, pokud řetězec dotazu obsahoval pravopisnou chybu.|[QueryContext](#querycontext)|  


## <a name="error-codes"></a>Kódy chyb

Níže jsou uvedené možné stavové kódy HTTP, které požadavek vrátí.  
  
|Stavový kód|Description|  
|-----------------|-----------------|  
|200|Úspěch.|  
|400|Jeden z parametrů dotazu chybí nebo je neplatný.|  
|401|Klíč předplatného chybí nebo není platný.|  
|403|Uživatel je ověřený (například používá platný klíč předplatného), ale nemá oprávnění k požadovanému prostředku.<br /><br /> Bing může tento stav vrátit také v případě, že volající překročil kvótu na měsíc.|  
|410|Požadavek použil protokol HTTP místo protokolu HTTPS. Jediným podporovaným protokolem je protokol HTTPS.|  
|429|Volající překročil kvótu pro počet dotazů za sekundu.|  
|500|Neočekávaná chyba serveru.|

Pokud se požadavek nepovede, odpověď obsahuje objekt [ErrorResponse](#errorresponse) , který obsahuje seznam [chybových](#error) objektů, které popisují, co způsobilo chybu. Pokud chyba souvisí s parametrem, `parameter` pole identifikuje parametr, který je problémem. A pokud chyba souvisí s hodnotou parametru, `value` pole identifikuje neplatnou hodnotu.

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

Níže jsou možné kódy chyb a hodnoty kódu dílčí chyby.

|Kód|Podřízeného kódu|Description
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Stavový kód HTTP je 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokované|Bing vrátí InvalidRequest, pokud jakákoli část požadavku není platná. Například povinný parametr chybí nebo hodnota parametru není platná.<br/><br/>Pokud se jedná o chybu ParameterMissing nebo ParameterInvalidValue, kód stavu HTTP je 400.<br/><br/>Pokud místo protokolu HTTPS použijete protokol HTTP, Bing vrátí HttpNotAllowed a kód stavu HTTP je 410.
|RateLimitExceeded|Žádné dílčí kódy|Bing vrátí RateLimitExceeded vždy, když překročíte kvótu dotazů za sekundu (QPS) nebo dotazů za měsíc (QPM).<br/><br/>Pokud překročíte QPS, Bing vrátí stavový kód HTTP 429 a pokud překročíte QPM, Bing vrátí 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing vrátí InvalidAuthorization, když Bing nemůže ověřit volajícího. `Ocp-Apim-Subscription-Key`Hlavička například chybí nebo klíč předplatného není platný.<br/><br/>Redundance probíhá, pokud zadáte více než jednu metodu ověřování.<br/><br/>Pokud je chyba InvalidAuthorization, kód stavu HTTP je 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing vrátí InsufficientAuthorization, pokud volající nemá oprávnění pro přístup k prostředku. Tato situace může nastat, pokud byl klíč předplatného zakázán nebo vypršela jeho platnost. <br/><br/>Pokud je chyba InsufficientAuthorization, kód stavu HTTP je 403.

## <a name="next-steps"></a>Další kroky
- [Rychlý start hledání místních obchodních obchodů](quickstarts/local-quickstart.md)
- [Rychlý Start pro místní vyhledávání v jazyce Java](quickstarts/local-search-java-quickstart.md)
- [Rychlý Start uzlu místního hledání firmy](quickstarts/local-search-node-quickstart.md)
- [Rychlý Start pro místní vyhledávání v Pythonu](quickstarts/local-search-python-quickstart.md)
