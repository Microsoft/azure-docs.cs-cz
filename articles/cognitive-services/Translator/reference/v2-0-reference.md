---
title: Překladač Text API v2.0
titleSuffix: Azure Cognitive Services
description: Referenční dokumentace pro překladač text api v2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: f111169558118a80602bcb2136bc63ce54c9e0d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "72242489"
---
# <a name="translator-text-api-v20"></a>Překladač Text API v2.0

> [!IMPORTANT]
> Tato verze překladače text api byla zastaralá. [Zobrazení dokumentace pro verzi 3 rozhraní Translator Text API](v3-0-reference.md).

Verzi 2 rozhraní Translator Text API lze bezproblémově integrovat do aplikací, webů, nástrojů nebo jiných řešení, která poskytují vícejazyčné uživatelské prostředí. Můžete jej použít na libovolné hardwarové platformě a s libovolným operačním systémem k provádění jazykového překladu a dalších jazykových úloh, jako je detekce textového jazyka a převod textu na řeč podle oborových standardů. Další informace naleznete v [tématu Translator Text API](../translator-info-overview.md).

## <a name="getting-started"></a>Začínáme
Chcete-li získat přístup k rozhraní Translator Text API, musíte [se zaregistrovat do Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authentication"></a>Ověřování 
Všechna volání překladače textového rozhraní API vyžadují klíč předplatného pro ověřování. Rozhraní API podporuje tři metody ověřování:

- Přístupový token. Pomocí klíče předplatného vytvořte přístupový token vytvořením požadavku POST pro ověřovací službu. Podrobnosti naleznete v dokumentaci ke službě tokenů. Předajte přístupový token službě `Authorization` Translator `access_token` pomocí záhlaví nebo parametru dotazu. Přístupový token je platný po dobu 10 minut. Získejte nový přístupový token každých 10 minut a pokračujte v používání stejného přístupového tokenu pro opakované požadavky během 10 minut.
- Klíč předplatného používaný přímo. Předejte klíč předplatného jako `Ocp-Apim-Subscription-Key` hodnotu v záhlaví, které je součástí vašeho požadavku, do textového rozhraní TRANSLATOR API. Při použití klíče předplatného přímo, není třeba volat službu ověřování tokenu k vytvoření přístupového tokenu.
- Předplatné [azure cognitive services pro více služeb](https://azure.microsoft.com/pricing/details/cognitive-services/). Tato metoda umožňuje použít jeden tajný klíč k ověření požadavků pro více služeb.
Při použití klíče s více službami je třeba do požadavku zahrnout dvě záhlaví ověřování. První záhlaví předá tajný klíč. Druhá hlavička určuje oblast přidruženou k vašemu předplatnému:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

Oblast je vyžadována pro předplatné víceservisních textových rozhraní API. Vybraná oblast je jedinou oblastí, kterou můžete použít pro překlad textu při použití klíče předplatného s více službami. Musí se stát stejná oblast, kterou jste vybrali při registraci k předplatnému s více službami na webu Azure Portal.

Dostupné oblasti `australiaeast`jsou `brazilsouth` `canadacentral`, `centralindia` `centraluseuap`, `eastasia` `eastus`, `eastus2` `japaneast`, `northeurope` `southcentralus`, `southeastasia` `uksouth`, `westcentralus` `westeurope`, `westus`, `westus2`, , , , , , , , a .

Klíč předplatného a přístupový token jsou tajné klíče, které by měly být skryté.

## <a name="profanity-handling"></a>Manipulace s vulgárními výrazy
Za normálních okolností služba Translator zachová vulgární výrazy, které jsou k dispozici ve zdroji. Stupeň vulgárních výrazů a kontext, který dělá slova profánní se liší podle kultury. Stupeň vulgárních výrazů v cílovém jazyce by se tedy mohl zvýšit nebo snížit.

Pokud chcete zabránit vulgárním výrazům v překladu, i když je ve zdrojovém textu, můžete použít možnost filtrování vulgárních výrazů pro metody, které jej podporují. Tato možnost umožňuje zvolit, zda chcete zobrazit vulgární výrazy odstraněné nebo označené příslušnými značkami, nebo zda chcete povolit vulgární výrazy v cíli. Přijaté hodnoty `ProfanityAction` jsou `NoAction` (výchozí), `Marked` `Deleted`a .


|Vulgární akce    |Akce |Příklad zdroje (japonština)  |Ukázkový překlad (anglicky)  |
|:--|:--|:--|:--|
|NoAkce   |Default (Výchozí). Stejné jako nenastavovat možnost. Vulgární výrazy budou přecházet ze zdroje na cíl.        |V ěnní věnčitá     |Je to blbec.   |
|Označeny     |Vulgární slova budou obklopena xml \<tagy vulgárních \<výrazů> a /vulgární chudinské>.       |V ěnní věnčitá |Je to \<vulgární>osel\</ vulgární>.  |
|Odstranění    |Vulgární slova budou odstraněna z výstupu bez náhrady.     |V ěnní věnčitá |On je.   |

    
## <a name="excluding-content-from-translation"></a>Vyloučení obsahu z překladu
Při překladu obsahu pomocí značek, jako je HTML (`contentType=text/html`), je někdy užitečné vyloučit určitý obsah z překladu. Atribut `class=notranslate` můžete použít k určení obsahu, který by měl zůstat v původním jazyce. V následujícím příkladu obsah v `div` prvním prvku nebude přeložen, ale obsah `div` v druhém prvku bude přeložen.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Přeložit

### <a name="implementation-notes"></a>Poznámky k implementaci
Přeloží textový řetězec z jednoho jazyka do druhého.

Identifikátor URI `https://api.microsofttranslator.com/V2/Http.svc/Translate`požadavku je .

**Vrácená hodnota:** Řetězec, který představuje přeložený text.

Pokud jste `AddTranslation` dříve `AddTranslationArray` používali nebo zadali překlad s hodnocením 5 `Translate` nebo vyšší pro stejnou zdrojovou větu, vrátí pouze nejlepší volbu, která je k dispozici vašemu systému. "Stejná zdrojová věta" znamená přesně stejnou (100% odpovídající), s výjimkou velkých písmen, prázdného místa, hodnot tagů a interpunkce na konci věty. Pokud není uloženo žádné hodnocení s hodnocením 5 nebo vyšší, bude vráceným výsledkem automatický překlad aplikací Microsoft Translator.

### <a name="response-class-status-200"></a>Třída odezvy (stav 200)

řetězec

Typ obsahu odpovědi: aplikace/xml

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis    |Typ parametru|datový typ|
|:--|:--|:--|:--|:--|
|Appid  |(prázdné)    |Povinná hodnota. Pokud `Authorization` je `Ocp-Apim-Subscription-Key` použito záhlaví nebo, ponechte `appid` pole prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|text|(prázdné)   |Povinná hodnota. Řetězec, který představuje text přeložit. Text nesmí obsahovat více než 10 000 znaků.|query|řetězec|
|Z|(prázdné)   |Nepovinný parametr. Řetězec, který představuje kód jazyka textu, který je přeložen. Například en pro angličtinu.|query|řetězec|
|na|(prázdné) |Povinná hodnota. Řetězec, který představuje kód jazyka přeložit text do.|query|řetězec|
|Contenttype|(prázdné)    |Nepovinný parametr. Formát textu, který je přeložen. Podporované formáty `text/plain` jsou (výchozí) a `text/html`. Všechny prvky HTML musí být dobře tvarované, úplné prvky.|query|řetězec|
|category|(prázdné)   |Nepovinný parametr. Řetězec, který obsahuje kategorii (doménu) překladu. Výchozí formát je `general`.|query|řetězec|
|Autorizace|(prázdné)  |Povinné, `appid` pokud pole `Ocp-Apim-Subscription-Key` i záhlaví zůstanou prázdné. Autorizační `"Bearer" + " " + "access_token"`token: .|záhlaví|řetězec|
|Ocp-Apim-Subscription-Key|(prázdné)  |Povinné, `appid` pokud pole `Authorization` i záhlaví zůstanou prázdné.|záhlaví|řetězec|


### <a name="response-messages"></a>Odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400    |Špatná žádost. Zkontrolujte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatná pověření.|
|500    |Chyba serveru. Pokud chyba přetrvává, dejte nám vědět. Poskytněte nám přibližné datum & čas žádosti a `X-MS-Trans-Info`ID žádosti obsažené v hlavičce odpovědi .|
|503    |Služba je dočasně nedostupná. Zkuste to zopakovat a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte překlady pro více zdrojových textů.

Identifikátor URI `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`požadavku je .

Zde je formát těla požadavku:

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

Tyto prvky `TranslateArrayRequest`jsou v :


* `AppId`: Povinné. Pokud `Authorization` je `Ocp-Apim-Subscription-Key` použito záhlaví nebo, ponechte `AppId` pole prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.
* `From`: Nepovinné. Řetězec, který představuje kód jazyka textu, který je přeložen. Pokud je toto pole prázdné, odpověď bude obsahovat výsledek automatického rozpoznávání jazyka.
* `Options`: Nepovinné. Objekt, `Options` který obsahuje následující hodnoty. Všechny jsou volitelné a ve výchozím nastavení se vevýchozím nastavení. Zadané prvky musí být uvedeny v abecedním pořadí.
    - `Category`: Řetězec, který obsahuje kategorii (doménu) překladu. Výchozí formát je `general`.
    - `ContentType`: Formát překládaného textu. Podporované formáty `text/plain` jsou `text/xml`(výchozí), `text/html`a . Všechny prvky HTML musí být dobře tvarované, úplné prvky.
    - `ProfanityAction`: Určuje způsob zpracování vulgárních výrazů, jak bylo vysvětleno výše. Přijaté hodnoty `NoAction` jsou `Marked`(výchozí), a `Deleted`.
    - `State`: Stav uživatele, který pomáhá korelovat požadavek a odpověď. Stejný obsah bude vrácen v odpovědi.
    - `Uri`: Filtrování výsledků podle tohoto identifikátoru URI. Výchozí: `all`.
    - `User`: Filtrování výsledků podle tohoto uživatele. Výchozí: `all`.
* `Texts`: Povinné. Pole, které obsahuje text pro překlad. Všechny řetězce musí být ve stejném jazyce. Celkový počet textu, který má být přeložen, nesmí překročit 10 000 znaků. Maximální počet prvků pole je 2 000.
* `To`: Povinné. Řetězec, který představuje kód jazyka přeložit text do.

Volitelné prvky můžete vynechat. Prvky, které `TranslateArrayRequest` jsou přímé podřízené musí být uvedeny v abecedním pořadí.

Metoda `TranslateArray` přijímá `application/xml` nebo `text/xml` `Content-Type`pro .

**Vrácená hodnota:** Pole. `TranslateArrayResponse` Každý `TranslateArrayResponse` z nich má tyto prvky:

* `Error`: Označuje chybu, pokud dojde k chybě. Jinak je nastavena na hodnotu null.
* `OriginalSentenceLengths`: Pole celá čísla, která označuje délku každé věty ve zdrojovém textu. Délka pole označuje počet vět.
* `TranslatedText`: Přeložený text.
* `TranslatedSentenceLengths`: Pole celá čísla, která označuje délku každé věty v přeloženém textu. Délka pole označuje počet vět.
* `State`: Stav uživatele, který pomáhá korelovat požadavek a odpověď. Vrátí stejný obsah jako požadavek.

Zde je formát těla odezvy:

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

### <a name="response-class-status-200"></a>Třída odezvy (stav 200)
Úspěšná odpověď zahrnuje `TranslateArrayResponse` pole polí ve formátu popsaném výše.

řetězec

Typ obsahu odpovědi: aplikace/xml

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|Autorizace|(prázdné)  |Povinné, `appid` pokud pole `Ocp-Apim-Subscription-Key` i záhlaví zůstanou prázdné. Autorizační `"Bearer" + " " + "access_token"`token: .|záhlaví|řetězec|
|Ocp-Apim-Subscription-Key|(prázdné)|Povinné, `appid` pokud pole `Authorization` i záhlaví zůstanou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Odpovědi

|Stavový kód HTTP   |Důvod|
|:--|:--|
|400    |Špatná žádost. Zkontrolujte vstupní parametry a podrobnou chybovou odpověď. Běžné chyby zahrnují: <ul><li>Prvek pole nemůže být prázdný.</li><li>Neplatná kategorie.</li><li>Z jazyka je neplatný.</li><li>Jazyk je neplatný.</li><li>Požadavek obsahuje příliš mnoho prvků.</li><li>Jazyk Od není podporován.</li><li>Jazyk To není podporován.</li><li>Požadavek na překlad má příliš mnoho dat.</li><li>HTML není ve správném formátu.</li><li>V požadavku na překlad byl předáno příliš mnoho řetězců.</li></ul>|
|401    |Neplatná pověření.|
|500    |Chyba serveru. Pokud chyba přetrvává, dejte nám vědět. Poskytněte nám přibližné datum & čas žádosti a `X-MS-Trans-Info`ID žádosti obsažené v hlavičce odpovědi .|
|503    |Služba je dočasně nedostupná. Zkuste to zopakovat a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte popisné názvy jazyků předaných jako `languageCodes`parametr `locale` , lokalizované do předaná jazyka.

Identifikátor URI `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`požadavku je .

Tělo požadavku obsahuje pole řetězců, které představuje kódy jazyka ISO 639-1, pro které chcete načíst popisné názvy. Tady je příklad:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Vrácená hodnota:** Pole řetězců, které obsahuje názvy jazyků podporované službou Translator, lokalizované do požadovaného jazyka.

### <a name="response-class-status-200"></a>Třída odezvy (stav 200)
Pole řetězců, které obsahuje názvy jazyků podporované službou Translator, lokalizované do požadovaného jazyka.

řetězec

Typ obsahu odpovědi: aplikace/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|Appid|(prázdné)|Povinná hodnota. Pokud `Authorization` je `Ocp-Apim-Subscription-Key` použito záhlaví nebo, ponechte `appid` pole prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|locale|(prázdné) |Povinná hodnota. Řetězec, který představuje jednu z následujících, slouží k lokalizaci názvů jazyků: <ul><li>Kombinace dvoupísmenného kódu jazykové verze ISO 639 s malou písmena spojenou s jazykem a dvoupísmenným kódem subkultury ISO 3166. <li>Iso 639 kód jazykové verze s modrými písmeny sám.|query|řetězec|
|Autorizace|(prázdné)  |Povinné, `appid` pokud pole `Ocp-Apim-Subscription-Key` i záhlaví zůstanou prázdné. Autorizační `"Bearer" + " " + "access_token"`token: .|záhlaví|řetězec|
|Ocp-Apim-Subscription-Key|(prázdné)  |Povinné, `appid` pokud pole `Authorization` i záhlaví zůstanou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400    |Špatná žádost. Zkontrolujte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatná pověření.|
|500    |Chyba serveru. Pokud chyba přetrvává, dejte nám vědět. Poskytněte nám přibližné datum & čas žádosti a `X-MS-Trans-Info`ID žádosti obsažené v hlavičce odpovědi .|
|503    |Služba je dočasně nedostupná. Zkuste to zopakovat a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Poznámky k implementaci
Získá seznam kódů jazyků, které představují jazyky podporované překladatelské služby.  `Translate`a `TranslateArray` lze překládat mezi dvěma z těchto jazyků.

Identifikátor URI `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`požadavku je .

**Vrácená hodnota:** Pole řetězců, které obsahuje kódy jazyků podporované službou Translator.

### <a name="response-class-status-200"></a>Třída odezvy (stav 200)
Pole řetězců, které obsahuje kódy jazyků podporované službou Translator.

řetězec

Typ obsahu odpovědi: aplikace/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|Appid|(prázdné)|Povinná hodnota. Pokud `Authorization` je `Ocp-Apim-Subscription-Key` použito záhlaví nebo, ponechte `appid` pole prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|Autorizace|(prázdné)  |Povinné, `appid` pokud pole `Ocp-Apim-Subscription-Key` i záhlaví zůstanou prázdné. Autorizační `"Bearer" + " " + "access_token"`token: .|záhlaví|řetězec|
|Ocp-Apim-Subscription-Key|(prázdné)|Povinné, `appid` pokud pole `Authorization` i záhlaví zůstanou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400    |Špatná žádost. Zkontrolujte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatná pověření.|
|500    |Chyba serveru. Pokud chyba přetrvává, dejte nám vědět. Poskytněte nám přibližné datum & čas žádosti a `X-MS-Trans-Info`ID žádosti obsažené v hlavičce odpovědi .|
|503|Služba je dočasně nedostupná. Zkuste to zopakovat a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte jazyky, které jsou k dispozici pro syntézu řeči.

Identifikátor URI `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`požadavku je .

**Vrácená hodnota:** Pole řetězců, které obsahuje kódy jazyka podporované pro syntézu řeči službou Translator.

### <a name="response-class-status-200"></a>Třída odezvy (stav 200)
Pole řetězců, které obsahuje kódy jazyka podporované pro syntézu řeči službou Translator.

řetězec

Typ obsahu odpovědi: aplikace/xml

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|Appid|(prázdné)|Povinná hodnota. Pokud `Authorization` je `Ocp-Apim-Subscription-Key` použito záhlaví nebo, ponechte `appid` pole prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|Autorizace|(prázdné)|Povinné, `appid` pokud pole `Ocp-Apim-Subscription-Key` i záhlaví zůstanou prázdné. Autorizační `"Bearer" + " " + "access_token"`token: .|záhlaví|řetězec|
|Ocp-Apim-Subscription-Key|(prázdné)|Povinné, `appid` pokud pole `Authorization` i záhlaví zůstanou prázdné.|záhlaví|řetězec|
 
### <a name="response-messages"></a>Odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400|Špatná žádost. Zkontrolujte vstupní parametry a podrobnou chybovou odpověď.|
|401|Neplatná pověření.|
|500    |Chyba serveru. Pokud chyba přetrvává, dejte nám vědět. Poskytněte nám přibližné datum & čas žádosti a `X-MS-Trans-Info`ID žádosti obsažené v hlavičce odpovědi .|
|503    |Služba je dočasně nedostupná. Zkuste to zopakovat a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>Poznámky k implementaci
Vrátí datový proud WAV nebo MP3 předávaného textu, který se používá v požadovaném jazyce.

Identifikátor URI `https://api.microsofttranslator.com/V2/Http.svc/Speak`požadavku je .

**Vrácená hodnota:** Wav nebo MP3 proud předávaného textu, mluvený v požadovaném jazyce.

### <a name="response-class-status-200"></a>Třída odezvy (stav 200)

binární

Typ obsahu odpovědi: aplikace/xml

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|Appid|(prázdné)|Povinná hodnota. Pokud `Authorization` je `Ocp-Apim-Subscription-Key` použito záhlaví nebo, ponechte `appid` pole prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|text|(prázdné)   |Povinná hodnota. Řetězec, který obsahuje jednu nebo více vět, které mají být mluvené pro datový proud, v zadaném jazyce. Text nesmí přesáhnout 2 000 znaků.|query|řetězec|
|language|(prázdné)   |Povinná hodnota. Řetězec, který představuje kód podporovaného jazyka jazyka, ve kterém má být text vyslovován. Kód musí být jedním z kódů `GetLanguagesForSpeak`vrácených metodou .|query|řetězec|
|formát|(prázdné)|Nepovinný parametr. Řetězec, který určuje ID typu obsahu. V současné `audio/wav` `audio/mp3` době, a jsou k dispozici. Výchozí hodnota je `audio/wav`.|query|řetězec|
|možnosti|(prázdné)    |Nepovinný parametr. Řetězec, který určuje vlastnosti syntetizované řeči:<ul><li>`MaxQuality`a `MinSize` určete kvalitu zvukového signálu. `MaxQuality`poskytuje nejvyšší kvalitu. `MinSize`poskytuje nejmenší velikost souboru. Výchozí hodnota `MinSize`je .</li><li>`female`a `male` určete požadované pohlaví hlasu. Výchozí formát je `female`. Pomocí svislého pruhu (<code>\|</code>) můžete zahrnout více možností. Například `MaxQuality|Male`.</li></li></ul>  |query|řetězec|
|Autorizace|(prázdné)|Povinné, `appid` pokud pole `Ocp-Apim-Subscription-Key` i záhlaví zůstanou prázdné. Autorizační `"Bearer" + " " + "access_token"`token: .|záhlaví|řetězec|
|Ocp-Apim-Subscription-Key|(prázdné)  |Povinné, `appid` pokud pole `Authorization` i záhlaví zůstanou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400    |Špatná žádost. Zkontrolujte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatná pověření.|
|500    |Chyba serveru. Pokud chyba přetrvává, dejte nám vědět. Poskytněte nám přibližné datum & čas žádosti a `X-MS-Trans-Info`ID žádosti obsažené v hlavičce odpovědi .|
|503    |Služba je dočasně nedostupná. Zkuste to zopakovat a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>Poznámky k implementaci
Identifikuje jazyk části textu.

Identifikátor URI `https://api.microsofttranslator.com/V2/Http.svc/Detect`požadavku je .

**Vrácená hodnota:** Řetězec, který obsahuje dvouznakový kód jazyka pro text.

### <a name="response-class-status-200"></a>Třída odezvy (stav 200)

řetězec

Typ obsahu odpovědi: aplikace/xml

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|Appid|(prázdné)  |Povinná hodnota. Pokud `Authorization` je `Ocp-Apim-Subscription-Key` použito záhlaví nebo, ponechte `appid` pole prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|text|(prázdné)|Povinná hodnota. Řetězec, který obsahuje text, jehož jazyk má být identifikován. Text nesmí přesáhnout 10 000 znaků.|query|  řetězec|
|Autorizace|(prázdné)|Povinné, `appid` pokud pole `Ocp-Apim-Subscription-Key` i záhlaví zůstanou prázdné. Autorizační `"Bearer" + " " + "access_token"`token: .|záhlaví|řetězec|
|Ocp-Apim-Subscription-Key  |(prázdné)    |Povinné, `appid` pokud pole `Authorization` i záhlaví zůstanou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400|Špatná žádost. Zkontrolujte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatná pověření.|
|500    |Chyba serveru. Pokud chyba přetrvává, dejte nám vědět. Poskytněte nám přibližné datum & čas žádosti a `X-MS-Trans-Info`ID žádosti obsažené v hlavičce odpovědi .|
|503    |Služba je dočasně nedostupná. Zkuste to zopakovat a dejte nám vědět, pokud chyba přetrvává.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Poznámky k implementaci

Identifikuje jazyky v poli řetězců. Nezávisle detekuje jazyk každého jednotlivého prvku pole a vrátí výsledek pro každý řádek pole.

Identifikátor URI `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`požadavku je .

Zde je formát těla požadavku:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Text nesmí přesáhnout 10 000 znaků.

**Vrácená hodnota:** Pole řetězce, které obsahuje dvouznakový kód jazyka pro každý řádek ve vstupním poli.

Zde je formát těla odezvy:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Třída odezvy (stav 200)
`DetectArray`byla úspěšná. Vrátí pole řetězce, které obsahuje dvouznakový kód jazyka pro každý řádek vstupního pole.

řetězec

Typ obsahu odpovědi: aplikace/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|Appid|(prázdné)|Povinná hodnota. Pokud `Authorization` je `Ocp-Apim-Subscription-Key` použito záhlaví nebo, ponechte `appid` pole prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|Autorizace|(prázdné)|Povinné, `appid` pokud pole `Ocp-Apim-Subscription-Key` i záhlaví zůstanou prázdné.  Autorizační `"Bearer" + " " + "access_token"`token: .|záhlaví|řetězec|
|Ocp-Apim-Subscription-Key|(prázdné)|Povinné, `appid` pokud pole `Authorization` i záhlaví zůstanou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400    |Špatná žádost. Zkontrolujte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatná pověření.|
|500    |Chyba serveru. Pokud chyba přetrvává, dejte nám vědět. Poskytněte nám přibližné datum & čas žádosti a `X-MS-Trans-Info`ID žádosti obsažené v hlavičce odpovědi .|
|503    |Služba je dočasně nedostupná. Zkuste to zopakovat a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Poznámky k implementaci

> [!IMPORTANT]
> **Poznámka k vyřazení:** ledna 2018 tato metoda nepřijme nové příspěvky na větu. Zobrazí se chybová zpráva. Viz oznámení o změnách rámce pro kolaborativní překlady (CTF).

Přidá překlad do překladové paměti.

Identifikátor URI `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`požadavku je .

### <a name="response-class-status-200"></a>Třída odezvy (stav 200)

řetězec

Typ obsahu odpovědi: aplikace: xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Datový typ   |
|:--|:--|:--|:--|:--|
|Appid|(prázdné)|Povinná hodnota. Pokud `Authorization` je `Ocp-Apim-Subscription-Key` použito záhlaví nebo, ponechte `appid` pole prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|originalText|(prázdné)|Povinná hodnota. Řetězec, který obsahuje text přeložit. Maximální délka řetězce je 1 000 znaků.|query|řetězec|
|přeloženýText|(prázdné) |Povinná hodnota. Řetězec, který obsahuje text přeložený do cílového jazyka. Maximální délka řetězce je 2 000 znaků.|query|řetězec|
|Z|(prázdné)   |Povinná hodnota. Řetězec, který představuje kód jazyka původního jazyka textu. Například en pro angličtinu a de pro němčinu.|query|řetězec|
|na|(prázdné)|Povinná hodnota. Řetězec, který představuje kód jazyka přeložit text do.|query|řetězec|
|rating|(prázdné) |Nepovinný parametr. Celé číslo, které představuje hodnocení kvality řetězce. Hodnota je mezi -10 a 10. Výchozí hodnota je 1.|query|celé číslo|
|Contenttype|(prázdné)    |Nepovinný parametr. Formát textu, který je přeložen. Podporované formáty `text/plain` jsou `text/html`a . Všechny prvky HTML musí být dobře tvarované, úplné prvky.    |query|řetězec|
|category|(prázdné)|Nepovinný parametr. Řetězec, který obsahuje kategorii (doménu) překladu. Výchozí formát je `general`.|query|řetězec|
|uživatel|(prázdné)|Povinná hodnota. Řetězec, který se používá ke sledování původce odeslání.|query|řetězec|
|Uri|(prázdné)|Nepovinný parametr. Řetězec, který obsahuje umístění obsahu překladu.|query|řetězec|
|Autorizace|(prázdné)|Povinné, `appid` pokud pole `Ocp-Apim-Subscription-Key` i záhlaví zůstanou prázdné.  Autorizační `"Bearer" + " " + "access_token"`token: .  |záhlaví|řetězec|
|Ocp-Apim-Subscription-Key|(prázdné)|Povinné, `appid` pokud pole `Authorization` i záhlaví zůstanou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400    |Špatná žádost. Zkontrolujte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatná pověření.|
|410|`AddTranslation`již není podporována.|
|500    |Chyba serveru. Pokud chyba přetrvává, dejte nám vědět. Poskytněte nám přibližné datum & čas žádosti a `X-MS-Trans-Info`ID žádosti obsažené v hlavičce odpovědi .|
|503    |Služba je dočasně nedostupná. Zkuste to zopakovat a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Poznámky k implementaci

> [!IMPORTANT]
> **Poznámka k vyřazení:** ledna 2018 tato metoda nepřijme nové příspěvky na větu. Zobrazí se chybová zpráva. Viz oznámení o změnách rámce pro kolaborativní překlady (CTF).

Přidá pole překladů do překladové paměti. Tato metoda je verze `AddTranslation`pole .

Identifikátor URI `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`požadavku je .

Zde je formát těla požadavku:

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

Tyto prvky `AddtranslationsRequest`jsou v :

* `AppId`: Povinné. Pokud `Authorization` je `Ocp-Apim-Subscription-Key` použito záhlaví nebo, ponechte `AppId` pole prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.
* `From`: Povinné. Řetězec, který obsahuje kód jazyka zdrojového jazyka. Musí být jeden z jazyků `GetLanguagesForTranslate` vrácených metodou.
* `To`: Povinné. Řetězec, který obsahuje kód jazyka cílového jazyka. Musí být jeden z jazyků `GetLanguagesForTranslate` vrácených metodou.
* `Translations`: Povinné. Pole překladů, které chcete přidat do překladové paměti. Každý překlad `OriginalText`musí `TranslatedText`obsahovat , a `Rating`. Maximální velikost každého `OriginalText` `TranslatedText` a je 1 000 znaků. Součet všech `OriginalText` `TranslatedText` a prvků nesmí překročit 10 000 znaků. Maximální počet prvků pole je 100.
* `Options`: Povinné. Sada možností, včetně `Category` `ContentType`, `Uri`, `User`a . `User` je povinné. `Category`, `ContentType`a `Uri` jsou volitelné. Zadané prvky musí být uvedeny v abecedním pořadí.

### <a name="response-class-status-200"></a>Třída odezvy (stav 200)
`AddTranslationArray`metoda byla úspěšná. 

ledna 2018 nebudou příspěvky na vynesení rozsudku přijaty. Služba bude reagovat s kódem chyby 410.

řetězec

Typ obsahu odpovědi: aplikace/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|Autorizace|(prázdné)|Povinné, `appid` pokud pole `Ocp-Apim-Subscription-Key` i záhlaví zůstanou prázdné.  Autorizační `"Bearer" + " " + "access_token"`token: .|záhlaví|řetězec|
|Ocp-Apim-Subscription-Key|(prázdné)|Povinné, `appid` pokud pole `Authorization` i záhlaví zůstanou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400    |Špatná žádost. Zkontrolujte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatná pověření.|
|410    |`AddTranslation`již není podporována.|
|500    |Chyba serveru. Pokud chyba přetrvává, dejte nám vědět. Poskytněte nám přibližné datum & čas žádosti a `X-MS-Trans-Info`ID žádosti obsažené v hlavičce odpovědi .|
|503|Služba je dočasně nedostupná. Zkuste to zopakovat a dejte nám vědět, pokud chyba přetrvává.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Poznámky k implementaci
Rozdělí část textu na věty a vrátí pole, které obsahuje délky každé věty.

Identifikátor URI `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`požadavku je .

**Vrácená hodnota:** Pole celá čísla, která představuje délky vět. Délka pole představuje počet vět. Hodnoty představují délku každé věty.

### <a name="response-class-status-200"></a>Třída odezvy (stav 200)
Pole celá čísla, která představuje délky vět. Délka pole představuje počet vět. Hodnoty představují délku každé věty.

celé číslo

Typ obsahu odpovědi: aplikace/xml

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|Appid|(prázdné)  |Povinná hodnota. Pokud `Authorization` je `Ocp-Apim-Subscription-Key` použito záhlaví nebo, ponechte `appid` pole prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query| řetězec|
|text|(prázdné)   |Povinná hodnota. Řetězec, který představuje text rozdělit do vět. Maximální velikost textu je 10 000 znaků.|query|řetězec|
|language   |(prázdné)    |Povinná hodnota. Řetězec, který představuje kód jazyka vstupního textu.|query|řetězec|
|Autorizace|(prázdné)|Povinné, `appid` pokud pole `Ocp-Apim-Subscription-Key` i záhlaví zůstanou prázdné. Autorizační `"Bearer" + " " + "access_token"`token: .   |záhlaví|řetězec|
|Ocp-Apim-Subscription-Key|(prázdné)|Povinné, `appid` pokud pole `Authorization` i záhlaví zůstanou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400|Špatná žádost. Zkontrolujte vstupní parametry a podrobnou chybovou odpověď.|
|401|Neplatná pověření.|
|500|Chyba serveru. Pokud chyba přetrvává, dejte nám vědět. Poskytněte nám přibližné datum & čas žádosti a `X-MS-Trans-Info`ID žádosti obsažené v hlavičce odpovědi .|
|503|Služba je dočasně nedostupná. Zkuste to zopakovat a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte pole překladů pro daný pár jazyka z úložiště a modulu MT. `GetTranslations`se liší `Translate` v tom, že vrací všechny dostupné překlady.

Identifikátor URI `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`požadavku je .

Tělo požadavku obsahuje volitelný `TranslationOptions` objekt, který má tento formát:

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

Objekt `TranslateOptions` obsahuje hodnoty v následujícím seznamu. Všechny jsou volitelné a ve výchozím nastavení se vevýchozím nastavení. Zadané prvky musí být uvedeny v abecedním pořadí.

* `Category`: Řetězec, který obsahuje kategorii (doménu) překladu. Výchozí formát je `general`.
* `ContentType`: Jedinou podporovanou možností a `text/plain`výchozí možností je .
* `IncludeMultipleMTAlternatives`: Logický příznak k určení, zda více než jednu alternativu by měla být vrácena z motoru MT. Platné hodnoty `true` `false` jsou a (malá a velká písmena). Výchozí hodnota `false`je , která vrací pouze jednu alternativu. Nastavení vlajky `true` umožňuje vytváření umělých alternativ, plně integrovaných s rámcem kolaborativního překladu (CTF). Tato funkce umožňuje vrácení alternativ pro věty, které nemají žádné překlady v CTF přidáním umělé alternativy z *n*-nejlepší seznam dekodéru.
    - Hodnocení. Hodnocení se použijí takto: 
         - Nejlepší automatický překlad má hodnocení 5.
       - Alternativy CTF odrážejí autoritu recenzenta. Jejich rozsah se pohybuje od -10 do +10.
       - Automaticky generované *(n*-nejlepší) alternativy překladu mají hodnocení 0 a stupeň shody 100.
    - Počet alternativ. Počet vrácených alternativ může být stejně vysoký `maxTranslations`jako hodnota zadaná v , ale může být nižší.
    - Jazykové páry. Tato funkce není k dispozici pro překlady mezi zjednodušenou čínštinou a tradiční čínštinou v obou směrech. Je k dispozici pro všechny ostatní jazykové páry podporované aplikací Microsoft Translator.
* `State`: Stav uživatele, který pomáhá korelovat požadavek a odpověď. Stejný obsah bude vrácen v odpovědi.
* `Uri`: Filtrování výsledků podle tohoto identifikátoru URI. Pokud není nastavena žádná `all`hodnota, výchozí hodnota je .
* `User`: Filtrování výsledků podle tohoto uživatele. Pokud není nastavena žádná `all`hodnota, výchozí hodnota je .

Žádost `Content-Type` by `text/xml`měla být .

**Vrácená hodnota:** Zde je formát odpovědi:

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

Tato odpověď `GetTranslationsResponse` obsahuje prvek, který obsahuje následující hodnoty:

* `Translations`: Pole nalezených shod, které jsou uloženy v `TranslationMatch` objektech (popsané v následující části). Překlady mohou obsahovat nepatrné varianty původního textu (přibližná shoda). Překlady budou seřazeny: 100% odpovídá první, fuzzy shody další.
* `From`: Pokud metoda neurčuje `From` jazyk, bude tato hodnota pocházet z automatického rozpoznávání jazyka. V opačném případě bude `From` zadaný jazyk.
* `State`: Stav uživatele, který pomáhá korelovat požadavek a odpověď. Obsahuje hodnotu zadanou v parametru. `TranslateOptions`

Objekt `TranslationMatch` se skládá z těchto hodnot:

* `Error`: Kód chyby, pokud dojde k chybě pro konkrétní vstupní řetězec. V opačném případě je toto pole prázdné.
* `MatchDegree`: Označuje, jak úzce se vstupní text shoduje s původním textem nalezeným v obchodě. Systém odpovídá vstupní věty proti úložiště, včetně nepřesné shody. Vrácená hodnota se pohybuje od 0 do 100, kde 0 není podobnost a 100 je přesná shoda rozlišující malá a velká písmena.
* `MatchedOriginalText`: Původní text, který byl spárován pro tento výsledek. Tato hodnota je vrácena pouze v případě, že odpovídající původní text se liší od vstupního textu. Používá se k vrácení zdrojového textu rozmazanou shodu. Tato hodnota není vrácena pro výsledky microsoft translator.
* `Rating`: Označuje autoritu osoby, která rozhoduje o kvalitě. Výsledky strojového překladu mají hodnocení 5. Anonymně poskytované překlady mají obecně hodnocení od 1 do 4. Autoritativně poskytované překlady budou mít obecně hodnocení od 6 do 10.
* `Count`: Počet, kolikrát byl tento překlad s tímto hodnocením vybrán. Hodnota je 0 pro automaticky přeloženou odpověď.
* `TranslatedText`: Přeložený text.

### <a name="response-class-status-200"></a>Třída odezvy (stav 200)
Objekt `GetTranslationsResponse` ve formátu popsaném výše.

řetězec

Typ obsahu odpovědi: aplikace/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|Appid|(prázdné)|Povinná hodnota. Pokud `Authorization` je `Ocp-Apim-Subscription-Key` použito záhlaví nebo, ponechte `appid` pole prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|text|(prázdné)|Povinná hodnota. Řetězec, který představuje text přeložit. Maximální velikost textu je 10 000 znaků.|query|řetězec|
|Z|(prázdné)|Povinná hodnota. Řetězec, který představuje kód jazyka textu, který je přeložen.|query|řetězec|
|na |(prázdné)    |Povinná hodnota. Řetězec, který představuje kód jazyka přeložit text do.|query|řetězec|
|maxPřeklady|(prázdné)|Povinná hodnota. Celé číslo, které představuje maximální počet překladů, které mají být vráceny.|query|celé číslo|
|Autorizace| (prázdné)|Povinné, `appid` pokud pole `Ocp-Apim-Subscription-Key` i záhlaví zůstanou prázdné. Autorizační `"Bearer" + " " + "access_token"`token: .|řetězec|  záhlaví|
|Ocp-Apim-Subscription-Key|(prázdné)  |Povinné, `appid` pokud pole `Authorization` i záhlaví zůstanou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400    |Špatná žádost. Zkontrolujte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatná pověření.|
|500    |Chyba serveru. Pokud chyba přetrvává, dejte nám vědět. Poskytněte nám přibližné datum & čas žádosti a `X-MS-Trans-Info`ID žádosti obsažené v hlavičce odpovědi .|
|503|Služba je dočasně nedostupná. Zkuste to zopakovat a dejte nám vědět, pokud chyba přetrvává.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte více kandidátů překladu pro více zdrojových textů.

Identifikátor URI `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`požadavku je .

Zde je formát těla požadavku:

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

`GetTranslationsArrayRequest`obsahuje tyto prvky:

* `AppId`: Povinné. Pokud `Authorization` je záhlaví použito, `AppId` ponechte pole prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.
* `From`: Povinné. Řetězec, který představuje kód jazyka textu, který je přeložen.
* `MaxTranslations`: Povinné. Celé číslo, které představuje maximální počet překladů, které mají být vráceny.
* `Options`: Nepovinné. Objekt, `Options` který obsahuje následující hodnoty. Všechny jsou volitelné a ve výchozím nastavení se vevýchozím nastavení. Zadané prvky musí být uvedeny v abecedním pořadí.
    - `Category`: Řetězec, který obsahuje kategorii (doménu) překladu. Výchozí formát je `general`.
    - `ContentType`: Jedinou podporovanou možností a `text/plain`výchozí možností je .
    - `IncludeMultipleMTAlternatives`: Logický příznak k určení, zda více než jednu alternativu by měla být vrácena z motoru MT. Platné hodnoty `true` `false` jsou a (malá a velká písmena). Výchozí hodnota `false`je , která vrací pouze jednu alternativu. Nastavení vlajky `true` umožňuje generování umělých alternativ v překladu, plně integrované s collaborative translations framework (CTF). Tato funkce umožňuje vrácení alternativ pro věty, které nemají žádné alternativy v CTF přidáním umělé alternativy z *n*-nejlepší seznam dekodéru.
        - Hodnocení Hodnocení se používají takto:
          - Nejlepší automatický překlad má hodnocení 5.
          - Alternativy CTF odrážejí autoritu recenzenta. Jejich rozsah se pohybuje od -10 do +10.
          - Automaticky generované *(n*-nejlepší) alternativy překladu mají hodnocení 0 a stupeň shody 100.
        - Počet alternativ. Počet vrácených alternativ může být stejně vysoký `maxTranslations`jako hodnota zadaná v , ale může být nižší.
        - Jazykové páry. Tato funkce není k dispozici pro překlady mezi zjednodušenou čínštinou a tradiční čínštinou v obou směrech. Je k dispozici pro všechny ostatní jazykové páry podporované aplikací Microsoft Translator.
* `State`: Stav uživatele, který pomáhá korelovat požadavek a odpověď. Stejný obsah bude vrácen v odpovědi.
* `Uri`: Filtrování výsledků podle tohoto identifikátoru URI. Pokud není nastavena žádná `all`hodnota, výchozí hodnota je .
* `User`: Filtrování výsledků podle tohoto uživatele. Pokud není nastavena žádná `all`hodnota, výchozí hodnota je .
* `Texts`: Povinné. Pole, které obsahuje text pro překlad. Všechny řetězce musí být ve stejném jazyce. Celkový počet textu, který má být přeložen, nesmí překročit 10 000 znaků. Maximální počet prvků pole je 10.
* `To`: Povinné. Řetězec, který představuje kód jazyka přeložit text do.

Volitelné prvky můžete vynechat. Prvky, které `GetTranslationsArrayRequest` jsou přímé podřízené musí být uvedeny v abecedním pořadí.

Žádost `Content-Type` by `text/xml`měla být .

**Vrácená hodnota:** Zde je formát odpovědi:

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

* `Translations`: Pole nalezených shod, které jsou uloženy v `TranslationMatch` objektech (popsané v následující části). Překlady mohou obsahovat nepatrné varianty původního textu (přibližná shoda). Překlady budou seřazeny: 100% odpovídá první, fuzzy shody další.
* `From`: Pokud metoda neurčuje `From` jazyk, bude tato hodnota pocházet z automatického rozpoznávání jazyka. V opačném případě bude `From` zadaný jazyk.
* `State`: Stav uživatele, který pomáhá korelovat požadavek a odpověď. Obsahuje hodnotu zadanou v parametru. `TranslateOptions`

Objekt `TranslationMatch` obsahuje následující hodnoty:
* `Error`: Kód chyby, pokud dojde k chybě pro konkrétní vstupní řetězec. V opačném případě je toto pole prázdné.
* `MatchDegree`: Označuje, jak úzce se vstupní text shoduje s původním textem nalezeným v obchodě. Systém odpovídá vstupní věty proti úložiště, včetně nepřesné shody. Vrácená hodnota se pohybuje od 0 do 100, kde 0 není podobnost a 100 je přesná shoda rozlišující malá a velká písmena.
* `MatchedOriginalText`: Původní text, který byl spárován pro tento výsledek. Tato hodnota je vrácena pouze v případě, že odpovídající původní text se liší od vstupního textu. Používá se k vrácení zdrojového textu rozmazanou shodu. Tato hodnota není vrácena pro výsledky microsoft translator.
* `Rating`: Označuje autoritu osoby, která rozhoduje o kvalitě. Výsledky strojového překladu mají hodnocení 5. Anonymně poskytované překlady mají obecně hodnocení od 1 do 4. Autoritativně poskytované překlady mají obecně hodnocení od 6 do 10.
* `Count`: Počet, kolikrát byl tento překlad s tímto hodnocením vybrán. Hodnota je 0 pro automaticky přeloženou odpověď.
* `TranslatedText`: Přeložený text.


### <a name="response-class-status-200"></a>Třída odezvy (stav 200)

řetězec

Typ obsahu odpovědi: aplikace/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Datový typ|
|:--|:--|:--|:--|:--|
|Autorizace  |(prázdné)    |Povinné, `appid` pokud pole `Ocp-Apim-Subscription-Key` i záhlaví zůstanou prázdné.  Autorizační `"Bearer" + " " + "access_token"`token: .|záhlaví|řetězec|
|Ocp-Apim-Subscription-Key|(prázdné)  |Povinné, `appid` pokud pole `Authorization` i záhlaví zůstanou prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400    |Špatná žádost. Zkontrolujte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatná pověření.|
|500    |Chyba serveru. Pokud chyba přetrvává, dejte nám vědět. Poskytněte nám přibližné datum & čas žádosti a `X-MS-Trans-Info`ID žádosti obsažené v hlavičce odpovědi .|
|503    |Služba je dočasně nedostupná. Zkuste to zopakovat a dejte nám vědět, pokud chyba přetrvává.|

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Migrace do rozhraní Translator Text API v3](../migrate-to-v3.md)


