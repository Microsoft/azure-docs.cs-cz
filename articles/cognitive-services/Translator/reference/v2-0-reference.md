---
title: Translator Text API V2.0
titleSuffix: Azure Cognitive Services
description: Referenční dokumentace pro verze 2.0 Translator Text API.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: v-jansko
ms.openlocfilehash: 9491550aae8f88621d947572741f492adcf9cdd0
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55463221"
---
# <a name="translator-text-api-v20"></a>Translator Text API v2.0

> [!IMPORTANT]
> Tato verze rozhraní Translator Text API je zastaralá. [Zobrazit dokumentaci rozhraní Translator Text API v3](v3-0-reference.md).

Translator Text API V2 můžete bez problémů integrovat do vašich aplikací, webů, nástrojů nebo jiná řešení poskytují vícejazyčné uživatelské prostředí. Využívá oborové standardy, ho lze použít na jakékoli platformě hardwaru a s libovolným operačním systémem k provedení překladu jazyka a další operace týkající se jazyka, jako je například rozpoznávání jazyka textu nebo převod textu na řeč. Další informace o rozhraní Microsoft Translator API, klikněte sem.

## <a name="getting-started"></a>Začínáme
Pro přístup k rozhraní Translator Text API je potřeba [zaregistrovat do Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authorization"></a>Autorizace
Všechna volání rozhraní Translator Text API vyžadují klíč předplatného k ověření. Rozhraní API podporuje dva režimy ověřování:

* Pomocí přístupového tokenu. Použijte klíč předplatného odkazuje **krok** 9 k vygenerování přístupového tokenu tím, že požadavek POST do služby autorizace. Viz podrobnosti naleznete v dokumentaci služby tokenů. Přístupový token předejte službě Translator pomocí autorizační hlavičky nebo parametr dotazu access_token. Přístupový token je platný 10 minut. Získejte nový přístupový token každých 10 minut a mohli dál využívat stejný přístup token pro opakované požadavky v rámci těchto 10 minut.

* Přímo pomocí klíč předplatného. Předejte klíč předplatného. jako hodnotu v `Ocp-Apim-Subscription-Key` záhlaví vaši žádost o rozhraní Translator API je součástí. V tomto režimu není nutné volat ověřovací token služby k vygenerování přístupového tokenu.

Vezměte v úvahu váš klíč předplatného a přístupový token jako tajné kódy, které by měl být skryta.

## <a name="profanity-handling"></a>Zpracování vulgárních výrazů
Službu Translator obvykle zachová vulgárních výrazů, který je k dispozici ve zdroji v překladu. Stupeň vulgárních výrazů a kontext, který umožňuje neslušná slova se liší mezi jazykové verze, a díky tomu mohou rozšířit nebo nižší stupeň vulgárních výrazů v cílovém jazyce.

Pokud chcete předejít vulgárních výrazů v překladu, bez ohledu na přítomnost vulgární výrazy ve zdrojovém textu, můžete použít vulgárních výrazů pro metody, které ji podporují možnost filtrování. Možnost můžete zvolit, jestli chcete zobrazit vulgárních výrazů odstraněn nebo označené odpovídající značky nebo nebyla provedena žádná akce. Přijímané hodnoty `ProfanityAction` jsou `NoAction` (výchozí), označené a `Deleted`.


|ProfanityAction    |Akce |Příklad zdroje (japonština)  |Příklad překladu (v angličtině)  |
|:--|:--|:--|:--|
|NoAction   |Default (Výchozí). Stejně jako nastavit možnost. Vulgárních výrazů předá ze zdroje do cíle.        |彼はジャッカスです。     |Je jackass.   |
|Označené     |Urážlivá slova umístí značky XML <profanity> a </profanity>.     |彼はジャッカスです。 |Je <profanity>jackass</profanity>.    |
|Odstraněno    |Urážlivá slova se odebere z výstupu bez nahrazení.     |彼はジャッカスです。 |Je.   |

    
## <a name="excluding-content-from-translation"></a>S výjimkou obsahu z překladu
Při překládání obsahu pomocí značek, například HTML (`contentType=text/html`), je někdy užitečné chcete vyloučit z překladu konkrétní obsah. Můžete použít atribut `class=notranslate` k určení obsahu, který by měla zůstat v původním jazyce. V následujícím příkladu je obsah uvnitř první `div` element nebude přenesen, při obsah ve druhém `div` element bude fungovat.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>ZÍSKAT / převést

### <a name="implementation-notes"></a>Poznámky k implementaci
Převede textový řetězec z jednoho jazyka do druhého.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Návratová hodnota:** Řetězec představující přeloženého textu.

Pokud jste dříve používali `AddTranslation` nebo `AddTranslationArray` zadat překlad hodnocení 5 nebo novější pro stejný zdroj věty, `Translate` vrátí pouze volba, která je k dispozici pro váš systém. "Stejný zdroj větu" znamená, že přesně stejné (odpovídající 100 %), s výjimkou malá a velká písmena, mezery, hodnoty značek a interpunkční znaménka na konci věty. Pokud žádné hodnocení se uloží spolu s hodnocení 5 nebo vyšší budou vrácený výsledek automatický překlad pomocí Microsoft Translatoru.

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)

řetězec

Typ obsahu odpovědi: application/xml 

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis    |Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|ID aplikace  |(prázdné)    |Povinná hodnota. Pokud hlavička autorizace nebo Ocp-Apim-Subscription-Key se používá, ponechejte tuto položku appid pole prázdné jinak obsahovat řetězec obsahující "Nosiče" + "" + "access_token".|query|řetězec|
|text|(prázdné)   |Povinná hodnota. Řetězec představující text k přeložení. Velikost textu nesmí být delší než 10000 znaků.|query|řetězec|
|od|(prázdné)   |Volitelné. Řetězec představující kód jazyka textu překlad. Například en pro angličtinu.|query|řetězec|
|na|(prázdné) |Povinná hodnota. Řetězec představující kód jazyka můžete přeložit text do.|query|řetězec|
|contentType|(prázdné)    |Volitelné. Formát textu, který je překládán. Podporované formáty jsou text/plain (výchozí) a text/html. Musí být ve správném formátu, dokončení element veškeré kódování HTML.|query|řetězec|
|category|(prázdné)   |Volitelné. Řetězec obsahující kategorie překladu (domény). Výchozí hodnota je "general".|query|řetězec|
|Autorizace|(prázdné)  |Povinné, pokud není zadán appid pole nebo záhlaví Ocp-Apim-Subscription-Key. Autorizační token:  "Nosiče" + "" + "access_token".|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)  |Povinné, pokud není zadán appid pole nebo autorizační hlavičky.|záhlaví|řetězec|


### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatná pověření|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi X-MS-Trans-Info.|
|503    |Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-translatearray"></a>Publikovat /TranslateArray

### <a name="implementation-notes"></a>Poznámky k implementaci
Použití `TranslateArray` metodu pro načtení překlady pro více textů zdroje.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Formát textu požadavku by měl vypadat takto:

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

Prvky v rámci `TranslateArrayRequest` jsou:


* `appid`: Povinná hodnota. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte tuto položku appid pole prázdné jinak zahrnout řetězec obsahující `"Bearer" + " " + "access_token"`.
* `from`: Volitelné. Řetězec představující kód jazyka můžete přeložit text z. Pokud zůstane prázdná odpověď bude obsahovat výsledek automatické rozpoznávání jazyka.
* `options`: Volitelné. `Options` Objekt, který obsahuje níže uvedené hodnoty. Jsou nepovinné a nejběžnější nastavení ve výchozím nastavení. Zadané elementy musí být uvedeny v abecedním pořadí.
    - `Category`: Řetězec obsahující kategorie překladu (domény). Výchozí hodnota je `general`.
    - `ContentType`: Formát textu, který je překládán. Podporované formáty jsou `text/plain` (výchozí), `text/xml` a `text/html`. Musí být ve správném formátu, dokončení element veškeré kódování HTML.
    - `ProfanityAction`: Určuje, jak se zpracovává profanities, jak je vysvětleno výše. Přijmout hodnoty `ProfanityAction` jsou `NoAction` (výchozí), `Marked` a `Deleted`.
    - `State`: Stav uživatele korelovat požadavku a odpovědi. Vrátí se stejným obsahem v odpovědi.
    - `Uri`: Filtrovat výsledky podle tohoto identifikátoru URI. Výchozí hodnota: `all`.
    - `User`: Filtrovat výsledky podle tohoto uživatele. Výchozí hodnota: `all`.
* `texts`: Povinná hodnota. Pole obsahující texty k překladu. Všechny řetězce musí být stejný jazyk. Celkový součet všech texty k převodu nesmí být delší než 10000 znaků. Maximální počet elementů pole je 2000.
* `to`: Povinná hodnota. Řetězec představující kód jazyka můžete přeložit text do.

Volitelné prvky lze vynechat. Elementy, které jsou přímo podřízené TranslateArrayRequest musí být uvedeny v abecedním pořadí.

Metoda TranslateArray přijímá `application/xml` nebo `text/xml` pro `Content-Type`.

**Návratová hodnota:** A `TranslateArrayResponse` pole. Každý `TranslateArrayResponse` obsahuje následující prvky:

* `Error`: Označuje chybu, pokud došlo. Jinak nastavený na hodnotu null.
* `OriginalSentenceLengths`: Pole celých čísel identifikující délku jednotlivé věty v původní zdrojový text. Délka pole označuje počet věty.
* `TranslatedText`: Přeložený text.
* `TranslatedSentenceLengths`: Pole celých čísel identifikující délku jednotlivé věty v přeloženého textu. Délka pole označuje počet věty.
* `State`: Stav uživatele korelovat požadavku a odpovědi. Vrací stejný obsah, jako v žádosti.

Formát datové části odpovědi je následujícím způsobem.

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)
Úspěšná odpověď obsahuje celou řadu `TranslateArrayResponse` ve formátu, je popsáno výše.

řetězec

Typ obsahu odpovědi: application/xml

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|Autorizace|(prázdné)) |Povinné, pokud není zadán appid pole nebo záhlaví Ocp-Apim-Subscription-Key. Autorizační token:  "Nosiče" + "" + "access_token".|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Povinné, pokud není zadán appid pole nebo autorizační hlavičky.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP   |Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi. Běžné chyby patří: <ul><li>Element pole nemůže být prázdný</li><li>Neplatná kategorie</li><li>Z jazyka je neplatný</li><li>Jazyk je neplatný</li><li>Požadavek obsahuje příliš mnoho elementů</li><li>Od jazyk není podporován.</li><li>Na jazyk není podporován.</li><li>Přeložit požadavek má příliš mnoho dat.</li><li>HTML není ve správném formátu</li><li>V požadavku překlad bylo předáno příliš mnoho řetězců</li></ul>|
|401    |Neplatná pověření|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi X-MS-Trans-Info.|
|503    |Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-getlanguagenames"></a>/GetLanguageNames příspěvku

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte popisné názvy pro jazyky předaného jako parametr `languageCodes`a lokalizované v jazyce předané národní prostředí.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

Text požadavku obsahuje pole řetězců představující kódy ISO 639-1 jazyků k načtení popisné názvy. Příklad:

```
<ArrayOfstring xmlns:i="http://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Návratová hodnota:** Pole řetězců obsahující názvy jazyků podporovaných službu Translator, lokalizován do požadovaný jazyk.

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)
Pole řetězců obsahující názvy jazyků podporovaných službu Translator, lokalizován do požadovaný jazyk.

řetězec

Typ obsahu odpovědi: application/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|ID aplikace|(prázdné)|Povinná hodnota. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte tuto položku appid pole prázdné jinak zahrnout řetězec obsahující `"Bearer" + " " + "access_token"`.|query|řetězec|
|národní prostředí|(prázdné) |Povinná hodnota. Řetězec představující kombinaci kód ISO 639 dvoupísmenné malá jazykové verze přidružený jazyk a kód ISO 3166 velká subkulturu dvoupísmenné lokalizovat názvy jazyků nebo malými písmeny kód ISO 639 samostatně.|query|řetězec|
|Autorizace|(prázdné)  |Požadováno pokud pole appid nebo `Ocp-Apim-Subscription-Key` není zadána hlavička. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)  |Požadováno pokud pole appid nebo `Authorization` není zadána hlavička.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatná pověření|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi X-MS-Trans-Info.|
|503    |Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-getlanguagesfortranslate"></a>ZÍSKAT /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Poznámky k implementaci
Získáte seznam kódů pro jazyky, které podporuje službu překladu.  `Translate` a `TranslateArray` jsou dobře převeditelné mezi dvěma z těchto jazyků.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Návratová hodnota:** Pole řetězců obsahující kód jazyka podporované službou Translator.

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)
Pole řetězců obsahující kód jazyka podporované službou Translator.

řetězec

Typ obsahu odpovědi: application/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|ID aplikace|(prázdné)|Povinná hodnota. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte tuto položku appid pole prázdné jinak zahrnout řetězec obsahující `"Bearer" + " " + "access_token"`.|query|řetězec|
|Autorizace|(prázdné)  |Požadováno pokud `appid` pole nebo `Ocp-Apim-Subscription-Key` není zadána hlavička. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Požadováno pokud `appid` pole nebo `Authorization` není zadána hlavička.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatná pověření|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi X-MS-Trans-Info.|
|503|Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-getlanguagesforspeak"></a>ZÍSKAT /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte jazyky dostupné pro syntézu řeči.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Návratová hodnota:** Pole řetězců obsahující kód jazyka nepodporuje službu Translator pro syntézu řeči.

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)
Pole řetězců obsahující kód jazyka nepodporuje službu Translator pro syntézu řeči.

řetězec

Typ obsahu odpovědi: application/xml

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|ID aplikace|(prázdné)|Povinná hodnota. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte tuto položku appid pole prázdné jinak zahrnout řetězec obsahující `"Bearer" + " " + "access_token"`.|query|řetězec|
|Autorizace|(prázdné)|Požadováno pokud `appid` pole nebo `Ocp-Apim-Subscription-Key` není zadána hlavička. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Požadováno pokud `appid` pole nebo `Authorization` není zadána hlavička.|záhlaví|řetězec|
 
### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400|Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401|Neplatná pověření|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-speak"></a>GET / řeči

### <a name="implementation-notes"></a>Poznámky k implementaci
Vrací wave nebo mp3 datový proud, který se používá v požadovaném jazyce předané v textu.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Návratová hodnota:** Wave nebo mp3 proud předaný textu se používá v požadovaném jazyce.

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)

Binární

Typ obsahu odpovědi: application/xml 

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|ID aplikace|(prázdné)|Povinná hodnota. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte tuto položku appid pole prázdné jinak zahrnout řetězec obsahující `"Bearer" + " " + "access_token"`.|query|řetězec|
|text|(prázdné)   |Povinná hodnota. Řetězec obsahující věty nebo věty zadaného jazyka, který má být používaný wave Stream. Velikost textu mluvit nesmí přesáhnout 2000 znaků.|query|řetězec|
|language|(prázdné)   |Povinná hodnota. Řetězec představující kód podporovaného jazyka mluvit textu. Kód musí být uvedený v seznamu kódů vrátil z metody `GetLanguagesForSpeak`.|query|řetězec|
|formát|(prázdné)|Volitelné. Řetězec určující identifikátor typu obsahu V současné době `audio/wav` a `audio/mp3` jsou k dispozici. Výchozí hodnota je `audio/wav`.|query|řetězec|
|Možnosti|(prázdné)    |<ul><li>Volitelné. Řetězec určující vlastnosti syntetizovaný řeči:<li>`MaxQuality` a `MinSize` je možné určit kvalitu zvukového signálu. S `MaxQuality`, můžete získat hlasy s nejvyšší kvalitu a `MinSize`, můžete získat hlasy s nejmenší velikost. Výchozí hodnota je `MinSize`.</li><li>`female` a `male` je možné určit požadovanou pohlaví hlasu. Výchozí hodnota je `female`. Použít svislá čára.|` to include multiple options. For example  `MaxQuality|Muž ".</li></li></ul> |query|řetězec|
|Autorizace|(prázdné)|Požadováno pokud `appid` pole nebo `Ocp-Apim-Subscription-Key` není zadána hlavička. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)  |Požadováno pokud `appid` pole nebo `Authorization` není zadána hlavička.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatná pověření|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-detect"></a>ZÍSKAT / Detect

### <a name="implementation-notes"></a>Poznámky k implementaci
Použití `Detect` metodu, jak identifikovat jazyk vybrané části textu.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Návratová hodnota:** Řetězec obsahující dvoumístný kód jazyka pro daný text. .

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)

řetězec

Typ obsahu odpovědi: application/xml

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|ID aplikace|(prázdné)  |Povinná hodnota. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte tuto položku appid pole prázdné jinak zahrnout řetězec obsahující `"Bearer" + " " + "access_token"`.|query|řetězec|
|text|(prázdné)|Povinná hodnota. Řetězec obsahující text, jehož jazyk je možné identifikovat. Velikost textu nesmí být delší než 10000 znaků.|query| řetězec|
|Autorizace|(prázdné)|Požadováno pokud `appid` pole nebo `Ocp-Apim-Subscription-Key` není zadána hlavička. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key  |(prázdné)    |Požadováno pokud `appid` pole nebo `Authorization` není zadána hlavička.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400|Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatná pověření|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|


## <a name="post-detectarray"></a>Publikovat /DetectArray

### <a name="implementation-notes"></a>Poznámky k implementaci
Použití `DetectArray` metodu, jak identifikovat jazyk pole řetězce najednou. Provádí nezávislé detekce jednotlivých prvků jednotlivá pole a vrátí výsledek pro každý řádek pole.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Formát textu požadavku by měl vypadat takto.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Velikost textu nesmí být delší než 10000 znaků.

**Návratová hodnota:** Pole řetězců obsahující dvou znaků jazyka kódy pro každý řádek vstupního pole.

Formát datové části odpovědi je následujícím způsobem.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)
DetectArray bylo úspěšné. Vrátí pole řetězců obsahující dvou znaků jazyka kódy pro každý řádek vstupního pole.

řetězec

Typ obsahu odpovědi: application/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|ID aplikace|(prázdné)|Povinná hodnota. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte tuto položku appid pole prázdné jinak zahrnout řetězec obsahující `"Bearer" + " " + "access_token"`.|query|řetězec|
|Autorizace|(prázdné)|Požadováno pokud `appid` pole nebo `Ocp-Apim-Subscription-Key` není zadána hlavička. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Požadováno pokud `appid` není zadáno pole nebo autorizační hlavičky.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatná pověření|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi X-MS-Trans-Info.|
|503    |Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-addtranslation"></a>ZÍSKAT /AddTranslation

### <a name="implementation-notes"></a>Poznámky k implementaci

> [!IMPORTANT]
> **POZNÁMKA: VYŘAZENÍ:** Po 31. ledna 2018 tato metoda nebude přijímat nové příspěvky věty a zobrazí se chybová zpráva. Najdete na toto oznámení o změnách na spolupráci funkce překladu.

Přidá překlad do paměti překladu.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)

řetězec

Typ obsahu odpovědi: aplikace: xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat   |
|:--|:--|:--|:--|:--|
|ID aplikace|(prázdné)|Povinná hodnota. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte tuto položku appid pole prázdné jinak zahrnout řetězec obsahující `"Bearer" + " " + "access_token"`.|query|řetězec|
|originalText|(prázdné)|Povinná hodnota. Řetězec obsahující text na překlad z. Řetězec má maximální délku 1000 znaků.|query|řetězec|
|translatedText|(prázdné) |Povinná hodnota. Řetězec obsahující přeložený text v cílovém jazyce. Řetězec má maximální délku 2000 znaků.|query|řetězec|
|od|(prázdné)   |Povinná hodnota. Řetězec představující kód jazyka textu překlad. cs = angličtina, de = německé atd...|query|řetězec|
|na|(prázdné)|Povinná hodnota. Řetězec představující kód jazyka můžete přeložit text do.|query|řetězec|
|rating|(prázdné) |Volitelné. Celé číslo představující hodnocení kvality pro tento řetězec. Hodnotu od -10 do 10. Výchozí hodnota je 1.|query|integer|
|contentType|(prázdné)    |Volitelné. Formát textu, který je překládán. Podporované formáty jsou "text/plain" a "text/html". Musí být ve správném formátu, dokončení element veškeré kódování HTML.   |query|řetězec|
|category|(prázdné)|Volitelné. Řetězec obsahující kategorie překladu (domény). Výchozí hodnota je "general".|query|řetězec|
|uživatel|(prázdné)|Povinná hodnota. Řetězec lze sledovat odesílatel požadavku dostane informaci odeslání.|query|řetězec|
|identifikátor uri|(prázdné)|Volitelné. Řetězec obsahující umístění obsahu tohoto převodu.|query|řetězec|
|Autorizace|(prázdné)|Požadováno pokud pole appid nebo `Ocp-Apim-Subscription-Key` není zadána hlavička. Autorizační token: `"Bearer" + " " + "access_token"`.    |záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Požadováno pokud `appid` pole nebo `Authorization` není zadána hlavička.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatná pověření|
|410|AddTranslation se už nepodporuje.|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi X-MS-Trans-Info.|
|503    |Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-addtranslationarray"></a>Publikovat /AddTranslationArray

### <a name="implementation-notes"></a>Poznámky k implementaci

> [!IMPORTANT]
> **POZNÁMKA: VYŘAZENÍ:** Po 31. ledna 2018 tato metoda nebude přijímat nové příspěvky věty a zobrazí se chybová zpráva. Najdete na toto oznámení o změnách na spolupráci funkce překladu.

Přidá pole překlady přidat překlad paměti. Toto je verze pole `AddTranslation`.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Formát textu požadavku je následujícím způsobem.

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

Prvky v rámci elementu AddtranslationsRequest jsou:

* `AppId`: Povinná hodnota. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte tuto položku appid pole prázdné jinak zahrnout řetězec obsahující `"Bearer" + " " + "access_token"`.
* `From`: Povinná hodnota. Řetězec obsahující kód jazyka zdroje jazyka. Musí být jeden z těchto jazyků vrácené `GetLanguagesForTranslate` metody.
* `To`: Povinná hodnota. Řetězec obsahující kód jazyka v cílovém jazyce. Musí být jeden z těchto jazyků vrácené `GetLanguagesForTranslate` metody.
* `Translations`: Povinná hodnota. Pole překlady pro přidání do paměti překladu. Každý překlad musí obsahovat: originalText translatedText a hodnocení. Velikost jednotlivých originalText a translatedText je omezena na 1 000 znaků. Celkový součet všech originalText(s) a translatedText(s) nesmí být delší než 10000 znaků. Maximální počet elementů pole je 100.
* `Options`: Povinná hodnota. Sadu možností, včetně kategorií, ContentType, identifikátor Uri a uživatele. Uživatel je vyžadován. Kategorie, ContentType a identifikátor Uri jsou volitelné. Zadané elementy musí být uvedeny v abecedním pořadí.

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)
Metoda AddTranslationArray bylo úspěšné. Po 31. ledna 2018 odesílání věty, nebude změna přijata. Služba bude reagovat s kódem chyby 410.

řetězec

Typ obsahu odpovědi: application/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|Autorizace|(prázdné)|Povinné, pokud není zadán appid pole nebo záhlaví Ocp-Apim-Subscription-Key. Autorizační token:  "Nosiče" + "" + "access_token".|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Povinné, pokud není zadán appid pole nebo autorizační hlavičky.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatná pověření|
|410    |AddTranslation se už nepodporuje.|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
|503|Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-breaksentences"></a>ZÍSKAT /BreakSentences

### <a name="implementation-notes"></a>Poznámky k implementaci
Část textu dělí na věty a vrátí pole obsahující délky ve větě.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Návratová hodnota:** Pole celých čísel reprezentujících délky vět. Délka pole je počet věty a hodnoty jsou délka jednotlivé věty.

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)
Pole celých čísel reprezentujících délky vět. Délka pole je počet věty a hodnoty jsou délka jednotlivé věty.

integer

Typ obsahu odpovědi: application/xml 

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|ID aplikace|(prázdné)  |Povinná hodnota. Pokud hlavička autorizace nebo Ocp-Apim-Subscription-Key se používá, ponechejte tuto položku appid pole prázdné jinak obsahovat řetězec obsahující "Nosiče" + "" + "access_token".|query| řetězec|
|text|(prázdné)   |Povinná hodnota. Řetězec představující text, který má rozdělení na věty. Velikost textu nesmí být delší než 10000 znaků.|query|řetězec|
|language   |(prázdné)    |Povinná hodnota. Řetězec představující kód jazyka vstupního textu.|query|řetězec|
|Autorizace|(prázdné)|Povinné, pokud není zadán appid pole nebo záhlaví Ocp-Apim-Subscription-Key. Autorizační token:  "Nosiče" + "" + "access_token".    |záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Povinné, pokud není zadán appid pole nebo autorizační hlavičky.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400|Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401|Neplatná pověření|
|500|Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi X-MS-Trans-Info.|
|503|Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-gettranslations"></a>/GetTranslations příspěvku

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte pole posunutí pro daný jazyk pár z úložiště a modul MT. GetTranslations se liší od přeložit, protože vrátí všechny dostupné překlady.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

Text žádosti obsahuje volitelné TranslationOptions objekt, který má následující formát.

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

`TranslateOptions` Objekt obsahuje níže uvedené hodnoty. Jsou nepovinné a nejběžnější nastavení ve výchozím nastavení. Zadané elementy musí být uvedeny v abecedním pořadí.

* `Category`: Řetězec obsahující kategorie překladu (domény). Výchozí hodnota je "general".
* `ContentType`: Jediný podporovaný a výchozí hodnota, je možnost "text/plain".
* `IncludeMultipleMTAlternatives`: logický příznak k určení, zda má být vrácen více než jeden alternativy MT modul. Platné hodnoty jsou true a false (malá a velká písmena). Výchozí hodnotu false a obsahuje pouze 1 alternativu. Nastavení příznaku na hodnotu true umožňuje generování umělé alternativy v překladu, nabízí plnou integraci s spolupráci překlady framework (CTF). Tato funkce umožňuje vrácení alternativy na věty, které mají žádné alternativy CTF, přidáním umělé alternativy ze seznamu n-best dekodéru.
    - Hodnocení hodnocení se použijí následujícím způsobem: (1) na nejlepší automatický překlad má hodnocení 5. (2) alternativ z CTF odrážet oprávnění kontrolor, -10 do + 10. (3) alternativ automaticky generované překladu (n-best) mají hodnocení 0 a shoda stupeň 100.
    - Počet alternativy záleží maxTranslations počet vrácených alternativy, ale může být nižší.
    - Dvojice jazyků tato funkce není k dispozici pro překlad mezi zjednodušená a tradiční čínštiny, obou směrech. Je k dispozici pro všechny ostatní dvojice jazyků Microsoft Translatoru podporována.
* `State`: Stav uživatele korelovat požadavku a odpovědi. Vrátí se stejným obsahem v odpovědi.
* `Uri`: Filtrovat výsledky podle tohoto identifikátoru URI. Pokud není nastavena žádná hodnota, výchozí hodnota je všechny.
* `User`: Filtrovat výsledky podle tohoto uživatele. Pokud není nastavena žádná hodnota, výchozí hodnota je všechny.

Požádat o `Content-Type` by měl být `text/xml`.

**Návratová hodnota:** Formát odpovědi je následujícím způsobem.

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

Jedná se `GetTranslationsResponse` elementu, který obsahuje následující hodnoty:

* `Translations`: Najít pole odpovídá uložené v objektech TranslationMatch (viz níže). Překlady je možné mírné varianty původního textu (přibližné shody). Překlady budou seřazeny: 100 % odpovídá nejprve přibližné shody níže.
* `From`: Pokud metoda neurčil From jazyk, bude výsledkem automatické rozpoznávání jazyka. V opačném případě bude daném z jazyka.
* `State`: Stav uživatele korelovat požadavku a odpovědi. Obsahuje stejnou hodnotu, jak je uvedeno v parametru TranslateOptions.

Objekt TranslationMatch se skládá z následujících akcí:

* `Error`: Pokud pro konkrétní vstupní řetězec došlo k chybě, kód chyby je uložen. V opačném případě je pole prázdné.
* `MatchDegree`: Systém odpovídá vstupní věty proti úložišti, včetně té neodpovídá.  MatchDegree označuje, jak blízko vstupní text odpovídá původní text v úložišti nalezena. Vrácená hodnota rozsahy od 0 do 100, kde 0 je žádné podobnosti a 100 je velká a malá písmena přesně shodovat.
MatchedOriginalText: Původní text, odpovídající pro tento výsledek. Pouze pokud odpovídající původní text byla vrácena jiná než vstupního textu. Používá k vrácení zdrojového textu přibližné shody. Nebyla vrácena pro Microsoft Translatoru výsledky.
* `Rating`: Označuje autority osoby, která rozhodnutí kvality. Strojový překlad výsledky budou mít hodnocení 5. Anonymně zadaná překlady má obecně vzato hodnocení 1 až 4, zatímco autoritativně zadaná překlady má obecně vzato hodnocení 6 až 10.
* `Count`: Počet pokusů, které tento překlad textů pomocí toto hodnocení se vybral. Hodnota bude 0 pro automaticky přeložené odpověď.
* `TranslatedText`: Přeložený text.

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)
A `GetTranslationsResponse` objektu ve formátu, je popsáno výše.

řetězec

Typ obsahu odpovědi: application/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|ID aplikace|(prázdné)|Povinná hodnota. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte tuto položku appid pole prázdné jinak zahrnout řetězec obsahující `"Bearer" + " " + "access_token"`.|query|řetězec|
|text|(prázdné)|Povinná hodnota. Řetězec představující text k přeložení. Velikost textu nesmí být delší než 10000 znaků.|query|řetězec|
|od|(prázdné)|Povinná hodnota. Řetězec představující kód jazyka textu překlad.|query|řetězec|
|na |(prázdné)    |Povinná hodnota. Řetězec představující kód jazyka můžete přeložit text do.|query|řetězec|
|maxTranslations|(prázdné)|Povinná hodnota. Celé číslo představující maximální počet překlady se vraťte.|query|integer|
|Autorizace| (prázdné)|Požadováno pokud `appid` pole nebo `Ocp-Apim-Subscription-Key` není zadána hlavička. Autorizační token: `"Bearer" + " " + "access_token"`.|řetězec| záhlaví|
|OCP-Apim-Subscription-Key|(prázdné)  |Požadováno pokud `appid` pole nebo `Authorization` není zadána hlavička.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatná pověření|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
|503|Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-gettranslationsarray"></a>Publikovat /GetTranslationsArray

### <a name="implementation-notes"></a>Poznámky k implementaci
Použití `GetTranslationsArray` metody k získání více překlad kandidáty pro více textů zdroje.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Formát textu požadavku je následujícím způsobem.

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest` obsahuje následující prvky:

* `AppId`: Povinná hodnota. Pokud se používá hodnota hlavičky autorizace, ponechejte tuto položku appid pole prázdné jinak zahrnout, řetězec obsahující `"Bearer" + " " + "access_token"`.
* `From`: Povinná hodnota. Řetězec představující kód jazyka textu překlad.
* `MaxTranslations`: Povinná hodnota. Celé číslo představující maximální počet překlady se vraťte.
* `Options`: Volitelné. Možnosti objekt, který obsahuje níže uvedené hodnoty. Jsou nepovinné a nejběžnější nastavení ve výchozím nastavení. Zadané elementy musí být uvedeny v abecedním pořadí.
    - Kategorie ": Řetězec obsahující kategorie překladu (domény). Výchozí hodnota je obecná.
    - `ContentType`: Jediný podporovaný a výchozí možností je text/plain.
    - `IncludeMultipleMTAlternatives`: logický příznak k určení, zda má být vrácen více než jeden alternativy MT modul. Platné hodnoty jsou true a false (malá a velká písmena). Výchozí hodnotu false a obsahuje pouze 1 alternativu. Nastavení příznaku na hodnotu true umožňuje generování umělé alternativy v překladu, nabízí plnou integraci s spolupráci překlady framework (CTF). Tato funkce umožňuje vrácení alternativy na věty, které mají žádné alternativy CTF, přidáním umělé alternativy ze seznamu n-best dekodéru.
        - Hodnocení hodnocení se použijí následujícím způsobem: (1) na nejlepší automatický překlad má hodnocení 5. (2) alternativ z CTF odrážet oprávnění kontrolor, -10 do + 10. (3) alternativ automaticky generované překladu (n-best) mají hodnocení 0 a shoda stupeň 100.
        - Počet alternativy záleží maxTranslations počet vrácených alternativy, ale může být nižší.
        - Dvojice jazyků tato funkce není k dispozici pro překlad mezi zjednodušená a tradiční čínštiny, obou směrech. Je k dispozici pro všechny ostatní dvojice jazyků Microsoft Translatoru podporována.
* `State`: Stav uživatele korelovat požadavku a odpovědi. Vrátí se stejným obsahem v odpovědi.
* `Uri`: Filtrovat výsledky podle tohoto identifikátoru URI. Pokud není nastavena žádná hodnota, výchozí hodnota je všechny.
* `User`: Filtrovat výsledky podle tohoto uživatele. Pokud není nastavena žádná hodnota, výchozí hodnota je všechny.
* `Texts`: Povinná hodnota. Pole obsahující texty k překladu. Všechny řetězce musí být stejný jazyk. Celkový součet všech texty k převodu nesmí být delší než 10000 znaků. Maximální počet elementů pole je 10.
* `To`: Povinná hodnota. Řetězec představující kód jazyka můžete přeložit text do.

Volitelné prvky lze vynechat. Elementy, které jsou přímo podřízené `GetTranslationsArrayRequest` musí být uvedeny v abecedním pořadí.

Požádat o `Content-Type` by měl být `text/xml`.

**Návratová hodnota:** Formát odpovědi je následujícím způsobem.

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

Každý `GetTranslationsResponse` prvek obsahuje následující hodnoty:

* `Translations`: Najít pole shod, uložené v `TranslationMatch` objekty (viz níže). Překlady je možné mírné varianty původního textu (přibližné shody). Překlady budou seřazeny: 100 % odpovídá nejprve přibližné shody níže.
* `From`: Pokud metoda nezadali `From` jazyk, bude výsledkem automatické rozpoznávání jazyka. V opačném případě bude daném z jazyka.
* `State`: Stav uživatele korelovat požadavku a odpovědi. Obsahuje stejnou hodnotu, jak je uvedeno v `TranslateOptions` parametru.

`TranslationMatch` objekt se skládá z následujících akcí:
* `Error`: Pokud pro konkrétní vstupní řetězec došlo k chybě, kód chyby je uložen. V opačném případě je pole prázdné.
* `MatchDegree`: Systém odpovídá vstupní věty proti úložišti, včetně té neodpovídá.  `MatchDegree` Určuje, jak blízko vstupní text odpovídá původní text v úložišti nalezena. Vrácená hodnota rozsahy od 0 do 100, kde 0 je žádné podobnosti a 100 je velká a malá písmena přesně shodovat.
* `MatchedOriginalText`: Původní text, odpovídající pro tento výsledek. Pouze pokud odpovídající původní text byla vrácena jiná než vstupního textu. Používá k vrácení zdrojového textu přibližné shody. Nebyla vrácena pro Microsoft Translatoru výsledky.
* `Rating`: Označuje autority osoby, která rozhodnutí kvality. Strojový překlad výsledky budou mít hodnocení 5. Anonymně zadaná překlady má obecně vzato hodnocení 1 až 4, zatímco autoritativně zadaná překlady má obecně vzato hodnocení 6 až 10.
* `Count`: Počet pokusů, které tento překlad textů pomocí toto hodnocení se vybral. Hodnota bude 0 pro automaticky přeložené odpověď.
* `TranslatedText`: Přeložený text.


### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)

řetězec

Typ obsahu odpovědi: application/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|Autorizace  |(prázdné)    |Požadováno pokud `appid` pole nebo `Ocp-Apim-Subscription-Key` není zadána hlavička. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)  |Požadováno pokud `appid` pole nebo `Authorization` není zadána hlavička.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatná pověření|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Migrace na v3 Translator Text API](../migrate-to-v3.md)










