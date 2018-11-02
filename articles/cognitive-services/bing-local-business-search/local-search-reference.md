---
title: Odkaz v7 rozhraní API pro vyhledávání na Bingu místní pracovní | Dokumentace Microsoftu
description: Popisuje programovací prvky místní firmy API pro vyhledávání Bingu.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: dc394093e9e2db223defe61229832ce5448bdfaa
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748653"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Odkaz na místní firmy hledání rozhraní API Bingu v7

Rozhraní API pro vyhledávání místní firmy odešle dotaz vyhledávání pro Bing zobrazíte výsledky, které zahrnují zahrnují restaurace, hotely nebo jiné místní firmy. Místa můžete dotaz zadat název místní firmy nebo kategorii (například restaurace v okolí). Mezi výsledky entit patří osoby, místa nebo věci. V tomto kontextu se obchodní entity, státy, země atd.  

Tato část obsahuje technické podrobnosti o objekty odpovědi a parametry dotazů a hlaviček, které ovlivňují výsledky hledání. Příklady, které ukazují, jak vytvářet požadavky najdete v tématu [místní vyhledávání obchodních C# rychlý Start](quickstarts/local-quickstart.md) nebo [místní firmy hledání Java quickstart](quickstarts/local-search-java-quickstart.md). 
  
Informace o hlavičky, které by měl obsahovat požadavky najdete v tématu [záhlaví](#headers).  
  
Informace o parametrech dotazů, které by měl obsahovat požadavky najdete v tématu [parametrů dotazu](#query-parameters).  
  
Informace o JSON objektů, že odpověď obsahuje, naleznete v tématu [objekty odpovědi](#response-objects).

Informace o povolených použití a zobrazení výsledků najdete v tématu [použít a zobrazit požadavky](use-display-requirements.md).


  
## <a name="endpoint"></a>Koncový bod  
Požádat o místní obchodní výsledky, odeslat požadavek GET na: 

``` 
https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search

```
  
Požadavek musí používat protokol HTTPS.  
  
> [!NOTE]
> Maximální délka adresy URL je 2 048 znaků. Aby bylo zajištěno, že vaše délka adresy URL nepřekračuje limit, maximální délka parametry dotazu musí být menší než 1 500 znaků. Pokud adresa URL je delší než 2 048 znaků, server vrátí 404 Nenalezeno.  
  
  
## <a name="headers"></a>Hlavičky  
Níže jsou hlavičky, které mohou zahrnovat požadavek a odpověď.  
  
|Hlavička|Popis|  
|------------|-----------------|  
|Přijmout|Nepovinná hlavička požadavku.<br /><br /> Výchozí typ média je application/json. Určující, že odpověď [JSON-LD](http://json-ld.org/), nastavit hlavičku Accept application/ld + json.|  
|<a name="acceptlanguage" />Accept-Language|Nepovinná hlavička požadavku.<br /><br /> Čárkami oddělený seznam jazyků pro řetězce uživatelského rozhraní. Seznam je v sestupném pořadí podle priority. Další informace včetně očekávaného formátu najdete v [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Tato hlavička a parametr dotazu [setLang](#setlang) se vzájemně vylučují &mdash; nezadávejte obojí.<br /><br /> Pokud tuto hlavičku nastavíte, musíte zadat také parametr dotazu [cc](#cc). K určení trhu, pro který se mají vracet výsledky, Bing použije první podporovaný jazyk, který najde v seznamu, a zkombinuje ho s hodnotou parametru `cc`. Pokud seznam jazyků podporovaný jazyk neobsahuje, Bing najde nejbližší jazyk a trh, který požadavek podporuje, nebo pro výsledky použije agregovaný nebo výchozí trh. Pokud chcete zjistit, který trh Bing použil, podívejte se do hlavičky BingAPIs-Market.<br /><br /> Tuto hlavičku a parametr dotazu `cc` použijte jenom v případě, že zadáte více jazyků. Jinak použijte parametry dotazu [mkt](#mkt) a [setLang](#setlang).<br /><br /> Řetězec uživatelského rozhraní je řetězec, který se používá jako popisek v uživatelském rozhraní. V objektech odpovědí JSON je několik řetězců uživatelského rozhraní. Zadaný jazyk použijí všechny odkazy na vlastnosti Bing.com v objektech odpovědi.|  
|<a name="market" />BingAPIs-Market|Hlavička odpovědi.<br /><br /> Trh používaný požadavkem. Forma je \<kódJazyka\>-\<kódZemě\>. Například cs-CZ.|  
|<a name="traceid" />BingAPIs-TraceId|Hlavička odpovědi.<br /><br /> ID položky protokolu obsahující podrobnosti požadavku. Pokud dojde k chybě, toto ID zachyťte. Pokud problém nedokážete určit a vyřešit, uveďte toto ID spolu s dalšími informacemi, které poskytnete týmu podpory.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Povinná hlavička požadavku.<br /><br /> Klíč předplatného, který jste dostali při registraci k této službě v [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Nepovinná hlavička požadavku<br /><br /> Ve výchozím nastavení Bing vrátí obsah uložený v mezipaměti, pokud je k dispozici. Abyste Bingu zabránili ve vrácení obsahu uloženého v mezipaměti, hlavičku Pragma nastavte na hodnotu no-cache (například Pragma: no-cache).
|<a name="useragent" />User-Agent|Nepovinná hlavička požadavku.<br /><br /> Uživatelský agent, ze kterého požadavek pochází. Bing používá uživatelského agenta k poskytnutí optimalizovaného prostředí pro mobilní uživatele. I když je tato hlavička nepovinná, doporučujeme ji vždy zadat.<br /><br /> Uživatelský agent by měl být stejný řetězec, který odesílá kterýkoli běžně používaný prohlížeč. Informace o uživatelských agentech najdete v [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Následují příklady řetězců uživatelského agenta.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Nepovinná hlavička požadavku a odpovědi<br /><br /> Bing tuto hlavičku používá k tomu, aby uživatelům poskytoval konzistentní chování napříč voláními rozhraní API Bingu. Bing často testuje nové funkce a vylepšení a ID klienta používá jako klíč pro přiřazení provozu v různých testovacích verzích. Pokud nepoužíváte stejné ID klienta pro uživatele napříč více požadavky, pak může Bing uživatele přiřadit k více konfliktním testovacím verzím. Přiřazení k více konfliktním testovacím verzím může vést k nekonzistentnímu prostředí pro uživatele. Pokud třeba druhý požadavek má jiné přiřazení k testovací verzi než prví, může být prostředí neočekávané. Bing také může ID klienta použít pro přizpůsobení webových výsledků historii hledání daného ID klienta. Uživatel tak získá bohatší prostředí.<br /><br /> Bing také tuto hlavičku používá ke zlepšování hodnocení výsledků díky analýze aktivity generované tímto ID klienta. Zlepšení relevance pomáhá zlepšit kvalitu výsledků poskytovaných rozhraním API Bingu a stejně tak umožňuje vyšší míru prokliků uživatele rozhraní API.<br /><br /> **DŮLEŽITÉ:** I když je tato hlavička nepovinná, měli byste ji považovat za povinnou. Zachování ID klienta mezi více požadavky pro stejnou kombinaci koncového uživatele a zařízení umožňuje 1) aby uživatel rozhraní API získával konzistentní uživatelské prostředí a 2) vyšší míry prokliků díky kvalitnějším výsledkům z rozhraní API Bingu.<br /><br /> Následují základní pravidla používání, která se na tuto hlavičku vztahují.<br /><ul><li>Každý uživatel, který používá vaši aplikaci na zařízení, musí mít jedinečné ID klienta vygenerované Bingem.<br /><br/>Pokud tuto hlavičku do požadavku nezahrnete, Bing vygeneruje ID a vrátí ho v hlavičce odpovědi X-MSEdge ClientID. Tuto hlavičku byste v požadavku zahrnout NEMĚLI jenom v případě, kdy uživatel aplikaci na zařízení používá poprvé.<br /><br/></li><li>Použijte ID klienta pro každý požadavek rozhraní API Bingu, který vaše aplikace provede pro tohoto uživatele na zařízení.<br /><br/></li><li>**Pozor:** musíte zajistit, že toto ID klienta není propojovací informací authenticatable uživatelského účtu.</li><br/><li>ID klienta zachovejte. Pokud chcete zachovat ID v aplikaci prohlížeče, použijte trvalý soubor cookie HTTP, aby se zaručilo, že se ID použije ve všech relacích. Nepoužívejte soubor cookie relace. Pro jiné aplikace, jako jsou mobilní aplikace, použijte k zachování ID trvalé úložiště zařízení.<br /><br/>Když uživatel aplikaci na zařízení příště použije, získejte ID klienta, které jste zachovali.</li></ul><br /> **POZNÁMKA:** Odpovědi Bingu tuto hlavičku mohou nebo nemusí obsahovat. Pokud odpověď tuto hlavičku obsahuje, ID klienta zachyťte a použijte pro všechny následné požadavky Bingu pro uživatele na tomto zařízení.<br /><br /> **POZNÁMKA:** Pokud zahrnete X-MSEdge-ClientID, nesmíte do požadavku zahrnout soubory cookie.|  
|<a name="clientip" />X-MSEdge-ClientIP|Nepovinná hlavička požadavku.<br /><br /> Adresa IPv4 nebo IPv6 klientského zařízení. IP adresa se používá ke zjištění polohy uživatele. Bing informace o poloze používá k určení chování bezpečného hledání.<br /><br /> **POZNÁMKA:** I když je tato hlavička nepovinná, doporučujeme ji vždy zadat, stejně jako hlavičku X-Search-Location.<br /><br /> Neprovádějte obfuskaci adresy (například změnou posledního oktetu na 0). Obfuskace adresy vede k tomu, že poloha nebude blízko skutečné polohy zařízení. Bing pak může dodávat chybné výsledky.|  
|<a name="location" />X-Search-Location|Nepovinná hlavička požadavku.<br /><br /> Středníky oddělený seznam párů klíč/hodnota, které popisují zeměpisnou polohu klienta. Bing informace o poloze používá k určení chování bezpečného hledání a vracení relevantního místního obsahu. Pár klíč/hodnota zadejte jako \<klíč\>:\<hodnota\>. Následují klíče, které se používají k určení polohy uživatele.<br /><br /><ul><li>LAT&mdash;zeměpisná šířka umístění klienta ve stupních. Zeměpisná šířka musí být větší nebo rovná -90,0 a menší nebo rovná +90,0. Záporné hodnoty značí jižní šířku a kladné hodnoty značí severní šířku.<br /><br /></li><li>dlouhé&mdash;zeměpisná délka umístění klienta ve stupních. Zeměpisná délka musí být větší nebo rovná -180,0 a menší nebo rovná +180,0. Záporné hodnoty značí západní délku a kladné hodnoty značí východní délku.<br /><br /></li><li>RE&mdash; radius, v metrech, který určuje vodorovný přesnost souřadnic. Předejte hodnotu vrácenou službou zjišťování polohy zařízení. Typické hodnoty můžou být 22 m pro GPS/Wi-Fi, 380 m pro triangulaci mobilních vysílačů a 18 000 m pro reverzní vyhledávání IP adresy.<br /><br /></li><li>TS&mdash; The UTC UNIXOVÉ časové razítko z když klient se v umístění. (Časové razítko UNIX je počet sekund od 1. ledna 1970.)<br /><br /></li><li>head &mdash; Nepovinné. Relativní směr pohybu klienta. Zadejte směr pohybu ve stupních od 0 do 360 ve směru hodinových ručiček vzhledem k severu. Tento klíč zadejte jenom tehdy, když je klíč `sp` nenulový.<br /><br /></li><li>SP&mdash; vodorovné rychlost (rychlost), v metrech za sekundu, cestování klientského zařízení.<br /><br /></li><li>ALT&mdash; výška klientské zařízení, v metrech.<br /><br /></li><li>are &mdash; Nepovinné. Poloměr v metrech, který určuje svislou přesnost souřadnic. Výchozí hodnota protokolu RADIUS je 50 kilometrů. Tento klíč zadejte jenom tehdy, když zadáte klíč `alt`.<br /><br /></li></ul> **Poznámka:** i když tyto klíče jsou volitelné, další informace, které zadáte, jsou přesnější výsledky umístění.<br /><br /> **Poznámka:** doporučujeme vždy zadat zeměpisné umístění uživatele. Poskytnutí polohy je zvlášť důležité, pokud IP adresa klienta přesně neodráží fyzickou polohu uživatele (třeba pokud klient používá síť VPN). Pro dosažení optimálních výsledků byste měli zahrnout tuto hlavičku i hlavičku X-MSEdge ClientIP, minimálně ale aspoň tuto hlavičku.|

> [!NOTE] 
> Nezapomínejte, že podmínky použití vyžadují dodržování příslušných zákonů, včetně těch týkajících se použití těchto hlaviček. Například v určitých jurisdikcích, třeba v Evropě, se před umístěním určitých sledovacích zařízení do zařízení uživatelů požaduje získání souhlasu uživatele.
  

## <a name="query-parameters"></a>Parametry dotazu  
Žádost mohou zahrnovat tyto parametry dotazu. Zobrazte požadovaný sloupec pro požadované parametry. Adresa URL musíte zakódovat parametry dotazu.  
  
  
|Název|Hodnota|Typ|Požaduje se|  
|----------|-----------|----------|--------------|
|<a name="count" />Počet|Počet výsledků k vrácení, počínaje indexem určené `offset` parametru.|Řetězec|Ne|   
|<a name="localCategories" />localCategories|Seznam možností, které definují vyhledávání podle kategorie business.  Zobrazit [prohledávat místní firmy kategorie](local-categories.md)|Řetězec|Ne|  
|<a name="mkt" />mkt|Trh, odkud pochází výsledky. <br /><br />Seznam možných hodnot na trhu, najdete v tématu [trhu kódy](#market-codes).<br /><br /> **Poznámka:** rozhraní API pro vyhledávání místní firmy aktuálně podporuje pouze en-nám vstup na trh a jazyk.<br /><br />|Řetězec|Ano|
|<a name="offset"/>Posun|Index spustit výsledky určené `count` parametru.|Integer|Ne|  
|<a name="query" />q|Hledaný termín daného uživatele.|Řetězec|Ne|  
|<a name="responseformat" />Formát odpovědi|Typ média určený pro odpověď. Níže jsou uvedeny možné hodnoty velká a malá písmena.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Výchozí hodnota je JSON. Informace o JSON objektů, že odpověď obsahuje, naleznete v tématu [objekty odpovědi](#response-objects).<br /><br />  Pokud chcete zadat JsonLd, tělo odpovědi obsahuje objekty JSON-LD, obsahující výsledky hledání. Informace o JSON-LD, naleznete v tématu [JSON-LD](http://json-ld.org/).|Řetězec|Ne|  
|<a name="safesearch" />safeSearch|Filtr, který slouží k filtrování obsahu pro dospělé. Následují možné hodnoty filtru s rozlišováním velkých a malých písmen.<br /><ul><li>Vypnout&mdash;vrátit webových stránek s dospělé textu, obrázků nebo videa.<br /><br/></li><li>Střední&mdash;vrátit webových stránek s dospělé text, ale nezletilý imagí nebo videa.<br /><br/></li><li>Striktní&mdash;nevrátí webových stránek s dospělé textu, obrázků nebo videa.</li></ul><br /> Výchozí hodnota je Moderate.<br /><br /> **Poznámka:** Pokud žádost pochází z na trhu této Bing pro dospělé zásad vyžaduje, aby `safeSearch` je nastavena na Strict, ignoruje Bing `safeSearch` hodnotu a používá Strict.<br/><br/>**POZNÁMKA:** Pokud použijete operátor dotazu `site:`, je možné, že odpověď bude zahrnovat obsah pro dospělé bez ohledu na nastavení parametru dotazu `safeSearch`. `site:` použijte jenom v případě, že znáte obsah příslušného webu a váš scénář podporuje možnost zobrazení obsahu pro dospělé. |Řetězec|Ne|  
|<a name="setlang" />setLang|Jazyk, který se má použít pro řetězce uživatelského rozhraní. Jazyk zadejte pomocí dvoupísmenného kódu jazyka ISO 639-1. Například kód jazyka pro češtinu je CS. Výchozí hodnota je EN (angličtina).<br /><br /> I když je jazyk volitelný, měli byste ho vždy zadat. Obvykle se `setLang` nastavuje na stejný jazyk, jaký určuje parametr `mkt`, pokud uživatel nechce řetězce uživatelského rozhraní zobrazené v jiném jazyce.<br /><br /> Tento parametr a hlavička [Accept-Language](#acceptlanguage) se vzájemně vylučují &mdash; nezadávejte obojí.<br /><br /> Řetězec uživatelského rozhraní je řetězec, který se používá jako popisek v uživatelském rozhraní. V objektech odpovědí JSON je několik řetězců uživatelského rozhraní. Zadaný jazyk použijí také všechny odkazy na vlastnosti Bing.com v objektech odpovědi.|Řetězec|Ne| 


## <a name="response-objects"></a>Objekty odpovědi  
Následují objekty JSON odpovědi, které mohou obsahovat odpovědi. Pokud neproběhne úspěšně, je objekt nejvyšší úrovně v odpovědi [SearchResponse](#searchresponse) objektu. Pokud požadavek selže, je objekt nejvyšší úrovně [ErrorResponse](#errorresponse) objektu.


|Objekt|Popis|  
|------------|-----------------|  
|[Místo](#place)|Definuje informace o místní firmy, jako je například restaurace nebo hotelu.|  

  
### <a name="error"></a>Chyba  
Definuje chyby, ke které došlo k chybě.  
  
|Element|Popis|Typ|  
|-------------|-----------------|----------|  
|<a name="error-code" />kód|Kód chyby, který určuje kategorii chyby. Seznam možných kódů najdete v tématu [kódy chyb](#error-codes).|Řetězec|  
|<a name="error-message" />Zpráva|Popis chyby.|Řetězec|  
|<a name="error-moredetails" />moreDetails|Popis, který poskytuje další informace o této chybě.|Řetězec|  
|<a name="error-parameter" />Parametr|Parametr dotazu v žádosti, která způsobila chybu.|Řetězec|  
|<a name="error-subcode" />Podřízeného|Kód chyby: identifikující chybu. Například pokud `code` je InvalidRequest, `subCode` může být ParameterInvalid nebo ParameterInvalidValue. |Řetězec|  
|<a name="error-value" />Hodnota|Hodnota parametru dotazu, která nebyla platná.|Řetězec|  
  

### <a name="errorresponse"></a>ErrorResponse  
Objekt nejvyšší úrovně, který obsahuje odpověď, pokud požadavek selže.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_typ|Pomocný parametr typu.|Řetězec|  
|<a name="errors" />Chyby|Seznam chyb, které popisují důvody, proč žádost selhala.|[Chyba](#error)]|  

  
  
### <a name="license"></a>Licence  
Definuje licence, pod kterým mohou být použity text nebo fotografie.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|jméno|Název licence.|Řetězec|  
|url|Adresa URL webu, kde uživatel získat další informace o licenci.<br /><br /> Vytvoření hypertextového odkazu, použijte název a adresu URL.|Řetězec|  


### <a name="link"></a>Odkaz  
Definuje komponenty hypertextový odkaz.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_typ|Pomocný parametr typu.|Řetězec|  
|text|Zobrazení textu.|Řetězec|  
|url|ADRESA URL. Použijte adresu URL a zobrazit text Vytvoření hypertextového odkazu.|Řetězec|  
  


  
### <a name="organization"></a>Organizace  
Definuje vydavatele.  
  
Všimněte si, že vydavatel může zadat jeho název nebo jejich webu nebo obojí.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|jméno|Název vydavatele.|Řetězec|  
|url|Adresa URL webu vydavatele.<br /><br /> Všimněte si, že vydavatel nemusí poskytnout webu.|Řetězec|  
  
  

### <a name="place"></a>Místo  
Definuje informace o místní firmy, jako je například restaurace nebo hotelu.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_typ|Zadejte pokyn, který může být nastaven na jednu z následujících akcí:<br /><br /><ul><li>Hotel</li><li>LocalBusiness<br /></li><li>Restaurace</ul><li>|Řetězec|  
|Adresa|Poštovní adresa ve kterém se entita nachází.|[PostalAddress služby Active Directory](#postaladdress)|  
|entityPresentationInfo|Další informace o entitě, jako je například pomocné parametry, které můžete použít k určení typu entity. Například zda je restaurace nebo hotelu. `entityScenario` Je nastaveno na ListItem.|[entityPresentationInfo](#entitypresentationinfo)|  
|jméno|Název entity.|Řetězec|  
|Telefon|Entity telefonní číslo.|Řetězec|  
|url|Adresa URL webu entity.<br /><br /> Pomocí této adresy URL spolu s názvem subjektu k vytvoření hypertextového odkazu, který po kliknutí uživatele na web entity.|Řetězec|  
|webSearchUrl|Adresa URL na výsledky hledání na Bingu pro toto umístění.|Řetězec| 
  
  
### <a name="querycontext"></a>QueryContext  
Definuje kontext dotazu, který používá Bing pro daný požadavek.  
  
|Element|Popis|Typ|  
|-------------|-----------------|----------|  
|adultIntent|Logická hodnota, která určuje, zda má zadaný dotaz dospělé. Hodnota je **true** Pokud dotaz obsahuje dospělé; v opačném případě **false**.|Logická hodnota|  
|alterationOverrideQuery|Řetězec dotazu, který se má použít k vynucení Bingu použít původní řetězec. Například, pokud je řetězec dotazu *saling po směru větru*, bude přepsání řetězce dotazu *+ saling po směru větru*. Nezapomeňte zakódujte řetězec dotazu, což vede k *% 2Bsaling + po směru větru*.<br /><br /> Toto pole je zahrnuta pouze v případě, že původní řetězec dotazu obsahuje pravopisné chyby.|Řetězec|  
|alteredQuery|Řetězec dotazu použitý bingem k provedení dotazu. Bing používá řetězec upravený dotaz, pokud původní řetězce dotazu obsažené pravopisné chyby hned. Například, pokud je řetězec dotazu `saling downwind`, řetězec upravený dotaz bude `sailing downwind`.<br /><br /> Toto pole je zahrnuta pouze v případě, že původní řetězec dotazu obsahuje pravopisné chyby.|Řetězec|  
|askUserForLocation|Logická hodnota, která určuje, zda vyžaduje Bing podle umístění uživatele poskytnou přesné výsledky. Pokud jste zadali umístění uživatele pomocí [X-MSEdge ClientIP](#clientip) a [X-Search-umístění](#location) záhlaví, můžete ignorovat toto pole.<br /><br /> Umístění vědět dotazů, jako je například "dnešní počasí" nebo "restaurace v okolí", které je třeba podle umístění uživatele poskytnou přesné výsledky, toto pole je nastaven na **true**.<br /><br /> Umístění vědět dotazů, které obsahují umístění (například "Seattle počasí"), toto pole je nastaven na **false**. Toto pole je také nastavena na **false** pro dotazy, které nejsou místem, jako je například "nejpopulárnější".|Logická hodnota|  
|originalQuery|Řetězec dotazu jako zadaný v požadavku.|Řetězec|  

### <a name="identifiable"></a>Údaje
|Název|Hodnota|Typ|  
|-------------|-----------------|----------|
|id|Identifikátor prostředku|Řetězec|
 
### <a name="rankinggroup"></a>RankingGroup
Definuje skupinu výsledky, jako například mainline.
|Název|Hodnota|Typ|  
|-------------|-----------------|----------|
|položek|Seznam výsledků hledání pro zobrazení ve skupině.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Definuje položku výsledek vyhledávání k zobrazení.
|Název|Hodnota|Typ|  
|-------------|-----------------|----------|
|resultIndex|Z nuly vycházející index položky v odpovědi na dotaz k zobrazení. Pokud položka neobsahuje toto pole, zobrazí všechny položky v odpovědi na dotaz. Například zobrazte všechny články o novinkách v zpráv odpovědí.|Integer|
|answerType|Odpověď obsahující položku k zobrazení. Například příspěvky.<br /><br />Použijte typ odpovědi SearchResponse objektu. Typ je název SearchResponse pole.<br /><br /> Pouze v případě, že tento objekt obsahuje pole hodnoty; však použijte typ odpovědi v opačném případě ji ignorujte.|Řetězec|
|textualIndex|Index odpovědí v textualAnswers k zobrazení.| Celé číslo bez znaménka|
|hodnota|Identifikátor, který identifikuje odpověď zobrazíte nebo položku odpověď zobrazíte. Pokud ID identifikuje odpověď, zobrazení všech položek odpovědi.|Údaje|

### <a name="rankingresponse"></a>RankingResponse  
Definuje, kde na hledání by měl být umístěn obsah stránky výsledků a v jakém pořadí.  
  
|Název|Hodnota|  
|----------|-----------|  
|<a name="ranking-mainline" />mainline|Výsledky hledání pro zobrazení v hlavní linii.|  
|<a name="ranking-pole" />pole|Výsledky hledání, které by měl být poskytnuta nejviditelnější zpracování (například zobrazený nad hlavní linie a boční panel).|  
|<a name="ranking-sidebar" />boční panel|Výsledky hledání pro zobrazení na bočním panelu.| 

### <a name="searchresponse"></a>SearchResponse  
Definuje objekt nejvyšší úrovně, který obsahuje odpověď po úspěšném požadavku.  
  
Všimněte si, že pokud služba má podezření útoku DOS, požadavek bude úspěšné (stavový kód protokolu HTTP je 200 OK); text odpovědi však bude prázdný.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_typ|Pomocný parametr typu, který je nastaven na SearchResponse.|Řetězec|  
|Místa|Seznam entit, které odpovídají vyhledávacímu dotazu.|JSON – objekt|  
|QueryContext|Objekt, který obsahuje řetězec dotazu, který používá Bing pro daný požadavek.<br /><br /> Tento objekt obsahuje řetězec dotazu jako zadané uživatelem. Může také obsahovat řetězec upravený dotaz, který Bingu použitá pro dotaz, pokud řetězec dotazu obsahuje pravopisné chyby.|[QueryContext](#querycontext)|  


## <a name="error-codes"></a>Kódy chyb

Tady jsou možné stavové kódy HTTP, které vrátí žádost o.  
  
|Stavový kód|Popis|  
|-----------------|-----------------|  
|200|Úspěch|  
|400|Jeden z parametrů dotazu je chybí nebo není platný.|  
|401|Klíč předplatného chybí nebo není platný.|  
|403|Ověření uživatele (například používají klíče platným předplatným), ale nemají oprávnění k požadovanému prostředku.<br /><br /> Bing může také vracet tento stav překročení volající jejich dotazů za měsíc kvóty.|  
|410|Požadavek na používá protokol HTTP místo protokolu HTTPS. HTTPS je jediný podporovaný protokol.|  
|429|Volající překročení jejich dotazů za druhé kvóty.|  
|500|Neočekávané chybě serveru.|

Pokud požadavek selže, obsahuje odpověď [ErrorResponse](#errorresponse) objektu, který obsahuje seznam [chyba](#error) objekty, které popisují, co způsobilo chybu. Pokud se chyba, vztahuje k parametru, `parameter` pole určuje parametr, který je problém. A pokud se chyba, se vztahuje na hodnotu parametru `value` pole označuje hodnotu, která není platná.

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

Jsou následující hodnoty možnou chybu kódu a dílčí chyba kódu.

|Kód|Podřízeného|Popis
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Neimplementováno|Stavový kód protokolu HTTP je 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokováno|Bing vrátí InvalidRequest pokaždé, když libovolnou část žádosti není platný. Například povinný parametr chybí nebo není platná hodnota parametru.<br/><br/>Pokud je chyba ParameterMissing nebo ParameterInvalidValue, je stavový kód HTTP 400.<br/><br/>Pokud používáte protokol HTTP místo protokolu HTTPS, Bing vrátí HttpNotAllowed a je stavový kód HTTP 410.
|RateLimitExceeded|Žádné dílčí kódy|Bing vrátí RateLimitExceeded pokaždé, když překročíte dotazů za sekundu (QPS) nebo dotazů za měsíc (QPM) kvóty.<br/><br/>Pokud překročíte QPS, Bing, vrátí stavový kód HTTP 429 a pokud překročíte QPM, Bing vrátí 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing vrátí InvalidAuthorization při Bingu se nemůže ověřit volající. Například `Ocp-Apim-Subscription-Key` záhlaví chybí nebo není platný klíč předplatného.<br/><br/>Redundance nastane, pokud zadáte více než jednu metodu ověřování.<br/><br/>Pokud je chyba InvalidAuthorization, je stavový kód HTTP 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing vrátí InsufficientAuthorization volající nemá oprávnění k přístupu k prostředku. Tato situace může nastat, pokud klíč předplatného se zakázalo, nebo vypršela platnost. <br/><br/>Pokud je chyba InsufficientAuthorization, je stavový kód HTTP 403.

## <a name="next-steps"></a>Další postup
- [Rychlý start místní firmy vyhledávání](quickstarts/local-quickstart.md)
- [Rychlý start místní firmy hledání Java](quickstarts/local-search-java-quickstart.md)
- [Rychlý start místní uzel hledat firmy](quickstarts/local-search-node-quickstart.md)
- [Rychlý start místní firmy hledání Pythonu](quickstarts/local-search-python-quickstart.md)