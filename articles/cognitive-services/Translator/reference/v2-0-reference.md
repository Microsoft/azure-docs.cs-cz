---
title: Překladač Microsoft Text API V2.0 odkaz | Microsoft Docs
titleSuffix: Cognitive Services
description: Referenční dokumentace rozhraní API aktualizace V2.0 Microsoft překladač Text.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 05/15/2018
ms.author: v-jansko
ms.openlocfilehash: e32e28608d2fecf27b61acff74af7eb6849f0ba1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343620"
---
# <a name="translator-text-api-v20"></a>Překladač Text API v2.0

> [!IMPORTANT]
> Tato verze rozhraní API Text překladač je zastaralá. [Zobrazit dokumentaci rozhraní API Text překladač v3](v3-0-reference.md).

Rozhraní API služby Microsoft překladač V2 Text může bezproblémově integrovaná do aplikace, weby, nástroje nebo jiných řešení k poskytování vícejazyčné uživatelské prostředí. Využití oborových standardů, může sloužit na jakékoli platformě hardware a všechny operační systémy, k provádění překladu jazyka a dalších operací souvisejících se jazyk například zjišťování jazyka text nebo převod textu na řeč. Kliknutím sem získáte další informace o rozhraní API služby Microsoft překladač.

## <a name="getting-started"></a>Začínáme
Pro přístup k rozhraní API Text Microsoft překladač budete muset [registrace pro Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authorization"></a>Autorizace
Všechna volání rozhraní API služby Microsoft překladač Text vyžadují klíč předplatného k ověření. Rozhraní API podporuje dva režimy ověřování:

* Pomocí přístupového tokenu. Použijte klíč předplatného, kterou se odkazuje v **krok** 9 k vygenerování tokenu přístupu tím, že požadavek POST do služby autorizace. Najdete v dokumentaci služby tokenů podrobnosti. Předejte přístupový token služby překladač pomocí autorizační hlavičky nebo parametr dotazu access_token. Přístupový token je platný 10 minut. Získejte nový přístupový token každých 10 minut a udržovat stejný přístup pomocí tokenu pro opakované žádosti v rámci těchto 10 minut.

* Přímo pomocí klíč předplatného. Svůj klíč předplatného předat jako hodnota v `Ocp-Apim-Subscription-Key` záhlaví součástí vaši žádost o rozhraní API překladač. V tomto režimu nemáte volání ověřovací token služby k vygenerování tokenu přístupu.

Zvažte svůj klíč předplatného a přístupový token jako tajné klíče, které by měla být v zobrazení skrytá.

## <a name="profanity-handling"></a>Zpracování vulgárnost
Za normálních okolností službu překladač zachovají vulgárnost, která je k dispozici ve zdroji v překladu. Stupeň vulgárnost a kontext, který umožňuje slova znevažujícího liší pro jednotlivé jazykové verze, a proto může rozšířit nebo snížit stupeň vulgárnost v jazyce cíl.

Pokud chcete, aby se zabránilo získání vulgárnost v překlad, bez ohledu na přítomnost vulgárnost v zdrojový text můžete vulgárnost filtrování možnost pro metody, které ji podporují. Možnost umožňuje vybrat, zda chcete zobrazit vulgárnost odstraněna, nebo pokud označené odpovídající značky nebo nebyla provedena žádná akce. Přijaté hodnoty `ProfanityAction` jsou `NoAction` (výchozí), označené a `Deleted`.


|ProfanityAction    |Akce |Příklad zdroje (japonština)  |Příklad překlad (angličtina)  |
|:--|:--|:--|:--|
|NoAction   |Default (Výchozí). Stejné jako nastavení není možnost. Vulgárnost předá ze zdroje k cíli.        |彼はジャッカスです。     |Se jackass.   |
|Označit     |Znevažujícího slova bude obklopená značky XML <profanity> a </profanity>.     |彼はジャッカスです。 |Se <profanity>jackass</profanity>.    |
|Odstraněno    |Znevažujícího slova se odeberou z výstupu bez nahrazení.     |彼はジャッカスです。 |Se.   |

    
## <a name="excluding-content-from-translation"></a>S výjimkou obsahu z překlad
Při překladu obsah pomocí značek, například HTML (`contentType=text/html`), je někdy umožňuje vyloučit konkrétní obsah z překlad. Můžete použít atribut `class=notranslate` k určení obsahu, který by měla zůstat v původním jazyce. V následujícím příkladu, obsah uvnitř první `div` element nebude přeložit, při obsah za sekundu `div` element se přeložit.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>ZÍSKAT / převede

### <a name="implementation-notes"></a>Poznámky k implementaci
Převede textový řetězec z jednoho jazyka do druhého.

Identifikátor URI požadavku `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Návratová hodnota:** řetězec představující přeložený text.

Pokud jste už dřív použili `AddTranslation` nebo `AddTranslationArray` zadat překlad hodnocení 5 nebo vyšší pro stejný zdroj větu, `Translate` vrátí pouze nejvyšší volbou, která je k dispozici v systému. "Stejný zdroj větu" znamená přesné stejné (100 % párování), s výjimkou použití velkých písmen, mezer, hodnoty značky a interpunkce na konci věty. Pokud je uložen žádný hodnocení s hodnocení 5 nebo novější verze na vrácený výsledek bude automatické překlad pomocí Microsoft Translator.

### <a name="response-class-status-200"></a>Odpověď – třída (stav 200)

řetězec

Typ obsahu odpovědi: application/xml 

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis    |Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|AppID  |(prázdné)    |Povinná hodnota. Pokud se používá hlavičku autorizace nebo Ocp-Apim-Subscription-Key, ponechejte pole appid prázdný jinak obsahovat řetězec obsahující "Nosiče" + "" + "access_token".|query|řetězec|
|text|(prázdné)   |Povinná hodnota. Řetězec představující text přeložit. Velikost textu nesmí být delší než 10000 znaků.|query|řetězec|
|od|(prázdné)   |Volitelné. Řetězec představující kód jazyka překlad textu. Například en pro angličtinu.|query|řetězec|
|na|(prázdné) |Povinná hodnota. Řetězec představující kód jazyka přeložit text do.|query|řetězec|
|Typ obsahu|(prázdné)    |Volitelné. Formát textu se překlad vztahuje. Podporované formáty jsou text/plain (výchozí) a text nebo html. Musí být ve správném formátu, dokončení element HTML.|query|řetězec|
|category|(prázdné)   |Volitelné. Řetězec obsahující kategorii překlad (domény). Výchozí hodnota je "Obecné".|query|řetězec|
|Autorizace|(prázdné)  |Vyžaduje, pokud není zadána appid pole nebo záhlaví Ocp-Apim-Subscription-Key. Autorizační token: "Nosiče" + "" + "access_token".|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)  |Vyžaduje, pokud není zadána appid pole nebo autorizační hlavičky.|záhlaví|řetězec|


### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám svoje přibližné datum a čas požadavku a s ID žádosti, které jsou zahrnuty v odpovědi záhlaví X-MS-napříč-Info.|
|503    |Služba je dočasně nedostupná. Zkuste to prosím a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Poznámky k implementaci
Použití `TranslateArray` metoda pro načtení překladů pro více textů zdroje.

Identifikátor URI požadavku `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Formát textu žádosti by měl vypadat takto:

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


* `appid`: Vyžaduje se. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte pole appid prázdný jinak obsahovat řetězec obsahující `"Bearer" + " " + "access_token"`.
* `from`: Volitelné. Řetězec představující kód jazyka přeložit text z. Pokud je ponecháno prázdné odpověď bude obsahovat výsledek automatické rozpoznávání jazyka.
* `options`: Volitelné. `Options` Objekt, který obsahuje níže uvedené hodnoty. Jsou všechny volitelné a výchozí nejběžnější nastavení. Zadané elementy musí být uvedené v abecedním pořadí.
    - `Category`: Řetězec obsahující kategorii překlad (domény). Použije se výchozí hodnota `general`.
    - `ContentType`: Formát textu se překlad vztahuje. Podporované formáty jsou `text/plain` (výchozí), `text/xml` a `text/html`. Musí být ve správném formátu, dokončení element HTML.
    - `ProfanityAction`: Určuje způsob zpracování profanities jak je popsáno výše. Přijaté hodnoty z `ProfanityAction` jsou `NoAction` (výchozí), `Marked` a `Deleted`.
    - `State`: Stav uživatele k correlate požadavku a odpovědi. Stejný obsah, bude vrácen v odpovědi.
    - `Uri`: Filtrovat výsledky podle tento identifikátor URI. Výchozí hodnota: `all`.
    - `User`: Filtrování výsledků tímto uživatelem. Výchozí hodnota: `all`.
* `texts`: Vyžaduje se. Pole obsahující texty pro překlad. Všechny řetězce musí být ve stejném jazyce. Celkový počet všech textů k převodu nesmí překročit 10000 znaků. Maximální počet elementů pole je 2000.
* `to`: Vyžaduje se. Řetězec představující kód jazyka přeložit text do.

Volitelné elementy lze vynechat. Elementy, které jsou přímo podřízené TranslateArrayRequest musí být uvedené v abecedním pořadí.

Metoda TranslateArray přijímá `application/xml` nebo `text/xml` pro `Content-Type`.

**Návratová hodnota:** A `TranslateArrayResponse` pole. Každý `TranslateArrayResponse` obsahuje následující prvky:

* `Error`: Označuje chybu, pokud jeden došlo k chybě. Jinak nastaveno na hodnotu null.
* `OriginalSentenceLengths`: Pole celá čísla, která délka každé větě v původní zdrojový text. Délka pole označuje počet věty.
* `TranslatedText`: Přeložený text.
* `TranslatedSentenceLengths`: Pole celá čísla, která délka každé větě v přeložený text. Délka pole označuje počet věty.
* `State`: Stav uživatele k correlate požadavku a odpovědi. Vrátí stejný obsah jako požadavek.

Formát text odpovědi je následující.

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

### <a name="response-class-status-200"></a>Odpověď – třída (stav 200)
Úspěšná odpověď obsahuje pole `TranslateArrayResponse` ve formátu popsané výše.

řetězec

Typ obsahu odpovědi: application/xml

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|Autorizace|(prázdný)) |Vyžaduje, pokud není zadána appid pole nebo záhlaví Ocp-Apim-Subscription-Key. Autorizační token: "Nosiče" + "" + "access_token".|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Vyžaduje, pokud není zadána appid pole nebo autorizační hlavičky.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP   |Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi. Běžné chyby patří: <ul><li>Element pole nemůže být prázdný</li><li>Neplatný kategorie</li><li>Z jazyka je neplatný</li><li>Jazyk je neplatná</li><li>Žádost obsahuje příliš mnoho prvků</li><li>Jazyk From není podporován.</li><li>Na jazyk není podporován</li><li>Převede požadavek má příliš mnoho dat</li><li>Není ve správném formátu HTML</li><li>Bylo předáno příliš mnoho řetězce převede požadavku</li></ul>|
|401    |Neplatné přihlašovací údaje|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám svoje přibližné datum a čas požadavku a s ID žádosti, které jsou zahrnuty v odpovědi záhlaví X-MS-napříč-Info.|
|503    |Služba je dočasně nedostupná. Zkuste to prosím a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte popisné názvy pro jazyky předaná jako parametr `languageCodes`a lokalizované pomocí jazyka předaný národního prostředí.

Identifikátor URI požadavku `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

Text žádosti obsahuje pole řetězců představující ISO kód jazyka 639-1 pro načtení popisné názvy. Příklad:

```
<ArrayOfstring xmlns:i="http://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Návratová hodnota:** pole řetězců obsahující názvy jazyky podporované službou překladač lokalizované do požadovaný jazyk.

### <a name="response-class-status-200"></a>Odpověď – třída (stav 200)
Pole řetězců obsahující názvy jazyky podporované službou překladač lokalizované do požadovaný jazyk.

řetězec

Typ obsahu odpovědi: application/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|AppID|(prázdné)|Povinná hodnota. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte pole appid prázdný jinak obsahovat řetězec obsahující `"Bearer" + " " + "access_token"`.|query|řetězec|
|Národní prostředí|(prázdné) |Povinná hodnota. Řetězec představující kombinace zadání kódu dvoupísmenným malá jazyková verze ISO 639 související s jazykem a zadání kódu velká písmena subkulturu dvoupísmenným ISO 3166 k lokalizaci názvy jazyků nebo zadání kódu malá písmena jazyková verze ISO 639 samostatně.|query|řetězec|
|Autorizace|(prázdné)  |Pokud požadovaná pole appid nebo `Ocp-Apim-Subscription-Key` hlavičky není zadán. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)  |Pokud požadovaná pole appid nebo `Authorization` hlavičky není zadán.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám svoje přibližné datum a čas požadavku a s ID žádosti, které jsou zahrnuty v odpovědi záhlaví X-MS-napříč-Info.|
|503    |Služba je dočasně nedostupná. Zkuste to prosím a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-getlanguagesfortranslate"></a>ZÍSKAT /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Poznámky k implementaci
Získejte seznam kódů představující jazyky, které jsou podporovány službou překlad.  `Translate` a `TranslateArray` může překládat mezi dvěma z těchto jazyků.

Identifikátor URI požadavku `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Návratová hodnota:** pole řetězců obsahující kódy jazyků podporována službami překladač.

### <a name="response-class-status-200"></a>Odpověď – třída (stav 200)
Pole řetězců obsahující kódy jazyků podporována službami překladač.

řetězec

Typ obsahu odpovědi: application/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|AppID|(prázdné)|Povinná hodnota. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte pole appid prázdný jinak obsahovat řetězec obsahující `"Bearer" + " " + "access_token"`.|query|řetězec|
|Autorizace|(prázdné)  |Požadováno pokud `appid` pole nebo `Ocp-Apim-Subscription-Key` hlavičky není zadán. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Požadováno pokud `appid` pole nebo `Authorization` hlavičky není zadán.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám svoje přibližné datum a čas požadavku a s ID žádosti, které jsou zahrnuty v odpovědi záhlaví X-MS-napříč-Info.|
|503|Služba je dočasně nedostupná. Zkuste to prosím a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-getlanguagesforspeak"></a>ZÍSKAT /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte jazyky dostupné pro syntézu řeči.

Identifikátor URI požadavku `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Návratová hodnota:** pole řetězců obsahující kódy jazyků pro rozpoznávání řeči souhrnnou podporována službou překladač.

### <a name="response-class-status-200"></a>Odpověď – třída (stav 200)
Pole řetězců obsahující kódy jazyků pro rozpoznávání řeči souhrnnou podporována službou překladač.

řetězec

Typ obsahu odpovědi: application/xml

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|AppID|(prázdné)|Povinná hodnota. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte pole appid prázdný jinak obsahovat řetězec obsahující `"Bearer" + " " + "access_token"`.|query|řetězec|
|Autorizace|(prázdné)|Požadováno pokud `appid` pole nebo `Ocp-Apim-Subscription-Key` hlavičky není zadán. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Požadováno pokud `appid` pole nebo `Authorization` hlavičky není zadán.|záhlaví|řetězec|
 
### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400|Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401|Neplatné přihlašovací údaje|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti, které jsou součástí hlavičku odpovědi `X-MS-Trans-Info`.|
|503    |Služba je dočasně nedostupná. Zkuste to prosím a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-speak"></a>ZÍSKAT / řeči

### <a name="implementation-notes"></a>Poznámky k implementaci
Vrátí zvuku WAV nebo mp3 proud předán text použitém v požadovaném jazyce.

Identifikátor URI požadavku `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Návratová hodnota:** zvuku WAV nebo mp3 proud předán text použitém v požadovaném jazyce.

### <a name="response-class-status-200"></a>Odpověď – třída (stav 200)

binární

Typ obsahu odpovědi: application/xml 

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|AppID|(prázdné)|Povinná hodnota. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte pole appid prázdný jinak obsahovat řetězec obsahující `"Bearer" + " " + "access_token"`.|query|řetězec|
|text|(prázdné)   |Povinná hodnota. Řetězec obsahující věty jazyka zadaného přečteno pro datový proud wave nebo věty. Velikost textu k řeči nesmí překročit 2000 znaků.|query|řetězec|
|jazyk|(prázdné)   |Povinná hodnota. Řetězec představující kód podporované jazykové mluvit textu v. Kód musí být uvedena v seznamu kódy vrátila z metody `GetLanguagesForSpeak`.|query|řetězec|
|Formát|(prázdné)|Volitelné. Řetězec určující ID typu obsahu. V současné době `audio/wav` a `audio/mp3` jsou k dispozici. Výchozí hodnota je `audio/wav`.|query|řetězec|
|Možnosti|(prázdné)    |<ul><li>Volitelné. Řetězec určující vlastnosti řečového:<li>`MaxQuality` a `MinSize` je možné zadat kvalitu zvukového signálu. S `MaxQuality`, můžete získat hlasy s nejvyšší kvality a se `MinSize`, můžete získat hlasy s na nejmenší velikost. Výchozí hodnota je `MinSize`.</li><li>`female` a `male` je možné zadat požadované pohlaví hlasu. Výchozí hodnota je `female`. Použití panelu svislý.|` to include multiple options. For example  `MaxQuality|Mužského '.</li></li></ul> |query|řetězec|
|Autorizace|(prázdné)|Požadováno pokud `appid` pole nebo `Ocp-Apim-Subscription-Key` hlavičky není zadán. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)  |Požadováno pokud `appid` pole nebo `Authorization` hlavičky není zadán.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti, které jsou součástí hlavičku odpovědi `X-MS-Trans-Info`.|
|503    |Služba je dočasně nedostupná. Zkuste to prosím a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-detect"></a>ZÍSKAT / Detect

### <a name="implementation-notes"></a>Poznámky k implementaci
Použití `Detect` metodu, jak identifikovat jazyk vybranou část textu.

Identifikátor URI požadavku `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Návratová hodnota:** řetězec obsahující kód jazyka dvou znaků pro zadaný text. .

### <a name="response-class-status-200"></a>Odpověď – třída (stav 200)

řetězec

Typ obsahu odpovědi: application/xml

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|AppID|(prázdné)  |Povinná hodnota. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte pole appid prázdný jinak obsahovat řetězec obsahující `"Bearer" + " " + "access_token"`.|query|řetězec|
|text|(prázdné)|Povinná hodnota. Řetězec obsahující nějaký text, jehož jazyk je možné identifikovat. Velikost textu nesmí být delší než 10000 znaků.|query| řetězec|
|Autorizace|(prázdné)|Požadováno pokud `appid` pole nebo `Ocp-Apim-Subscription-Key` hlavičky není zadán. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key  |(prázdné)    |Požadováno pokud `appid` pole nebo `Authorization` hlavičky není zadán.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400|Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti, které jsou součástí hlavičku odpovědi `X-MS-Trans-Info`.|
|503    |Služba je dočasně nedostupná. Zkuste to prosím a dejte nám vědět, pokud chyba přetrvává.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Poznámky k implementaci
Použití `DetectArray` metodu, jak identifikovat jazyk pole řetězce najednou. Provede nezávislé detekce jednotlivých prvků jednotlivá pole a vrátí výsledek pro každý řádek pole.

Identifikátor URI požadavku `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Formát textu žádosti by měl vypadat takto.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Velikost textu nesmí být delší než 10000 znaků.

**Návratová hodnota:** pole řetězců obsahující dvou znaků jazyka kódy pro každý řádek vstupního pole.

Formát text odpovědi je následující.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Odpověď – třída (stav 200)
DetectArray byla úspěšná. Vrátí pole řetězců obsahující kódů dvou znaků pro každý řádek vstupního pole.

řetězec

Typ obsahu odpovědi: application/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|AppID|(prázdné)|Povinná hodnota. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte pole appid prázdný jinak obsahovat řetězec obsahující `"Bearer" + " " + "access_token"`.|query|řetězec|
|Autorizace|(prázdné)|Požadováno pokud `appid` pole nebo `Ocp-Apim-Subscription-Key` hlavičky není zadán. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Požadováno pokud `appid` není zadána pole nebo autorizační hlavičky.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám svoje přibližné datum a čas požadavku a s ID žádosti, které jsou zahrnuty v odpovědi záhlaví X-MS-napříč-Info.|
|503    |Služba je dočasně nedostupná. Zkuste to prosím a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-addtranslation"></a>ZÍSKAT /AddTranslation

### <a name="implementation-notes"></a>Poznámky k implementaci

> [!IMPORTANT]
> **VYŘAZENÍ Poznámka:** po 31. ledna 2018, tato metoda nebude přijímat nové větu odesílání, a zobrazí se chybová zpráva. Naleznete v tomto oznámení o změnách spolupráce funkce překladu.

Přidá překlad do paměti překlad.

Identifikátor URI požadavku `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Odpověď – třída (stav 200)

řetězec

Typ obsahu odpovědi: aplikace: xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat   |
|:--|:--|:--|:--|:--|
|AppID|(prázdné)|Povinná hodnota. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte pole appid prázdný jinak obsahovat řetězec obsahující `"Bearer" + " " + "access_token"`.|query|řetězec|
|originalText|(prázdné)|Povinná hodnota. Řetězec obsahující text, který se převede z. Řetězec má maximální délku než 1 000 znaků.|query|řetězec|
|translatedText|(prázdné) |Povinná hodnota. Řetězec obsahující přeložit text v jazyce cíl. Řetězec má maximální délku než 2000 znaků.|query|řetězec|
|od|(prázdné)   |Povinná hodnota. Řetězec představující kód jazyka překlad textu. en = angličtina, de = němčině atd...|query|řetězec|
|na|(prázdné)|Povinná hodnota. Řetězec představující kód jazyka přeložit text do.|query|řetězec|
|rating|(prázdné) |Volitelné. Celé číslo představující hodnocení kvality pro tento řetězec. Hodnota -10 až 10. Výchozí hodnota je 1.|query|integer|
|Typ obsahu|(prázdné)    |Volitelné. Formát textu se překlad vztahuje. Podporované formáty jsou "text/plain" a "text/html". Musí být ve správném formátu, dokončení element HTML.   |query|řetězec|
|category|(prázdné)|Volitelné. Řetězec obsahující kategorii překlad (domény). Výchozí hodnota je "Obecné".|query|řetězec|
|uživatel|(prázdné)|Povinná hodnota. Řetězec, který se používá ke sledování odesílatel odesílání.|query|řetězec|
|identifikátor uri|(prázdné)|Volitelné. Řetězec obsahující umístění obsahu tohoto překladu.|query|řetězec|
|Autorizace|(prázdné)|Pokud požadovaná pole appid nebo `Ocp-Apim-Subscription-Key` hlavičky není zadán. Autorizační token: `"Bearer" + " " + "access_token"`.    |záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Požadováno pokud `appid` pole nebo `Authorization` hlavičky není zadán.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje|
|410|AddTranslation již není podporována.|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám svoje přibližné datum a čas požadavku a s ID žádosti, které jsou zahrnuty v odpovědi záhlaví X-MS-napříč-Info.|
|503    |Služba je dočasně nedostupná. Zkuste to prosím a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Poznámky k implementaci

> [!IMPORTANT]
> **VYŘAZENÍ Poznámka:** po 31. ledna 2018, tato metoda nebude přijímat nové větu odesílání, a zobrazí se chybová zpráva. Naleznete v tomto oznámení o změnách spolupráce funkce překladu.

Přidá pole překladů přidat překlad paměti. Toto je pole verze `AddTranslation`.

Identifikátor URI požadavku `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Formát textu požadavku je následující.

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

Prvky v rámci elementu AddtranslationsRequest patří:

* `AppId`: Vyžaduje se. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte pole appid prázdný jinak obsahovat řetězec obsahující `"Bearer" + " " + "access_token"`.
* `From`: Vyžaduje se. Řetězec obsahující kód jazyka zdroje jazyka. Musí být jeden z jazyků vrácený `GetLanguagesForTranslate` metoda.
* `To`: Vyžaduje se. Řetězec obsahující kód jazyka cílový jazyk. Musí být jeden z jazyků vrácený `GetLanguagesForTranslate` metoda.
* `Translations`: Vyžaduje se. Pole překladů pro přidání do paměti překlad. Každý překlad musí obsahovat: originalText, translatedText a hodnocení. Velikost jednotlivých originalText a translatedText je omezena na 1 000 znaků. Celkový počet všech originalText(s) a translatedText(s) nesmí být delší než 10000 znaků. Maximální počet elementů pole je 100.
* `Options`: Vyžaduje se. Sada možnosti, včetně kategorie, ContentType, identifikátor Uri a uživatelů. Uživatele je povinný. Kategorie, ContentType a identifikátoru Uri jsou volitelné. Zadané elementy musí být uvedené v abecedním pořadí.

### <a name="response-class-status-200"></a>Odpověď – třída (stav 200)
Metoda AddTranslationArray byla úspěšná. Po 31. ledna 2018 nebudou přijímány odesílání věty. Služba bude reagovat s kódem chyby 410.

řetězec

Typ obsahu odpovědi: application/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|Autorizace|(prázdné)|Vyžaduje, pokud není zadána appid pole nebo záhlaví Ocp-Apim-Subscription-Key. Autorizační token: "Nosiče" + "" + "access_token".|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Vyžaduje, pokud není zadána appid pole nebo autorizační hlavičky.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje|
|410    |AddTranslation již není podporována.|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti, které jsou součástí hlavičku odpovědi `X-MS-Trans-Info`.|
|503|Služba je dočasně nedostupná. Zkuste to prosím a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-breaksentences"></a>ZÍSKAT /BreakSentences

### <a name="implementation-notes"></a>Poznámky k implementaci
Rozdělí část textu na věty a vrátí pole obsahující délky v každé větě.

Identifikátor URI požadavku `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Návratová hodnota:** pole celých čísel představující délek věty. Délka pole je číslo vět a hodnoty jsou délka jednotlivé věty.

### <a name="response-class-status-200"></a>Odpověď – třída (stav 200)
Pole celých čísel představující délek věty. Délka pole je číslo vět a hodnoty jsou délka jednotlivé věty.

integer

Typ obsahu odpovědi: application/xml 

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|AppID|(prázdné)  |Povinná hodnota. Pokud se používá hlavičku autorizace nebo Ocp-Apim-Subscription-Key, ponechejte pole appid prázdný jinak obsahovat řetězec obsahující "Nosiče" + "" + "access_token".|query| řetězec|
|text|(prázdné)   |Povinná hodnota. Řetězec představující text, který se rozdělí na věty. Velikost textu nesmí být delší než 10000 znaků.|query|řetězec|
|jazyk   |(prázdné)    |Povinná hodnota. Řetězec představující kód jazyka vstupního textu.|query|řetězec|
|Autorizace|(prázdné)|Vyžaduje, pokud není zadána appid pole nebo záhlaví Ocp-Apim-Subscription-Key. Autorizační token: "Nosiče" + "" + "access_token".    |záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Vyžaduje, pokud není zadána appid pole nebo autorizační hlavičky.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400|Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401|Neplatné přihlašovací údaje|
|500|Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám svoje přibližné datum a čas požadavku a s ID žádosti, které jsou zahrnuty v odpovědi záhlaví X-MS-napříč-Info.|
|503|Služba je dočasně nedostupná. Zkuste to prosím a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte pole překladů pro daný jazyk páry z úložiště a modul MT. GetTranslations se liší od přeložit, protože vrátí všechny dostupné překladů.

Identifikátor URI požadavku `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

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

`TranslateOptions` Objekt obsahuje níže uvedené hodnoty. Jsou všechny volitelné a výchozí nejběžnější nastavení. Zadané elementy musí být uvedené v abecedním pořadí.

* `Category`: Řetězec obsahující kategorii překlad (domény). Výchozí hodnota je "Obecné".
* `ContentType`: Jediný podporovaný, a výchozí hodnota, je možnost "text/plain".
* `IncludeMultipleMTAlternatives`: logický příznak k určení, zda má být vrácen více než jeden alternativy ze MT stroje. Platné hodnoty jsou true a false (malá a velká písmena). Výchozí je hodnota false a obsahuje pouze 1 alternativní. Nastavení příznaku na hodnotu true umožňuje generování umělé alternativy v překlad plně integrované se službou spolupráce překlady framework (CTF). Tato funkce umožňuje vrácení alternativy na věty, které nemají žádné alternativy ve CTF, přidáním umělé alternativy ze seznamu n nejvhodnější dekodér.
    - Hodnocení hodnocení se používají takto: 1) má nejlepší automatické překlad hodnocení 5. 2) alternativ z CTF projeví autorita kontrolorovi z -10 na + 10. 3) alternativy na automaticky generovaný překlad (n nejlepší) mají hodnocení 0 a mají určitý stupeň shody 100.
    - Počet alternativy počet vrácených alternativy je až maxTranslations, ale může být nižší.
    - Dvojice jazyků tato funkce není k dispozici pro překlad mezi zjednodušená a tradiční čínštině, obou směrech. Je k dispozici pro všechny ostatní dvojice jazyka Microsoft Translator podporována.
* `State`: Stav uživatele k correlate požadavku a odpovědi. Stejný obsah, bude vrácen v odpovědi.
* `Uri`: Filtrovat výsledky podle tento identifikátor URI. Pokud není nastavena žádná hodnota, výchozí hodnota je všechny.
* `User`: Filtrování výsledků tímto uživatelem. Pokud není nastavena žádná hodnota, výchozí hodnota je všechny.

Žádosti o `Content-Type` by měla být `text/xml`.

**Návratová hodnota:** formát odpovědi je následující.

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

To zahrnuje `GetTranslationsResponse` element obsahující následující hodnoty:

* `Translations`: Pole odpovídá nalezen, uložené v objekty TranslationMatch (viz níže). Překlady může zahrnovat mírné variant původní text (přibližné shody). Seřadí překlady: 100 % odpovídá nejdřív přibližné shody níže.
* `From`: Pokud metoda nezadali From jazyk, bude výsledkem automatické zjišťování jazyka. V opačném případě bude daném z jazyka.
* `State`: Stav uživatele k correlate požadavku a odpovědi. Obsahuje stejnou hodnotu jako dané v parametru TranslateOptions.

Objekt TranslationMatch se skládá z následujících akcí:

* `Error`: Pokud pro konkrétní vstupní řetězec došlo k chybě, kód chyby je uložen. V opačném případě je pole prázdné.
* `MatchDegree`: Systém odpovídá vstupní věty proti úložiště, včetně nepřesný odpovídá.  MatchDegree Určuje, jak blízko vstupní text odpovídá původní text v úložišti nalezen. Vrácená hodnota rozsahy od 0 do 100, kde 0 je žádné podobnosti a 100 se přesně shoduje velká a malá písmena.
MatchedOriginalText: Původní text, který byl odpovídá pro tento výsledek. Vrácen pouze, pokud se liší od vstupního textu odpovídající původní text. Používá k vrácení zdrojový text přibližné shody. Nebyla vrácena Microsoft Translator výsledků.
* `Rating`: Označuje autority osoby, která kvality rozhodnutí. Výsledky strojový překlad, bude mít hodnocení 5. Anonymně poskytnutý překlady má obecně vzato hodnocení 1 až 4, a přitom se autoritativně zadaný překlady má obecně vzato hodnocení 6 až 10.
* `Count`: Počet zvolené tento překlad s Tento hodnocení. Hodnota bude 0 pro automaticky přeložený odpověď.
* `TranslatedText`: Přeložený text.

### <a name="response-class-status-200"></a>Odpověď – třída (stav 200)
A `GetTranslationsResponse` objekt ve formátu popsané výše.

řetězec

Typ obsahu odpovědi: application/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|AppID|(prázdné)|Povinná hodnota. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechejte pole appid prázdný jinak obsahovat řetězec obsahující `"Bearer" + " " + "access_token"`.|query|řetězec|
|text|(prázdné)|Povinná hodnota. Řetězec představující text přeložit. Velikost textu nesmí být delší než 10000 znaků.|query|řetězec|
|od|(prázdné)|Povinná hodnota. Řetězec představující kód jazyka překlad textu.|query|řetězec|
|na |(prázdné)    |Povinná hodnota. Řetězec představující kód jazyka přeložit text do.|query|řetězec|
|maxTranslations|(prázdné)|Povinná hodnota. Celé číslo představující maximální počet překladů vrátit.|query|integer|
|Autorizace| (prázdné)|Požadováno pokud `appid` pole nebo `Ocp-Apim-Subscription-Key` hlavičky není zadán. Autorizační token: `"Bearer" + " " + "access_token"`.|řetězec| záhlaví|
|OCP-Apim-Subscription-Key|(prázdné)  |Požadováno pokud `appid` pole nebo `Authorization` hlavičky není zadán.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti, které jsou součástí hlavičku odpovědi `X-MS-Trans-Info`.|
|503|Služba je dočasně nedostupná. Zkuste to prosím a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Poznámky k implementaci
Použití `GetTranslationsArray` metoda pro načtení více překlad kandidáty pro více textů zdroje.

Identifikátor URI požadavku `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Formát textu požadavku je následující.

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

* `AppId`: Vyžaduje se. Pokud se používá autorizační hlavičky, ponechejte pole appid prázdný jinak zahrnout, řetězec obsahující `"Bearer" + " " + "access_token"`.
* `From`: Vyžaduje se. Řetězec představující kód jazyka překlad textu.
* `MaxTranslations`: Vyžaduje se. Celé číslo představující maximální počet překladů vrátit.
* `Options`: Volitelné. Možnosti objektu, který obsahuje níže uvedené hodnoty. Jsou všechny volitelné a výchozí nejběžnější nastavení. Zadané elementy musí být uvedené v abecedním pořadí.
    - Kategorie ': řetězec obsahující kategorii překlad (domény). Výchozí hodnota je obecná.
    - `ContentType`: Jediný podporovaný, a výchozí možnost je text/plain.
    - `IncludeMultipleMTAlternatives`: logický příznak k určení, zda má být vrácen více než jeden alternativy ze MT stroje. Platné hodnoty jsou true a false (malá a velká písmena). Výchozí je hodnota false a obsahuje pouze 1 alternativní. Nastavení příznaku na hodnotu true umožňuje generování umělé alternativy v překlad plně integrované se službou spolupráce překlady framework (CTF). Tato funkce umožňuje vrácení alternativy na věty, které nemají žádné alternativy ve CTF, přidáním umělé alternativy ze seznamu n nejvhodnější dekodér.
        - Hodnocení hodnocení se používají takto: 1) má nejlepší automatické překlad hodnocení 5. 2) alternativ z CTF projeví autorita kontrolorovi z -10 na + 10. 3) alternativy na automaticky generovaný překlad (n nejlepší) mají hodnocení 0 a mají určitý stupeň shody 100.
        - Počet alternativy počet vrácených alternativy je až maxTranslations, ale může být nižší.
        - Dvojice jazyků tato funkce není k dispozici pro překlad mezi zjednodušená a tradiční čínštině, obou směrech. Je k dispozici pro všechny ostatní dvojice jazyka Microsoft Translator podporována.
* `State`: Stav uživatele k correlate požadavku a odpovědi. Stejný obsah, bude vrácen v odpovědi.
* `Uri`: Filtrovat výsledky podle tento identifikátor URI. Pokud není nastavena žádná hodnota, výchozí hodnota je všechny.
* `User`: Filtrování výsledků tímto uživatelem. Pokud není nastavena žádná hodnota, výchozí hodnota je všechny.
* `Texts`: Vyžaduje se. Pole obsahující texty pro překlad. Všechny řetězce musí být ve stejném jazyce. Celkový počet všech textů k převodu nesmí překročit 10000 znaků. Maximální počet elementů pole je 10.
* `To`: Vyžaduje se. Řetězec představující kód jazyka přeložit text do.

Volitelné elementy lze vynechat. Elementy, které jsou přímo podřízené `GetTranslationsArrayRequest` musí být uvedené v abecedním pořadí.

Žádosti o `Content-Type` by měla být `text/xml`.

**Návratová hodnota:** formát odpovědi je následující.

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

Každý `GetTranslationsResponse` element obsahuje následující hodnoty:

* `Translations`: Najde pole shod, uložené v `TranslationMatch` objekty (viz níže). Překlady může zahrnovat mírné variant původní text (přibližné shody). Seřadí překlady: 100 % odpovídá nejdřív přibližné shody níže.
* `From`: Pokud metoda nezadali `From` jazyk, bude výsledkem automatické zjišťování jazyka. V opačném případě bude daném z jazyka.
* `State`: Stav uživatele k correlate požadavku a odpovědi. Obsahuje stejnou hodnotu jako v dané `TranslateOptions` parametr.

`TranslationMatch` objekt se skládá z následujících akcí:
* `Error`: Pokud pro konkrétní vstupní řetězec došlo k chybě, kód chyby je uložen. V opačném případě je pole prázdné.
* `MatchDegree`: Systém odpovídá vstupní věty proti úložiště, včetně nepřesný odpovídá.  `MatchDegree` Určuje, jak blízko vstupní text odpovídá původní text v úložišti nalezen. Vrácená hodnota rozsahy od 0 do 100, kde 0 je žádné podobnosti a 100 se přesně shoduje velká a malá písmena.
* `MatchedOriginalText`: Původní text, který byl odpovídá pro tento výsledek. Vrácen pouze, pokud se liší od vstupního textu odpovídající původní text. Používá k vrácení zdrojový text přibližné shody. Nebyla vrácena Microsoft Translator výsledků.
* `Rating`: Označuje autority osoby, která kvality rozhodnutí. Výsledky strojový překlad, bude mít hodnocení 5. Anonymně poskytnutý překlady má obecně vzato hodnocení 1 až 4, a přitom se autoritativně zadaný překlady má obecně vzato hodnocení 6 až 10.
* `Count`: Počet zvolené tento překlad s Tento hodnocení. Hodnota bude 0 pro automaticky přeložený odpověď.
* `TranslatedText`: Přeložený text.


### <a name="response-class-status-200"></a>Odpověď – třída (stav 200)

řetězec

Typ obsahu odpovědi: application/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|:--|
|Autorizace  |(prázdné)    |Požadováno pokud `appid` pole nebo `Ocp-Apim-Subscription-Key` hlavičky není zadán. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)  |Požadováno pokud `appid` pole nebo `Authorization` hlavičky není zadán.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti, které jsou součástí hlavičku odpovědi `X-MS-Trans-Info`.|
|503    |Služba je dočasně nedostupná. Zkuste to prosím a dejte nám vědět, pokud chyba přetrvává.|

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Migrace na Text překladač v3 rozhraní API](../migrate-to-v3.md)










