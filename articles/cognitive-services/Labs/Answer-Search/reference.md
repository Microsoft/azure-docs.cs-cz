---
title: Referenční informace ke službě Project Answer Search
titlesuffix: Azure Cognitive Services
description: Odkaz na koncový bod hledání odpovědí projektu
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: reference
ms.date: 04/13/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 28449435479aef0d6a1d8aee3e53de1a78f401b3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396797"
---
# <a name="project-answer-search-v7-reference"></a>Odkaz na hledání odpovědí projektu v7

SearchAPI odpovědi Bingu přebírá parametr dotazu a vrací `searchResponse` s `answerType`: `facts` nebo `entities`. 

Aplikace, které používají rozhraní API pro hledání odpovědí, odesílají požadavky na koncový bod s adresou URL pro náhled v parametru dotazu.  Požadavek musí zahrnovat parametr `q=searchTerm` a hlavičku *OCP-APIM-Subscription-Key* .   

Odpověď JSON lze analyzovat pro fakta a entity, které obsahují podrobné informace o objektu hledání.

## <a name="endpoint"></a>Koncový bod
Chcete-li vyžádat výsledky hledání odpovědí, odešlete požadavek do následujícího koncového bodu. K definování dalších specifikací použijte záhlaví a parametry URL.

Koncový bod GET: 
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=<searchTerm>&subscription-key=0123456789ABCDEF&mkt=en-us

```

Požadavek musí používat protokol HTTPS a zahrnout následující parametr dotazu:
-  `q=<URL>` – dotaz, který identifikuje objekt hledání

Příklady, které ukazují, jak vytvářet požadavky, najdete v tématu [ C# rychlý](c-sharp-quickstart.md) Start nebo rychlý [Start Java](java-quickstart.md). 

V následujících oddílech jsou uvedeny technické podrobnosti o objektech odpovědí, parametrech dotazů a hlavičkách, které mají vliv na výsledky hledání. 
  
Informace o hlavičkách, které by měly požadavky zahrnovat, najdete v části [Headers](#headers).  
  
Informace o parametrech dotazu, které by měly požadavky zahrnovat, najdete v tématu [parametry dotazu](#query-parameters).  
  
Informace o objektech JSON, které odpověď obsahuje, naleznete v tématu [objekty odpovědi](#response-objects).

Maximální délka adresy URL dotazu je 2 048 znaků. Aby se zajistilo, že délka vaší adresy URL nepřekračuje limit, maximální délka parametrů dotazu by měla být kratší než 1 500 znaků. Pokud adresa URL překračuje 2 048 znaků, server nenajde hodnotu 404.  

Informace o povoleném použití a zobrazení výsledků najdete v tématu [použití a zobrazení požadavků](use-display-requirements.md). 

> [!NOTE]
> Některé hlavičky požadavků, které jsou smysluplné pro jiná rozhraní API pro vyhledávání, nemají vliv na Náhled adresy URL.
> - Pragma – volající nemá kontrolu nad tím, jestli adresa URL ve verzi Preview používá mezipaměť.
> - Řízení mezipaměti – volající nemá kontrolu nad tím, zda adresa URL ve verzi Preview používá mezipaměť.
> - User-Agent
> 
> Některé parametry nejsou v současné době smysluplné pro rozhraní API pro náhled verze Preview, ale můžou se použít i v budoucnu pro lepší globalizaci. 
 
## <a name="headers"></a>Hlavičky  
Následují hlavičky, které může obsahovat požadavek a odpověď.  
  
|Hlavička|Popis|  
|------------|-----------------|  
|Přijmout|Nepovinná hlavička požadavku.<br /><br /> Výchozí typ média je Application/JSON. Chcete-li určit, že odpověď používá [JSON-ld](https://json-ld.org/), nastavte hlavičku Accept na Application/ld + JSON.|  
|<a name="acceptlanguage" />Accept-Language|Nepovinná hlavička požadavku.<br /><br /> Čárkami oddělený seznam jazyků pro řetězce uživatelského rozhraní. Seznam je v sestupném pořadí podle priority. Další informace včetně očekávaného formátu najdete v [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Tato hlavička a parametr dotazu [setLang](#setlang) se vzájemně vylučují &mdash; nezadávejte obojí.<br /><br /> Pokud nastavíte tuto hlavičku, musíte zadat také parametr dotazu CC. K určení trhu, pro který se mají vracet výsledky, Bing použije první podporovaný jazyk, který najde v seznamu, a zkombinuje ho s hodnotou parametru `cc`. Pokud seznam jazyků podporovaný jazyk neobsahuje, Bing najde nejbližší jazyk a trh, který požadavek podporuje, nebo pro výsledky použije agregovaný nebo výchozí trh. Pokud chcete zjistit, který trh Bing použil, podívejte se do hlavičky BingAPIs-Market.<br /><br /> Tuto hlavičku a parametr dotazu `cc` použijte jenom v případě, že zadáte více jazyků. Jinak použijte parametry dotazu [mkt](#mkt) a [setLang](#setlang).<br /><br /> Řetězec uživatelského rozhraní je řetězec, který se používá jako popisek v uživatelském rozhraní. V objektech odpovědí JSON je několik řetězců uživatelského rozhraní. Zadaný jazyk použijí všechny odkazy na vlastnosti Bing.com v objektech odpovědi.|  
|<a name="market" />BingAPIs-Market|Hlavička odpovědi.<br /><br /> Trh používaný požadavkem. Forma je \<kódJazyka\>-\<kódZemě\>. Například cs-CZ.|  
|<a name="traceid" />BingAPIs-TraceId|Hlavička odpovědi.<br /><br /> ID položky protokolu obsahující podrobnosti požadavku. Pokud dojde k chybě, toto ID zachyťte. Pokud problém nedokážete určit a vyřešit, uveďte toto ID spolu s dalšími informacemi, které poskytnete týmu podpory.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Povinná hlavička požadavku.<br /><br /> Klíč předplatného, který jste dostali při registraci k této službě v [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Nepovinná hlavička požadavku<br /><br /> Ve výchozím nastavení Bing vrátí obsah uložený v mezipaměti, pokud je k dispozici. Abyste Bingu zabránili ve vrácení obsahu uloženého v mezipaměti, hlavičku Pragma nastavte na hodnotu no-cache (například Pragma: no-cache).
|<a name="useragent" />User-Agent|Nepovinná hlavička požadavku.<br /><br /> Uživatelský agent, ze kterého požadavek pochází. Bing používá uživatelského agenta k poskytnutí optimalizovaného prostředí pro mobilní uživatele. I když je tato hlavička nepovinná, doporučujeme ji vždy zadat.<br /><br /> Uživatelský agent by měl být stejný řetězec, který odesílá kterýkoli běžně používaný prohlížeč. Informace o uživatelských agentech najdete v [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Následují příklady řetězců uživatelského agenta.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Nepovinná hlavička požadavku a odpovědi<br /><br /> Bing tuto hlavičku používá k tomu, aby uživatelům poskytoval konzistentní chování napříč voláními rozhraní API Bingu. Bing často testuje nové funkce a vylepšení a ID klienta používá jako klíč pro přiřazení provozu v různých testovacích verzích. Pokud nepoužíváte stejné ID klienta pro uživatele napříč více požadavky, pak může Bing uživatele přiřadit k více konfliktním testovacím verzím. Přiřazení k více konfliktním testovacím verzím může vést k nekonzistentnímu prostředí pro uživatele. Pokud třeba druhý požadavek má jiné přiřazení k testovací verzi než prví, může být prostředí neočekávané. Bing také může ID klienta použít pro přizpůsobení webových výsledků historii hledání daného ID klienta. Uživatel tak získá bohatší prostředí.<br /><br /> Bing také tuto hlavičku používá ke zlepšování hodnocení výsledků díky analýze aktivity generované tímto ID klienta. Zlepšení relevance pomáhá zlepšit kvalitu výsledků poskytovaných rozhraním API Bingu a stejně tak umožňuje vyšší míru prokliků uživatele rozhraní API.<br /><br /> **DŮLEŽITÉ:** I když je tato hlavička nepovinná, měli byste ji považovat za povinnou. Zachování ID klienta mezi více požadavky pro stejnou kombinaci koncového uživatele a zařízení umožňuje 1) aby uživatel rozhraní API získával konzistentní uživatelské prostředí a 2) vyšší míry prokliků díky kvalitnějším výsledkům z rozhraní API Bingu.<br /><br /> Následují základní pravidla používání, která se na tuto hlavičku vztahují.<br /><ul><li>Každý uživatel, který používá vaši aplikaci na zařízení, musí mít jedinečné ID klienta vygenerované Bingem.<br /><br/>Pokud tuto hlavičku do požadavku nezahrnete, Bing vygeneruje ID a vrátí ho v hlavičce odpovědi X-MSEdge ClientID. Tuto hlavičku byste v požadavku zahrnout NEMĚLI jenom v případě, kdy uživatel aplikaci na zařízení používá poprvé.<br /><br/></li><li>Použijte ID klienta pro každý požadavek rozhraní API Bingu, který vaše aplikace provede pro tohoto uživatele na zařízení.<br /><br/></li><li>**Pozornost:** Je nutné zajistit, aby toto ID klienta nebylo propojeno s žádnými informacemi o ověřujícím uživatelském účtu.</li><br/><li>ID klienta zachovejte. Pokud chcete zachovat ID v aplikaci prohlížeče, použijte trvalý soubor cookie HTTP, aby se zaručilo, že se ID použije ve všech relacích. Nepoužívejte soubor cookie relace. Pro jiné aplikace, jako jsou mobilní aplikace, použijte k zachování ID trvalé úložiště zařízení.<br /><br/>Když uživatel aplikaci na zařízení příště použije, získejte ID klienta, které jste zachovali.</li></ul><br /> **POZNÁMKA:** Odpovědi Bingu tuto hlavičku mohou nebo nemusí obsahovat. Pokud odpověď tuto hlavičku obsahuje, ID klienta zachyťte a použijte pro všechny následné požadavky Bingu pro uživatele na tomto zařízení.<br /><br /> **POZNÁMKA:** Pokud zahrnete X-MSEdge-ClientID, nesmíte do požadavku zahrnout soubory cookie.|  
|<a name="clientip" />X-MSEdge-ClientIP|Nepovinná hlavička požadavku.<br /><br /> Adresa IPv4 nebo IPv6 klientského zařízení. IP adresa se používá ke zjištění polohy uživatele. Bing informace o poloze používá k určení chování bezpečného hledání.<br /><br /> **POZNÁMKA:** I když je tato hlavička nepovinná, doporučujeme ji vždy zadat, stejně jako hlavičku X-Search-Location.<br /><br /> Neprovádějte obfuskaci adresy (například změnou posledního oktetu na 0). Obfuskace adresy vede k tomu, že poloha nebude blízko skutečné polohy zařízení. Bing pak může dodávat chybné výsledky.|  
|<a name="location" />X-Search-Location|Nepovinná hlavička požadavku.<br /><br /> Středníky oddělený seznam párů klíč/hodnota, které popisují zeměpisnou polohu klienta. Bing informace o poloze používá k určení chování bezpečného hledání a vracení relevantního místního obsahu. Pár klíč/hodnota zadejte jako \<klíč\>:\<hodnota\>. Následují klíče, které se používají k určení polohy uživatele.<br /><br /><ul><li>lat&mdash;zeměpisnou šířku umístění klienta ve stupních. Zeměpisná šířka musí být větší nebo rovná -90,0 a menší nebo rovná +90,0. Záporné hodnoty značí jižní šířku a kladné hodnoty značí severní šířku.<br /><br /></li><li>Long&mdash;Zeměpisná délka umístění klienta ve stupních. Zeměpisná délka musí být větší nebo rovná -180,0 a menší nebo rovná +180,0. Záporné hodnoty značí západní délku a kladné hodnoty značí východní délku.<br /><br /></li><li>znovu&mdash; poloměr, v měřičích, který určuje vodorovnou přesnost souřadnic. Předejte hodnotu vrácenou službou zjišťování polohy zařízení. Typické hodnoty můžou být 22 m pro GPS/Wi-Fi, 380 m pro triangulaci mobilních vysílačů a 18 000 m pro reverzní vyhledávání IP adresy.<br /><br /></li><li>TS&mdash; časové razítko systému UNIX pro systém UNIX, kdy byl klient v umístění. (Časové razítko UNIX je počet sekund od 1. ledna 1970.)<br /><br /></li><li>head &mdash; Nepovinné. Relativní směr pohybu klienta. Zadejte směr pohybu ve stupních od 0 do 360 ve směru hodinových ručiček vzhledem k severu. Tento klíč zadejte jenom tehdy, když je klíč `sp` nenulový.<br /><br /></li><li>SP&mdash; horizontální rychlost (rychlost) v metrech za sekundu, na kterou klientské zařízení cestuje.<br /><br /></li><li>ALT&mdash; nadmořské výšce klientského zařízení v měřičích.<br /><br /></li><li>are &mdash; Nepovinné. Poloměr v metrech, který určuje svislou přesnost souřadnic. Výchozí poloměr je 50 kilometrů. Tento klíč zadejte jenom tehdy, když zadáte klíč `alt`.<br /><br /></li></ul> **Poznámka:** I když jsou tyto klíče volitelné, další informace, které poskytnete, jsou přesnější.<br /><br /> **Poznámka:** Doporučujeme vždy zadat zeměpisnou polohu uživatele. Poskytnutí polohy je zvlášť důležité, pokud IP adresa klienta přesně neodráží fyzickou polohu uživatele (třeba pokud klient používá síť VPN). Pro dosažení optimálních výsledků byste měli zahrnout tuto hlavičku i hlavičku X-MSEdge ClientIP, minimálně ale aspoň tuto hlavičku.|

> [!NOTE] 
> Nezapomínejte, že podmínky použití vyžadují dodržování příslušných zákonů, včetně těch týkajících se použití těchto hlaviček. Například v určitých jurisdikcích, třeba v Evropě, se před umístěním určitých sledovacích zařízení do zařízení uživatelů požaduje získání souhlasu uživatele.
  

## <a name="query-parameters"></a>Parametry dotazu  
Požadavek může obsahovat následující parametry dotazu. Požadované parametry najdete v požadovaném sloupci. Je nutné zadat adresu URL pro kódování parametrů dotazu.  
  
  
|Název|Hodnota|Typ|Požaduje se|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Trh, odkud pochází výsledky. <br /><br />Seznam možných hodnot trhu najdete v tématu kódy trhů.<br /><br /> **Poznámka:** Rozhraní API pro náhled adresy URL aktuálně podporuje jenom trh a jazyk EN-US.<br /><br />|Řetězec|Ano|  
|<a name="query" />q|Adresa URL náhledu|Řetězec|Ano|  
|<a name="responseformat" />responseFormat|Typ média, který se má použít pro odpověď Níže jsou uvedené možné hodnoty nerozlišující malá a velká písmena.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Výchozí hodnota je JSON. Informace o objektech JSON, které odpověď obsahuje, naleznete v tématu [objekty Response](#response-objects).<br /><br />  Pokud zadáte JsonLd, tělo odpovědi zahrnuje objekty JSON-LD, které obsahují výsledky hledání. Informace o formátu JSON-LD najdete v tématu [JSON-ld](https://json-ld.org/).|Řetězec|Ne|  
|<a name="safesearch" />safeSearch|Filtr, který slouží k filtrování obsahu pro dospělé. Následují možné hodnoty filtru s rozlišováním velkých a malých písmen.<br /><ul><li>Off&mdash;vracet webové stránky s použitím textu, obrázků a videí pro dospělé.<br /><br/></li><li>Mírné&mdash;vrácení webových stránek s textem pro dospělé, ale ne pro dospělé obrázky nebo videa.<br /><br/></li><li>Striktní&mdash;nevrací webové stránky s použitím textu, obrázků a videí pro dospělé.</li></ul><br /> Výchozí hodnota je Moderate.<br /><br /> **Poznámka:** Pokud žádost pochází z trhu, ve kterém zásada pro dospělé Bingu vyžaduje, aby byla `safeSearch` nastavená na Strict, Bing ignoruje `safeSearch` hodnotu a použije Strict.<br/><br/>**POZNÁMKA:** Pokud použijete operátor dotazu `site:`, je možné, že odpověď bude zahrnovat obsah pro dospělé bez ohledu na nastavení parametru dotazu `safeSearch`. `site:` použijte jenom v případě, že znáte obsah příslušného webu a váš scénář podporuje možnost zobrazení obsahu pro dospělé. |Řetězec|Ne|  
|<a name="setlang" />setLang|Jazyk, který se má použít pro řetězce uživatelského rozhraní. Jazyk zadejte pomocí dvoupísmenného kódu jazyka ISO 639-1. Například kód jazyka pro češtinu je CS. Výchozí hodnota je EN (angličtina).<br /><br /> I když je jazyk volitelný, měli byste ho vždy zadat. Obvykle se `setLang` nastavuje na stejný jazyk, jaký určuje parametr `mkt`, pokud uživatel nechce řetězce uživatelského rozhraní zobrazené v jiném jazyce.<br /><br /> Tento parametr a hlavička [Accept-Language](#acceptlanguage) se vzájemně vylučují &mdash; nezadávejte obojí.<br /><br /> Řetězec uživatelského rozhraní je řetězec, který se používá jako popisek v uživatelském rozhraní. V objektech odpovědí JSON je několik řetězců uživatelského rozhraní. Zadaný jazyk použijí také všechny odkazy na vlastnosti Bing.com v objektech odpovědi.|Řetězec|Ne| 


## <a name="response-objects"></a>Objekty Response  
Schéma odpovědi je buď [webová stránka], nebo ErrorResponse, jako v rozhraní Vyhledávání na webu API. Pokud se požadavek nezdařil, objekt nejvyšší úrovně je objekt [ErrorResponse](#errorresponse) .


|Objekt|Popis|  
|------------|-----------------|  
|Stránku|Objekt JSON nejvyšší úrovně, který obsahuje atributy Preview|  
|Že|Objekt JSON nejvyšší úrovně, který obsahuje fakta.| 
|[Entity|Objekt JSON nejvyšší úrovně, který obsahuje podrobnosti o entitě.| 

  
### <a name="error"></a>Chyba  
Definuje chybu, ke které došlo.  
  
|Prvek|Popis|Typ|  
|-------------|-----------------|----------|  
|kód <a name="error-code" />|Kód chyby, který identifikuje kategorii chyby. Seznam možných kódů naleznete v tématu [kódy chyb](#error-codes).|Řetězec|  
|<a name="error-message" />zpráva|Popis chyby|Řetězec|  
|<a name="error-moredetails" />moreDetails|Popis, který poskytuje další informace o chybě.|Řetězec|  
|<a name="error-parameter" />parametr|Parametr dotazu v žádosti, která způsobila chybu.|Řetězec|  
|<a name="error-subcode" />Subcode|Kód chyby, který identifikuje chybu. Například pokud je `code` InvalidRequest, `subCode` může být ParameterInvalid nebo ParameterInvalidValue. |Řetězec|  
|hodnota <a name="error-value" />|Hodnota parametru dotazu, která není platná.|Řetězec|  
  

### <a name="errorresponse"></a>ErrorResponse  
Objekt nejvyšší úrovně, který odpověď zahrnuje v případě, že se požadavek nezdařil.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_type|Zadejte pomocný parametr.|Řetězec|  
|chyby <a name="errors" />|Seznam chyb popisujících důvody, proč se žádost nezdařila.|[Chyba](#error)|  

  
  
### <a name="license"></a>Licence  
Definuje licenci, za kterou se dá text nebo fotka použít.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|jméno|Název licence.|Řetězec|  
|url|Adresa URL webu, kde může uživatel získat další informace o licenci.<br /><br /> K vytvoření hypertextového odkazu použijte název a adresu URL.|Řetězec|  
  

### <a name="licenseattribution"></a>LicenseAttribution  
Definuje smluvní pravidlo pro přidělení licence.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_type|Pomocný parametr typu, který je nastaven na LicenseAttribution.|Řetězec|  
|průkaz|Licence, za kterou je možné obsah použít.|[Průkaz](#license)|  
|licenseNotice|Licence, která se má zobrazit vedle cíleného pole Například "text v rámci licence CC-BY-SA".<br /><br /> Pomocí názvu a adresy URL licence v poli `license` Vytvořte hypertextový odkaz na web, který popisuje podrobnosti licence. Potom nahraďte název licence v řetězci `licenseNotice` (například CC-BY-SA) hypertextovým odkazem, který jste právě vytvořili.|Řetězec|  
|mustBeCloseToContent|Logická hodnota určující, zda obsah pravidla musí být umístěn v blízkosti pole, na které se pravidlo vztahuje. V případě **hodnoty true**musí být obsah umístěn v blízkosti. Pokud je **hodnota false**nebo toto pole neexistuje, může být obsah umístěn na základě rozhodnutí volajícího.|Logická hodnota|  
|targetPropertyName|Název pole, na které se pravidlo vztahuje.|Řetězec|  
  

### <a name="link"></a>Odkaz  
Definuje součásti hypertextového odkazu.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_type|Zadejte pomocný parametr.|Řetězec|  
|text|Zobrazený text|Řetězec|  
|url|ADRESA URL. Hypertextový odkaz vytvoříte pomocí adresy URL a zobrazovaného textu.|Řetězec|  
  

### <a name="linkattribution"></a>LinkAttribution  
Definuje smluvní pravidlo pro přidělení odkazu.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_type|Pomocný parametr typu, který je nastaven na LinkAttribution.|Řetězec|  
|mustBeCloseToContent|Logická hodnota určující, zda obsah pravidla musí být umístěn v blízkosti pole, na které se pravidlo vztahuje. V případě **hodnoty true**musí být obsah umístěn v blízkosti. Pokud je **hodnota false**nebo toto pole neexistuje, může být obsah umístěn na základě rozhodnutí volajícího.|Logická hodnota|  
|targetPropertyName|Název pole, na které se pravidlo vztahuje.<br /><br /> Pokud není zadán cíl, vztahuje se k entitě jako celek a měl by se zobrazovat hned za prezentací entity. Pokud je k dispozici více pravidel textu a propojení, která neurčují cíl, je třeba je zřetězit a zobrazit pomocí popisku "data z:". Například "data ze < Provider název1\> &#124; < provider název2\>".|Řetězec|  
|text|Text přidělení.|Řetězec|  
|url|Adresa URL webu poskytovatele. K vytvoření hypertextového odkazu použijte `text` a adresu URL.|Řetězec|  
  
  
### <a name="mediaattribution"></a>MediaAttribution  
Definuje smluvní pravidlo pro přidělení médií.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_type|Pomocný parametr typu, který je nastaven na MediaAttribution.|Řetězec|  
|mustBeCloseToContent|Logická hodnota určující, zda obsah pravidla musí být umístěn v blízkosti pole, na které se pravidlo vztahuje. V případě **hodnoty true**musí být obsah umístěn v blízkosti. Pokud je **hodnota false**nebo toto pole neexistuje, může být obsah umístěn na základě rozhodnutí volajícího.|Logická hodnota|  
|targetPropertyName|Název pole, na které se pravidlo vztahuje.|Řetězec|  
|url|Adresa URL, kterou použijete k vytvoření hypertextového odkazu na multimediální obsah. Pokud je cílem například obrázek, měli byste použít adresu URL, aby bylo možné obrázek kliknout.|Řetězec|  
  
  
  
### <a name="organization"></a>Organizace  
Definuje vydavatele.  
  
Všimněte si, že Vydavatel může poskytnout svůj název nebo web nebo obojí.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|jméno|Název vydavatele|Řetězec|  
|url|Adresa URL webu vydavatele<br /><br /> Všimněte si, že vydavatel nemusí poskytovat Web.|Řetězec|  
  
  

### <a name="webpage"></a>Stránku  
Definuje informace o webové stránce ve verzi Preview.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|
|jméno|Nadpis stránky, ne nutně název HTML|Řetězec|
|url|Adresa URL, která byla ve skutečnosti procházena (žádost může mít přesměrování za následovat)|Řetězec|  
|description|Stručný popis stránky a obsahu|Řetězec|  
|isFamilyFriendly|Nejpřesnější pro položky ve webovém indexu; načítá se v reálném čase. Tato detekce je založená výhradně na adrese URL, nikoli na obsahu stránky.|Boolean|
|primaryImageOfPage/contentUrl|Adresa URL zástupce obrázku, který má být zahrnut do verze Preview|Řetězec| 
  
  
### <a name="querycontext"></a>QueryContext  
Definuje kontext dotazu, který Bing použil pro požadavek.  
  
|Prvek|Popis|Typ|  
|-------------|-----------------|----------|  
|adultIntent|Logická hodnota, která označuje, zda má zadaný dotaz záměr k dospělému. Hodnota je **true** , pokud má dotaz záměr k dospělému; v opačném případě **false**.|Logická hodnota|  
|alterationOverrideQuery|Řetězec dotazu, který se má použít k vynucení použití původního řetězce v Bingu. Například pokud je řetězec dotazu *Saling downwind*, řetězec přepisu řetězce dotazu bude *+ Saling downwind*. Nezapomeňte zakódovat řetězec dotazu, který má za následek *% 2Bsaling + downwind*.<br /><br /> Toto pole je zahrnuto pouze v případě, že původní řetězec dotazu obsahuje pravopisnou chybu.|Řetězec|  
|alteredQuery|Řetězec dotazu, který Bing používá k provedení dotazu. Bing používá změněný řetězec dotazu, pokud původní řetězec dotazu obsahoval pravopisné chyby. Například pokud je řetězec dotazu `saling downwind`, změněný řetězec dotazu bude `sailing downwind`.<br /><br /> Toto pole je zahrnuto pouze v případě, že původní řetězec dotazu obsahuje pravopisnou chybu.|Řetězec|  
|askUserForLocation|Logická hodnota, která určuje, zda Bing vyžaduje umístění uživatele, aby poskytovalo přesné výsledky. Pokud jste zadali umístění uživatele pomocí hlaviček [x-MSEdge-IP adresa klienta](#clientip) a [x-Search-Location](#location) , můžete toto pole ignorovat.<br /><br /> Pro dotazy na umístění, jako je "dnešní počasí" nebo "Restaurace v mém okolí", které potřebují umístění uživatele, aby poskytovaly přesné výsledky, je toto pole nastaveno na **hodnotu true**.<br /><br /> Pro dotazy na umístění, které zahrnují umístění (například "povětrnostní počasí"), je toto pole nastaveno na **hodnotu NEPRAVDA**. Toto pole je také nastaveno na **hodnotu false** u dotazů, které nejsou v umístění, například "nejlepší prodejci".|Logická hodnota|  
|originalQuery|Řetězec dotazu, jak je uveden v požadavku.|Řetězec|  

### <a name="identifiable"></a>Identifikovatelné údaje

|Název|Hodnota|Typ|  
|-------------|-----------------|----------|
|id|Identifikátor prostředku|Řetězec|
 
### <a name="rankinggroup"></a>Klasifikace
Definuje skupinu výsledků hledání, jako je například hlavní.

|Název|Hodnota|Typ|  
|-------------|-----------------|----------|
|items|Seznam výsledků hledání, které se mají zobrazit ve skupině|RankingItem|

### <a name="rankingitem"></a>RankingItem
Definuje položku výsledku hledání, která se má zobrazit.

|Název|Hodnota|Typ|  
|-------------|-----------------|----------|
|resultIndex|Index položky vycházející z nuly v odpovědi, která se má zobrazit Pokud tato položka neobsahuje toto pole, zobrazí všechny položky v odpovědi. Můžete například zobrazit všechny články v odpovědi na zprávy.|Integer|
|answerType|Odpověď obsahující položku, která se má zobrazit Například zprávy.<br /><br />Pomocí typu vyhledejte odpověď v objektu SearchResponse. Typ je název pole SearchResponse.<br /><br /> Typ odpovědi však použijte pouze v případě, že tento objekt obsahuje pole hodnota. v opačném případě tuto chybu ignorujte.|Řetězec|
|textualIndex|Index odpovědi v textualAnswers, který se má zobrazit| Celé číslo bez znaménka|
|hodnota|ID, které identifikuje odpověď pro zobrazení nebo položku odpovědi, která se má zobrazit Pokud ID identifikuje odpověď, zobrazí všechny položky odpovědi.|Identifikovatelné údaje|

### <a name="rankingresponse"></a>RankingResponse  
Definuje, kde má být obsah stránky výsledků hledání umístěn a v jakém pořadí.  
  
|Název|Hodnota|  
|----------|-----------|  
|<a name="ranking-mainline" />hlavní|Výsledky hledání, které se mají zobrazit v hlavní|  
|<a name="ranking-pole" />pole|Výsledky hledání, které by měly být uváděny jako nejužitečnější způsob zpracování (například zobrazené nad hlavní a postranní panel).|  
|<a name="ranking-sidebar" />boční panel|Výsledky hledání, které se mají zobrazit na bočním panelu| 


### <a name="searchresponse"></a>SearchResponse  
Definuje objekt nejvyšší úrovně, který odpověď zahrnuje, když je požadavek úspěšný.  
  
Všimněte si, že pokud by služba způsobila útok DoS (Denial of Service), požadavek bude úspěšný (kód stavu HTTP je 200 OK); tělo odpovědi však bude prázdné.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_type|Zadejte pomocný parametr typu, který je nastavený na SearchResponse.|Řetězec|  
|Stránku|Objekt JSON, který definuje verzi Preview.|řetězec|  
  
  
### <a name="textattribution"></a>TextAttribution  
Definuje smluvní pravidlo pro přidělení prostého textu.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_type|Pomocný parametr typu, který je nastaven na TextAttribution.|Řetězec|  
|text|Text přidělení.<br /><br /> Označení textu se vztahuje na entitu jako celek a mělo by se zobrazovat hned za prezentací entity. Pokud je k dispozici více pravidel textu nebo propojení, která neurčují cíl, je třeba je zřetězit a zobrazit pomocí popisku "data z:".|Řetězec| 


## <a name="error-codes"></a>Kódy chyb

Níže jsou uvedené možné stavové kódy HTTP, které požadavek vrátí.  
  
|Stavový kód|Popis|  
|-----------------|-----------------|  
|200|Úspěch|  
|400|Jeden z parametrů dotazu chybí nebo je neplatný.|  
|401|Klíč předplatného chybí nebo není platný.|  
|403|Uživatel je ověřený (například používá platný klíč předplatného), ale nemá oprávnění k požadovanému prostředku.<br /><br /> Bing může tento stav vrátit také v případě, že volající překročil kvótu na měsíc.|  
|410|Požadavek použil protokol HTTP místo protokolu HTTPS. Jediným podporovaným protokolem je protokol HTTPS.|  
|429|Volající překročil kvótu pro počet dotazů za sekundu.|  
|500|Došlo k neočekávané chybě serveru.|

Pokud se požadavek nepovede, odpověď obsahuje objekt [ErrorResponse](#errorresponse) , který obsahuje seznam [chybových](#error) objektů, které popisují, co způsobilo chybu. Pokud chyba souvisí s parametrem, pole `parameter` identifikuje parametr, který je problémem. A pokud chyba souvisí s hodnotou parametru, pole `value` identifikuje neplatnou hodnotu.

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

|Kód|Podřízeného kódu|Popis
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Stavový kód HTTP je 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokováno|Bing vrátí InvalidRequest, pokud jakákoli část požadavku není platná. Například povinný parametr chybí nebo hodnota parametru není platná.<br/><br/>Pokud se jedná o chybu ParameterMissing nebo ParameterInvalidValue, kód stavu HTTP je 400.<br/><br/>Pokud místo protokolu HTTPS použijete protokol HTTP, Bing vrátí HttpNotAllowed a kód stavu HTTP je 410.
|RateLimitExceeded|Žádné dílčí kódy|Bing vrátí RateLimitExceeded vždy, když překročíte kvótu dotazů za sekundu (QPS) nebo dotazů za měsíc (QPM).<br/><br/>Pokud překročíte QPS, Bing vrátí stavový kód HTTP 429 a pokud překročíte QPM, Bing vrátí 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing vrátí InvalidAuthorization, když Bing nemůže ověřit volajícího. Například hlavička `Ocp-Apim-Subscription-Key` chybí nebo klíč předplatného není platný.<br/><br/>Redundance probíhá, pokud zadáte více než jednu metodu ověřování.<br/><br/>Pokud je chyba InvalidAuthorization, kód stavu HTTP je 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing vrátí InsufficientAuthorization, pokud volající nemá oprávnění pro přístup k prostředku. Tato situace může nastat, pokud byl klíč předplatného zakázán nebo vypršela jeho platnost. <br/><br/>Pokud je chyba InsufficientAuthorization, kód stavu HTTP je 403.

## <a name="next-steps"></a>Další kroky
- [Rychlý start C#](c-sharp-quickstart.md)
- [Rychlý start pro Javu](java-quickstart.md)
- [Rychlý start pro Node](node-quickstart.md)
- [Rychlý start pro Python](python-quickstart.md)

