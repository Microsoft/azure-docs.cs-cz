---
title: Translator Text API v 2.0
titleSuffix: Azure Cognitive Services
description: Referenční dokumentace pro Translator Text API v 2.0
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: f111169558118a80602bcb2136bc63ce54c9e0d9
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242489"
---
# <a name="translator-text-api-v20"></a>Translator Text API v 2.0

> [!IMPORTANT]
> Tato verze Translator Text API je zastaralá. [Zobrazit dokumentaci k verzi 3 Translator text API](v3-0-reference.md).

Verze 2 Translator Text API se dá bez problémů integrovat do aplikací, webů, nástrojů nebo jiných řešení, aby poskytovala vícejazyčná uživatelská prostředí. Můžete ji použít na libovolné hardwarové platformě a s jakýmkoli operačním systémem k provádění překladu jazyka a dalších úloh souvisejících s jazyky, jako je rozpoznávání textu a převod textu na řeč v souladu s oborovým standardem. Další informace najdete v tématu [Translator text API](../translator-info-overview.md).

## <a name="getting-started"></a>Začínáme
Pro přístup k Translator Text API se musíte [zaregistrovat Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authentication"></a>Ověřování 
Všechna volání Translator Text API vyžadují pro ověřování klíč předplatného. Rozhraní API podporuje tři metody ověřování:

- Přístupový token Použijte klíč předplatného k vytvoření přístupového tokenu tím, že odešlete požadavek POST do ověřovací služby. Podrobnosti najdete v dokumentaci ke službě tokenů. K předání přístupového tokenu ke službě Translator použijte `Authorization` záhlaví nebo parametr dotazu `access_token`. Přístupový token je platný po dobu 10 minut. Získejte nový přístupový token každých 10 minut a používejte stejný přístupový token pro opakované žádosti za 10 minut.
- Klíč předplatného, který se používá přímo Předejte klíč předplatného jako hodnotu v hlavičce `Ocp-Apim-Subscription-Key`, která je součástí vaší žádosti do Translator Text API. Když použijete klíč předplatného přímo, nemusíte volat službu ověřování tokenů pro vytvoření přístupového tokenu.
- [Předplatné Azure Cognitive Services s více službami](https://azure.microsoft.com/pricing/details/cognitive-services/). Tato metoda umožňuje použití jednoho tajného klíče k ověřování žádostí pro více služeb.
Když použijete tajný klíč s více službami, musíte do své žádosti zahrnout dvě ověřovací hlavičky. První hlavička projde tajným klíčem. Druhá hlavička určuje oblast přidruženou k vašemu předplatnému:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

Pro předplatné rozhraní API pro více služeb text je vyžadována oblast. Oblast, kterou vyberete, je jediná oblast, kterou můžete použít pro překlad textu, když použijete klíč předplatného s více službami. Pokud jste se zaregistrovali k předplatnému více služeb na Azure Portal, musí být to stejná oblast, kterou jste vybrali.

Dostupné oblasti jsou `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`a `westus2`.

Klíč předplatného a přístupový token jsou tajné kódy, které by měly být skryté ze zobrazení.

## <a name="profanity-handling"></a>Zpracování vulgárních výrazů
Služba Translator obvykle uchová vulgární výrazy, které se nacházejí ve zdroji. Stupeň vulgárních výrazů a kontext, který dává slova vulgární textům, se liší podle jazykové verze. Proto se dá zvýšit nebo snížit stupeň vulgárních výrazů v cílovém jazyce.

Pokud chcete zabránit vulgárním slovům v překladu i v případě, že je ve zdrojovém textu, můžete použít možnost filtrování vulgárních výrazů pro metody, které ji podporují. Tato možnost umožňuje zvolit, zda chcete odstranit vulgární výrazy, které jsou označeny příslušnými značkami, nebo zda chcete v cíli povolit vulgární výrazy. Přijaté hodnoty `ProfanityAction` jsou `NoAction` (výchozí), `Marked`a `Deleted`.


|ProfanityAction    |Akce |Ukázkový zdroj (japonština)  |Příklad překladu (angličtina)  |
|:--|:--|:--|:--|
|Akce   |Default (Výchozí). Stejné jako nastavení možnosti. Vulgární výrazy přecházejí ze zdroje do cíle.        |彼はジャッカスです。     |Je Jackass.   |
|Vyznačen     |Slova v vulgárních výrazech budou obklopeny značkami XML \<vulgárních výrazů > a \</Profanity >.       |彼はジャッカスです。 |\<vulgárních výrazů > Jackass\</Profanity >.  |
|Odstranění    |Slova v vulgárních textech se z výstupu odeberou bez náhrady.     |彼はジャッカスです。 |Je.   |

    
## <a name="excluding-content-from-translation"></a>Vyloučení obsahu z překladu
Při překladu obsahu pomocí značek, jako je HTML (`contentType=text/html`), je někdy užitečné vyloučit konkrétní obsah z překladu. Atribut `class=notranslate` můžete použít k určení obsahu, který by měl zůstat v původním jazyce. V následujícím příkladu nebude přeložen obsah v prvním `div` elementu, ale obsah v druhém elementu `div` bude přeložen.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>ZÍSKAT/Translate

### <a name="implementation-notes"></a>Poznámky k implementaci
Přeloží textový řetězec z jednoho jazyka do druhého.

Identifikátor URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Návratová hodnota:** Řetězec, který představuje přeložený text.

Pokud jste dříve použili `AddTranslation` nebo `AddTranslationArray` k zadání překladu se hodnocením 5 nebo vyšším pro stejnou zdrojovou větu, `Translate` vrátí pouze nejvyšší možnost, která je k dispozici pro váš systém. Stejná zdrojová věta znamená naprosto stejnou hodnotu (100% shoda), s výjimkou velkých a malých písmen, hodnot značek a interpunkce na konci věty. Pokud není uloženo žádné hodnocení s hodnocením 5 nebo vyšší, vrácený výsledek bude automatický překlad pomocí Microsoft translatoru.

### <a name="response-class-status-200"></a>Response – třída (stav 200)

řetězec

Typ obsahu odpovědi: aplikace/XML

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis    |Typ parametru|datový typ|
|:--|:--|:--|:--|:--|
|appid  |obsahovat    |Povinná hodnota. Pokud se používá hlavička `Authorization` nebo `Ocp-Apim-Subscription-Key`, nechte pole `appid` prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|text|obsahovat   |Povinná hodnota. Řetězec, který představuje text, který se má přeložit Text nesmí obsahovat více než 10 000 znaků.|query|řetězec|
|from|obsahovat   |Volitelné. Řetězec, který představuje kód jazyka převáděného textu. Například en pro angličtinu.|query|řetězec|
|na|obsahovat |Povinná hodnota. Řetězec, který představuje kód jazyka, do kterého má být text přeložen.|query|řetězec|
|contentType|obsahovat    |Volitelné. Formát textu, který se má přeložit Podporované formáty jsou `text/plain` (výchozí) a `text/html`. Všechny prvky HTML musí být ve správném formátu, kompletní prvky.|query|řetězec|
|category|obsahovat   |Volitelné. Řetězec, který obsahuje kategorii (doménu) překladu. Výchozí formát je `general`.|query|řetězec|
|Autorizace|obsahovat  |Povinné, pokud je pole `appid` a záhlaví `Ocp-Apim-Subscription-Key` ponecháno prázdné. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|obsahovat  |Povinné, pokud je pole `appid` a záhlaví `Authorization` ponecháno prázdné.|záhlaví|řetězec|


### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Ověřte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatné přihlašovací údaje.|
|500    |Chyba serveru Pokud s tím budou dál problémy, dejte nám prosím jistotu. Poskytněte nám prosím přibližnou dobu & čas požadavku a ID žádosti zahrnuté v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba je dočasně nedostupná. Zkuste to prosím znovu a dejte nám prosím informovat, jestli chyba přetrvává.|

## <a name="post-translatearray"></a>PŘÍSPĚVEK/TranslateArray

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte překlady pro více zdrojových textů.

Identifikátor URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Tady je formát textu žádosti:

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


* `AppId`: povinné. Pokud se používá hlavička `Authorization` nebo `Ocp-Apim-Subscription-Key`, nechte pole `AppId` prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.
* `From`: volitelné. Řetězec, který představuje kód jazyka převáděného textu. Pokud je toto pole prázdné, bude odpověď zahrnovat výsledek automatického rozpoznání jazyka.
* `Options`: volitelné. Objekt `Options`, který obsahuje následující hodnoty. Jsou to všechna volitelná a výchozí nastavení pro nejběžnější nastavení. Zadané elementy musí být uvedeny v abecedním pořadí.
    - `Category`: řetězec, který obsahuje kategorii (doménu) překladu. Výchozí formát je `general`.
    - `ContentType`: formát převáděného textu. Podporované formáty jsou `text/plain` (výchozí), `text/xml`a `text/html`. Všechny prvky HTML musí být ve správném formátu, kompletní prvky.
    - `ProfanityAction`: Určuje způsob zpracování vulgárních výrazů, jak je vysvětleno výše. Přijaté hodnoty jsou `NoAction` (výchozí), `Marked`a `Deleted`.
    - `State`: stav uživatele, který pomůže korelovat požadavek a odpověď. V odpovědi se vrátí stejný obsah.
    - `Uri`: filtrovat výsledky podle tohoto identifikátoru URI. Výchozí: `all`.
    - `User`: filtrovat výsledky podle tohoto uživatele. Výchozí: `all`.
* `Texts`: povinné. Pole, které obsahuje text pro překlad. Všechny řetězce musí být ve stejném jazyce. Celkový text, který se má přeložit, nemůže být delší než 10 000 znaků. Maximální počet prvků pole je 2 000.
* `To`: povinné. Řetězec, který představuje kód jazyka, do kterého má být text přeložen.

Volitelné prvky můžete vynechat. Prvky, které jsou přímými podřízenými `TranslateArrayRequest`, musí být uvedeny v abecedním pořadí.

Metoda `TranslateArray` přijímá `application/xml` nebo `text/xml` pro `Content-Type`.

**Návratová hodnota:** Pole `TranslateArrayResponse`. Každý `TranslateArrayResponse` má tyto prvky:

* `Error`: označuje chybu, pokud k ní dojde. Jinak nastavte na hodnotu null.
* `OriginalSentenceLengths`: pole celých čísel, které určuje délku každé věty ve zdrojovém textu. Délka pole označuje počet vět.
* `TranslatedText`: přeložený text.
* `TranslatedSentenceLengths`: pole celých čísel, které určuje délku každé věty v přeloženém textu. Délka pole označuje počet vět.
* `State`: stav uživatele, který pomůže korelovat požadavek a odpověď. Vrátí stejný obsah jako požadavek.

Zde je formát textu odpovědi:

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

### <a name="response-class-status-200"></a>Response – třída (stav 200)
Úspěšná odpověď zahrnuje pole `TranslateArrayResponse` pole ve formátu popsaném výše.

řetězec

Typ obsahu odpovědi: aplikace/XML

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Data type|
|:--|:--|:--|:--|:--|
|Autorizace|obsahovat  |Povinné, pokud je pole `appid` a záhlaví `Ocp-Apim-Subscription-Key` ponecháno prázdné. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|obsahovat|Povinné, pokud je pole `appid` a záhlaví `Authorization` ponecháno prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP   |Důvod|
|:--|:--|
|400    |Chybný požadavek. Ověřte vstupní parametry a podrobnou chybovou odpověď. Běžné chyby patří: <ul><li>Element Array nemůže být prázdný.</li><li>Neplatná kategorie</li><li>Jazyk je neplatný.</li><li>Do jazyka je neplatné.</li><li>Požadavek obsahuje příliš mnoho prvků.</li><li>Jazyk z není podporován.</li><li>Jazyk pro není podporován.</li><li>Požadavek na překlad má příliš mnoho dat.</li><li>KÓD HTML nemá správný formát.</li><li>V požadavku na překlad bylo předáno příliš mnoho řetězců.</li></ul>|
|401    |Neplatné přihlašovací údaje.|
|500    |Chyba serveru Pokud s tím budou dál problémy, dejte nám prosím jistotu. Poskytněte nám prosím přibližnou dobu & čas požadavku a ID žádosti zahrnuté v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba je dočasně nedostupná. Zkuste to prosím znovu a dejte nám prosím informovat, jestli chyba přetrvává.|

## <a name="post-getlanguagenames"></a>PŘÍSPĚVEK/GetLanguageNames

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte popisné názvy jazyků předaných jako parametr `languageCodes`, lokalizovány do předaného `locale`ho jazyka.

Identifikátor URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

Tělo žádosti zahrnuje pole řetězců, které představuje kódy jazyka ISO 639-1, pro které se mají načíst popisné názvy. Tady je příklad:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Návratová hodnota:** Pole řetězců, které obsahuje názvy jazyků podporované službou Translator, lokalizované v požadovaném jazyce.

### <a name="response-class-status-200"></a>Response – třída (stav 200)
Pole řetězců obsahující názvy jazyků podporované službou Translator, které jsou lokalizovány do požadovaného jazyka.

řetězec

Typ obsahu odpovědi: aplikace/XML
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Data type|
|:--|:--|:--|:--|:--|
|appid|obsahovat|Povinná hodnota. Pokud se používá hlavička `Authorization` nebo `Ocp-Apim-Subscription-Key`, nechte pole `appid` prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|jazyka|obsahovat |Povinná hodnota. Řetězec, který představuje jednu z následujících možností, který slouží k lokalizaci názvů jazyků: <ul><li>Kombinace kódu kultury s malým písmenem (ISO 639 2), která je přidružená k jazyku a kódu podkultury s velkými písmeny ISO 3166 2. <li>Kód jazykové verze ISO 639, sám o sobě.|query|řetězec|
|Autorizace|obsahovat  |Povinné, pokud je pole `appid` a záhlaví `Ocp-Apim-Subscription-Key` ponecháno prázdné. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|obsahovat  |Povinné, pokud je pole `appid` a záhlaví `Authorization` ponecháno prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Ověřte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatné přihlašovací údaje.|
|500    |Chyba serveru Pokud s tím budou dál problémy, dejte nám prosím jistotu. Poskytněte nám prosím přibližnou dobu & čas požadavku a ID žádosti zahrnuté v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba je dočasně nedostupná. Zkuste to prosím znovu a dejte nám prosím informovat, jestli chyba přetrvává.|

## <a name="get-getlanguagesfortranslate"></a>ZÍSKAT/GetLanguagesForTranslate

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte seznam kódů jazyka, které reprezentují jazyky podporované překladovou službou.  `Translate` a `TranslateArray` lze překládat mezi libovolnými dvěma z těchto jazyků.

Identifikátor URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Návratová hodnota:** Pole řetězců obsahující kódy jazyka podporované službou Translator.

### <a name="response-class-status-200"></a>Response – třída (stav 200)
Pole řetězců obsahující kódy jazyka podporované službou Translator.

řetězec

Typ obsahu odpovědi: aplikace/XML
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Data type|
|:--|:--|:--|:--|:--|
|appid|obsahovat|Povinná hodnota. Pokud se používá hlavička `Authorization` nebo `Ocp-Apim-Subscription-Key`, nechte pole `appid` prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|Autorizace|obsahovat  |Povinné, pokud je pole `appid` a záhlaví `Ocp-Apim-Subscription-Key` ponecháno prázdné. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|obsahovat|Povinné, pokud je pole `appid` a záhlaví `Authorization` ponecháno prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Ověřte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatné přihlašovací údaje.|
|500    |Chyba serveru Pokud s tím budou dál problémy, dejte nám prosím jistotu. Poskytněte nám prosím přibližnou dobu & čas požadavku a ID žádosti zahrnuté v hlavičce odpovědi `X-MS-Trans-Info`.|
|503|Služba je dočasně nedostupná. Zkuste to prosím znovu a dejte nám prosím informovat, jestli chyba přetrvává.|

## <a name="get-getlanguagesforspeak"></a>ZÍSKAT/GetLanguagesForSpeak

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte jazyky dostupné pro syntézu řeči.

Identifikátor URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Návratová hodnota:** Pole řetězců obsahující jazykové kódy podporované pro syntézu řeči pomocí služby Translator.

### <a name="response-class-status-200"></a>Response – třída (stav 200)
Pole řetězců obsahující jazykové kódy podporované pro syntézu řeči pomocí služby Translator.

řetězec

Typ obsahu odpovědi: aplikace/XML

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Data type|
|:--|:--|:--|:--|:--|
|appid|obsahovat|Povinná hodnota. Pokud se používá hlavička `Authorization` nebo `Ocp-Apim-Subscription-Key`, nechte pole `appid` prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|Autorizace|obsahovat|Povinné, pokud je pole `appid` a záhlaví `Ocp-Apim-Subscription-Key` ponecháno prázdné. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|obsahovat|Povinné, pokud je pole `appid` a záhlaví `Authorization` ponecháno prázdné.|záhlaví|řetězec|
 
### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400|Chybný požadavek. Ověřte vstupní parametry a podrobnou chybovou odpověď.|
|401|Neplatné přihlašovací údaje.|
|500    |Chyba serveru Pokud s tím budou dál problémy, dejte nám prosím jistotu. Poskytněte nám prosím přibližnou dobu & čas požadavku a ID žádosti zahrnuté v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba je dočasně nedostupná. Zkuste to prosím znovu a dejte nám prosím informovat, jestli chyba přetrvává.|

## <a name="get-speak"></a>ZÍSKAT/Speak

### <a name="implementation-notes"></a>Poznámky k implementaci
Vrátí datový proud WAV nebo MP3 předávaného textu, který se používá v požadovaném jazyce.

Identifikátor URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Návratová hodnota:** Datový proud WAV nebo MP3 předávaného textu, který se používá v požadovaném jazyce.

### <a name="response-class-status-200"></a>Response – třída (stav 200)

binary

Typ obsahu odpovědi: aplikace/XML

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Data type|
|:--|:--|:--|:--|:--|
|appid|obsahovat|Povinná hodnota. Pokud se používá hlavička `Authorization` nebo `Ocp-Apim-Subscription-Key`, nechte pole `appid` prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|text|obsahovat   |Povinná hodnota. Řetězec obsahující jednu nebo více vět, které mají být v zadaném jazyce mluvené pro tento datový proud. Text nesmí být delší než 2 000 znaků.|query|řetězec|
|jazyk|obsahovat   |Povinná hodnota. Řetězec, který představuje kód podporovaného jazyka jazyka, ve kterém se má text mluvit. Kód musí být jeden z kódů vrácených metodou `GetLanguagesForSpeak`.|query|řetězec|
|format|obsahovat|Volitelné. Řetězec, který určuje ID typu obsahu. V současné době jsou k dispozici `audio/wav` a `audio/mp3`. Výchozí hodnota je `audio/wav`.|query|řetězec|
|options|obsahovat    |Volitelné. Řetězec, který určuje vlastnosti syntetizového řeči:<ul><li>`MaxQuality` a `MinSize` určují kvalitu zvukového signálu. `MaxQuality` poskytuje nejvyšší kvalitu. `MinSize` poskytuje nejmenší velikost souboru. Výchozí hodnota je `MinSize`.</li><li>`female` a `male` určují požadované pohlaví hlasu. Výchozí formát je `female`. Použijte svislou čáru (<code>\|</code>), chcete-li zahrnout více možností. Například `MaxQuality|Male`.</li></li></ul>  |query|řetězec|
|Autorizace|obsahovat|Povinné, pokud je pole `appid` a záhlaví `Ocp-Apim-Subscription-Key` ponecháno prázdné. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|obsahovat  |Povinné, pokud je pole `appid` a záhlaví `Authorization` ponecháno prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Ověřte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatné přihlašovací údaje.|
|500    |Chyba serveru Pokud s tím budou dál problémy, dejte nám prosím jistotu. Poskytněte nám prosím přibližnou dobu & čas požadavku a ID žádosti zahrnuté v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba je dočasně nedostupná. Zkuste to prosím znovu a dejte nám prosím informovat, jestli chyba přetrvává.|

## <a name="get-detect"></a>ZÍSKAT/Detect

### <a name="implementation-notes"></a>Poznámky k implementaci
Určuje jazyk oddílu textu.

Identifikátor URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Návratová hodnota:** Řetězec, který obsahuje kód jazyka se dvěma znaky pro text.

### <a name="response-class-status-200"></a>Response – třída (stav 200)

řetězec

Typ obsahu odpovědi: aplikace/XML

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Data type|
|:--|:--|:--|:--|:--|
|appid|obsahovat  |Povinná hodnota. Pokud se používá hlavička `Authorization` nebo `Ocp-Apim-Subscription-Key`, nechte pole `appid` prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|text|obsahovat|Povinná hodnota. Řetězec obsahující text, jehož jazyk má být identifikován. Text nesmí být delší než 10 000 znaků.|query|  řetězec|
|Autorizace|obsahovat|Povinné, pokud je pole `appid` a záhlaví `Ocp-Apim-Subscription-Key` ponecháno prázdné. Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key  |obsahovat    |Povinné, pokud je pole `appid` a záhlaví `Authorization` ponecháno prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400|Chybný požadavek. Ověřte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatné přihlašovací údaje.|
|500    |Chyba serveru Pokud s tím budou dál problémy, dejte nám prosím jistotu. Poskytněte nám prosím přibližnou dobu & čas požadavku a ID žádosti zahrnuté v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba je dočasně nedostupná. Zkuste to prosím znovu a dejte nám prosím informovat, jestli chyba přetrvává.|


## <a name="post-detectarray"></a>PŘÍSPĚVEK/DetectArray

### <a name="implementation-notes"></a>Poznámky k implementaci

Identifikuje jazyky v poli řetězců. Nezávisle detekuje jazyk každého jednotlivého prvku pole a vrátí výsledek pro každý řádek pole.

Identifikátor URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Tady je formát textu žádosti:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Text nemůže být delší než 10 000 znaků.

**Návratová hodnota:** Pole řetězců, které obsahuje kód jazyka se dvěma znaky pro každý řádek ve vstupním poli.

Zde je formát textu odpovědi:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Response – třída (stav 200)
`DetectArray` bylo úspěšné. Vrátí pole řetězců, které obsahuje kód jazyka se dvěma znaky pro každý řádek vstupního pole.

řetězec

Typ obsahu odpovědi: aplikace/XML
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Data type|
|:--|:--|:--|:--|:--|
|appid|obsahovat|Povinná hodnota. Pokud se používá hlavička `Authorization` nebo `Ocp-Apim-Subscription-Key`, nechte pole `appid` prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|Autorizace|obsahovat|Povinné, pokud je pole `appid` a záhlaví `Ocp-Apim-Subscription-Key` ponecháno prázdné.  Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|obsahovat|Povinné, pokud je pole `appid` a záhlaví `Authorization` ponecháno prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Ověřte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatné přihlašovací údaje.|
|500    |Chyba serveru Pokud s tím budou dál problémy, dejte nám prosím jistotu. Poskytněte nám prosím přibližnou dobu & čas požadavku a ID žádosti zahrnuté v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba je dočasně nedostupná. Zkuste to prosím znovu a dejte nám prosím informovat, jestli chyba přetrvává.|

## <a name="get-addtranslation"></a>ZÍSKAT/AddTranslation

### <a name="implementation-notes"></a>Poznámky k implementaci

> [!IMPORTANT]
> **Zastaralá Poznámka:** Po 31. ledna 2018 Tato metoda nepřijímá nové odeslání vět. Zobrazí se chybová zpráva. Podívejte se prosím na oznámení o změnách v rámci rozhraní CTF (pro spolupráci s překlady).

Přidá překlad do paměti pro překlad.

Identifikátor URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Response – třída (stav 200)

řetězec

Typ obsahu odpovědi: aplikace: XML
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Data type   |
|:--|:--|:--|:--|:--|
|appid|obsahovat|Povinná hodnota. Pokud se používá hlavička `Authorization` nebo `Ocp-Apim-Subscription-Key`, nechte pole `appid` prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|originalText|obsahovat|Povinná hodnota. Řetězec obsahující text, který se má přeložit Maximální délka řetězce je 1 000 znaků.|query|řetězec|
|translatedText|obsahovat |Povinná hodnota. Řetězec, který obsahuje přeložený text do cílového jazyka. Maximální délka řetězce je 2 000 znaků.|query|řetězec|
|from|obsahovat   |Povinná hodnota. Řetězec, který představuje kód jazyka původního jazyka textu. Například en pro angličtinu a de pro němčinu.|query|řetězec|
|na|obsahovat|Povinná hodnota. Řetězec, který představuje kód jazyka, do kterého má být text přeložen.|query|řetězec|
|rating|obsahovat |Volitelné. Celé číslo, které představuje hodnocení kvality pro řetězec. Hodnota je mezi-10 a 10. Výchozí hodnota je 1.|query|celé číslo|
|contentType|obsahovat    |Volitelné. Formát textu, který se má přeložit Podporované formáty jsou `text/plain` a `text/html`. Všechny prvky HTML musí být ve správném formátu, kompletní prvky.    |query|řetězec|
|category|obsahovat|Volitelné. Řetězec, který obsahuje kategorii (doménu) překladu. Výchozí formát je `general`.|query|řetězec|
|uživatel|obsahovat|Povinná hodnota. Řetězec, který se používá ke sledování původce odeslání.|query|řetězec|
|uri|obsahovat|Volitelné. Řetězec, který obsahuje umístění obsahu překladu.|query|řetězec|
|Autorizace|obsahovat|Povinné, pokud je pole `appid` a záhlaví `Ocp-Apim-Subscription-Key` ponecháno prázdné.  Autorizační token: `"Bearer" + " " + "access_token"`.  |záhlaví|řetězec|
|OCP-Apim-Subscription-Key|obsahovat|Povinné, pokud je pole `appid` a záhlaví `Authorization` ponecháno prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Ověřte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatné přihlašovací údaje.|
|410|`AddTranslation` už není podporovaný.|
|500    |Chyba serveru Pokud s tím budou dál problémy, dejte nám prosím jistotu. Poskytněte nám prosím přibližnou dobu & čas požadavku a ID žádosti zahrnuté v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba je dočasně nedostupná. Zkuste to prosím znovu a dejte nám prosím informovat, jestli chyba přetrvává.|

## <a name="post-addtranslationarray"></a>PŘÍSPĚVEK/AddTranslationArray

### <a name="implementation-notes"></a>Poznámky k implementaci

> [!IMPORTANT]
> **Zastaralá Poznámka:** Po 31. ledna 2018 Tato metoda nepřijímá nové odeslání vět. Zobrazí se chybová zpráva. Podívejte se prosím na oznámení o změnách v rámci rozhraní CTF (pro spolupráci s překlady).

Přidá pole překladů do paměti pro překlad. Tato metoda je `AddTranslation`á verze pole.

Identifikátor URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Tady je formát textu žádosti:

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

* `AppId`: povinné. Pokud se používá hlavička `Authorization` nebo `Ocp-Apim-Subscription-Key`, nechte pole `AppId` prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.
* `From`: povinné. Řetězec, který obsahuje kód jazyka zdrojového jazyka. Musí být jedním z jazyků vrácených metodou `GetLanguagesForTranslate`.
* `To`: povinné. Řetězec, který obsahuje kód jazyka cílového jazyka. Musí být jedním z jazyků vrácených metodou `GetLanguagesForTranslate`.
* `Translations`: povinné. Pole překladů, které chcete přidat do paměti pro překlad. Každý překlad musí obsahovat `OriginalText`, `TranslatedText`a `Rating`. Maximální velikost každého `OriginalText` a `TranslatedText` je 1 000 znaků. Celkový počet všech prvků `OriginalText` a `TranslatedText` nesmí překročit 10 000 znaků. Maximální počet prvků pole je 100.
* `Options`: povinné. Sada možností, včetně `Category`, `ContentType`, `Uri`a `User`. `User` se vyžaduje. `Category`, `ContentType`a `Uri` jsou volitelné. Zadané elementy musí být uvedeny v abecedním pořadí.

### <a name="response-class-status-200"></a>Response – třída (stav 200)
Metoda `AddTranslationArray` byla úspěšná. 

Po 31. ledna 2018 se nepřijmou odeslání vět. Služba bude reagovat s kódem chyby 410.

řetězec

Typ obsahu odpovědi: aplikace/XML
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Data type|
|:--|:--|:--|:--|:--|
|Autorizace|obsahovat|Povinné, pokud je pole `appid` a záhlaví `Ocp-Apim-Subscription-Key` ponecháno prázdné.  Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|obsahovat|Povinné, pokud je pole `appid` a záhlaví `Authorization` ponecháno prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Ověřte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatné přihlašovací údaje.|
|410    |`AddTranslation` už není podporovaný.|
|500    |Chyba serveru Pokud s tím budou dál problémy, dejte nám prosím jistotu. Poskytněte nám prosím přibližnou dobu & čas požadavku a ID žádosti zahrnuté v hlavičce odpovědi `X-MS-Trans-Info`.|
|503|Služba je dočasně nedostupná. Zkuste to prosím znovu a dejte nám prosím informovat, jestli chyba přetrvává.|

## <a name="get-breaksentences"></a>ZÍSKAT/BreakSentences

### <a name="implementation-notes"></a>Poznámky k implementaci
Rozdělí oddíl textu na věty a vrátí pole, které obsahuje délky každé věty.

Identifikátor URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Návratová hodnota:** Pole celých čísel, která představují délky vět. Délka pole představuje počet vět. Hodnoty udávají délku každé věty.

### <a name="response-class-status-200"></a>Response – třída (stav 200)
Pole celých čísel, která představují délky vět. Délka pole představuje počet vět. Hodnoty udávají délku každé věty.

celé číslo

Typ obsahu odpovědi: aplikace/XML

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Data type|
|:--|:--|:--|:--|:--|
|appid|obsahovat  |Povinná hodnota. Pokud se používá hlavička `Authorization` nebo `Ocp-Apim-Subscription-Key`, nechte pole `appid` prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query| řetězec|
|text|obsahovat   |Povinná hodnota. Řetězec, který představuje text, který se má rozdělit na věty Maximální velikost textu je 10 000 znaků.|query|řetězec|
|jazyk   |obsahovat    |Povinná hodnota. Řetězec, který představuje kód jazyka vstupního textu.|query|řetězec|
|Autorizace|obsahovat|Povinné, pokud je pole `appid` a záhlaví `Ocp-Apim-Subscription-Key` ponecháno prázdné. Autorizační token: `"Bearer" + " " + "access_token"`.   |záhlaví|řetězec|
|OCP-Apim-Subscription-Key|obsahovat|Povinné, pokud je pole `appid` a záhlaví `Authorization` ponecháno prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400|Chybný požadavek. Ověřte vstupní parametry a podrobnou chybovou odpověď.|
|401|Neplatné přihlašovací údaje.|
|500|Chyba serveru Pokud s tím budou dál problémy, dejte nám prosím jistotu. Poskytněte nám prosím přibližnou dobu & čas požadavku a ID žádosti zahrnuté v hlavičce odpovědi `X-MS-Trans-Info`.|
|503|Služba je dočasně nedostupná. Zkuste to prosím znovu a dejte nám prosím informovat, jestli chyba přetrvává.|

## <a name="post-gettranslations"></a>PŘÍSPĚVEK/GetTranslations

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte pole překladů pro danou dvojici jazyků z úložiště a jádra MT. `GetTranslations` se liší od `Translate` v tom, že vrátí všechny dostupné překlady.

Identifikátor URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

Tělo žádosti zahrnuje volitelný objekt `TranslationOptions`, který má tento formát:

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

Objekt `TranslateOptions` obsahuje hodnoty v následujícím seznamu. Jsou to všechna volitelná a výchozí nastavení pro nejběžnější nastavení. Zadané elementy musí být uvedeny v abecedním pořadí.

* `Category`: řetězec, který obsahuje kategorii (doménu) překladu. Výchozí formát je `general`.
* `ContentType`: jedinou podporovanou možností a výchozí hodnota je `text/plain`.
* `IncludeMultipleMTAlternatives`: příznak Boolean pro určení, zda má být vráceno více než jedna alternativa z jádra MT. Platné hodnoty jsou `true` a `false` (rozlišuje velká a malá písmena). Výchozí hodnota je `false`, která vrací jenom jednu alternativu. Nastavení příznaku na `true` umožňuje vytvoření umělých alternativ, plně integrovaných s rozhraním překladu spolupráce (CTF). Funkce umožňuje vracet alternativy pro věty, které nemají v CTF žádné překlady, přidáním umělých alternativ z *n*-nejlepších seznamů dekodéru.
    - Hodnotící. Tato hodnocení se používají takto: 
         - Nejlepší automatický překlad má hodnocení 5.
       - Alternativy z CTF odráží oprávnění kontrolora. Jsou v rozsahu od-10 do + 10.
       - Automaticky vygenerované alternativypřekladu mají hodnocení 0 a stupeň shody 100.
    - Počet alternativ Počet vrácených alternativ může být stejně vysoký jako hodnota zadaná v poli `maxTranslations`, ale může být nižší.
    - Páry jazyků. Tato funkce není v obou směrech k dispozici pro překlady mezi zjednodušenou čínskou a tradiční čínštinou. Je k dispozici pro všechny ostatní páry jazyků podporované Microsoft Translatorem.
* `State`: stav uživatele, který pomůže korelovat požadavek a odpověď. V odpovědi se vrátí stejný obsah.
* `Uri`: filtrovat výsledky podle tohoto identifikátoru URI. Pokud není nastavená žádná hodnota, výchozí hodnota je `all`.
* `User`: filtrovat výsledky podle tohoto uživatele. Pokud není nastavená žádná hodnota, výchozí hodnota je `all`.

Požadavek `Content-Type` by měl být `text/xml`.

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

Tato odpověď zahrnuje `GetTranslationsResponse` element, který obsahuje následující hodnoty:

* `Translations`: pole nalezených shod je uloženo v `TranslationMatch`ch objektech (popsaných v následující části). Překlady mohou zahrnovat drobné varianty původního textu (přibližná shoda). Překlady budou seřazeny: 100%, první odpovídá, Přibližná shoda a další.
* `From`: Pokud metoda neurčuje `From` jazyk, bude tato hodnota vycházet z automatického rozpoznávání jazyka. V opačném případě bude zadaný `From` jazyk.
* `State`: stav uživatele, který pomůže korelovat požadavek a odpověď. Obsahuje hodnotu zadanou v parametru `TranslateOptions`.

Objekt `TranslationMatch` se skládá z těchto hodnot:

* `Error`: kód chyby, pokud dojde k chybě pro konkrétní vstupní řetězec. V opačném případě je toto pole prázdné.
* `MatchDegree`: Určuje, jak blízkou vstupní text odpovídá původnímu textu nalezenému v úložišti. Systém se shoduje se vstupními větami proti obchodu, včetně nepřesných shod. Hodnota vrátila rozsah od 0 do 100, kde 0 není podobná a 100 je přesně rozlišovat velikost písmen.
* `MatchedOriginalText`: původní text, který byl pro tento výsledek spárován. Tato hodnota se vrátí jenom v případě, že se odpovídající původní text liší od vstupního textu. Slouží k vrácení zdrojového textu přibližné shody. Tato hodnota se pro výsledky Microsoft translatoru nevrátí.
* `Rating`: označuje autoritu osoby, která provádí rozhodnutí o kvalitě. Výsledky strojového překladu mají hodnocení 5. Anonymní poskytnuté překlady mají zpravidla hodnocení od 1 do 4. Směrodatně poskytnuté překlady budou obvykle hodnocení od 6 do 10.
* `Count`: kolikrát byl tento překlad s tímto hodnocením vybrán. Hodnota je 0 pro automaticky přeloženou odpověď.
* `TranslatedText`: přeložený text.

### <a name="response-class-status-200"></a>Response – třída (stav 200)
Objekt `GetTranslationsResponse` ve formátu popsaném dříve.

řetězec

Typ obsahu odpovědi: aplikace/XML
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Data type|
|:--|:--|:--|:--|:--|
|appid|obsahovat|Povinná hodnota. Pokud se používá hlavička `Authorization` nebo `Ocp-Apim-Subscription-Key`, nechte pole `appid` prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.|query|řetězec|
|text|obsahovat|Povinná hodnota. Řetězec, který představuje text, který se má přeložit Maximální velikost textu je 10 000 znaků.|query|řetězec|
|from|obsahovat|Povinná hodnota. Řetězec, který představuje kód jazyka převáděného textu.|query|řetězec|
|na |obsahovat    |Povinná hodnota. Řetězec, který představuje kód jazyka, do kterého má být text přeložen.|query|řetězec|
|maxTranslations|obsahovat|Povinná hodnota. Celé číslo, které představuje maximální počet vrácených překladů.|query|celé číslo|
|Autorizace| obsahovat|Povinné, pokud je pole `appid` a záhlaví `Ocp-Apim-Subscription-Key` ponecháno prázdné. Autorizační token: `"Bearer" + " " + "access_token"`.|řetězec|  záhlaví|
|OCP-Apim-Subscription-Key|obsahovat  |Povinné, pokud je pole `appid` a záhlaví `Authorization` ponecháno prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Ověřte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatné přihlašovací údaje.|
|500    |Chyba serveru Pokud s tím budou dál problémy, dejte nám prosím jistotu. Poskytněte nám prosím přibližnou dobu & čas požadavku a ID žádosti zahrnuté v hlavičce odpovědi `X-MS-Trans-Info`.|
|503|Služba je dočasně nedostupná. Zkuste to prosím znovu a dejte nám prosím informovat, jestli chyba přetrvává.|

## <a name="post-gettranslationsarray"></a>PŘÍSPĚVEK/GetTranslationsArray

### <a name="implementation-notes"></a>Poznámky k implementaci
Načte více kandidátů na překlad pro několik zdrojových textů.

Identifikátor URI žádosti je `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Tady je formát textu žádosti:

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

* `AppId`: povinné. Pokud je použita hlavička `Authorization`, nechte pole `AppId` prázdné. V opačném případě zahrňte řetězec, který obsahuje `"Bearer" + " " + "access_token"`.
* `From`: povinné. Řetězec, který představuje kód jazyka převáděného textu.
* `MaxTranslations`: povinné. Celé číslo, které představuje maximální počet vrácených překladů.
* `Options`: volitelné. Objekt `Options`, který obsahuje následující hodnoty. Jsou to všechna volitelná a výchozí nastavení pro nejběžnější nastavení. Zadané elementy musí být uvedeny v abecedním pořadí.
    - `Category`: řetězec, který obsahuje kategorii (doménu) překladu. Výchozí formát je `general`.
    - `ContentType`: jedinou podporovanou možností a výchozí hodnota je `text/plain`.
    - `IncludeMultipleMTAlternatives`: příznak Boolean pro určení, zda má být vráceno více než jedna alternativa z jádra MT. Platné hodnoty jsou `true` a `false` (rozlišuje velká a malá písmena). Výchozí hodnota je `false`, která vrací jenom jednu alternativu. Nastavení příznaku na `true` umožňuje generaci umělých alternativ v překladu, plně integrovaných s rozhraním pro spolupráci s překlady (CTF). Funkce umožňuje vracet alternativy pro věty, které nemají žádné alternativy v CTF, a to přidáním umělých alternativ ze seznamu *n*-nejlepších dekodéru.
        - Hodnocení hodnocení se používají takto:
          - Nejlepší automatický překlad má hodnocení 5.
          - Alternativy z CTF odráží oprávnění kontrolora. Jsou v rozsahu od-10 do + 10.
          - Automaticky vygenerované alternativypřekladu mají hodnocení 0 a stupeň shody 100.
        - Počet alternativ Počet vrácených alternativ může být stejně vysoký jako hodnota zadaná v poli `maxTranslations`, ale může být nižší.
        - Páry jazyků. Tato funkce není v obou směrech k dispozici pro překlady mezi zjednodušenou čínskou a tradiční čínštinou. Je k dispozici pro všechny ostatní páry jazyků podporované Microsoft Translatorem.
* `State`: stav uživatele, který pomůže korelovat požadavek a odpověď. V odpovědi se vrátí stejný obsah.
* `Uri`: filtrovat výsledky podle tohoto identifikátoru URI. Pokud není nastavená žádná hodnota, výchozí hodnota je `all`.
* `User`: filtrovat výsledky podle tohoto uživatele. Pokud není nastavená žádná hodnota, výchozí hodnota je `all`.
* `Texts`: povinné. Pole, které obsahuje text pro překlad. Všechny řetězce musí být ve stejném jazyce. Celkový text, který se má přeložit, nemůže být delší než 10 000 znaků. Maximální počet prvků pole je 10.
* `To`: povinné. Řetězec, který představuje kód jazyka, do kterého má být text přeložen.

Volitelné prvky můžete vynechat. Prvky, které jsou přímými podřízenými `GetTranslationsArrayRequest`, musí být uvedeny v abecedním pořadí.

Požadavek `Content-Type` by měl být `text/xml`.

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

Každý prvek `GetTranslationsResponse` obsahuje tyto hodnoty:

* `Translations`: pole nalezených shod je uloženo v `TranslationMatch`ch objektech (popsaných v následující části). Překlady mohou zahrnovat drobné varianty původního textu (přibližná shoda). Překlady budou seřazeny: 100%, první odpovídá, Přibližná shoda a další.
* `From`: Pokud metoda neurčuje `From` jazyk, bude tato hodnota vycházet z automatického rozpoznávání jazyka. V opačném případě bude zadaný `From` jazyk.
* `State`: stav uživatele, který pomůže korelovat požadavek a odpověď. Obsahuje hodnotu zadanou v parametru `TranslateOptions`.

Objekt `TranslationMatch` obsahuje následující hodnoty:
* `Error`: kód chyby, pokud dojde k chybě pro konkrétní vstupní řetězec. V opačném případě je toto pole prázdné.
* `MatchDegree`: Určuje, jak blízkou vstupní text odpovídá původnímu textu nalezenému v úložišti. Systém se shoduje se vstupními větami proti obchodu, včetně nepřesných shod. Hodnota vrátila rozsah od 0 do 100, kde 0 není podobná a 100 je přesně rozlišovat velikost písmen.
* `MatchedOriginalText`: původní text, který byl pro tento výsledek spárován. Tato hodnota se vrátí jenom v případě, že se odpovídající původní text liší od vstupního textu. Slouží k vrácení zdrojového textu přibližné shody. Tato hodnota se pro výsledky Microsoft translatoru nevrátí.
* `Rating`: označuje autoritu osoby, která provádí rozhodnutí o kvalitě. Výsledky strojového překladu mají hodnocení 5. Anonymní poskytnuté překlady mají zpravidla hodnocení od 1 do 4. Autoritativní poskytnuté překlady mají zpravidla hodnocení od 6 do 10.
* `Count`: kolikrát byl tento překlad s tímto hodnocením vybrán. Hodnota je 0 pro automaticky přeloženou odpověď.
* `TranslatedText`: přeložený text.


### <a name="response-class-status-200"></a>Response – třída (stav 200)

řetězec

Typ obsahu odpovědi: aplikace/XML
 
### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Data type|
|:--|:--|:--|:--|:--|
|Autorizace  |obsahovat    |Povinné, pokud je pole `appid` a záhlaví `Ocp-Apim-Subscription-Key` ponecháno prázdné.  Autorizační token: `"Bearer" + " " + "access_token"`.|záhlaví|řetězec|
|OCP-Apim-Subscription-Key|obsahovat  |Povinné, pokud je pole `appid` a záhlaví `Authorization` ponecháno prázdné.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Důvod|
|:--|:--|
|400    |Chybný požadavek. Ověřte vstupní parametry a podrobnou chybovou odpověď.|
|401    |Neplatné přihlašovací údaje.|
|500    |Chyba serveru Pokud s tím budou dál problémy, dejte nám prosím jistotu. Poskytněte nám prosím přibližnou dobu & čas požadavku a ID žádosti zahrnuté v hlavičce odpovědi `X-MS-Trans-Info`.|
|503    |Služba je dočasně nedostupná. Zkuste to prosím znovu a dejte nám prosím informovat, jestli chyba přetrvává.|

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Migrace na Translator Text API V3](../migrate-to-v3.md)


