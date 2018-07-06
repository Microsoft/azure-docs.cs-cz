---
title: Náhled adresy URL odkaz – Microsoft Cognitive Services projektu | Dokumentace Microsoftu
description: Referenční informace pro koncový bod Náhled adresy URL projektu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 46c011d62b6ae51f5f7d292345e6ece0e27a8541
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865871"
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
 
## <a name="headers"></a>Záhlaví  
Níže jsou hlavičky, které mohou zahrnovat požadavek a odpověď.  
  
|Záhlaví|Popis|  
|------------|-----------------|   
|<a name="market" />BingAPIs trh|Hlavička odpovědi.<br /><br /> Na trhu použít v požadavku. Formulář je \<languageCode\>-\<countryCode\>. Například en US.|  
|<a name="traceid" />BingAPIs TraceId|Hlavička odpovědi.<br /><br /> ID položky protokolu, který obsahuje podrobné informace o požadavku. Pokud dojde k chybě, zachycení číslem ID této. Pokud nejste schopni zjistit a vyřešte problém, patří toto ID spolu s dalšími informacemi, že zadáte na tým podpory.|  
|<a name="subscriptionkey" />OCP-Apim-Subscription-Key|Hlavička požadavku vyžaduje.<br /><br /> Klíč předplatného, který jste dostali při registraci pro tuto službu v [služeb Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="clientid" />X-MSEdge ClientID|Volitelné záhlaví žádostí a odpovědí.<br /><br /> Bing používá tuto hlavičku uživatelům poskytnout konzistentní chování napříč volání rozhraní API Bingu. Bing často letů nových funkcí a vylepšení a používá ID klienta jako klíč pro přiřazení provoz na jiné lety. Pokud je velmi riskantní používat stejné ID klienta pro uživatele mezi více požadavků, pak Bingu může přiřadit uživatele k více konfliktních lety. Přiřazení na více konfliktních lety může vést k nekonzistentním uživatelské prostředí. Například pokud druhá žádost o přiřazení jiné cestě než první, prostředí může neočekávané. Navíc můžete použít ID klienta pro přizpůsobení webové výsledky pro tohoto klienta Bingu ID historie hledání, poskytuje pohodlnější a pestřejší prostředí pro uživatele.<br /><br /> Bing používá této hlavičky také jí pomohou zlepšit výsledek hodnocení díky analýze aktivitu generovanou podle ID klienta. Zlepšení relevance pomoct s lepší kvalitou výsledků přednesl rozhraní API Bingu a zase umožňuje vyšší proklikávání sazby pro rozhraní API příjemců.<br /><br />Tady jsou základní informace o využití pravidel, které se vztahují k této hlavičce.<br /><ul><li>Každý uživatel, který používá vaše aplikace v zařízení musí mít jedinečný, Bing, vygeneruje ID klienta.<br /><br/>Pokud neuvedete této hlavičky v požadavku, Bingu vygeneruje ID a vrátí jej v hlavičce X-MSEdge ClientID odpovědi. Že by měl nezahrnuje této hlavičky v požadavku se pouze první uživatel použije aplikaci na daném zařízení.<br /><br/></li><li>Použijte ID klienta pro každé rozhraní API Bingu pro žádosti, která vaše aplikace provádí pro tohoto uživatele na zařízení.<br /><br/></li><li>**Pozor:** musíte zajistit, že toto ID klienta není propojovací informací authenticatable uživatelského účtu.</li><br/><li>Zachovat ID klienta. Pokud chcete zachovat ID v aplikaci prohlížeče, ujistěte se, že ID se používá ve všech relacích pomocí trvalého souboru cookie HTTP. Nepoužívejte souboru cookie relace. Pro jiné aplikace, jako jsou například mobilní aplikace použijte k uchování ID. trvalého úložiště zařízení<br /><br/>Při příštím uživatel použije aplikaci na zařízení, získejte ID klienta, který je trvalé.</li></ul><br /> **Poznámka:** odpovědi Bingu může nebo nemusí obsahovat tato záhlaví. Pokud odpověď obsahuje tato záhlaví, ID klienta zachytit a používat ho pro všechny následné požadavky Bing pro uživatele na tomto zařízení.<br /><br /> **Poznámka:** Pokud zahrnete X MSEdge ClientID, nesmí obsahovat soubory cookie v požadavku.|  
|<a name="clientip" />X-MSEdge ClientIP|Hlavička požadavku volitelné.<br /><br /> Adresa IPv4 nebo IPv6 klienta zařízení. IP adresa se používá ke zjišťování podle umístění uživatele. Bing používá k určení chování bezpečného hledání informace o poloze.<br /><br />  Není obfuskaci adresu (například tak, že změníte poslední oktet na hodnotu 0). Že maskuje výsledky adres v umístění nebude kdekoli v zařízení aktuální umístění, což může vést k Bingu obsluhující chybné výsledky.|  
<br /><br /></li></ul>   

## <a name="query-parameters"></a>Parametry dotazu  
Žádost mohou zahrnovat tyto parametry dotazu. Zobrazte požadovaný sloupec pro požadované parametry. Adresa URL musíte zakódovat parametry dotazu. Dotaz musí být absolutní adresa URL se schématem http nebo https; Nepodporujeme relativní adresy URL nebo jiná schémata, jako je ftp: / /
  
  
|Název|Hodnota|Typ|Požaduje se|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Na trhu, odkud pochází výsledky. <br /><br />Seznam možných hodnot na trhu, najdete v tématu [trhu kódy](#market-codes).<br /><br /> **Poznámka:** rozhraní API ve verzi Preview URL aktuálně podporuje jenom USA podle zeměpisného umístění a angličtině.<br /><br />|Řetězec|Ano|  
|<a name="query" />q|Adresa URL ve verzi preview|Řetězec|Ano|  
|<a name="responseformat" />Formát odpovědi|Typ média určený pro odpověď. Níže jsou uvedeny možné hodnoty velká a malá písmena.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Výchozí hodnota je JSON. Informace o JSON objektů, že odpověď obsahuje, naleznete v tématu [objekty odpovědi](#response-objects).<br /><br />  Pokud chcete zadat JsonLd, tělo odpovědi obsahuje objekty JSON-LD, obsahující výsledky hledání. Informace o JSON-LD, naleznete v tématu [JSON-LD](http://json-ld.org/).|Řetězec|Ne|
|<a name="safesearch"/>bezpečné hledání|Neplatný obsah pro dospělé nebo nelegální obsah blokovaný s kódem chyby 400 a *isFamilyFriendly* příznak nevrátí. <p>Platný obsah pro dospělé níže je chování. Vrátí stavový kód 200 a *isFamilyFriendly* je příznak nastaven na hodnotu false.<ul><li>bezpečné hledání = strict: název, popis, adresu URL a image se nezobrazí.</li><li>bezpečné hledání = střední; Získejte název, adresu URL a popis, ale není popisný obraz.</li><li>bezpečné hledání = off; Získejte odpovědi objekty/všeho – název, URL, popis a obrázek.</li></ul> |Řetězec|Není nutné. </br> Výchozí hodnota je bezpečné hledání = strict.| 

## <a name="response-objects"></a>Objekty odpovědi  
Schéma odpovědi je buď [webové stránky] nebo ErrorResponse, stejně jako v hledání webového rozhraní API. Pokud požadavek selže, je objekt nejvyšší úrovně [ErrorResponse](#errorresponse) objektu.


|Objekt|Popis|  
|------------|-----------------|  
|[Webová stránka](#webpage)|Nejvyšší úroveň objektu JSON, který obsahuje atributy ve verzi preview.|  

  
### <a name="error"></a>Chyba  
Definuje chyby, ke které došlo k chybě.  
  
|Element|Popis|Typ|  
|-------------|-----------------|----------|  
|<a name="error-code" />kód|Kód chyby, který určuje kategorii chyby. Seznam možných kódů najdete v tématu [kódy chyb](#error-codes).|Řetězec|  
|<a name="error-message" />Zpráva|Popis chyby.|Řetězec|  
|<a name="error-moredetails" />moreDetails|Popis, který poskytuje další informace o této chybě.|Řetězec|  
|<a name="error-parameter" />Parametr|Parametr dotazu v žádosti, která způsobila chybu.|Řetězec|  
|<a name="error-subcode" />podřízeného|Kód chyby: identifikující chybu. Například pokud `code` je InvalidRequest, `subCode` může být ParameterInvalid nebo ParameterInvalidValue. |Řetězec|  
|<a name="error-value" />Hodnota|Hodnota parametru dotazu, která nebyla platná.|Řetězec|  
  

### <a name="errorresponse"></a>ErrorResponse  
Objekt nejvyšší úrovně, který obsahuje odpověď, pokud požadavek selže.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|  
|_typ|Pomocný parametr typu.|Řetězec|  
|<a name="errors" />chyby|Seznam chyb, které popisují důvody, proč žádost selhala.|[Chyba](#error)]|   
  

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

