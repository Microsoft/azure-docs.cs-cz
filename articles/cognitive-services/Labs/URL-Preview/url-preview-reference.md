---
title: Náhled adresy URL odkaz - kognitivní služby společnosti Microsoft na projekt | Microsoft Docs
description: Referenční dokumentace pro koncového bodu náhledu adresa URL projektu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: adc2f83f703e740e40d9ba4fd3ed08ba429e5d97
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343297"
---
# <a name="project-url-preview-v7-reference"></a>Referenční dokumentace v7 Náhled adresy URL projektu

Adresa URL Preview podporuje stručný popis webové prostředky pro příspěvcích na blogu, fórum diskusí, preview stránky atd.  Adresa URL může být libovolný typ prostředku Internet: webové stránky, zprávy, image nebo video. Dotaz musí být absolutní adresu URL pomocí protokolu http nebo https schématu; relativní adresy URL nebo jiná schémata jako ftp: / / nejsou podporovány.

Aplikace, které používají adresu URL náhledu odesílají webové požadavky na koncový bod s adresou URL náhled v parametru dotazu.  Musí zahrnovat požadavek *Ocp-Apim-Subscription-Key* záhlaví.   

Informace preview lze analyzovat odpověď JSON: název, popis prostředku, *isFamilyFriendly*a odkazy, které poskytují přístup k reprezentativní bitové kopie a k dokončení prostředek online.

Pouze data z adresy URL Preview musíte použít k zobrazení náhledu fragmenty a miniatury s hypertextovým odkazem na jejich zdrojových lokalit ve sdílení na sociálních médií, chatovací robota nebo podobné nabídky URL iniciované koncovým uživatelem. Nesmí kopírování, uložení nebo mezipaměti žádná data, které jste získali od Preview adresa URL projektu. Musí všechny žádosti, chcete-li zakázat verze Preview, které se mohou zobrazit z webu nebo vlastníci obsahu případném dalším sdílení dodržovat.

## <a name="endpoint"></a>Koncový bod
O adresu URL náhledu výsledků odeslat požadavek na následující koncový bod. Můžete definovat další specifikace pomocí hlaviček a parametrů adresy URL.

Koncový bod GET: 
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

````

Žádost musí používat protokol HTTPS a zahrnují následující parametr dotazu:

 q - dotaz, který identifikuje adresu URL pro zobrazení náhledu 

Následující části obsahují technické podrobnosti o objektech odpovědi, parametry dotazu a hlaviček, které ovlivňují výsledky hledání. 
  
Informace o hlavičkách, které by měly obsahovat požadavky najdete v tématu [hlavičky](#headers).  
  
Informace o parametrech dotazů, které by měly obsahovat požadavky najdete v tématu [parametrů dotazu](#query-parameters).  
  
Informace o formátu JSON objekty, že odpověď obsahuje, najdete v části [odpovědi objekty](#response-objects).

Délka adresy URL maximální dotazu je 2 048 znaků. Aby se zajistilo, že vaše délka adresy URL není delší než tento limit, maximální délka parametry dotazu by měla být menší než 1 500 znaků. Pokud adresa URL delší než 2 048 znaků, server vrátí 404 nebyl nalezen.  

Informace o povolených použití a zobrazení výsledků najdete v tématu [používat a zobrazit požadavky](use-display-requirements.md). 

> [!NOTE]
> Některé hlavičky žádosti, které jsou srozumitelné pro ostatní rozhraní API pro vyhledávání neovlivňuje Náhled adresy URL
> - Pragma – volající nemá kontrolu nad jestli Náhled adresy URL používá mezipaměť
> - Uživatelský Agent – prozatím se adresa Url rozhraní API Preview neposkytuje různé odezvy pro volání pocházející z počítačů, přenosných počítačů nebo mobilních.

> Navíc některé parametry nejsou aktuálně smysl pro adresu URL náhledu API, ale mohou být použity v budoucnosti pro vylepšené globalizace. 
 
## <a name="headers"></a>Záhlaví  
Následuje seznam hlaviček, které mohou zahrnovat žádostí a odpovědí.  
  
|Záhlaví|Popis|  
|------------|-----------------|   
|<a name="market" />BingAPIs trhu|Hlavička odpovědi.<br /><br /> Na trhu používané žádosti. Formulář je \<languageCode\>-\<countryCode\>. Například en US.|  
|<a name="traceid" />BingAPIs TraceId|Hlavička odpovědi.<br /><br /> ID položky protokolu, který obsahuje podrobnosti o žádosti. Když dojde k chybě, zachytit číslem ID této. Pokud si nejste schopni zjistit a vyřešte problém, zahrnují toto ID spolu s další informace, že zadáte tým podpory.|  
|<a name="subscriptionkey" />OCP-Apim-Subscription-Key|Hlavička požadované požadavku.<br /><br /> Klíč předplatného, který jste obdrželi při registraci pro tuto službu v [kognitivní služby](https://www.microsoft.com/cognitive-services/).|  
|<a name="clientid" />X-MSEdge-ClientID|Volitelné hlavička požadavku a odpovědi.<br /><br /> Bing používá tuto hlavičku zajišťuje uživatelům konzistentní chování napříč volání rozhraní API služby Bing. Bing často letů nové funkce a vylepšení a jako klíč pro přiřazení provoz na jiné letech používá ID klienta. Pokud nepoužijete stejný ID klienta pro uživatele pro víc požadavků, pak Bing může přiřadit uživatele k několika lety konfliktní. Přiřazení k několika lety konfliktní může vést k nekonzistentním uživatelské prostředí. Například pokud má druhý požadavek přiřazení jiné cestě než první, prostředí může neočekávaná. Navíc můžete použít ID klienta pro přizpůsobení webové výsledky pro tohoto klienta Bing ID, která historii hledání, poskytování a pestřejší prostředí pro uživatele.<br /><br /> Tuto hlavičku Bing také používá k vylepšení výsledek hodnocení analýzou aktivitu generovanou podle ID klienta. Vylepšení relevance pomůže s lepší kvality výsledků doručeny podle rozhraní API pro Bing a zase umožňuje vyšší kliknutí sazby pro příjemce rozhraní API.<br /><br />Níže jsou uvedeny základní informace o využití pravidla, která se vztahují k této hlavičce.<br /><ul><li>Každý uživatel, který používá vaše aplikace na tomto zařízení musí mít jedinečný, Bing vygeneruje ID klienta.<br /><br/>Pokud neuvedete této hlavičky v požadavku, Bing vygeneruje ID a vrátí ji v záhlaví X-MSEdge-ClientID odpovědi. Jenom jednou, můžete tuto hlavičku obsahovat není v požadavku je prvním uživatel používá aplikaci na daném zařízení.<br /><br/></li><li>Použijte ID klienta pro každého požadavku rozhraní API služby Bing, které vytváří vaše aplikace pro tohoto uživatele na zařízení.<br /><br/></li><li>**Upozornění:** Ujistěte se, že tohoto ID klienta není možné korelovat k nějaké informace o účtu authenticatable uživateli.</li><br/><li>Zachovat ID klienta. Udržení ID v aplikaci Prohlížeč, použijte trvalého souboru cookie HTTP k zajištění, že Identifikátor se používá napříč všechny relace. Nepoužívejte souboru cookie relace. Pro jiné aplikace, jako je například mobilní aplikace použijte trvalého úložiště v zařízení k uchování ID.<br /><br/>Při příštím uživatel používá aplikaci na zařízení, získání ID klienta, který jste jako trvalé.</li></ul><br /> **Poznámka:** Bing odpovědí může nebo nemusí obsahovat hlavičku. Pokud odpověď obsahuje hlavičku, zaznamenat ID klienta a použít jej pro všechny následné žádosti Bingu pro uživatele na tomto zařízení.<br /><br /> **Poznámka:** Pokud zahrnete X-MSEdge-ClientID, nesmí obsahovat soubory cookie v požadavku.|  
|<a name="clientip" />X-MSEdge když|Hlavička požadavku volitelné.<br /><br /> IPv4 nebo IPv6 adresa klientského zařízení. IP adresa se používá ke zjišťování umístění uživatele. Bing používá k určení chování bezpečné vyhledávání informace o umístění.<br /><br />  Není obfuskováním adresu (například změnou poslední oktet 0). Zmatení data adresu výsledky v umístění nebude kdekoli téměř skutečné umístění zařízení, což může vést k Bing obsluhující chybné výsledky.|  
<br /><br /></li></ul>   

## <a name="query-parameters"></a>Parametry dotazu  
Žádost může zahrnovat následující parametry dotazu. V tématu požadovaný sloupec pro požadované parametry. Adresa URL, je potřeba kódování parametry dotazu. Dotaz musí být absolutní adresu URL pomocí protokolu http nebo https schématu; Nepodporujeme relativní adresy URL nebo jiná schémata jako ftp: / /
  
  
|Název|Hodnota|Typ|Požaduje se|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Na trhu, odkud pocházejí výsledky. <br /><br />Seznam možných hodnot trhu najdete v tématu [trhu kódy](#market-codes).<br /><br /> **Poznámka:** rozhraní API Preview URL aktuálně podporuje jenom USA Geografie a anglickém jazyce.<br /><br />|Řetězec|Ano|  
|<a name="query" />OTÁZKY|Adresa URL náhled|Řetězec|Ano|  
|<a name="responseformat" />formátu responseFormat|Typ média pro použit pro odpověď. Dále jsou možné hodnoty velká a malá písmena.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Výchozí hodnota je JSON. Informace o formátu JSON objekty, že odpověď obsahuje, najdete v části [odpovědi objekty](#response-objects).<br /><br />  Pokud zadáte JsonLd, zahrnuje text odpovědi JSON-LD objektů, které obsahují výsledky hledání. Informace o JSON-LD najdete v tématu [JSON-LD](http://json-ld.org/).|Řetězec|Ne|
|<a name="safesearch"/>bezpečné hledání|Neplatný obsah pro dospělé nebo nelegální obsahu, je blokovaný s kódem chyby 400 a *isFamilyFriendly* příznak nevrátí. <p>Pro právní obsah pro dospělé je nižší než chování. Stavový kód vrátí hodnotu 200 a *isFamilyFriendly* příznak nastaven na hodnotu false.<ul><li>bezpečné hledání = strict: název, popis, adresa URL, bitové kopie nejsou k dispozici.</li><li>bezpečné hledání = mírný; Získáte název, URL a popis ale není popisný bitové kopie.</li><li>bezpečné hledání = vypnuto; Získáte všechny odpovědi objekty nebo prvky – název, URL, popis a bitové kopie.</li></ul> |Řetězec|Není požadováno. </br> Výchozí nastavení je bezpečné hledání = strict.| 

## <a name="response-objects"></a>Objekty odpovědi  
Schéma odpovědi je buď [webové stránky] nebo ErrorResponse jako vyhledávání webového rozhraní API. Pokud se požadavek nezdaří, je objekt nejvyšší úrovně [ErrorResponse](#errorresponse) objektu.


|Objekt|Popis|  
|------------|-----------------|  
|[Webová stránka](#webpage)|Nejvyšší úrovně objekt JSON obsahující atributy Preview.|  

  
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
  

### <a name="webpage"></a>Webová stránka  
Definuje informace o webové stránce ve verzi preview.  
  
|Název|Hodnota|Typ|  
|----------|-----------|----------|
|jméno|Název stránky, nikoli nutně Nadpis HTML|Řetězec|
|url|Adresu URL, která byla ve skutečnosti procházení (žádosti může provedli přesměrování)|Řetězec|  
|description|Stručný popis na stránce a obsahu|Řetězec|  
|isFamilyFriendly|Nejpřesnější pro položky v indexu webové; Toto zjišťování pouze na adresu URL a není obsah stránky se načte v reálném čase|Boolean|
|primaryImageOfPage/contentUrl|Adresu URL do reprezentativní bitové kopie pro zahrnutí do verze preview|Řetězec| 


### <a name="identifiable"></a>Osobní
|Název|Hodnota|Typ|  
|-------------|-----------------|----------|
|id|Identifikátor prostředku|Řetězec|
 

## <a name="error-codes"></a>Kódy chyb

Níže jsou uvedeny možné stavové kódy HTTP, které vrací žádost.  
  
|Stavový kód|Popis|  
|-----------------|-----------------|  
|200|Úspěch|  
|400|Jeden z parametrů dotazu nebyl nalezen nebo není platný.| 
|400|ServerError, subkódu ResourceError: požadovanou adresu URL není dosažitelná.|
|400|ServerError, subkódu ResourceError: požadovanou adresu URL nevrátil úspěch kód (včetně, pokud je vrácen HTTP 404)|
|400|InvalidRequest, subkódu blokované: požadovanou adresu URL může obsahovat obsah pro dospělé a byla blokována| 
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
- [Rychlý start C#](csharp.md)
- [Rychlý start Java](java-quickstart.md)
- [Rychlý start JavaScript](javascript.md)
- [Rychlé spuštění uzlu](node-quickstart.md)
- [Rychlý start Python](python-quickstart.md)

