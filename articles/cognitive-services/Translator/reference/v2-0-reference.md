---
title: Translator Text API v2.0
titleSuffix: Azure Cognitive Services
description: Referenční dokumentace pro rozhraní Translator Text API verze 2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: c18c062d5537603284acb37081ac0a4eb8d2fd20
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797818"
---
# <a name="translator-text-api-v20"></a>Translator Text API v2.0

> [!IMPORTANT]
> Tato verze rozhraní Translator Text API je zastaralá. [Zobrazit dokumentaci k verzi 3 rozhraní Translator Text API](v3-0-reference.md).

Verze 2 rozhraní Translator Text API je možné bez problémů integrovat do vašich aplikací, webů, nástrojů nebo jiná řešení k poskytování vícejazyčné uživatelské prostředí. Můžete ji na libovolné platformě hardwaru a s libovolným operačním systémem provádět překlad jazyka a další úkoly týkající se jazyka, jako je rozpoznávání jazyka text a převod textu na řeč, podle oborových standardů. Další informace najdete v tématu [Translator Text API](../translator-info-overview.md).

## <a name="getting-started"></a>Začínáme
Chcete-li získat přístup k rozhraní Translator Text API, je potřeba [zaregistrovat do Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authentication"></a>Ověřování 
Všechna volání rozhraní Translator Text API vyžadují klíč předplatného pro ověřování. Rozhraní API podporuje tři metody ověřování:

- Přístupový token. Klíč předplatného, který je odkazováno v kroku 9 použijte k vytvoření přístupového tokenu tím, že požadavek POST k ověřovací službě. Viz podrobnosti naleznete v dokumentaci služby tokenů. Přístupový token předat službě Translator pomocí `Authorization` záhlaví nebo `access_token` parametr dotazu. Přístupový token je platný 10 minut. Získejte nový přístupový token každých 10 minut a mohli dál využívat stejný přístup token pro opakované požadavky během 10 minut.
- Klíč předplatného použít přímo. Předejte klíč předplatného. jako hodnotu `Ocp-Apim-Subscription-Key` záhlaví zahrnuty při zpracování požadavku na Translator Text API. Použijete-li klíč předplatného přímo, není nutné volat službu ověřování pomocí tokenu k vytvoření přístupového tokenu.
- [Předplatné víc služeb Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Tato metoda umožňuje používat jeden tajný klíč k ověření požadavků pro víc služeb.
Pokud používáte víc služeb tajný klíč, budete muset zahrnout dvě záhlaví ověřování při zpracování požadavku. První záhlaví předá tajný klíč. Druhé záhlaví určuje oblast spojených s vaším předplatným:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

Oblast je vyžadován pro víc služeb předplatného rozhraní Text API. Oblast, kterou jste vybrali je jen jedna oblast, kterou můžete použít pro překlad textu, pokud použijete klíč víc služeb předplatného. Musí být stejné oblasti, kterou jste vybrali při registraci předplatného víc služeb na portálu Azure portal.

Jsou dostupné oblasti `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`, a `westus2`.

Klíč předplatného a přístup k tokenu jsou tajné kódy, které by měl být skryta.

## <a name="profanity-handling"></a>Zpracování vulgárních výrazů
Službu Translator za normálních okolností si zachovají vulgárních výrazů, který je k dispozici ve zdroji. Do jaké míry vulgárních výrazů a stejného kontextu, který umožňuje neslušná slova se liší podle jazykové verze. Stupeň vulgárních výrazů v cílovém jazyce tak může zvýšit nebo snížit.

Pokud chcete, aby se zabránilo vulgárních výrazů v překladu i v případě, že je ve zdrojovém textu, můžete použít vulgárních výrazů pro metody, které ji podporují možnost filtrování. Možnost můžete zvolit, jestli chcete zobrazit vulgárních výrazů odstraněn nebo označené odpovídající značky nebo zda chcete povolit vulgárních výrazů v cílové. Přijímané hodnoty `ProfanityAction` jsou `NoAction` (výchozí), `Marked`, a `Deleted`.


|ProfanityAction    |Action |Příklad zdroje (japonština)  |Příklad překladu (v angličtině)  |
|:--|:--|:--|:--|
|NoAction   |Default (Výchozí). Stejně jako nastavit možnost. Vulgárních výrazů předá ze zdroje do cíle.        |彼はジャッカスです。     |Je jackass.   |
|Označené     |Urážlivá slova umístí značky XML \<vulgárních výrazů > a \</profanity >.       |彼はジャッカスです。 |Je \<vulgárních výrazů > jackass\</profanity >.  |
|Odstranění    |Urážlivá slova se odebere z výstupu bez nahrazení.     |彼はジャッカスです。 |Je.   |

    
## <a name="excluding-content-from-translation"></a>S výjimkou obsahu z překladu
Při překladu obsah s klíčovými slovy, jako je HTML (`contentType=text/html`), je někdy užitečné chcete vyloučit z překladu konkrétní obsah. Můžete použít atribut `class=notranslate` k určení obsahu, který by měla zůstat v původním jazyce. V následujícím příkladu, obsah v prvním `div` prvek nebude přeložit, ale obsah ve druhém `div` element bude fungovat.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>ZÍSKAT / převést

### <a name="implementation-notes"></a>Poznámky k implementaci
Převede textový řetězec z jednoho jazyka do druhého.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Návratová hodnota:** Řetězec, který představuje přeloženého textu.

Pokud jste dříve používali `AddTranslation` nebo `AddTranslationArray` zadat překlad hodnocení 5 nebo novější pro stejný zdroj věty, `Translate` vrátí pouze volba, která je k dispozici pro váš systém. "Stejný zdroj větu" znamená, že přesně stejné (100 % odpovídající), s výjimkou malá a velká písmena, mezery, hodnoty značek a interpunkční znaménka na konci věty. Pokud žádné hodnocení se uloží spolu s hodnocení 5 nebo vyšší, bude vrácený výsledek automatický překlad pomocí Microsoft Translatoru.

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)

řetězec

Typ obsahu odpovědi: application/xml

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis    |Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|appid  |(prázdné)    |Povinný parametr. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechte `appid` prázdné pole. V opačném případě obsahovat řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|text|(prázdné)   |Povinný parametr. Řetězec, který reprezentuje text k přeložení. Text nemůže obsahovat víc než 10 000 znaků.|query|řetězec|
|from|(prázdné)   |Volitelné. Řetězec, který představuje kód jazyka textu, který je překládán. Například en pro angličtinu.|query|řetězec|
|na|(prázdné) |Povinný parametr. Řetězec, který představuje kód jazyka můžete přeložit text do.|query|řetězec|
|contentType|(prázdné)    |Volitelné. Formát textu, který je překládán. Podporované formáty jsou `text/plain` (výchozí) a `text/html`. Elementy HTML musí být ve správném formátu, dokončení prvky.|query|řetězec|
|category|(prázdné)   |Volitelné. Řetězec, který obsahuje kategorie překladu (domény). Výchozí hodnota je `general`.|query|řetězec|
|Authorization|(prázdné)  |Vyžaduje se, pokud obě `appid` pole a `Ocp-Apim-Subscription-Key` záhlaví jsou prázdné. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)  |Vyžaduje se, pokud obě `appid` pole a `Authorization` záhlaví jsou prázdné.|záhlaví|řetězec|


### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Reason|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje.|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-translatearray"></a>Publikovat /TranslateArray

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte překlady pro více textů zdroje.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Tady je formát textu požadavku:

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

Tyto prvky jsou v `TranslateArrayRequest`:


* `AppId`: Povinný parametr. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechte `AppId` prázdné pole. V opačném případě obsahovat řetězec, který obsahuje `"Bearer" + " " + "access_token"`.
* `From`: Volitelné. Řetězec, který představuje kód jazyka textu, který je překládán. Pokud toto pole necháte prázdné, bude odpověď zahrnout výsledek automatické rozpoznávání jazyka.
* `Options`: Volitelné. `Options` Objekt, který obsahuje následující hodnoty. Jsou nepovinné a nejběžnější nastavení ve výchozím nastavení. Zadané elementy musí být uvedeny v abecedním pořadí.
    - `Category`: Řetězec, který obsahuje kategorie překladu (domény). Výchozí hodnota je `general`.
    - `ContentType`: Formát textu, který je překládán. Podporované formáty jsou `text/plain` (výchozí), `text/xml`, a `text/html`. Elementy HTML musí být ve správném formátu, dokončení prvky.
    - `ProfanityAction`: Určuje, jak se zpracovává profanities, jak je vysvětleno výše. Platné hodnoty jsou `NoAction` (výchozí), `Marked`, a `Deleted`.
    - `State`: Stav uživatele ke korelaci žádostí a odpovědí. Vrátí se stejným obsahem v odpovědi.
    - `Uri`: Filtrovat výsledky podle tohoto identifikátoru URI. Výchozí hodnota: `all`.
    - `User`: Filtrovat výsledky podle tohoto uživatele. Výchozí hodnota: `all`.
* `Texts`: Povinný parametr. Pole obsahující text pro překlad. Všechny řetězce musí být ve stejném jazyce. Celkový součet veškerého textu k převodu může mít maximálně 10 000 znaků. Maximální počet elementů pole je 2 000.
* `To`: Povinný parametr. Řetězec, který představuje kód jazyka můžete přeložit text do.

Volitelné prvky, můžete vynechat. Prvky, které jsou přímo podřízené `TranslateArrayRequest` musí být uvedeny v abecedním pořadí.

`TranslateArray` Metoda přijímá `application/xml` nebo `text/xml` pro `Content-Type`.

**Návratová hodnota:** A `TranslateArrayResponse` pole. Každý `TranslateArrayResponse` má tyto prvky:

* `Error`: Označuje chybu, pokud dojde k jedné. Jinak nastavený na hodnotu null.
* `OriginalSentenceLengths`: Pole celých čísel, která určuje délku jednotlivé věty ve zdrojovém textu. Délka pole označuje počet věty.
* `TranslatedText`: Přeložený text.
* `TranslatedSentenceLengths`: Pole celých čísel, která určuje délku jednotlivé věty v přeloženého textu. Délka pole označuje počet věty.
* `State`: Stav uživatele ke korelaci žádostí a odpovědí. Vrátí obsah jako požadavek.

Tady je formát těla odpovědi:

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
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
Úspěšná odpověď obsahuje celou řadu `TranslateArrayResponse` pole ve formátu, je popsáno výše.

řetězec

Typ obsahu odpovědi: application/xml

### <a name="parameters"></a>Parametry

|Parametr|Value|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|Authorization|(prázdné)  |Vyžaduje se, pokud obě `appid` pole a `Ocp-Apim-Subscription-Key` záhlaví jsou prázdné. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Vyžaduje se, pokud obě `appid` pole a `Authorization` záhlaví jsou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP   |Reason|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi. Běžné chyby patří: <ul><li>Element pole nemůže být prázdný.</li><li>Neplatná kategorie.</li><li>Z jazyka je neplatný.</li><li>Jazyk je neplatný.</li><li>Požadavek obsahuje příliš mnoho prvků.</li><li>Od jazyk není podporován.</li><li>Na jazyk není podporován.</li><li>Přeložit požadavek má příliš mnoho dat.</li><li>HTML není ve správném formátu.</li><li>V požadavku překlad bylo předáno příliš mnoho řetězců.</li></ul>|
|401    |Neplatné přihlašovací údaje.|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-getlanguagenames"></a>/GetLanguageNames příspěvku

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte popisné názvy pro jazyky předaného jako parametr `languageCodes`, lokalizovaný do předaný `locale` jazyka.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

Text požadavku obsahuje pole řetězců, který představuje kódů ISO 639-1 jazyka pro kterou se má načíst popisné názvy. Tady je příklad:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
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

|Parametr|Value|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|appid|(prázdné)|Povinný parametr. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechte `appid` prázdné pole. V opačném případě obsahovat řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|Národní prostředí|(prázdné) |Povinný parametr. Řetězec, který představuje jeden z těchto možností použít k lokalizování názvy jazyků: <ul><li>Kombinace kód ISO 639 dvoupísmenné malá jazykové verze přidružený jazyk a kód ISO 3166 subkulturu dvoupísmenné velká písmena. <li>Kód ISO 639 malými písmeny samostatně.|query|řetězec|
|Authorization|(prázdné)  |Vyžaduje se, pokud obě `appid` pole a `Ocp-Apim-Subscription-Key` záhlaví jsou prázdné. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)  |Vyžaduje se, pokud obě `appid` pole a `Authorization` záhlaví jsou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Reason|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje.|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-getlanguagesfortranslate"></a>ZÍSKAT /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Poznámky k implementaci
Získá seznam kódů jazyka, které představují jazyky podporovanými překladatelské služby.  `Translate` a `TranslateArray` jsou dobře převeditelné mezi dvěma z těchto jazyků.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Návratová hodnota:** Pole řetězců obsahující kód jazyka služba Translator podporuje.

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)
Pole řetězců obsahující kód jazyka služba Translator podporuje.

řetězec

Typ obsahu odpovědi: application/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|appid|(prázdné)|Povinný parametr. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechte `appid` prázdné pole. V opačném případě obsahovat řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|Authorization|(prázdné)  |Vyžaduje se, pokud obě `appid` pole a `Ocp-Apim-Subscription-Key` záhlaví jsou prázdné. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Vyžaduje se, pokud obě `appid` pole a `Authorization` záhlaví jsou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Reason|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje.|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
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

|Parametr|Value|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|appid|(prázdné)|Povinný parametr. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechte `appid` prázdné pole. V opačném případě obsahovat řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|Authorization|(prázdné)|Vyžaduje se, pokud obě `appid` pole a `Ocp-Apim-Subscription-Key` záhlaví jsou prázdné. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Vyžaduje se, pokud obě `appid` pole a `Authorization` záhlaví jsou prázdné.|záhlaví|řetězec|
 
### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Reason|
|:--|:--|
|400|Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401|Neplatné přihlašovací údaje.|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-speak"></a>GET / řeči

### <a name="implementation-notes"></a>Poznámky k implementaci
Vrátí WAV nebo MP3 toku textu předaným, s kterým se mluví v požadovaném jazyce.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Návratová hodnota:** Stream WAV nebo MP3 předané v textu používaný v požadovaném jazyce.

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)

binary

Typ obsahu odpovědi: application/xml

### <a name="parameters"></a>Parametry

|Parametr|Value|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|appid|(prázdné)|Povinný parametr. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechte `appid` prázdné pole. V opačném případě obsahovat řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|text|(prázdné)   |Povinný parametr. Řetězec, který obsahuje jeden nebo více věty a používaný pro datový proud v daném jazyce. Text nesmí být delší než 2 000 znaků.|query|řetězec|
|jazyk|(prázdné)   |Povinný parametr. Řetězec, který představuje kód podporovaného jazyka jazyk, ve kterém řeči text. Kód musí být některý z kódů vrácený metodou `GetLanguagesForSpeak`.|query|řetězec|
|format|(prázdné)|Volitelné. Řetězec, který určuje ID typu obsahu. V současné době `audio/wav` a `audio/mp3` jsou k dispozici. Výchozí hodnota je `audio/wav`.|query|řetězec|
|options|(prázdné)    |Volitelné. Řetězec, který určuje vlastnosti syntetizovaný řeči:<ul><li>`MaxQuality` a `MinSize` určuje kvalitu zvukový signál. `MaxQuality` nabízí nejvyšší kvality. `MinSize` poskytuje nejmenší velikost souboru. Výchozí hodnota je `MinSize`.</li><li>`female` a `male` zadejte požadovaný pohlaví hlasu. Výchozí hodnota je `female`. Použít svislá čára (<code>\|</code>) zahrnout více možností. Například `MaxQuality|Male`.</li></li></ul>  |query|řetězec|
|Authorization|(prázdné)|Vyžaduje se, pokud obě `appid` pole a `Ocp-Apim-Subscription-Key` záhlaví jsou prázdné. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)  |Vyžaduje se, pokud obě `appid` pole a `Authorization` záhlaví jsou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Reason|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje.|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-detect"></a>ZÍSKAT / Detect

### <a name="implementation-notes"></a>Poznámky k implementaci
Určuje jazyk část textu.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Návratová hodnota:** Řetězec, který obsahuje jazyk dvoumístný kód pro text.

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)

řetězec

Typ obsahu odpovědi: application/xml

### <a name="parameters"></a>Parametry

|Parametr|Value|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|appid|(prázdné)  |Povinný parametr. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechte `appid` prázdné pole. V opačném případě obsahovat řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|text|(prázdné)|Povinný parametr. Řetězec, který obsahuje text, jehož jazyk je možné identifikovat. Text nesmí být delší než 10 000 znaků.|query|  řetězec|
|Authorization|(prázdné)|Vyžaduje se, pokud obě `appid` pole a `Ocp-Apim-Subscription-Key` záhlaví jsou prázdné. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key  |(prázdné)    |Vyžaduje se, pokud obě `appid` pole a `Authorization` záhlaví jsou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Reason|
|:--|:--|
|400|Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje.|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|


## <a name="post-detectarray"></a>Publikovat /DetectArray

### <a name="implementation-notes"></a>Poznámky k implementaci

Určuje jazyky v poli řetězců. Nezávisle na sobě zjistí jazyk každý prvek jednotlivá pole a vrátí výsledek pro každý řádek pole.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Tady je formát textu požadavku:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Text nemůže být delší než 10 000 znaků.

**Návratová hodnota:** Pole řetězců obsahující kód jazyka dvou znaků pro každý řádek vstupního pole.

Tady je formát těla odpovědi:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)
`DetectArray` bylo úspěšné. Vrátí pole řetězců obsahující kód jazyka dvou znaků pro každý řádek vstupního pole.

řetězec

Typ obsahu odpovědi: application/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Value|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|appid|(prázdné)|Povinný parametr. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechte `appid` prázdné pole. V opačném případě obsahovat řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|Authorization|(prázdné)|Vyžaduje se, pokud obě `appid` pole a `Ocp-Apim-Subscription-Key` záhlaví jsou prázdné.  Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Vyžaduje se, pokud obě `appid` pole a `Authorization` záhlaví jsou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Reason|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje.|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-addtranslation"></a>ZÍSKAT /AddTranslation

### <a name="implementation-notes"></a>Poznámky k implementaci

> [!IMPORTANT]
> **Poznámka: vyřazení:** Po 31. ledna 2018 tato metoda nebude přijímat nové příspěvky věty. Zobrazí se zpráva chyby. Podrobnosti najdete v oznámení o změnách na Collaborative Translation Framework (CTF).

Přidá překlad do paměti překladu.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)

řetězec

Typ obsahu odpovědi: aplikace: xml
 
### <a name="parameters"></a>Parametry

|Parametr|Value|Popis|Typ parametru|Datový typ   |
|:--|:--|:--|:--|:--|
|appid|(prázdné)|Povinný parametr. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechte `appid` prázdné pole. V opačném případě obsahovat řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|originalText|(prázdné)|Povinný parametr. Řetězec, který obsahuje text k přeložení. Maximální délka řetězce je 1 000 znaků.|query|řetězec|
|translatedText|(prázdné) |Povinný parametr. Řetězec, který obsahuje text převést na cílový jazyk. Maximální délka řetězce je 2 000 znaků.|query|řetězec|
|from|(prázdné)   |Povinný parametr. Řetězec, který představuje kód jazyka původního jazyk textu. Třeba cs pro angličtinu a de pro němčinu.|query|řetězec|
|na|(prázdné)|Povinný parametr. Řetězec, který představuje kód jazyka jazyk pro překlad textu do.|query|řetězec|
|rating|(prázdné) |Volitelné. Celé číslo, které představuje hodnocení kvality pro řetězec. Hodnotu od -10 do 10. Výchozí hodnota je 1.|query|integer|
|contentType|(prázdné)    |Volitelné. Formát textu, který je překládán. Podporované formáty jsou `text/plain` a `text/html`. Elementy HTML musí být ve správném formátu, dokončení prvky.    |query|řetězec|
|category|(prázdné)|Volitelné. Řetězec, který obsahuje kategorie překladu (domény). Výchozí hodnota je `general`.|query|řetězec|
|uživatel|(prázdné)|Povinný parametr. Řetězec, který se používá ke sledování odesílatel požadavku dostane informaci odeslání.|query|řetězec|
|uri|(prázdné)|Volitelné. Řetězec, který obsahuje umístění obsahu překladu.|query|řetězec|
|Authorization|(prázdné)|Vyžaduje se, pokud obě `appid` pole a `Ocp-Apim-Subscription-Key` záhlaví jsou prázdné.  Autorizační token: `"Bearer" + " " + "access_token"`.  |záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Vyžaduje se, pokud obě `appid` pole a `Authorization` záhlaví jsou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Reason|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje.|
|410|`AddTranslation` je již nejsou podporovány.|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-addtranslationarray"></a>Publikovat /AddTranslationArray

### <a name="implementation-notes"></a>Poznámky k implementaci

> [!IMPORTANT]
> **Poznámka: vyřazení:** Po 31. ledna 2018 tato metoda nebude přijímat nové příspěvky věty. Zobrazí se zpráva chyby. Podrobnosti najdete v oznámení o změnách na Collaborative Translation Framework (CTF).

Přidá pole překlady do paměti překladu. Tato metoda je pole verze `AddTranslation`.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Tady je formát textu požadavku:

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

Tyto prvky jsou v `AddtranslationsRequest`:

* `AppId`: Povinný parametr. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechte `AppId` prázdné pole. V opačném případě obsahovat řetězec, který obsahuje `"Bearer" + " " + "access_token"`.
* `From`: Povinný parametr. Řetězec, který obsahuje kód jazyka zdroje jazyka. Musí být jeden z těchto jazyků vrácené `GetLanguagesForTranslate` metody.
* `To`: Povinný parametr. Řetězec, který obsahuje kód jazyka v cílovém jazyce. Musí být jeden z těchto jazyků vrácené `GetLanguagesForTranslate` metody.
* `Translations`: Povinný parametr. Pole překlady pro přidání do paměti překladu. Každý překlad musí obsahovat `OriginalText`, `TranslatedText`, a `Rating`. Maximální velikost každého `OriginalText` a `TranslatedText` je 1 000 znaků. Celkový součet všech `OriginalText` a `TranslatedText` prvky nemůže být delší než 10 000 znaků. Maximální počet elementů pole je 100.
* `Options`: Povinný parametr. Sadu možností, včetně `Category`, `ContentType`, `Uri`, a `User`. `User` je vyžadován. `Category`, `ContentType`, a `Uri` jsou volitelné. Zadané elementy musí být uvedeny v abecedním pořadí.

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)
`AddTranslationArray` Metoda byla úspěšná. 

Po 31. ledna 2018 odesílání věty, nebude změna přijata. Služba bude reagovat s kódem chyby 410.

řetězec

Typ obsahu odpovědi: application/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Value|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|Authorization|(prázdné)|Vyžaduje se, pokud obě `appid` pole a `Ocp-Apim-Subscription-Key` záhlaví jsou prázdné.  Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Vyžaduje se, pokud obě `appid` pole a `Authorization` záhlaví jsou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Reason|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje.|
|410    |`AddTranslation` je již nejsou podporovány.|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
|503|Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-breaksentences"></a>ZÍSKAT /BreakSentences

### <a name="implementation-notes"></a>Poznámky k implementaci
Část textu dělí na věty a vrátí pole, která obsahuje délky jednotlivých věty.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Návratová hodnota:** Pole celých čísel, která představuje délky vět. Délka pole představuje počet věty. Hodnoty představují délka jednotlivé věty.

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)
Pole celých čísel, která představuje délky vět. Délka pole představuje počet věty. Hodnoty představují délka jednotlivé věty.

integer

Typ obsahu odpovědi: application/xml

### <a name="parameters"></a>Parametry

|Parametr|Value|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|appid|(prázdné)  |Povinný parametr. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechte `appid` prázdné pole. V opačném případě obsahovat řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query| řetězec|
|text|(prázdné)   |Povinný parametr. Řetězec, který představuje text, který má rozdělení na věty. Maximální velikost textu je 10 000 znaků.|query|řetězec|
|jazyk   |(prázdné)    |Povinný parametr. Řetězec, který představuje kód jazyka vstupního textu.|query|řetězec|
|Authorization|(prázdné)|Vyžaduje se, pokud obě `appid` pole a `Ocp-Apim-Subscription-Key` záhlaví jsou prázdné. Autorizační token: `"Bearer" + " " + "access_token"`.   |záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Vyžaduje se, pokud obě `appid` pole a `Authorization` záhlaví jsou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Reason|
|:--|:--|
|400|Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401|Neplatné přihlašovací údaje.|
|500|Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
|503|Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-gettranslations"></a>/GetTranslations příspěvku

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte pole posunutí pro daný jazyk pár z úložiště a modul MT. `GetTranslations` se liší od `Translate` , vrátí se všechny dostupné překlady.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

Text žádosti obsahuje volitelný `TranslationOptions` objektu, který má tento formát:

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

`TranslateOptions` Objekt obsahuje hodnoty z následujícího seznamu. Jsou nepovinné a nejběžnější nastavení ve výchozím nastavení. Zadané elementy musí být uvedeny v abecedním pořadí.

* `Category`: Řetězec, který obsahuje kategorie překladu (domény). Výchozí hodnota je `general`.
* `ContentType`: Jedinou podporovanou možností a ve výchozím nastavení, je `text/plain`.
* `IncludeMultipleMTAlternatives`: Logický příznak k určení, zda má být vrácen více než jeden alternativní MT modul. Platné hodnoty jsou `true` a `false` (malá a velká písmena). Výchozí hodnota je `false`, který vrátí pouze jeden alternativní. Nastavení příznaku `true` umožňuje vytvořit umělý alternativy, plně integrovaná s Collaborative Translation Framework (CTF). Tato funkce umožňuje vracející alternativy na věty, které mají žádné překlady v CTF přidáním umělé alternativy ze *n*– nejlepší seznamu dekodéru.
    - Hodnocení. Hodnocení se použijí následujícím způsobem: 
         - Nejlepší automatický překlad má hodnocení 5.
       - Alternativy ze CTF odrážet autority kontrolora. Jsou v rozsahu od -10 do + 10.
       - Automaticky generované (*n*– nejlepší) překlad alternativy mají hodnocení 0 až shoda stupeň 100.
    - Počet alternativy. Počet vrácených alternativy může být až hodnota určená v `maxTranslations`, ale může být nižší.
    - Dvojice jazyků. Tato funkce není k dispozici pro překlad mezi zjednodušená a tradiční čínštiny v obou směrech. Je k dispozici pro všechny páry jazyka Microsoft Translator podporuje.
* `State`: Stav uživatele ke korelaci žádostí a odpovědí. Vrátí se stejným obsahem v odpovědi.
* `Uri`: Filtrovat výsledky podle tohoto identifikátoru URI. Pokud není nastavena žádná hodnota, výchozí hodnota je `all`.
* `User`: Filtrovat výsledky podle tohoto uživatele. Pokud není nastavena žádná hodnota, výchozí hodnota je `all`.

Požádat o `Content-Type` by měl být `text/xml`.

**Návratová hodnota:** Tady je formát odpovědi:

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
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

Tato odpověď obsahuje `GetTranslationsResponse` element, který obsahuje následující hodnoty:

* `Translations`: Najít pole shod, uložené v `TranslationMatch` objekty (popsané v následující části). Překlady můžou zahrnovat mírné varianty původního textu (přibližné shody). Překlady budou seřazeny: 100 % odpovídá nejprve přibližné shody dále.
* `From`: Pokud metoda nemá určenou `From` jazyka, tato hodnota budou přicházet z automatické rozpoznávání jazyka. V opačném případě bude zadaný `From` jazyka.
* `State`: Stav uživatele ke korelaci žádostí a odpovědí. Obsahuje hodnotu, dodané v `TranslateOptions` parametru.

`TranslationMatch` Objekt se skládá z těchto hodnot:

* `Error`: Kód chyby, pokud dojde k chybě pro konkrétní vstupní řetězec. V opačném případě toto pole je prázdné.
* `MatchDegree`: Určuje, jak blízko vstupní text odpovídá původní text v úložišti nalezena. Systém odpovídá vstupní věty proti úložišti, včetně té neodpovídá. Hodnota vrácena rozsahy od 0 do 100, kde 0 je žádné podobnosti a přesná, velká a malá písmena rozlišovat je 100.
* `MatchedOriginalText`: Původní text, odpovídající pro tento výsledek. Tato hodnota se vrátí jenom v případě, že odpovídající původní text se liší od vstupního textu. Se používá k vrácení zdrojového textu přibližné shody. Tato hodnota se nevrátí Microsoft Translatoru výsledků.
* `Rating`: Označuje autority osoby, která rozhodnutí kvality. Výsledky strojový překlad mají hodnocení 5. Anonymně zadaná překlady mají obvykle hodnocení od 1 do 4. Vynuceně zadaná překlady má obecně vzato hodnocení od 6 až 10.
* `Count`: Počet pokusů, které tento překlad textů pomocí toto hodnocení se vybral. Hodnota je 0 pro automaticky přeložené odpověď.
* `TranslatedText`: Přeložený text.

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)
A `GetTranslationsResponse` objektu ve formátu, je popsáno výše.

řetězec

Typ obsahu odpovědi: application/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Value|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|appid|(prázdné)|Povinný parametr. Pokud `Authorization` nebo `Ocp-Apim-Subscription-Key` záhlaví se používá, ponechte `appid` prázdné pole. V opačném případě obsahovat řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|text|(prázdné)|Povinný parametr. Řetězec, který reprezentuje text k přeložení. Maximální velikost textu je 10 000 znaků.|query|řetězec|
|from|(prázdné)|Povinný parametr. Řetězec, který představuje kód jazyka textu, který je překládán.|query|řetězec|
|na |(prázdné)    |Povinný parametr. Řetězec, který představuje kód jazyka jazyk pro překlad textu do.|query|řetězec|
|maxTranslations|(prázdné)|Povinný parametr. Celé číslo, které představuje maximální počet překlady se vraťte.|query|integer|
|Authorization| (prázdné)|Vyžaduje se, pokud obě `appid` pole a `Ocp-Apim-Subscription-Key` záhlaví jsou prázdné. Autorizační token: `"Bearer" + " " + "access_token"`.|řetězec|  záhlaví|
|OCP-Apim-Subscription-Key|(prázdné)  |Vyžaduje se, pokud obě `appid` pole a `Authorization` záhlaví jsou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Reason|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje.|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
|503|Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-gettranslationsarray"></a>Publikovat /GetTranslationsArray

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte více překlad kandidáty pro více textů zdroje.

URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Tady je formát textu požadavku:

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

`GetTranslationsArrayRequest` zahrnuje tyto prvky:

* `AppId`: Povinný parametr. Pokud `Authorization` záhlaví se používá, ponechte `AppId` prázdné pole. V opačném případě obsahovat řetězec, který obsahuje `"Bearer" + " " + "access_token"`.
* `From`: Povinný parametr. Řetězec, který představuje kód jazyka textu, který je překládán.
* `MaxTranslations`: Povinný parametr. Celé číslo, které představuje maximální počet překlady se vraťte.
* `Options`: Volitelné. `Options` Objekt, který obsahuje následující hodnoty. Jsou nepovinné a nejběžnější nastavení ve výchozím nastavení. Zadané elementy musí být uvedeny v abecedním pořadí.
    - `Category`: Řetězec, který obsahuje kategorie překladu (domény). Výchozí hodnota je `general`.
    - `ContentType`: Jedinou podporovanou možností a ve výchozím nastavení, je `text/plain`.
    - `IncludeMultipleMTAlternatives`: Logický příznak k určení, zda má být vrácen více než jeden alternativní MT modul. Platné hodnoty jsou `true` a `false` (malá a velká písmena). Výchozí hodnota je `false`, který vrátí pouze jeden alternativní. Nastavení příznaku `true` povolí generování umělé alternativy v překladu, plně integrovaná s spolupráci překlady Framework (CTF). Tato funkce umožňuje vracející alternativy na věty, které mají žádné alternativy CTF přidáním umělé alternativy ze *n*– nejlepší seznamu dekodéru.
        - Hodnocení hodnocení se použijí následujícím způsobem:
          - Nejlepší automatický překlad má hodnocení 5.
          - Alternativy ze CTF odrážet autority kontrolora. Jsou v rozsahu od -10 do + 10.
          - Automaticky generované (*n*– nejlepší) překlad alternativy mají hodnocení 0 až shoda stupeň 100.
        - Počet alternativy. Počet vrácených alternativy může být až hodnota určená v `maxTranslations`, ale může být nižší.
        - Dvojice jazyků. Tato funkce není k dispozici pro překlad mezi zjednodušená a tradiční čínštiny v obou směrech. Je k dispozici pro všechny páry jazyka Microsoft Translator podporuje.
* `State`: Stav uživatele ke korelaci žádostí a odpovědí. Vrátí se stejným obsahem v odpovědi.
* `Uri`: Filtrovat výsledky podle tohoto identifikátoru URI. Pokud není nastavena žádná hodnota, výchozí hodnota je `all`.
* `User`: Filtrovat výsledky podle tohoto uživatele. Pokud není nastavena žádná hodnota, výchozí hodnota je `all`.
* `Texts`: Povinný parametr. Pole obsahující text pro překlad. Všechny řetězce musí být ve stejném jazyce. Celkový součet veškerého textu k převodu může mít maximálně 10 000 znaků. Maximální počet elementů pole je 10.
* `To`: Povinný parametr. Řetězec, který představuje kód jazyka jazyk pro překlad textu do.

Volitelné prvky, můžete vynechat. Prvky, které jsou přímo podřízené `GetTranslationsArrayRequest` musí být uvedeny v abecedním pořadí.

Požádat o `Content-Type` by měl být `text/xml`.

**Návratová hodnota:** Tady je formát odpovědi:

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
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

Každý `GetTranslationsResponse` prvek obsahuje tyto hodnoty:

* `Translations`: Najít pole shod, uložené v `TranslationMatch` objekty (popsané v následující části). Překlady můžou zahrnovat mírné varianty původního textu (přibližné shody). Překlady budou seřazeny: 100 % odpovídá nejprve přibližné shody dále.
* `From`: Pokud metoda nemá určenou `From` jazyka, tato hodnota budou přicházet z automatické rozpoznávání jazyka. V opačném případě bude zadaný `From` jazyka.
* `State`: Stav uživatele ke korelaci žádostí a odpovědí. Obsahuje hodnotu, dodané v `TranslateOptions` parametru.

`TranslationMatch` Objekt obsahuje následující hodnoty:
* `Error`: Kód chyby, pokud dojde k chybě pro konkrétní vstupní řetězec. V opačném případě toto pole je prázdné.
* `MatchDegree`: Určuje, jak blízko vstupní text odpovídá původní text v úložišti nalezena. Systém odpovídá vstupní věty proti úložišti, včetně té neodpovídá. Hodnota vrácena rozsahy od 0 do 100, kde 0 je žádné podobnosti a přesná, velká a malá písmena rozlišovat je 100.
* `MatchedOriginalText`: Původní text, odpovídající pro tento výsledek. Tato hodnota se vrátí jenom v případě, že odpovídající původní text se liší od vstupního textu. Se používá k vrácení zdrojového textu přibližné shody. Tato hodnota se nevrátí Microsoft Translatoru výsledků.
* `Rating`: Označuje autority osoby, která rozhodnutí kvality. Výsledky strojový překlad mají hodnocení 5. Anonymně zadaná překlady mají obvykle hodnocení od 1 do 4. Vynuceně zadaná překlady mají obvykle hodnocení od 6 až 10.
* `Count`: Počet pokusů, které tento překlad textů pomocí toto hodnocení se vybral. Hodnota je 0 pro automaticky přeložené odpověď.
* `TranslatedText`: Přeložený text.


### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)

řetězec

Typ obsahu odpovědi: application/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Value|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|Authorization  |(prázdné)    |Vyžaduje se, pokud obě `appid` pole a `Ocp-Apim-Subscription-Key` záhlaví jsou prázdné.  Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|(prázdné)  |Vyžaduje se, pokud obě `appid` pole a `Authorization` záhlaví jsou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Reason|
|:--|:--|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a podrobné chybové odpovědi.|
|401    |Neplatné přihlašovací údaje.|
|500    |Došlo k chybě serveru. Pokud potíže potrvají, dejte nám vědět. Pošlete nám s přibližné datum a čas požadavku a s ID žádosti zahrnutý v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba není dočasně k dispozici. Zkuste to prosím znovu a dejte nám vědět, pokud chyba přetrvává.|

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Migrujte na Translator Text API v3](../migrate-to-v3.md)


