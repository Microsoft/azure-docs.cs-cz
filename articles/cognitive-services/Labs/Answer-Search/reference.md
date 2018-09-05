---
title: Hledání odpovědí odkaz – Microsoft Cognitive Services projektu | Dokumentace Microsoftu
description: Referenční informace pro koncový bod hledání odpovědí projektu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 28a73918b50d7b13248fe5b6a17f2c95287a1ba4
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666284"
---
# <a name="project-answer-search-v7-reference"></a>Odkaz na projekt hledání odpovědí v7

Bing odpovědí SearchAPI přebírá parametr dotazu a vrátí `searchResponse` s `answerType`: `facts` nebo `entities`. 

Aplikace, které používají rozhraní API pro hledání odpovědí odesílat žádosti do koncového bodu s adresou URL pro náhled v parametru dotazu.  Musí zahrnovat požadavek `q=searchTerm` parametr a *Ocp-Apim-Subscription-Key* záhlaví.   

Odpověď JSON může být analyzován pro fakty a entit, které obsahují podrobnosti o objektu služby search.

## <a name="endpoint"></a>Koncový bod
Požádat o výsledcích hledání odpovědí, odesílejte požadavek na následující koncový bod. Použijte k definování další specifikace hlaviček a parametrů adresy URL.

Koncový bod GET: 
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=<searchTerm>&subscription-key=0123456789ABCDEF&mkt=en-us

````

Žádost musí používat protokol HTTPS a obsahovat následující parametr dotazu:
-  q =<URL> -dotaz, který určuje objekt služby search

Příklady, které ukazují, jak vytvářet požadavky najdete v tématu [jazyka C# quickstart](c-sharp-quickstart.md) nebo [Java quickstart](java-quickstart.md). 

Následující části obsahují technické podrobnosti o odpovědi objekty, parametry dotazů a hlaviček, které ovlivňují výsledky hledání. 
  
Informace o hlavičky, které by měl obsahovat požadavky najdete v tématu [záhlaví](#headers).  
  
Informace o parametrech dotazů, které by měl obsahovat požadavky najdete v tématu [parametrů dotazu](#query-parameters).  
  
Informace o JSON objektů, že odpověď obsahuje, naleznete v tématu [objekty odpovědi](#response-objects).

Dotaz maximální délka adresy URL je 2 048 znaků. Aby bylo zajištěno, že vaše délka adresy URL nepřekračuje limit, maximální délka parametry dotazu musí být menší než 1 500 znaků. Pokud adresa URL je delší než 2 048 znaků, server vrátí 404 Nenalezeno.  

Informace o povolených použití a zobrazení výsledků najdete v tématu [použít a zobrazit požadavky](use-display-requirements.md). 

> [!NOTE]
> Některé hlavičky žádosti, které mají smysl pro jiná rozhraní API pro hledání nemají vliv na Náhled adresy URL
> - Pragma – volající nemá řídit, jestli ve verzi Preview se adresy URL používá mezipaměť
> - Cache-Control – volající nemá řídit, jestli ve verzi Preview se adresy URL používá mezipaměť
> - Uživatelský Agent

> Také některé parametry nejsou aktuálně smysl pro adresu URL ve verzi Preview rozhraní API, ale mohou být použity v budoucnosti pro lepší globalizace. 
 
## <a name="headers"></a>Záhlaví  
Níže jsou hlavičky, které mohou zahrnovat požadavek a odpověď.  
  
|Záhlaví|Popis|  
|------------|-----------------|  
|Přijmout|Hlavička požadavku volitelné.<br /><br /> Výchozí typ média je application/json. Určující, že odpověď [JSON-LD](http://json-ld.org/), nastavit hlavičku Accept application/ld + json.|  
|<a name="acceptlanguage" />Přijměte jazyka|Hlavička požadavku volitelné.<br /><br /> Čárkami oddělený seznam jazyků pro řetězce uživatelského rozhraní. V seznamu je v sestupném pořadí podle priority. Další informace, včetně očekávaném formátu najdete v části [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Tato hlavička a [setLang](#setlang) parametr dotazu se vzájemně vylučují&mdash;nezadávejte obě.<br /><br /> Pokud nastavíte tuto hlavičku, musíte zadat také [kopie](#cc) parametr dotazu. K určení na trhu a vracení výsledků pro Bing používá první podporované jazykové vyhledá v seznamu a sloučí ji `cc` hodnotu parametru. Pokud seznam neobsahuje podporovaném jazyce, Bingu vyhledá nejbližší jazyka a, který podporuje žádosti nebo použije agregované nebo výchozí trhu pro výsledky. Pokud chcete zjistit, který používá Bing, naleznete v tématu BingAPIs trh záhlaví.<br /><br /> Tuto hlavičku používají a `cc` parametr dotazu, pouze pokud zadáte více jazyků. Jinak použijte [mkt](#mkt) a [setLang](#setlang) parametrů dotazu.<br /><br /> Řetězec uživatelského rozhraní je řetězec, který se používá jako popisek v uživatelském rozhraní. V odpovědi objekty JSON je několik řetězců uživatelského rozhraní. Odkazy na vlastnosti Bing.com v odpovědi objektů použít zadaný jazyk.|  
|<a name="market" />BingAPIs trh|Hlavička odpovědi.<br /><br /> Na trhu použít v požadavku. Formulář je \<languageCode\>-\<countryCode\>. Například en US.|  
|<a name="traceid" />BingAPIs TraceId|Hlavička odpovědi.<br /><br /> ID položky protokolu, který obsahuje podrobné informace o požadavku. Pokud dojde k chybě, zachycení číslem ID této. Pokud nejste schopni zjistit a vyřešte problém, patří toto ID spolu s dalšími informacemi, že zadáte na tým podpory.|  
|<a name="subscriptionkey" />OCP-Apim-Subscription-Key|Hlavička požadavku vyžaduje.<br /><br /> Klíč předplatného, který jste dostali při registraci pro tuto službu v [služeb Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Direktiva pragma|Volitelné hlavičky<br /><br /> Ve výchozím nastavení Bing vrátí obsah uložený v mezipaměti, pokud je k dispozici. Abyste zabránili Bing vrátí obsah uložený v mezipaměti, hlavičku – direktiva Pragma nastavit na hodnotu no-cache (například – direktiva Pragma: no-cache).
|<a name="useragent" />Uživatelský Agent|Hlavička požadavku volitelné.<br /><br /> Uživatelský agent původní požadavek. Bing používá uživatelský agent mobilním uživatelům poskytnout prostředí optimalizované. I když je volitelné, jsou ukončena. doporučujeme vždy zadejte toto záhlaví.<br /><br /> Uživatelský agent by měl být stejný řetězec, který odešle všechny běžně používané prohlížeč. Informace o agentech uživatele najdete v tématu [dokumentu RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Následují příklady řetězců uživatelského agenta.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibilní; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Dotykové ovládání; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 sestavení/PERNÍK) AppleWebKit/533.1 (KHTML; prostředí Gecko, jako je) mobilní verze/4.0 Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; Operační systém Iphonu 6_1 procesoru jako Mac OS X) AppleWebKit/536.26 (KHTML; prostředí Gecko, jako je) iPhone4 mobilní/10B142; 1 BingWeb/3.03.1428.20120423<br /><br /></li><li>Počítač&mdash;Mozilla/5.0 (Windows NT 6.3; SUBSYSTÉM WOW64; Trident/7.0; Dotykové ovládání; Rv:11.0) prostředí Gecko, jako jsou<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; 7_0 procesoru operačního systému jako Mac OS X) AppleWebKit/537.51.1 (jako je prostředí gecko, jež KHTML) verze 7.0 nebo mobilní/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge ClientID|Volitelné záhlaví žádostí a odpovědí.<br /><br /> Bing používá tuto hlavičku uživatelům poskytnout konzistentní chování napříč volání rozhraní API Bingu. Bing často letů nových funkcí a vylepšení a používá ID klienta jako klíč pro přiřazení provoz na jiné lety. Pokud je velmi riskantní používat stejné ID klienta pro uživatele mezi více požadavků, pak Bingu může přiřadit uživatele k více konfliktních lety. Přiřazení na více konfliktních lety může vést k nekonzistentním uživatelské prostředí. Například pokud druhá žádost o přiřazení jiné cestě než první, prostředí může neočekávané. Navíc můžete použít ID klienta pro přizpůsobení webové výsledky pro tohoto klienta Bingu ID historie hledání, poskytuje pohodlnější a pestřejší prostředí pro uživatele.<br /><br /> Bing používá této hlavičky také jí pomohou zlepšit výsledek hodnocení díky analýze aktivitu generovanou podle ID klienta. Zlepšení relevance pomoct s lepší kvalitou výsledků přednesl rozhraní API Bingu a zase umožňuje vyšší proklikávání sazby pro rozhraní API příjemců.<br /><br /> **Důležité:** nepovinný, měli byste zvážit této hlavičky vyžaduje. Zachování ID klienta mezi více požadavků pro stejný koncový uživatel a kombinaci zařízení povolí (1) na rozhraní API příjemce pro příjem konzistentní uživatelské prostředí a (2) vyšší míru proklikávání prostřednictvím kvalitnější výsledky z rozhraní API Bingu.<br /><br /> Tady jsou základní informace o využití pravidel, které se vztahují k této hlavičce.<br /><ul><li>Každý uživatel, který používá vaše aplikace v zařízení musí mít jedinečný, Bing, vygeneruje ID klienta.<br /><br/>Pokud neuvedete této hlavičky v požadavku, Bingu vygeneruje ID a vrátí jej v hlavičce X-MSEdge ClientID odpovědi. Že by měl nezahrnuje této hlavičky v požadavku se pouze první uživatel použije aplikaci na daném zařízení.<br /><br/></li><li>Použijte ID klienta pro každé rozhraní API Bingu pro žádosti, která vaše aplikace provádí pro tohoto uživatele na zařízení.<br /><br/></li><li>**Pozor:** musíte zajistit, že toto ID klienta není propojovací informací authenticatable uživatelského účtu.</li><br/><li>Zachovat ID klienta. Pokud chcete zachovat ID v aplikaci prohlížeče, ujistěte se, že ID se používá ve všech relacích pomocí trvalého souboru cookie HTTP. Nepoužívejte souboru cookie relace. Pro jiné aplikace, jako jsou například mobilní aplikace použijte k uchování ID. trvalého úložiště zařízení<br /><br/>Při příštím uživatel použije aplikaci na zařízení, získejte ID klienta, který je trvalé.</li></ul><br /> **Poznámka:** odpovědi Bingu může nebo nemusí obsahovat tato záhlaví. Pokud odpověď obsahuje tato záhlaví, ID klienta zachytit a používat ho pro všechny následné požadavky Bing pro uživatele na tomto zařízení.<br /><br /> **Poznámka:** Pokud zahrnete X MSEdge ClientID, nesmí obsahovat soubory cookie v požadavku.|  
|<a name="clientip" />X-MSEdge ClientIP|Hlavička požadavku volitelné.<br /><br /> Adresa IPv4 nebo IPv6 klienta zařízení. IP adresa se používá ke zjišťování podle umístění uživatele. Bing používá k určení chování bezpečného hledání informace o poloze.<br /><br /> **Poznámka:** nepovinný vám doporučujeme, aby se vždy zadejte toto záhlaví a záhlaví X-Search-umístění.<br /><br /> Není obfuskaci adresu (například tak, že změníte poslední oktet na hodnotu 0). Že maskuje výsledky adres v umístění nebude kdekoli v zařízení aktuální umístění, což může vést k Bingu obsluhující chybné výsledky.|  
|<a name="location" />X-Search umístění|Hlavička požadavku volitelné.<br /><br /> Seznam oddělený středníkem páry klíč/hodnota, které popisují zeměpisné umístění klienta. Bing používá informace o poloze a určit chování bezpečného hledání vrátí odpovídající místní obsah. Zadejte pár klíč/hodnota jako \<klíč\>:\<hodnota\>. Níže jsou klíče, které se používá k určení umístění uživatele.<br /><br /><ul><li>LAT&mdash;zeměpisná šířka umístění klienta ve stupních. Zeměpisná šířka musí být větší než nebo rovna hodnotě-90.0 a menší než nebo rovna +90.0. Záporné hodnoty značí Jižní zeměpisná šířka a kladné hodnoty značí severní meze.<br /><br /></li><li>dlouhé&mdash;zeměpisná délka umístění klienta ve stupních. Zeměpisná délka musí být větší než nebo rovna hodnotě-180.0 a menší než nebo rovna +180.0. Záporné hodnoty značí západní délka a kladné hodnoty značí východní délka.<br /><br /></li><li>RE&mdash; radius, v metrech, který určuje vodorovný přesnost souřadnic. Předejte hodnotu vrácené službou umístění zařízení. Typické hodnoty mohou být 22m pro GPS/Wi-Fi, 380m triangulace tower buňky a 18 000 m pro reverzního vyhledávání IP adresy.<br /><br /></li><li>TS&mdash; The UTC UNIXOVÉ časové razítko z když klient se v umístění. (UNIXOVÉ časové razítko je počet sekund od 1. ledna 1970.)<br /><br /></li><li>hlavní&mdash;volitelné. Relativní záhlaví nebo směr cesty klienta. Určení směru cestovní jako stupních od 0 do 360, počítací po směru hodinových ručiček vzhledem k true – sever. Zadejte tento klíč pouze tehdy, pokud `sp` klíč není nenulová.<br /><br /></li><li>SP&mdash; vodorovné rychlost (rychlost), v metrech za sekundu, cestování klientského zařízení.<br /><br /></li><li>ALT&mdash; výška klientské zařízení, v metrech.<br /><br /></li><li>jsou&mdash;volitelné. Poloměr, v metrech, určující přesnost svislé souřadnice. Výchozí hodnota protokolu RADIUS je 50 kilometrů. Zadejte tento klíč jenom v případě, že zadáte `alt` klíč.<br /><br /></li></ul> **Poznámka:** i když tyto klíče jsou volitelné, další informace, které zadáte, jsou přesnější výsledky umístění.<br /><br /> **Poznámka:** doporučujeme vždy zadat zeměpisné umístění uživatele. Poskytuje umístění je obzvláště důležité, pokud IP adresa klienta neodráží přesně podle fyzického umístění uživatele (například pokud klient používá síť VPN). Pro dosažení optimálních výsledků by měl obsahovat tato záhlaví a záhlaví X-MSEdge ClientIP, ale minimálně, měli byste zahrnout tuto hlavičku.|

> [!NOTE] 
> Mějte na paměti, podmínky použití vyžadovat dodržování předpisů s příslušnými zákony, včetně týkající se použití těchto hlaviček. Například v určitých jurisdikce, jako jsou Evropa, existují požadavky na získat souhlas uživatele před uvedením určitá sledování zařízení na zařízení uživatelů.
  

## <a name="query-parameters"></a>Parametry dotazu  
Žádost mohou zahrnovat tyto parametry dotazu. Zobrazte požadovaný sloupec pro požadované parametry. Adresa URL musíte zakódovat parametry dotazu.  
  
  
|Název|Hodnota|Typ|Požaduje se|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Na trhu, odkud pochází výsledky. <br /><br />Seznam možných hodnot na trhu, najdete v tématu [trhu kódy](#market-codes).<br /><br /> **Poznámka:** rozhraní API ve verzi Preview URL aktuálně podporuje pouze en-nám vstup na trh a jazyk.<br /><br />|Řetězec|Ano|  
|<a name="query" />q|Adresa URL ve verzi preview|Řetězec|Ano|  
|<a name="responseformat" />Formát odpovědi|Typ média určený pro odpověď. Níže jsou uvedeny možné hodnoty velká a malá písmena.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Výchozí hodnota je JSON. Informace o JSON objektů, že odpověď obsahuje, naleznete v tématu [objekty odpovědi](#response-objects).<br /><br />  Pokud chcete zadat JsonLd, tělo odpovědi obsahuje objekty JSON-LD, obsahující výsledky hledání. Informace o JSON-LD, naleznete v tématu [JSON-LD](http://json-ld.org/).|Řetězec|Ne|  
|<a name="safesearch" />bezpečné hledání|Filtr, který se používá k filtrování obsahu pro dospělé. Jsou následující hodnoty možné filtrovat velká a malá písmena.<br /><ul><li>Vypnout&mdash;vrátit webových stránek s dospělé textu, obrázků nebo videa.<br /><br/></li><li>Střední&mdash;vrátit webových stránek s dospělé text, ale nezletilý imagí nebo videa.<br /><br/></li><li>Striktní&mdash;nevrátí webových stránek s dospělé textu, obrázků nebo videa.</li></ul><br /> Výchozí hodnota je střední.<br /><br /> **Poznámka:** Pokud žádost pochází z na trhu této Bing pro dospělé zásad vyžaduje, aby `safeSearch` je nastavena na Strict, ignoruje Bing `safeSearch` hodnotu a používá Strict.<br/><br/>**Poznámka:** používáte `site:` – operátor dotazu, je pravděpodobné, že odpovědi může obsahovat obsah pro dospělé bez ohledu na to, co `safeSearch` parametr dotazu je nastaven na. Použití `site:` pouze v případě, že si uvědomujete obsah na webu a váš scénář podporuje možnost obsahu pro dospělé. |Řetězec|Ne|  
|<a name="setlang" />setLang|Jazyk, který se má použít pro uživatelské rozhraní řetězce. Zadejte jazyk pomocí kód ISO 639-1 písmeno 2 jazyka. Kód jazyka angličtina je například cs. Výchozí hodnota je EN (v angličtině).<br /><br /> I když je volitelné, musíte vždycky zadat jazyk. Obvykle nastavena `setLang` ve stejném jazyce určeném `mkt` Pokud chce uživatel řetězců uživatelského rozhraní, které jsou zobrazeny v jiném jazyce.<br /><br /> Tento parametr a [Accept-Language](#acceptlanguage) záhlaví se vzájemně vylučují&mdash;nezadávejte obě.<br /><br /> Řetězec uživatelského rozhraní je řetězec, který se používá jako popisek v uživatelském rozhraní. V odpovědi objekty JSON je několik řetězců uživatelského rozhraní. Odkazy na vlastnosti Bing.com v objektech odpovědi platí také, zadaný jazyk.|Řetězec|Ne| 


## <a name="response-objects"></a>Objekty odpovědi  
Schéma odpovědi je buď [webové stránky] nebo ErrorResponse, stejně jako v hledání webového rozhraní API. Pokud požadavek selže, je objekt nejvyšší úrovně [ErrorResponse](#errorresponse) objektu.


|Objekt|Popis|  
|------------|-----------------|  
|[Webové stránky]|Nejvyšší úroveň objektu JSON, který obsahuje atributy ve verzi preview.|  
|[Fakt]|Nejvyšší úrovně objekt JSON, který obsahuje fakta.| 
|[Entity|Nejvyšší úroveň objektu JSON, který obsahuje podrobnosti o entitách.| 

  
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
|<a name="errors" />Chyby|Seznam chyb, které popisují důvody, proč žádost selhala.|[Chyba](#error)|  

  
  
### <a name="license"></a>Licence  
Definuje licence, pod kterým mohou být použity text nebo fotografie.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|jméno|Název licence.|Řetězec|  
|url|Adresa URL webu, kde uživatel získat další informace o licenci.<br /><br /> Vytvoření hypertextového odkazu, použijte název a adresu URL.|Řetězec|  
  

### <a name="licenseattribution"></a>LicenseAttribution  
Definuje smluvní pravidlo pro přiřazení licence.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_typ|Typ pomocného parametru, která je nastavena na LicenseAttribution.|Řetězec|  
|licence|Licence, pod kterým mohou být použity obsah.|[Licence](#license)|  
|licenseNotice|Licence, který se zobrazí vedle cílové pole. Například "Text v rámci licence kopie SA".<br /><br /> Použijte název a adresu URL licenci `license` pole, které chcete vytvořit hypertextový odkaz na web, který popisuje podrobnosti o licenci. Potom nahraďte název licence v `licenseNotice` řetězec (například CC-podle-SA) s odkazem, který jste právě vytvořili.|Řetězec|  
|mustBeCloseToContent|Logická hodnota, která určuje, zda obsah pravidlo musí být umístěn v blízkosti pole, které se pravidlo vztahuje. Pokud **true**, obsah musí být umístěn v těsné blízkosti. Pokud **false**, nebo tato pole neexistuje, můžete umístit obsah na základě vlastního uvážení volajícího.|Logická hodnota|  
|targetPropertyName|Název pole, které se pravidlo vztahuje.|Řetězec|  
  

### <a name="link"></a>Odkaz  
Definuje komponenty hypertextový odkaz.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_typ|Pomocný parametr typu.|Řetězec|  
|text|Zobrazení textu.|Řetězec|  
|url|ADRESA URL. Použijte adresu URL a zobrazit text Vytvoření hypertextového odkazu.|Řetězec|  
  

### <a name="linkattribution"></a>LinkAttribution  
Definuje pravidlo smluvní pro přidělení odkazu.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_typ|Typ pomocného parametru, která je nastavena na LinkAttribution.|Řetězec|  
|mustBeCloseToContent|Logická hodnota, která určuje, zda obsah pravidlo musí být umístěn v blízkosti pole, které se pravidlo vztahuje. Pokud **true**, obsah musí být umístěn v těsné blízkosti. Pokud **false**, nebo tato pole neexistuje, můžete umístit obsah na základě vlastního uvážení volajícího.|Logická hodnota|  
|targetPropertyName|Název pole, které se pravidlo vztahuje.<br /><br /> Pokud cíl není zadán, platí pro entity jako celek přidělení a měla by se zobrazit okamžitě po prezentace entity. Pokud existuje více text a odkaz attribution pravidla, která není zadejte cíl, by je zřetězit a jejich zobrazení pomocí "Data z:" popisek. Například "Data z < název1 poskytovatele\> &#124; < name2 poskytovatele\>".|Řetězec|  
|text|Attribution text.|Řetězec|  
|url|Adresa URL webu poskytovatele. Použití `text` a adresu URL pro vytvoření hypertextového odkazu.|Řetězec|  
  
  
### <a name="mediaattribution"></a>MediaAttribution  
Definuje smluvní pravidlo pro attribution média.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_typ|Typ pomocného parametru, která je nastavena na MediaAttribution.|Řetězec|  
|mustBeCloseToContent|Logická hodnota, která určuje, zda obsah pravidlo musí být umístěn v blízkosti pole, které se pravidlo vztahuje. Pokud **true**, obsah musí být umístěn v těsné blízkosti. Pokud **false**, nebo tato pole neexistuje, můžete umístit obsah na základě vlastního uvážení volajícího.|Logická hodnota|  
|targetPropertyName|Název pole, které se pravidlo vztahuje.|Řetězec|  
|url|Adresa URL, který použijete k vytvoření hypertextového odkazu mediálního obsahu. Například pokud je cílem bitovou kopii, použijete adresu URL k pořízení snímku po kliknutí.|Řetězec|  
  
  
  
### <a name="organization"></a>Organizace  
Definuje vydavatele.  
  
Všimněte si, že vydavatel může zadat jeho název nebo jejich webu nebo obojí.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|jméno|Název vydavatele.|Řetězec|  
|url|Adresa URL webu vydavatele.<br /><br /> Všimněte si, že vydavatel nemusí poskytnout webu.|Řetězec|  
  
  

### <a name="webpage"></a>Webová stránka  
Definuje informace o webové stránce ve verzi preview.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|
|jméno|Název stránky, ne tedy nutně Nadpis HTML|Řetězec|
|url|Adresu URL, kterou byl ve skutečnosti procházen (žádosti může provedli přesměrování)|Řetězec|  
|description|Stručný popis stránky a obsahu|Řetězec|  
|isFamilyFriendly|Co nejvíce zpřesnili pro položky v rejstříku web. Tato detekce založené výhradně na adresu URL a obsah stránky se načte v reálném čase|Boolean|
|primaryImageOfPage/contentUrl|Adresa URL reprezentativní image zahrnout ve verzi preview|Řetězec| 
  
  
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
|Webová stránka|Objekt JSON, který definuje verzi preview|řetězec|  
  
  
### <a name="textattribution"></a>TextAttribution  
Definuje pravidlo smluvní pro attribution prostý text.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_typ|Typ pomocného parametru, která je nastavena na TextAttribution.|Řetězec|  
|text|Attribution text.<br /><br /> Text attribution platí pro entity jako celek a měla by se zobrazit okamžitě po prezentace entity. Pokud existuje více textu nebo odkazů attribution pravidla, která není zadejte cíl, by měl je zřetězit a jejich zobrazení pomocí "Data z:" popisek.|Řetězec| 


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
- [Rychlý start C#](c-sharp-quickstart.md)
- [Rychlý start pro Javu](java-quickstart.md)
- [Rychlý start uzlu](node-quickstart.md)
- [Rychlý start Pythonu](python-quickstart.md)

