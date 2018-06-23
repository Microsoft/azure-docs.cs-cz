---
title: Vyhledávání odpovědí odkaz - kognitivní služby společnosti Microsoft na projekt | Microsoft Docs
description: Referenční informace pro koncový bod hledání odpovědí projektu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 8c95fac0c031ec62a9d98d6c3278bd3b3f345140
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343292"
---
# <a name="project-answer-search-v7-reference"></a>Odkaz na projekt odpovědí vyhledávání v7

Bing odpovědí SearchAPI přebírá parametr dotazu a vrátí `searchResponse` s `answerType`: `facts` nebo `entities`. 

Aplikace, které používají rozhraní API pro vyhledávání odpovědí odesílat požadavky na koncový bod s adresou URL náhled v parametru dotazu.  Musí zahrnovat požadavek `q=searchTerm` parametr a *Ocp-Apim-Subscription-Key* záhlaví.   

Pro fakty a entity, které obsahují podrobnosti o objektu vyhledávání lze analyzovat odpověď JSON.

## <a name="endpoint"></a>Koncový bod
O výsledky hledání odpovědí odeslat požadavek na následující koncový bod. Můžete definovat další specifikace pomocí hlaviček a parametrů adresy URL.

Koncový bod GET: 
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=<searchTerm>&subscription-key=0123456789ABCDEF&mkt=en-us

````

Žádost musí používat protokol HTTPS a zahrnují následující parametr dotazu:
-  q =<URL> -dotaz, který určuje objekt hledání

Příklady, které ukazují, jak provádět požadavky najdete v tématu [C# rychlý Start](c-sharp-quickstart.md) nebo [rychlý start Java](java-quickstart.md). 

Následující části obsahují technické podrobnosti o objektech odpovědi, parametry dotazu a hlaviček, které ovlivňují výsledky hledání. 
  
Informace o hlavičkách, které by měly obsahovat požadavky najdete v tématu [hlavičky](#headers).  
  
Informace o parametrech dotazů, které by měly obsahovat požadavky najdete v tématu [parametrů dotazu](#query-parameters).  
  
Informace o formátu JSON objekty, že odpověď obsahuje, najdete v části [odpovědi objekty](#response-objects).

Délka adresy URL maximální dotazu je 2 048 znaků. Aby se zajistilo, že vaše délka adresy URL není delší než tento limit, maximální délka parametry dotazu by měla být menší než 1 500 znaků. Pokud adresa URL delší než 2 048 znaků, server vrátí 404 nebyl nalezen.  

Informace o povolených použití a zobrazení výsledků najdete v tématu [používat a zobrazit požadavky](use-display-requirements.md). 

> [!NOTE]
> Některé hlavičky žádosti, které jsou srozumitelné pro ostatní rozhraní API pro vyhledávání neovlivňuje Náhled adresy URL
> - Pragma – volající nemá kontrolu nad jestli Náhled adresy URL používá mezipaměť
> - Cache-Control – volající nemá kontrolu nad jestli Náhled adresy URL používá mezipaměť
> - Uživatelský Agent

> Navíc některé parametry nejsou aktuálně smysl pro adresu URL náhledu API, ale mohou být použity v budoucnosti pro vylepšené globalizace. 
 
## <a name="headers"></a>Záhlaví  
Následuje seznam hlaviček, které mohou zahrnovat žádostí a odpovědí.  
  
|Záhlaví|Popis|  
|------------|-----------------|  
|Přijmout|Hlavička požadavku volitelné.<br /><br /> Výchozí typ média je application/json. Chcete-li určit, že odpověď používat [JSON-LD](http://json-ld.org/), nastavit hlavičku Accept aplikace nebo ld + json.|  
|<a name="acceptlanguage" />Přijměte jazyk|Hlavička požadavku volitelné.<br /><br /> Čárkami oddělený seznam jazyků, které chcete použít pro uživatelské rozhraní řetězce. V seznamu je v sestupném pořadí podle priority. Další informace, včetně očekává se formát, najdete v části [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Tuto hlavičku a [setLang](#setlang) parametr dotazu se vzájemně vylučují&mdash;nezadávejte i.<br /><br /> Pokud nastavíte tuto hlavičku, musíte zadat také [kopie](#cc) parametr dotazu. K určení trhu k vracení výsledků pro, používá Bing první podporované jazykové ho najde ze seznamu a její kombinuje `cc` hodnotu parametru. Pokud seznam neobsahuje podporovaném jazyce, Bing vyhledá nejbližší jazyk a trhu, který podporuje požadavku nebo používá agregované nebo výchozí trhu pro dané výsledky. Na trhu, který používá Bing, zjistíte v hlavičce BingAPIs trhu.<br /><br /> Tuto hlavičku používají a `cc` parametr dotazu, pouze pokud zadáte více jazyků. Jinak použijte [mkt](#mkt) a [setLang](#setlang) parametrů dotazu.<br /><br /> Řetězec uživatelského rozhraní je řetězec, který se používá jako popisek v uživatelském rozhraní. V odpovědi objekty JSON nejsou několik řetězců uživatelského rozhraní. Všechny odkazy na vlastnosti vyhledávače Bing.com v objektech odpovědi použít určený jazyk.|  
|<a name="market" />BingAPIs trhu|Hlavička odpovědi.<br /><br /> Na trhu používané žádosti. Formulář je \<languageCode\>-\<countryCode\>. Například en US.|  
|<a name="traceid" />BingAPIs TraceId|Hlavička odpovědi.<br /><br /> ID položky protokolu, který obsahuje podrobnosti o žádosti. Když dojde k chybě, zachytit číslem ID této. Pokud si nejste schopni zjistit a vyřešte problém, zahrnují toto ID spolu s další informace, že zadáte tým podpory.|  
|<a name="subscriptionkey" />OCP-Apim-Subscription-Key|Hlavička požadované požadavku.<br /><br /> Klíč předplatného, který jste obdrželi při registraci pro tuto službu v [kognitivní služby](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Direktiva pragma|Hlavička volitelné požadavku<br /><br /> Ve výchozím nastavení Bing vrátí obsah uložený v mezipaměti, pokud je k dispozici. Chcete-li Bing nedošlo k vrácení obsahu v mezipaměti, nastavte na hodnotu no cache hlavičku – direktiva Pragma (například – direktiva Pragma: Ne mezipaměti).
|<a name="useragent" />Uživatelský Agent|Hlavička požadavku volitelné.<br /><br /> Uživatelský agent pocházející žádosti. K poskytování mobilní uživatele optimalizované prostředí používá Bing uživatelský agent. I když je volitelné, vám doporučujeme, aby se vždycky zadat tuto hlavičku.<br /><br /> Uživatelský agent by měl být stejný řetězec, který odešle jakýkoli prohlížeč pro běžně používané. Informace o Uživatelští agenti najdete v tématu [dokumentu RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Následují příklady řetězců uživatelského agenta.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibilní; MSIE 10.0; Windows Phone 8.0; Trident nebo 6.0; IEMobile/10.0; ARM; Dotykové ovládání; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 sestavení nebo PERNÍK) AppleWebKit/533.1 (KHTML; prostředí Gecko, jako) verze nebo 4.0 Mobile Safari nebo 533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; Procesor 6_1 iPhone operačního systému jako Mac OS X) AppleWebKit/536.26 (KHTML; prostředí Gecko, jako) mobilní nebo 10B142 iPhone4; 1 BingWeb/3.03.1428.20120423<br /><br /></li><li>Počítač&mdash;Mozilla/5.0 (systém Windows NT 6.3; SUBSYSTÉM WOW64; Trident/7.0; Dotykové ovládání; Rv:11.0) prostředí Gecko, jako<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; 7_0 procesoru operačního systému jako Mac OS X) AppleWebKit/537.51.1 (jako jsou prostředí Gecko KHTML) verze 7.0 nebo mobilní nebo 11A465 Safari nebo 9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Volitelné hlavička požadavku a odpovědi.<br /><br /> Bing používá tuto hlavičku zajišťuje uživatelům konzistentní chování napříč volání rozhraní API služby Bing. Bing často letů nové funkce a vylepšení a jako klíč pro přiřazení provoz na jiné letech používá ID klienta. Pokud nepoužijete stejný ID klienta pro uživatele pro víc požadavků, pak Bing může přiřadit uživatele k několika lety konfliktní. Přiřazení k několika lety konfliktní může vést k nekonzistentním uživatelské prostředí. Například pokud má druhý požadavek přiřazení jiné cestě než první, prostředí může neočekávaná. Navíc můžete použít ID klienta pro přizpůsobení webové výsledky pro tohoto klienta Bing ID, která historii hledání, poskytování a pestřejší prostředí pro uživatele.<br /><br /> Tuto hlavičku Bing také používá k vylepšení výsledek hodnocení analýzou aktivitu generovanou podle ID klienta. Vylepšení relevance pomůže s lepší kvality výsledků doručeny podle rozhraní API pro Bing a zase umožňuje vyšší kliknutí sazby pro příjemce rozhraní API.<br /><br /> **Důležité:** i když je volitelné, měli byste zvážit tuto hlavičku vyžaduje. Uložením ID klienta pro víc požadavků pro stejnou koncových uživatelů a zařízení kombinace umožňuje 1) příjemce rozhraní API pro příjem konzistentní podmínky koncového uživatele a sazby 2) vyšší kliknutí prostřednictvím lepší kvalitu výsledky z rozhraní API služby Bing.<br /><br /> Níže jsou uvedeny základní informace o využití pravidla, která se vztahují k této hlavičce.<br /><ul><li>Každý uživatel, který používá vaše aplikace na tomto zařízení musí mít jedinečný, Bing vygeneruje ID klienta.<br /><br/>Pokud neuvedete této hlavičky v požadavku, Bing vygeneruje ID a vrátí ji v záhlaví X-MSEdge-ClientID odpovědi. Jenom jednou, můžete tuto hlavičku obsahovat není v požadavku je prvním uživatel používá aplikaci na daném zařízení.<br /><br/></li><li>Použijte ID klienta pro každého požadavku rozhraní API služby Bing, které vytváří vaše aplikace pro tohoto uživatele na zařízení.<br /><br/></li><li>**Upozornění:** Ujistěte se, že tohoto ID klienta není možné korelovat k nějaké informace o účtu authenticatable uživateli.</li><br/><li>Zachovat ID klienta. Udržení ID v aplikaci Prohlížeč, použijte trvalého souboru cookie HTTP k zajištění, že Identifikátor se používá napříč všechny relace. Nepoužívejte souboru cookie relace. Pro jiné aplikace, jako je například mobilní aplikace použijte trvalého úložiště v zařízení k uchování ID.<br /><br/>Při příštím uživatel používá aplikaci na zařízení, získání ID klienta, který jste jako trvalé.</li></ul><br /> **Poznámka:** Bing odpovědí může nebo nemusí obsahovat hlavičku. Pokud odpověď obsahuje hlavičku, zaznamenat ID klienta a použít jej pro všechny následné žádosti Bingu pro uživatele na tomto zařízení.<br /><br /> **Poznámka:** Pokud zahrnete X-MSEdge-ClientID, nesmí obsahovat soubory cookie v požadavku.|  
|<a name="clientip" />X-MSEdge když|Hlavička požadavku volitelné.<br /><br /> IPv4 nebo IPv6 adresa klientského zařízení. IP adresa se používá ke zjišťování umístění uživatele. Bing používá k určení chování bezpečné vyhledávání informace o umístění.<br /><br /> **Poznámka:** i když je volitelné, vám doporučujeme, aby se vždycky zadat umístění X vyhledávání a tento záhlaví.<br /><br /> Není obfuskováním adresu (například změnou poslední oktet 0). Zmatení data adresu výsledky v umístění nebude kdekoli téměř skutečné umístění zařízení, což může vést k Bing obsluhující chybné výsledky.|  
|<a name="location" />Umístění X vyhledávání|Hlavička požadavku volitelné.<br /><br /> Seznam páry klíč/hodnota, které popisují zeměpisné umístění klienta oddělené středníkem. Bing používá informace o umístění, k určení chování bezpečné vyhledávání a relevantní lokálního obsahu. Zadat dvojice klíč/hodnota jako \<klíč\>:\<hodnotu\>. Níže jsou uvedeny klíčů, které se používá k určení umístění uživatele.<br /><br /><ul><li>LAT&mdash;zeměpisnou šířku umístění klienta ve stupních. Zeměpisnou musí být větší než nebo rovna hodnotě-90.0 a menší než nebo rovna +90.0. Záporné hodnoty stanovují Jižní zeměpisné šířky a kladné hodnoty stanovují severní zeměpisné šířky.<br /><br /></li><li>dlouhé&mdash;zeměpisné délky umístění klienta ve stupních. Zeměpisné délky musí být větší než nebo rovna hodnotě-180.0 a menší než nebo rovna +180.0. Záporné hodnoty stanovují western stupně zeměpisné délky a kladné hodnoty stanovují východní délka.<br /><br /></li><li>RE&mdash; radius, v měřidla, který určuje vodorovné přesnost souřadnice. Předáte hodnoty vrácené služby umístění zařízení. Typické hodnoty může být 22m pro GPS/Wi-Fi, 380m pro buňky věž měny a 18 000 m pro reverzního vyhledávání IP.<br /><br /></li><li>TS&mdash; když klient se v umístění systému UNIX UTC časové razítko. (UNIX časové razítko je počet sekund od 1. ledna 1970.)<br /><br /></li><li>HEAD&mdash;volitelné. Relativní záhlaví nebo směr cesta klienta. Zadejte směr cesta jako od 0 do 360, počítání po směru hodinových ručiček relativně k – true sever stupňů. Zadejte tento klíč jenom Pokud `sp` klíč je nenulové hodnoty.<br /><br /></li><li>SP&mdash; vodorovné rychlosti (rychlost), v měřidla za sekundu, které při cestě klientské zařízení.<br /><br /></li><li>ALT&mdash; výška klientských zařízení, v měřidla.<br /><br /></li><li>jsou&mdash;volitelné. Hodnota úhlu, v měřidla, která určuje svislé přesnost souřadnice. Výchozí hodnota protokolu RADIUS kilometrů 50. Zadejte tento klíč jenom v případě, že zadáte `alt` klíč.<br /><br /></li></ul> **Poznámka:** i když tyto klíče jsou volitelné, další informace, které zadáte, jsou více přesné výsledky umístění.<br /><br /> **Poznámka:** doporučujeme vždycky zadat zeměpisné umístění uživatele. Poskytnutí umístění je obzvláště důležité, pokud IP adresa klienta přesně neodpovídají fyzické umístění uživatele (například pokud klient používá sítě VPN). Pro dosažení optimálních výsledků by měla obsahovat této hlavičky a hlavičky X-MSEdge – když, ale minimálně by měla obsahovat hlavičku.|

> [!NOTE] 
> Mějte na paměti, že podmínky použití vyžadují kompatibilitu s všechny platné zákony, včetně týkající se použití těchto hlaviček. Například v určité oblasti jurisdikce, jako je například Evropa, existují požadavky na získat souhlas uživatele před uvedením určitých sledování zařízení na zařízení uživatelů.
  

## <a name="query-parameters"></a>Parametry dotazu  
Žádost může zahrnovat následující parametry dotazu. V tématu požadovaný sloupec pro požadované parametry. Adresa URL, je potřeba kódování parametry dotazu.  
  
  
|Název|Hodnota|Typ|Požaduje se|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Na trhu, odkud pocházejí výsledky. <br /><br />Seznam možných hodnot trhu najdete v tématu [trhu kódy](#market-codes).<br /><br /> **Poznámka:** rozhraní API Preview URL aktuálně podporuje jenom en-us trhu a jazyk.<br /><br />|Řetězec|Ano|  
|<a name="query" />OTÁZKY|Adresa URL náhled|Řetězec|Ano|  
|<a name="responseformat" />formátu responseFormat|Typ média pro použit pro odpověď. Dále jsou možné hodnoty velká a malá písmena.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Výchozí hodnota je JSON. Informace o formátu JSON objekty, že odpověď obsahuje, najdete v části [odpovědi objekty](#response-objects).<br /><br />  Pokud zadáte JsonLd, zahrnuje text odpovědi JSON-LD objektů, které obsahují výsledky hledání. Informace o JSON-LD najdete v tématu [JSON-LD](http://json-ld.org/).|Řetězec|Ne|  
|<a name="safesearch" />bezpečné hledání|Filtr, který se používá k filtrování obsahu pro dospělé. Jsou následující hodnoty filtru možné velká a malá písmena.<br /><ul><li>Vypnout&mdash;vrátit webové stránky pro dospělé text, obrázky nebo videa.<br /><br/></li><li>Střední&mdash;vrátit webové stránky s pro dospělé text, ale není pro dospělé obrázky nebo videa.<br /><br/></li><li>Striktní&mdash;nevrátí webové stránky pro dospělé text, obrázky nebo videa.</li></ul><br /> Výchozí hodnota je střední.<br /><br /> **Poznámka:** Pokud požadavek pochází z na trhu pro dospělé zásady této Bing vyžaduje, aby `safeSearch` je nastaven na Strict, ignoruje Bing `safeSearch` hodnotu a používá Strict.<br/><br/>**Poznámka:** Pokud použijete `site:` – operátor dotazu, je pravděpodobné, že odpověď může obsahovat obsah pro dospělé bez ohledu na to, co `safeSearch` parametr dotazu je nastavena na. Použití `site:` pouze v případě, že jste si vědomi obsahu v lokalitě a váš scénář podporuje možnost obsah pro dospělé. |Řetězec|Ne|  
|<a name="setlang" />setLang|Jazyk pro použití řetězců uživatelského rozhraní. Zadejte jazyk pomocí kód ISO jazyka písmeno 2 639-1. Kód jazyka pro angličtina je například EN. Výchozí hodnota je EN (angličtina).<br /><br /> I když je volitelné, musíte vždycky zadat jazyk. Obvykle nastavíte `setLang` ve stejném jazyce určeného `mkt` Pokud uživatel chce řetězců uživatelského rozhraní, zobrazí v jiném jazyce.<br /><br /> Tento parametr a [Accept-Language](#acceptlanguage) záhlaví se vzájemně vylučují&mdash;nezadávejte i.<br /><br /> Řetězec uživatelského rozhraní je řetězec, který se používá jako popisek v uživatelském rozhraní. V odpovědi objekty JSON nejsou několik řetězců uživatelského rozhraní. Všechny odkazy na vlastnosti vyhledávače Bing.com v objektech odpovědi vztahovat určený jazyk.|Řetězec|Ne| 


## <a name="response-objects"></a>Objekty odpovědi  
Schéma odpovědi je buď [webové stránky] nebo ErrorResponse jako vyhledávání webového rozhraní API. Pokud se požadavek nezdaří, je objekt nejvyšší úrovně [ErrorResponse](#errorresponse) objektu.


|Objekt|Popis|  
|------------|-----------------|  
|[Webová stránka]|Nejvyšší úrovně objekt JSON obsahující atributy Preview.|  
|[Fakt]|Nejvyšší úrovně objekt JSON, který obsahuje fakta týkající se.| 
|[Entity|Nejvyšší úrovně objekt JSON, který obsahuje podrobnosti o entity.| 

  
### <a name="error"></a>Chyba  
Definuje chybu, která došlo k chybě.  
  
|Element|Popis|Typ|  
|-------------|-----------------|----------|  
|<a name="error-code" />Kód|Kód chyby, který identifikuje kategorie chyby. Seznam možných kódů najdete v tématu [kódy chyb](#error-codes).|Řetězec|  
|<a name="error-message" />Zpráva|Popis chyby.|Řetězec|  
|<a name="error-moredetails" />moreDetails|Popis, který poskytuje další informace o této chybě.|Řetězec|  
|<a name="error-parameter" />Parametr|Parametr dotazu v žádosti, která způsobila chybu.|Řetězec|  
|<a name="error-subcode" />Dílčí|Kód chyby, která identifikuje chybu. Například pokud `code` je InvalidRequest, `subCode` může být ParameterInvalid nebo ParameterInvalidValue. |Řetězec|  
|<a name="error-value" />Hodnota|Hodnota parametru dotazu, která nebyla platná.|Řetězec|  
  

### <a name="errorresponse"></a>ErrorResponse  
Objekt nejvyšší úrovně, který zahrnuje odpověď na žádost skončí s chybou.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_zadejte|Pomocný parametr typu.|Řetězec|  
|<a name="errors" />Chyby|Seznam chyb, které popisují z důvodů, proč zpracování žádosti se nezdařilo.|[Chyba](#error)]|  

  
  
### <a name="license"></a>Licence  
Definuje licence, pod kterým mohou být použity na text nebo fotografie.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|jméno|Název licence.|Řetězec|  
|url|Adresu URL webu, kde může uživatel získat další informace o licenci.<br /><br /> Použijte název a adresu URL vytvoření hypertextového odkazu.|Řetězec|  
  

### <a name="licenseattribution"></a>LicenseAttribution  
Definuje smluvními pravidlo pro přiřazení licence.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_zadejte|Parametr typu, který je nastaven na LicenseAttribution.|Řetězec|  
|licence|Licence, pod kterým může sloužit k obsahu.|[Licence](#license)|  
|licenseNotice|Licence k zobrazení vedle cílové pole. Například "Text v rámci licence kopie SA".<br /><br /> Použít název a adresu URL v platnost licence `license` pole, které chcete vytvořit hypertextový odkaz na web, který popisuje podrobnosti o licenci. Potom nahraďte název licencí v `licenseNotice` řetězec (například kopie podle-SA) s hypertextový odkaz, který jste právě vytvořili.|Řetězec|  
|mustBeCloseToContent|Logická hodnota, která určuje, zda obsah pravidla musí být umístěn v blízkosti pole, které se pravidlo vztahuje. Pokud **true**, obsah musí být umístěny v těsné blízkosti. Pokud **false**, nebo tato pole neexistuje, obsah může být umístěn uvážení volajícího.|Logická hodnota|  
|targetPropertyName|Název pole, které se pravidlo vztahuje.|Řetězec|  
  

### <a name="link"></a>Odkaz  
Definuje součástí hypertextový odkaz.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_zadejte|Pomocný parametr typu.|Řetězec|  
|text|Zobrazovaný text.|Řetězec|  
|url|ADRESA URL. Použijte adresu URL a zobrazit text pro vytvoření hypertextového odkazu.|Řetězec|  
  

### <a name="linkattribution"></a>LinkAttribution  
Definuje smluvními pravidlo pro uvedení odkaz.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_zadejte|Parametr typu, který je nastaven na LinkAttribution.|Řetězec|  
|mustBeCloseToContent|Logická hodnota, která určuje, zda obsah pravidla musí být umístěn v blízkosti pole, které se pravidlo vztahuje. Pokud **true**, obsah musí být umístěny v těsné blízkosti. Pokud **false**, nebo tato pole neexistuje, obsah může být umístěn uvážení volajícího.|Logická hodnota|  
|targetPropertyName|Název pole, které se pravidlo vztahuje.<br /><br /> Pokud není zadán cíl, přidělení platí pro entity jako celek a měla by se zobrazit okamžitě následující prezentaci entity. Pokud existuje víc textové a odkazu porušení pravidel, která není zadejte cíl, měli byste zřetězení je a zobrazit pomocí "Data z:" popisek. Například "Data z < zprostředkovatele name1\> &#124; < zprostředkovatele name2\>".|Řetězec|  
|text|Uvedení text.|Řetězec|  
|url|Adresu URL webu poskytovatele. Použití `text` a adresu URL pro vytvoření hypertextového odkazu.|Řetězec|  
  
  
### <a name="mediaattribution"></a>MediaAttribution  
Definuje smluvními pravidlo pro uvedení média.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_zadejte|Parametr typu, který je nastaven na MediaAttribution.|Řetězec|  
|mustBeCloseToContent|Logická hodnota, která určuje, zda obsah pravidla musí být umístěn v blízkosti pole, které se pravidlo vztahuje. Pokud **true**, obsah musí být umístěny v těsné blízkosti. Pokud **false**, nebo tato pole neexistuje, obsah může být umístěn uvážení volajícího.|Logická hodnota|  
|targetPropertyName|Název pole, které se pravidlo vztahuje.|Řetězec|  
|url|Adresu URL, kterou použijete k vytvoření hypertextového odkazu mediálního obsahu. Například pokud cíl bitovou kopii, použijete adresu URL k pořízení snímku můžete kliknout.|Řetězec|  
  
  
  
### <a name="organization"></a>Organizace  
Definuje vydavatel.  
  
Všimněte si, že vydavatelem může poskytovat jejich název nebo jejich webu nebo obojí.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|jméno|Název vydavatele.|Řetězec|  
|url|Adresu URL webu vydavatele.<br /><br /> Upozorňujeme, že vydavateli nemusí poskytovat webu.|Řetězec|  
  
  

### <a name="webpage"></a>Webová stránka  
Definuje informace o webové stránce ve verzi preview.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|
|jméno|Název stránky, nikoli nutně Nadpis HTML|Řetězec|
|url|Adresu URL, která byla ve skutečnosti procházení (žádosti může provedli přesměrování)|Řetězec|  
|description|Stručný popis na stránce a obsahu|Řetězec|  
|isFamilyFriendly|Nejpřesnější pro položky v indexu webové; Toto zjišťování pouze na adresu URL a není obsah stránky se načte v reálném čase|Boolean|
|primaryImageOfPage/contentUrl|Adresu URL do reprezentativní bitové kopie pro zahrnutí do verze preview|Řetězec| 
  
  
### <a name="querycontext"></a>QueryContext  
Definuje kontext dotazu, který Bing použitou pro danou žádost.  
  
|Element|Popis|Typ|  
|-------------|-----------------|----------|  
|adultIntent|Logická hodnota, která označuje, zda má zadaný dotaz pro dospělé záměr. Hodnota je **true** Pokud dotaz obsahuje pro dospělé záměr; jinak **false**.|Logická hodnota|  
|alterationOverrideQuery|Řetězec dotazu, který se má použít k vynucení Bing používat původního řetězce. Například, pokud řetězec dotazu je *saling po směru větru*, bude řetězec dotazu přepsání *+ saling po směru větru*. Mějte na paměti, ke kódování řetězec dotazu, což vede k *% 2Bsaling + po směru větru*.<br /><br /> Toto pole je zahrnuta pouze v případě, že původní řetězec dotazu obsahuje chybu pravopis.|Řetězec|  
|alteredQuery|Řetězec dotazu, který používá Bing k provedení dotazu. Bing používá řetězec dotazu změnit, pokud původního řetězce dotazu obsažené pravopisné chyby. Například, pokud řetězec dotazu je `saling downwind`, bude řetězec dotazu změněna `sailing downwind`.<br /><br /> Toto pole je zahrnuta pouze v případě, že původní řetězec dotazu obsahuje chybu pravopis.|Řetězec|  
|askUserForLocation|Logická hodnota, která určuje, jestli Bing vyžaduje umístění uživatele zajistit přesné výsledky. Pokud jste zadali umístění uživatele pomocí [X-MSEdge-když](#clientip) a [X umístění vyhledávání](#location) hlavičky, toto pole můžete ignorovat.<br /><br /> Umístění vědět dotazů, například "dnešní počasí" nebo "v mém okolí restaurace", které je třeba umístění uživatele zajistit přesné výsledky, v tomto poli je nastavena na **true**.<br /><br /> Umístění vědět dotazy, které zahrnují umístění (například "Seattle počasí"), v tomto poli je nastavena na **false**. Toto pole je také nastavena na **false** pro dotazy, které nejsou umístění vědět, jako je například "nejlepší prodejci".|Logická hodnota|  
|originalQuery|Řetězec dotazu zadaný v požadavku.|Řetězec|  

### <a name="identifiable"></a>Osobní
|Název|Hodnota|Typ|  
|-------------|-----------------|----------|
|id|Identifikátor prostředku|Řetězec|
 
### <a name="rankinggroup"></a>RankingGroup
Definuje skupiny výsledků vyhledávání, například mainline.
|Název|Hodnota|Typ|  
|-------------|-----------------|----------|
|položek|Seznam výsledků vyhledávání do zobrazení ve skupině.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Definuje položku výsledek hledání k zobrazení.
|Název|Hodnota|Typ|  
|-------------|-----------------|----------|
|resultIndex|Index počítaný od nuly položky v odpověď na zobrazení. Pokud položka neobsahuje toto pole, zobrazí všechny položky v odpovědi. Například zobrazte všechny články v zprávy odpovědi.|Integer|
|answerType|Odpověď obsahující položku k zobrazení. Například zprávy.<br /><br />Pomocí typu můžete nalézt v objektu SearchResponse. Typ je název SearchResponse pole.<br /><br /> Však použijte typ odpovědi pouze v případě, že tento objekt obsahuje pole hodnoty; jinak ji ignorujte.|Řetězec|
|textualIndex|Index odpovědí v textualAnswers k zobrazení.| Celé číslo bez znaménka|
|hodnota|Identifikátor, který identifikuje odpověď k zobrazení nebo položku odpověď pro zobrazení. Pokud ID identifikuje odpověď, zobrazí se všechny položky odpovědi.|Osobní|

### <a name="rankingresponse"></a>RankingResponse  
Definuje, kde na hledání musí být umístěny obsahu stránce výsledky a v jakém pořadí.  
  
|Název|Hodnota|  
|----------|-----------|  
|<a name="ranking-mainline" />mainline|Výsledky hledání zobrazíte v nejdůležitějších.|  
|<a name="ranking-pole" />pole|Výsledky hledání, které musí být poskytnuta nejvíce viditelné způsob zpracování (například zobrazen výše nejdůležitějších a bočním panelu).|  
|<a name="ranking-sidebar" />bočním panelu|Výsledky hledání zobrazíte na bočním panelu.| 


### <a name="searchresponse"></a>SearchResponse  
Definuje objekt nejvyšší úrovně, který odpověď obsahuje při neproběhne.  
  
Všimněte si, že pokud služba má podezření, že útoku DOS, bude úspěšné žádosti (stavový kód protokolu HTTP je 200 OK); text odpovědi bude však prázdný.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_zadejte|Pomocný parametr typu, který je nastaven na SearchResponse.|Řetězec|  
|Webová stránka|Objekt JSON, který definuje ve verzi preview|řetězec|  
  
  
### <a name="textattribution"></a>TextAttribution  
Definuje smluvními pravidlo pro uvedení prostý text.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_zadejte|Parametr typu, který je nastaven na TextAttribution.|Řetězec|  
|text|Uvedení text.<br /><br /> Text porušení platí pro entity jako celek a měla by se zobrazit okamžitě následující prezentaci entity. Pokud existuje víc text nebo odkaz porušení pravidel, která není zadejte cíl, měli byste zřetězení je a zobrazit pomocí "Data z:" popisek.|Řetězec| 


## <a name="error-codes"></a>Kódy chyb

Níže jsou uvedeny možné stavové kódy HTTP, které vrací žádost.  
  
|Stavový kód|Popis|  
|-----------------|-----------------|  
|200|Úspěch|  
|400|Jeden z parametrů dotazu nebyl nalezen nebo není platný.|  
|401|Klíč předplatného chybí nebo není platný.|  
|403|Ověření uživatele (například používají platné předplatné klíč), ale nemají oprávnění pro požadovaný prostředek.<br /><br /> Bing může také vrátit tento stav, pokud volající k překročení jejich dotazů za měsíc kvóty.|  
|410|Požadavek na používá protokol HTTP místo protokolu HTTPS. HTTPS je protokol pouze podporované.|  
|429|Volající překročena jejich dotazů na druhý kvóty.|  
|500|Neočekávané chybě serveru.|

Pokud se požadavek nezdaří, obsahuje odpovědi [ErrorResponse](#errorresponse) objekt, který obsahuje seznam [chyba](#error) objekty, které popisují, co způsobilo chyby. Pokud se chyba, vztahuje na parametr `parameter` pole identifikuje parametr, který je problém. A pokud chyba se týká hodnotu parametru `value` pole určuje hodnotu, která není platná.

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

Jsou následující hodnoty možná chyba kód a dílčí chyba kódu.

|Kód|Dílčí|Popis
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Neimplementováno|Stavový kód protokolu HTTP je 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokováno|Bing vrátí InvalidRequest vždy, když libovolná součást žádosti není platný. Například chybí povinný parametr nebo hodnota parametru není platný.<br/><br/>Pokud je chyba ParameterMissing nebo ParameterInvalidValue, je stavový kód HTTP 400.<br/><br/>Pokud používáte protokol HTTP místo protokolu HTTPS, Bing vrátí HttpNotAllowed a je stavový kód protokolu HTTP 410.
|RateLimitExceeded|Žádné dílčí kódy|Bing vrátí RateLimitExceeded vždy, když překročí dotazů za sekundu (QPS) nebo dotazů za měsíc (QPM) kvóty.<br/><br/>Pokud QPS, Bing vrátí stavový kód HTTP 429 a překročíte QPM Bing vrátí 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing vrátí InvalidAuthorization, pokud Bing nemůže ověřit volající. Například `Ocp-Apim-Subscription-Key` chybí hlavička nebo klíč předplatného není platný.<br/><br/>Pokud zadáte více než jednu metodu ověřování, dojde k redundance.<br/><br/>Pokud je chyba InvalidAuthorization, je stavový kód HTTP 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing vrátí InsufficientAuthorization, pokud má volající nemá oprávnění pro přístup k prostředku. Tato situace může nastat, pokud klíč předplatného je zakázané nebo vypršela platnost. <br/><br/>Pokud je chyba InsufficientAuthorization, je stavový kód HTTP 403.

## <a name="next-steps"></a>Další postup
- [Rychlý start C#](c-sharp-quickstart.md)
- [Rychlý start Java](java-quickstart.md)
- [Rychlé spuštění uzlu](node-quickstart.md)
- [Rychlý start Python](python-quickstart.md)

