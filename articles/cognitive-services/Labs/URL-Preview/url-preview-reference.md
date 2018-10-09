---
title: Náhled adresy URL odkaz na projekt
titlesuffix: Azure Cognitive Services
description: Referenční informace pro koncový bod Náhled adresy URL projektu.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: 3416fd9bc63c48e976d0b00f42ec9f8119a40eb8
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48870804"
---
# <a name="project-url-preview-v7-reference"></a>Odkaz na projekt ve verzi Preview se adresy URL v7

Adresa URL ve verzi Preview podporuje stručný popis webové prostředky pro blogové příspěvky, fórum pro diskuse, stránky ve verzi preview, atd.  Adresa URL může být libovolný typ prostředku Internet: webové stránky, diskusní, obrázek nebo video. Dotaz musí být absolutní adresa URL se schématem http nebo https; relativní adresy URL nebo jiná schémata, jako je ftp: / / nejsou podporovány.

Aplikace, které používají ve verzi Preview se adresy URL odesílání webových požadavků do koncového bodu s adresou URL pro náhled v parametru dotazu.  Musí zahrnovat požadavek *Ocp-Apim-Subscription-Key* záhlaví.   

Informace ve verzi preview můžete analyzovat odpověď JSON: název, popis prostředku, *isFamilyFriendly*a odkazy, které poskytují přístup k imagi reprezentativní a k dokončení prostředku online.

Jenom data z adresy URL ve verzi Preview musíte použít k zobrazení fragmenty kódu ve verzi preview a miniatury s hypertextovým odkazem na jejich zdrojových lokalit ve sdílení na sociálních médiích, chatovací robot nebo podobné nabídky URL iniciované koncovým uživatelem. Nesmí kopírování, uložení nebo všechna data, která jste dostali z Náhled adresy URL projektu do mezipaměti. Musí přijmout všechny žádosti o zakázání náhledů, které se může zobrazit z webu nebo vlastníci obsahu.

## <a name="endpoint"></a>Koncový bod
Požádat o adresy URL náhledu výsledků, odesílejte požadavek na následující koncový bod. Použijte k definování další specifikace hlaviček a parametrů adresy URL.

Koncový bod GET: 
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

````

Žádost musí používat protokol HTTPS a obsahovat následující parametr dotazu:

 q - dotaz, který určuje adresu URL ve verzi preview 

Následující části obsahují technické podrobnosti o odpovědi objekty, parametry dotazů a hlaviček, které ovlivňují výsledky hledání. 
  
Informace o hlavičky, které by měl obsahovat požadavky najdete v tématu [záhlaví](#headers).  
  
Informace o parametrech dotazů, které by měl obsahovat požadavky najdete v tématu [parametrů dotazu](#query-parameters).  
  
Informace o JSON objektů, že odpověď obsahuje, naleznete v tématu [objekty odpovědi](#response-objects).

Dotaz maximální délka adresy URL je 2 048 znaků. Aby bylo zajištěno, že vaše délka adresy URL nepřekračuje limit, maximální délka parametry dotazu musí být menší než 1 500 znaků. Pokud adresa URL je delší než 2 048 znaků, server vrátí 404 Nenalezeno.  

Informace o povolených použití a zobrazení výsledků najdete v tématu [použít a zobrazit požadavky](use-display-requirements.md). 

> [!NOTE]
> Některé hlavičky žádosti, které mají smysl pro jiná rozhraní API pro hledání nemají vliv na Náhled adresy URL
> - Pragma – volající nemá řídit, jestli ve verzi Preview se adresy URL používá mezipaměť
> - Uživatelský Agent – teď adresy Url rozhraní API ve verzi Preview neposkytuje různé odpovědi pro volání pocházející z počítače, přenosné počítače i mobilní zařízení.

> Také některé parametry nejsou aktuálně smysl pro adresu URL ve verzi Preview rozhraní API, ale mohou být použity v budoucnosti pro lepší globalizace. 
 
## <a name="headers"></a>Hlavičky  
Níže jsou hlavičky, které mohou zahrnovat požadavek a odpověď.  
  
|Hlavička|Popis|  
|------------|-----------------|   
|<a name="market" />BingAPIs-Market|Hlavička odpovědi.<br /><br /> Trh používaný požadavkem. Forma je \<kódJazyka\>-\<kódZemě\>. Například cs-CZ.|  
|<a name="traceid" />BingAPIs-TraceId|Hlavička odpovědi.<br /><br /> ID položky protokolu obsahující podrobnosti požadavku. Pokud dojde k chybě, toto ID zachyťte. Pokud problém nedokážete určit a vyřešit, uveďte toto ID spolu s dalšími informacemi, které poskytnete týmu podpory.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Povinná hlavička požadavku.<br /><br /> Klíč předplatného, který jste dostali při registraci k této službě v [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="clientid" />X-MSEdge-ClientID|Nepovinná hlavička požadavku a odpovědi<br /><br /> Bing tuto hlavičku používá k tomu, aby uživatelům poskytoval konzistentní chování napříč voláními rozhraní API Bingu. Bing často testuje nové funkce a vylepšení a ID klienta používá jako klíč pro přiřazení provozu v různých testovacích verzích. Pokud nepoužíváte stejné ID klienta pro uživatele napříč více požadavky, pak může Bing uživatele přiřadit k více konfliktním testovacím verzím. Přiřazení k více konfliktním testovacím verzím může vést k nekonzistentnímu prostředí pro uživatele. Pokud třeba druhý požadavek má jiné přiřazení k testovací verzi než prví, může být prostředí neočekávané. Bing také může ID klienta použít pro přizpůsobení webových výsledků historii hledání daného ID klienta. Uživatel tak získá bohatší prostředí.<br /><br /> Bing také tuto hlavičku používá ke zlepšování hodnocení výsledků díky analýze aktivity generované tímto ID klienta. Zlepšení relevance pomáhá zlepšit kvalitu výsledků poskytovaných rozhraním API Bingu a stejně tak umožňuje vyšší míru prokliků uživatele rozhraní API.<br /><br />Následují základní pravidla používání, která se na tuto hlavičku vztahují.<br /><ul><li>Každý uživatel, který používá vaši aplikaci na zařízení, musí mít jedinečné ID klienta vygenerované Bingem.<br /><br/>Pokud tuto hlavičku do požadavku nezahrnete, Bing vygeneruje ID a vrátí ho v hlavičce odpovědi X-MSEdge ClientID. Tuto hlavičku byste v požadavku zahrnout NEMĚLI jenom v případě, kdy uživatel aplikaci na zařízení používá poprvé.<br /><br/></li><li>Použijte ID klienta pro každý požadavek rozhraní API Bingu, který vaše aplikace provede pro tohoto uživatele na zařízení.<br /><br/></li><li>**Pozor:** musíte zajistit, že toto ID klienta není propojovací informací authenticatable uživatelského účtu.</li><br/><li>ID klienta zachovejte. Pokud chcete zachovat ID v aplikaci prohlížeče, použijte trvalý soubor cookie HTTP, aby se zaručilo, že se ID použije ve všech relacích. Nepoužívejte soubor cookie relace. Pro jiné aplikace, jako jsou mobilní aplikace, použijte k zachování ID trvalé úložiště zařízení.<br /><br/>Když uživatel aplikaci na zařízení příště použije, získejte ID klienta, které jste zachovali.</li></ul><br /> **POZNÁMKA:** Odpovědi Bingu tuto hlavičku mohou nebo nemusí obsahovat. Pokud odpověď tuto hlavičku obsahuje, ID klienta zachyťte a použijte pro všechny následné požadavky Bingu pro uživatele na tomto zařízení.<br /><br /> **POZNÁMKA:** Pokud zahrnete X-MSEdge-ClientID, nesmíte do požadavku zahrnout soubory cookie.|  
|<a name="clientip" />X-MSEdge-ClientIP|Nepovinná hlavička požadavku.<br /><br /> Adresa IPv4 nebo IPv6 klientského zařízení. IP adresa se používá ke zjištění polohy uživatele. Bing informace o poloze používá k určení chování bezpečného hledání.<br /><br />  Neprovádějte obfuskaci adresy (například změnou posledního oktetu na 0). Obfuskace adresy vede k tomu, že poloha nebude blízko skutečné polohy zařízení. Bing pak může dodávat chybné výsledky.|  
<br /><br /></li></ul>   

## <a name="query-parameters"></a>Parametry dotazu  
Žádost mohou zahrnovat tyto parametry dotazu. Zobrazte požadovaný sloupec pro požadované parametry. Adresa URL musíte zakódovat parametry dotazu. Dotaz musí být absolutní adresa URL se schématem http nebo https; Nepodporujeme relativní adresy URL nebo jiná schémata, jako je ftp: / /
  
  
|Název|Hodnota|Typ|Požaduje se|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Trh, odkud pochází výsledky. <br /><br />Seznam možných hodnot na trhu, najdete v tématu [trhu kódy](#market-codes).<br /><br /> **Poznámka:** rozhraní API ve verzi Preview URL aktuálně podporuje jenom USA podle zeměpisného umístění a angličtině.<br /><br />|Řetězec|Ano|  
|<a name="query" />q|Adresa URL ve verzi preview|Řetězec|Ano|  
|<a name="responseformat" />Formát odpovědi|Typ média určený pro odpověď. Níže jsou uvedeny možné hodnoty velká a malá písmena.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Výchozí hodnota je JSON. Informace o JSON objektů, že odpověď obsahuje, naleznete v tématu [objekty odpovědi](#response-objects).<br /><br />  Pokud chcete zadat JsonLd, tělo odpovědi obsahuje objekty JSON-LD, obsahující výsledky hledání. Informace o JSON-LD, naleznete v tématu [JSON-LD](http://json-ld.org/).|Řetězec|Ne|
|<a name="safesearch"/>safeSearch|Neplatný obsah pro dospělé nebo nelegální obsah blokovaný s kódem chyby 400 a *isFamilyFriendly* příznak nevrátí. <p>Platný obsah pro dospělé níže je chování. Vrátí stavový kód 200 a *isFamilyFriendly* je příznak nastaven na hodnotu false.<ul><li>bezpečné hledání = strict: název, popis, adresu URL a image se nezobrazí.</li><li>bezpečné hledání = střední; Získejte název, adresu URL a popis, ale není popisný obraz.</li><li>bezpečné hledání = off; Získejte odpovědi objekty/všeho – název, URL, popis a obrázek.</li></ul> |Řetězec|Není nutné. </br> Výchozí hodnota je bezpečné hledání = strict.| 

## <a name="response-objects"></a>Objekty odpovědi  
Schéma odpovědi je buď [webové stránky] nebo ErrorResponse, stejně jako v hledání webového rozhraní API. Pokud požadavek selže, je objekt nejvyšší úrovně [ErrorResponse](#errorresponse) objektu.


|Objekt|Popis|  
|------------|-----------------|  
|[Webová stránka](#webpage)|Nejvyšší úroveň objektu JSON, který obsahuje atributy ve verzi preview.|  

  
### <a name="error"></a>Chyba  
Definuje chyby, ke které došlo k chybě.  
  
|Prvek|Popis|Typ|  
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
  

### <a name="webpage"></a>Webová stránka  
Definuje informace o webové stránce ve verzi preview.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|
|jméno|Název stránky, ne tedy nutně Nadpis HTML|Řetězec|
|url|Adresu URL, kterou byl ve skutečnosti procházen (žádosti může provedli přesměrování)|Řetězec|  
|description|Stručný popis stránky a obsahu|Řetězec|  
|isFamilyFriendly|Co nejvíce zpřesnili pro položky v rejstříku web. Tato detekce založené výhradně na adresu URL a obsah stránky se načte v reálném čase|Boolean|
|primaryImageOfPage/contentUrl|Adresa URL reprezentativní image zahrnout ve verzi preview|Řetězec| 


### <a name="identifiable"></a>Údaje
|Název|Hodnota|Typ|  
|-------------|-----------------|----------|
|id|Identifikátor prostředku|Řetězec|
 

## <a name="error-codes"></a>Kódy chyb

Tady jsou možné stavové kódy HTTP, které vrátí žádost o.  
  
|Stavový kód|Popis|  
|-----------------|-----------------|  
|200|Úspěch|  
|400|Jeden z parametrů dotazu je chybí nebo není platný.| 
|400|ServerError, subkódu ResourceError: požadovanou adresu URL není dosažitelná.|
|400|ServerError, subkódu ResourceError: požadovanou adresu URL nevrátil kód úspěchu (včetně pokud vrátí chyby HTTP 404)|
|400|InvalidRequest, subkódu uzavřeno: požadovanou adresu URL může obsahovat obsah pro dospělé a byl zablokován| 
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
- [Rychlý start C#](csharp.md)
- [Rychlý start pro Javu](java-quickstart.md)
- [Rychlý start jazyka JavaScript](javascript.md)
- [Rychlý start uzlu](node-quickstart.md)
- [Rychlý start Pythonu](python-quickstart.md)

