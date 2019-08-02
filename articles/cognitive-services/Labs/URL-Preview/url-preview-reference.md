---
title: Odkaz na Náhled adresy URL projektu
titlesuffix: Azure Cognitive Services
description: Odkaz na koncový bod náhledu adresy URL projektu
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: f92c0faaaa3aa0cd2af16a031f3bed4c6b41fc22
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706850"
---
# <a name="project-url-preview-v7-reference"></a>Odkaz na adresu URL náhledu v7y projektu

Náhled adresy URL podporuje krátké popisy webových prostředků pro blogové příspěvky, diskuze fóra, stránky ve verzi Preview atd. Adresa URL může být libovolného typu internetového prostředku: Webová stránka, novinky, obrázek nebo video. Dotaz musí být absolutní adresa URL se schématem http nebo HTTPS. relativní adresy URL nebo jiná schémata, jako je ftp://, se nepodporují.

Aplikace, které používají náhled adres URL, odesílají webové požadavky na koncový bod s adresou URL pro náhled v parametru dotazu. Požadavek musí zahrnovat hlavičku *OCP-APIM-Subscription-Key* .

Odpověď JSON se dá analyzovat pro informace verze Preview: název, popis prostředku, *isFamilyFriendly*a odkazy, které poskytují přístup k reprezentativní imagi a k úplnému prostředku online.

Je nutné použít pouze data z náhledu adresy URL pro zobrazení fragmentů kódu a miniatury ve verzi Preview, které jsou propojeny s jejich zdrojovými lokalitami, v části Sdílení adresy URL inicializované koncovými uživateli na sociálních médiích, na robotech a na podobných nabídkách. Nemusíte kopírovat, ukládat ani ukládat do mezipaměti žádná data, která dostanete z verze Preview adresy URL projektu. Aby bylo možné zakázat náhledy, které můžete obdržet od vlastníků webu nebo obsahu, musíte splnit všechny žádosti.

## <a name="endpoint"></a>Koncový bod
Chcete-li požádat o adresu URL náhledu výsledků, odešlete požadavek do následujícího koncového bodu. K definování dalších specifikací použijte záhlaví a parametry URL.

Koncový bod GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

```

Požadavek musí používat protokol HTTPS a zahrnout následující parametr dotazu:

Otázka – dotaz, který identifikuje adresu URL náhledu

V následujících oddílech jsou uvedeny technické podrobnosti o objektech odpovědí, parametrech dotazů a hlavičkách, které mají vliv na výsledky hledání.

Informace o hlavičkách, které by měly požadavky zahrnovat [](#headers), najdete v části Headers.

Informace o parametrech dotazu, které by měly požadavky zahrnovat, najdete v tématu [parametry dotazu](#query-parameters).

Informace o objektech JSON, které odpověď obsahuje, naleznete v tématu [objekty odpovědi](#response-objects).

Maximální délka adresy URL dotazu je 2 048 znaků. Aby se zajistilo, že délka vaší adresy URL nepřekračuje limit, maximální délka parametrů dotazu by měla být kratší než 1 500 znaků. Pokud adresa URL překračuje 2 048 znaků, server nenajde hodnotu 404.

Informace o povoleném použití a zobrazení výsledků najdete v tématu [použití a zobrazení požadavků](use-display-requirements.md).

> [!NOTE]
> Některé hlavičky požadavků, které jsou smysluplné pro jiná rozhraní API pro vyhledávání, nemají vliv na Náhled adresy URL.
> - Pragma – volající nemá kontrolu nad tím, jestli adresa URL ve verzi Preview používá mezipaměť.
> - User-Agent – v současnosti rozhraní API pro náhled adres URL neposkytuje různé odpovědi na volání z počítačů, notebooků a mobilních zařízení.
> 
> Některé parametry nejsou v současné době smysluplné pro rozhraní API pro náhled verze Preview, ale můžou se použít i v budoucnu pro lepší globalizaci.

## <a name="headers"></a>Záhlaví
Následují hlavičky, které může obsahovat požadavek a odpověď.

|Záhlaví|Popis|
|------------|-----------------|
|<a name="market" />BingAPIs-Market|Hlavička odpovědi.<br /><br /> Trh používaný požadavkem. Forma je \<kódJazyka\>-\<kódZemě\>. Například cs-CZ.|
|<a name="traceid" />BingAPIs-TraceId|Hlavička odpovědi.<br /><br /> ID položky protokolu obsahující podrobnosti požadavku. Pokud dojde k chybě, toto ID zachyťte. Pokud problém nedokážete určit a vyřešit, uveďte toto ID spolu s dalšími informacemi, které poskytnete týmu podpory.|
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Povinná hlavička požadavku.<br /><br /> Klíč předplatného, který jste dostali při registraci k této službě v [Cognitive Services](https://www.microsoft.com/cognitive-services/).|
|<a name="clientid" />X-MSEdge-ClientID|Nepovinná hlavička požadavku a odpovědi<br /><br /> Bing tuto hlavičku používá k tomu, aby uživatelům poskytoval konzistentní chování napříč voláními rozhraní API Bingu. Bing často testuje nové funkce a vylepšení a ID klienta používá jako klíč pro přiřazení provozu v různých testovacích verzích. Pokud nepoužíváte stejné ID klienta pro uživatele napříč více požadavky, pak může Bing uživatele přiřadit k více konfliktním testovacím verzím. Přiřazení k více konfliktním testovacím verzím může vést k nekonzistentnímu prostředí pro uživatele. Pokud třeba druhý požadavek má jiné přiřazení k testovací verzi než prví, může být prostředí neočekávané. Bing také může ID klienta použít pro přizpůsobení webových výsledků historii hledání daného ID klienta. Uživatel tak získá bohatší prostředí.<br /><br /> Bing také tuto hlavičku používá ke zlepšování hodnocení výsledků díky analýze aktivity generované tímto ID klienta. Zlepšení relevance pomáhá zlepšit kvalitu výsledků poskytovaných rozhraním API Bingu a stejně tak umožňuje vyšší míru prokliků uživatele rozhraní API.<br /><br />Následují základní pravidla používání, která se na tuto hlavičku vztahují.<br /><ul><li>Každý uživatel, který používá vaši aplikaci na zařízení, musí mít jedinečné ID klienta vygenerované Bingem.<br /><br/>Pokud tuto hlavičku do požadavku nezahrnete, Bing vygeneruje ID a vrátí ho v hlavičce odpovědi X-MSEdge ClientID. Tuto hlavičku byste v požadavku zahrnout NEMĚLI jenom v případě, kdy uživatel aplikaci na zařízení používá poprvé.<br /><br/></li><li>Použijte ID klienta pro každý požadavek rozhraní API Bingu, který vaše aplikace provede pro tohoto uživatele na zařízení.<br /><br/></li><li>**UPOZORŇUJE** Je nutné zajistit, aby toto ID klienta nebylo propojeno s žádnými informacemi o ověřujícím uživatelském účtu.</li><br/><li>ID klienta zachovejte. Pokud chcete zachovat ID v aplikaci prohlížeče, použijte trvalý soubor cookie HTTP, aby se zaručilo, že se ID použije ve všech relacích. Nepoužívejte soubor cookie relace. Pro jiné aplikace, jako jsou mobilní aplikace, použijte k zachování ID trvalé úložiště zařízení.<br /><br/>Když uživatel aplikaci na zařízení příště použije, získejte ID klienta, které jste zachovali.</li></ul><br /> **POZNÁMKA:** Odpovědi služby Bing mohou nebo nemusí zahrnovat tuto hlavičku. Pokud odpověď tuto hlavičku obsahuje, ID klienta zachyťte a použijte pro všechny následné požadavky Bingu pro uživatele na tomto zařízení.<br /><br /> **POZNÁMKA:** Pokud zahrnete rozhraní X-MSEdge-ClientID, nesmíte v žádosti zahrnout soubory cookie.|
|<a name="clientip" />X-MSEdge-ClientIP|Nepovinná hlavička požadavku.<br /><br /> Adresa IPv4 nebo IPv6 klientského zařízení. IP adresa se používá ke zjištění polohy uživatele. Bing informace o poloze používá k určení chování bezpečného hledání.<br /><br /> Neprovádějte obfuskaci adresy (například změnou posledního oktetu na 0). Obfuskace adresy vede k tomu, že poloha nebude blízko skutečné polohy zařízení. Bing pak může dodávat chybné výsledky.|

## <a name="query-parameters"></a>Parametry dotazu
Požadavek může obsahovat následující parametry dotazu. Požadované parametry najdete v požadovaném sloupci. Je nutné zadat adresu URL pro kódování parametrů dotazu. Dotaz musí být absolutní adresa URL se schématem http nebo HTTPS. Nepodporujeme relativní adresy URL ani jiná schémata, jako je ftp://.

|Name|Hodnota|type|Požadováno|
|----------|-----------|----------|--------------|
|<a name="mkt" />mkt|Trh, odkud pochází výsledky. <br /><br />Seznam možných hodnot trhu najdete v tématu kódy trhů.<br /><br /> **POZNÁMKA:** Rozhraní API pro náhled adres URL v současné době podporuje pouze zeměpisnou angličtinu a jazyk angličtina.<br /><br />|Řetězec|Ano|
|<a name="query" />q|Adresa URL náhledu|Řetězec|Ano|
|<a name="responseformat" />responseFormat|Typ média, který se má použít pro odpověď Níže jsou uvedené možné hodnoty nerozlišující malá a velká písmena.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Výchozí hodnota je JSON. Informace o objektech JSON, které odpověď obsahuje, naleznete v tématu [objekty Response](#response-objects).<br /><br />Pokud zadáte JsonLd, tělo odpovědi zahrnuje objekty JSON-LD, které obsahují výsledky hledání. Informace o formátu JSON-LD najdete v tématu [JSON-ld](https://json-ld.org/).|Řetězec|Ne|
|<a name="safesearch"/>safeSearch|Neplatný obsah pro dospělé nebo kradený obsah je zablokován s kódem chyby 400 a příznak *isFamilyFriendly* se nevrátí. <p>Pro zákonný obsah pro dospělé níže je toto chování. Stavový kód vrátí 200 a příznak *isFamilyFriendly* je nastaven na hodnotu false.<ul><li>Bezpečné hledání = Strict: Název, popis, adresa URL a obrázek nebudou vráceny.</li><li>Bezpečné hledání = střední; Získejte název, adresu URL a popis, ale ne popisný obrázek.</li><li>Bezpečné hledání = off; Získá všechny objekty a elementy odpovědi – název, adresu URL, popis a obrázek.</li></ul> |Řetězec|Není nutné. </br> Výchozí hodnota je bezpečné hledání = Strict.|

## <a name="response-objects"></a>Objekty Response
Schéma odpovědi je buď [webová stránka], nebo ErrorResponse, jako v rozhraní Vyhledávání na webu API. Pokud se požadavek nezdařil, objekt nejvyšší úrovně je objekt [ErrorResponse](#errorresponse) .

|Object|Popis|
|------------|-----------------|
|[Stránku](#webpage)|Objekt JSON nejvyšší úrovně, který obsahuje atributy Preview|

### <a name="error"></a>Chyba
Definuje chybu, ke které došlo.

|Prvek|Popis|type|
|-------------|-----------------|----------|
|<a name="error-code" />znakovou|Kód chyby, který identifikuje kategorii chyby. Seznam možných kódů naleznete v tématu [kódy chyb](#error-codes).|Řetězec|
|<a name="error-message" />Zpráva|Popis chyby|Řetězec|
|<a name="error-moredetails" />moreDetails|Popis, který poskytuje další informace o chybě.|Řetězec|
|<a name="error-parameter" />ukazatele|Parametr dotazu v žádosti, která způsobila chybu.|Řetězec|
|<a name="error-subcode" />subCode|Kód chyby, který identifikuje chybu. Například pokud `code` je InvalidRequest, `subCode` může být ParameterInvalid nebo ParameterInvalidValue. |Řetězec|
|<a name="error-value" />osa|Hodnota parametru dotazu, která není platná.|Řetězec|

### <a name="errorresponse"></a>ErrorResponse
Objekt nejvyšší úrovně, který odpověď zahrnuje v případě, že se požadavek nezdařil.

|Name|Value|type|
|----------|-----------|----------|
|_type|Zadejte pomocný parametr.|Řetězec|
|<a name="errors" />vyskytl|Seznam chyb popisujících důvody, proč se žádost nezdařila.|[Chyba](#error) []|

### <a name="webpage"></a>Stránku
Definuje informace o webové stránce ve verzi Preview.

|Name|Hodnota|type|
|----------|-----------|----------|
|name|Nadpis stránky, ne nutně název HTML|Řetězec|
|url|Adresa URL, která byla ve skutečnosti procházena (žádost může mít přesměrování za následovat)|Řetězec|
|description|Stručný popis stránky a obsahu|Řetězec|
|isFamilyFriendly|Nejpřesnější pro položky ve webovém indexu; načítá se v reálném čase. Tato detekce je založená výhradně na adrese URL, nikoli na obsahu stránky.|boolean|
|primaryImageOfPage/contentUrl|Adresa URL zástupce obrázku, který má být zahrnut do verze Preview|Řetězec|

### <a name="identifiable"></a>Identifikovatelné údaje
|Name|Value|type|
|-------------|-----------------|----------|
|id|Identifikátor prostředku|Řetězec|

## <a name="error-codes"></a>Kódy chyb

Níže jsou uvedené možné stavové kódy HTTP, které požadavek vrátí.

|Stavový kód|Popis|
|-----------------|-----------------|
|200|Úspěšné|
|400|Jeden z parametrů dotazu chybí nebo je neplatný.|
|400|ServerError, ResourceError Subcode: Požadovaná adresa URL není dostupná.|
|400|ServerError, ResourceError Subcode: Požadovaná adresa URL nevrátila kód úspěšnosti (včetně toho, jestli vrátil HTTP 404).|
|400|InvalidRequest, dekódování je blokované: Požadovaná adresa URL může obsahovat obsah pro dospělé a byla zablokována.|
|401|Klíč předplatného chybí nebo není platný.|
|403|Uživatel je ověřený (například používá platný klíč předplatného), ale nemá oprávnění k požadovanému prostředku.<br /><br /> Bing může tento stav vrátit také v případě, že volající překročil kvótu na měsíc.|
|410|Požadavek použil protokol HTTP místo protokolu HTTPS. Jediným podporovaným protokolem je protokol HTTPS.|
|429|Volající překročil kvótu pro počet dotazů za sekundu.|
|500|Došlo k neočekávané chybě serveru.|

Pokud se požadavek nepovede, odpověď obsahuje objekt [ErrorResponse](#errorresponse) , který obsahuje seznam chybových objektů [](#error) , které popisují, co způsobilo chybu. Pokud chyba souvisí s parametrem, `parameter` pole identifikuje parametr, který je problémem. A pokud chyba souvisí s hodnotou parametru, `value` pole identifikuje neplatnou hodnotu.

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
|ServerError|UnexpectedError<br/>ResourceError<br/>Neimplementováno|Stavový kód HTTP je 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokováno|Bing vrátí InvalidRequest, pokud jakákoli část požadavku není platná. Například povinný parametr chybí nebo hodnota parametru není platná.<br/><br/>Pokud se jedná o chybu ParameterMissing nebo ParameterInvalidValue, kód stavu HTTP je 400.<br/><br/>Pokud místo protokolu HTTPS použijete protokol HTTP, Bing vrátí HttpNotAllowed a kód stavu HTTP je 410.
|RateLimitExceeded|Žádné dílčí kódy|Bing vrátí RateLimitExceeded vždy, když překročíte kvótu dotazů za sekundu (QPS) nebo dotazů za měsíc (QPM).<br/><br/>Pokud překročíte QPS, Bing vrátí stavový kód HTTP 429 a pokud překročíte QPM, Bing vrátí 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing vrátí InvalidAuthorization, když Bing nemůže ověřit volajícího. `Ocp-Apim-Subscription-Key` Hlavička například chybí nebo klíč předplatného není platný.<br/><br/>Redundance probíhá, pokud zadáte více než jednu metodu ověřování.<br/><br/>Pokud je chyba InvalidAuthorization, kód stavu HTTP je 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing vrátí InsufficientAuthorization, pokud volající nemá oprávnění pro přístup k prostředku. Tato situace může nastat, pokud byl klíč předplatného zakázán nebo vypršela jeho platnost. <br/><br/>Pokud je chyba InsufficientAuthorization, kód stavu HTTP je 403.

## <a name="next-steps"></a>Další postup
- [Rychlý start pro jazyk C#](csharp.md)
- [Rychlý start pro Javu](java-quickstart.md)
- [Rychlý start pro JavaScript](javascript.md)
- [Rychlý start pro Node](node-quickstart.md)
- [Rychlý start pro Python](python-quickstart.md)
