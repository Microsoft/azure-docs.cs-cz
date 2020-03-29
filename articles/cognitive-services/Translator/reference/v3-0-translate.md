---
title: Metoda překladače textového rozhraní API překladu
titleSuffix: Azure Cognitive Services
description: Seznamte se s parametry, záhlavími a základními zprávami pro metodu Překladač textu Překladače překladače překladače překladače překladače překladače azure cognitive services překladač ů.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: 1821623fbe2a22234af649934ac06e72897a19cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052395"
---
# <a name="translator-text-api-30-translate"></a>Překladač Text API 3.0: Přeložit

Překládá text.

## <a name="request-url"></a>Adresa URL požadavku

Odeslání `POST` žádosti na:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parametry požadavku

Parametry požadavku předané v řetězci dotazu jsou:

### <a name="required-parameters"></a>Požadované parametry

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>verze-api</td>
    <td><em>Povinný parametr</em>.<br/>Verze rozhraní API požadované klientem. Hodnota musí <code>3.0</code>být .</td>
  </tr>
  <tr>
    <td>na</td>
    <td><em>Povinný parametr</em>.<br/>Určuje jazyk výstupního textu. Cílový jazyk musí být jedním z <a href="./v3-0-languages.md">podporovaných jazyků</a> zahrnutých v <code>translation</code> oboru. Slouží <code>to=de</code> například k překladu do němčiny.<br/>Je možné překládat do více jazyků současně opakováním parametru v řetězci dotazu. Slouží například <code>to=de&to=it</code> k překladu do němčiny a italštiny.</td>
  </tr>
</table>

### <a name="optional-parameters"></a>Volitelné parametry

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>Z</td>
    <td><em>Volitelný parametr</em>.<br/>Určuje jazyk vstupního textu. Vyhledejte jazyky, ze kterých je možné <code>translation</code> je překládat, vyhledáním <a href="./v3-0-languages.md">podporovaných jazyků</a> pomocí oboru. Pokud <code>from</code> parametr není zadán, je k určení zdrojového jazyka použito automatické zjišťování jazyka. <br/><br/>Při použití <code>from</code> funkce <a href="https://docs.microsoft.com/azure/cognitive-services/translator/dynamic-dictionary">dynamického slovníku</a> je nutné použít parametr, nikoli automatickou detekci.</td>
  </tr>  
  <tr>
    <td>textTyp</td>
    <td><em>Volitelný parametr</em>.<br/>Definuje, zda je přeložený text ve formátu prostého textu nebo textu HTML. Každý HTML musí být dobře tvarovaný, kompletní prvek. Možné hodnoty <code>plain</code> jsou: (výchozí) nebo <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Volitelný parametr</em>.<br/>Řetězec určující kategorii (doménu) překladu. Tento parametr se používá k získání překladů z přizpůsobeného systému vytvořeného pomocí <a href="../customization.md">vlastního překladače</a>. Přidejte ID kategorie z vašeho vlastního překladače <a href="https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">podrobnosti projektu</a> do tohoto parametru použít nasazený přizpůsobený systém. Výchozí hodnota <code>general</code>je: .</td>
  </tr>
  <tr>
    <td>vulgární výrazyAkce</td>
    <td><em>Volitelný parametr</em>.<br/>Určuje, jak by mělo být vulgární výrazy v překladech zacházet. Možné hodnoty <code>NoAction</code> jsou: <code>Marked</code> (výchozí) nebo <code>Deleted</code>. Informace o způsobech zacházení s vulgárními výrazy naleznete v <a href="#handle-profanity">tématu Zpracování vulgárních výrazů</a>.</td>
  </tr>
  <tr>
    <td>vulgární chodMarker</td>
    <td><em>Volitelný parametr</em>.<br/>Určuje, jak mají být vulgární výrazy v překladech označeny. Možné hodnoty <code>Asterisk</code> jsou: (výchozí) nebo <code>Tag</code>. Informace o způsobech zacházení s vulgárními výrazy naleznete v <a href="#handle-profanity">tématu Zpracování vulgárních výrazů</a>.</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>Volitelný parametr</em>.<br/>Určuje, zda má být projekce zarovnání zahrnuta ze zdrojového textu na přeložený text. Možné hodnoty <code>true</code> jsou: nebo <code>false</code> (výchozí). </td>
  </tr>
  <tr>
    <td>includeDélka věty</td>
    <td><em>Volitelný parametr</em>.<br/>Určuje, zda mají být zahrnuty hranice věty pro vstupní text a přeložený text. Možné hodnoty <code>true</code> jsou: nebo <code>false</code> (výchozí).</td>
  </tr>
  <tr>
    <td>navrhlFrom</td>
    <td><em>Volitelný parametr</em>.<br/>Určuje záložní jazyk, pokud nelze identifikovat jazyk vstupního textu. Automatická detekce jazyka se <code>from</code> použije, když je parametr vynechán. Pokud se zjišťování <code>suggestedFrom</code> nezdaří, bude předpokládán jazyk.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>Volitelný parametr</em>.<br/>Určuje skript vstupního textu.</td>
  </tr>
  <tr>
    <td>doscriptu</td>
    <td><em>Volitelný parametr</em>.<br/>Určuje skript přeloženého textu.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>Volitelný parametr</em>.<br/>Určuje, že služba může záložní do obecného systému, pokud vlastní systém neexistuje. Možné hodnoty <code>true</code> jsou: (výchozí) nebo <code>false</code>.<br/><br/><code>allowFallback=false</code>určuje, že překlad by měl používat <code>category</code> pouze systémy vyškolené pro určené požadavkem. Pokud překlad jazyka X do jazyka Y vyžaduje řetězení přes otočný jazyk E, pak všechny systémy v řetězci (X->E a E->Y) bude muset být vlastní a mají stejnou kategorii. Pokud není nalezen žádný systém s konkrétní kategorií, požadavek vrátí stavový kód 400. <code>allowFallback=true</code>určuje, že služba může záložní do obecného systému, pokud vlastní systém neexistuje.
</td>
  </tr>
</table> 

Mezi hlavičky požadavku patří:

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
  <tr>
    <td>Autentizační hlavička (záhlaví)</td>
    <td><em>Hlavička požadavku .</em><br/>Viz <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostupné možnosti ověřování</a>.</td>
  </tr>
  <tr>
    <td>Typ obsahu</td>
    <td><em>Hlavička požadavku .</em><br/>Určuje typ obsahu datové části.<br/> Přijatá <code>application/json; charset=UTF-8</code>hodnota je .</td>
  </tr>
  <tr>
    <td>Délka obsahu</td>
    <td><em>Hlavička požadavku .</em><br/>Délka těla požadavku.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Nepovinné</em>.<br/>Identifikátor GUID generovaný klientem k jednoznačné identifikaci požadavku. Toto záhlaví můžete vynechat, pokud do řetězce dotazu zahrnete <code>ClientTraceId</code>ID trasování pomocí parametru dotazu s názvem .</td>
  </tr>
</table> 

## <a name="request-body"></a>Text požadavku

Tělo požadavku je pole JSON. Každý prvek pole je objekt JSON `Text`s vlastností string s názvem , která představuje řetězec přeložit.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Platí následující omezení:

* Pole může mít maximálně 100 prvků.
* Celý text obsažený v požadavku nesmí přesáhnout 5 000 znaků včetně mezer.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON s jedním výsledkem pro každý řetězec ve vstupním poli. Výsledný objekt obsahuje následující vlastnosti:

  * `detectedLanguage`: Objekt popisující zjištěný jazyk prostřednictvím následujících vlastností:

      * `language`: Řetězec představující kód zjištěného jazyka.

      * `score`: Hodnota plovoucího signálu označující důvěru ve výsledek. Skóre je mezi nulou a jedním a nízké skóre označuje nízkou důvěru.

    Vlastnost `detectedLanguage` je k dispozici pouze v objektu výsledek, pokud je požadováno automatické zjišťování jazyka.

  * `translations`: Pole výsledků překladu. Velikost pole odpovídá počtu cílových jazyků zadaných prostřednictvím parametru dotazu. `to` Každý prvek v poli obsahuje:

    * `to`: Řetězec představující kód jazyka cílového jazyka.

    * `text`: Řetězec s přeloženým textem.

    * `transliteration`: Objekt, který dává přeložený text `toScript` ve skriptu určeném parametrem.

      * `script`: Řetězec určující cílový skript.   

      * `text`: Řetězec s přeloženým textem v cílovém skriptu.

    Objekt `transliteration` není zahrnuta, pokud transliterace neprobíhá.

    * `alignment`: Objekt s vlastností `proj`jednoho řetězce s názvem , který mapuje vstupní text na přeložený text. Informace o zarovnání jsou k `includeAlignment` `true`dispozici pouze v případě, že je parametr požadavku . Zarovnání je vráceno jako řetězcová hodnota následujícího formátu: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  Dvojtečka odděluje počáteční a koncový index, pomlčka odděluje jazyky a prostor odděluje slova. Jedno slovo může být zarovnáno s nulou, jedním nebo více slovy v jiném jazyce a zarovnaná slova mohou být nesouvislá. Pokud nejsou k dispozici žádné informace o zarovnání, prvek zarovnání bude prázdný. Příklad a omezení najdete v tématu [Získání informací o zarovnání.](#obtain-alignment-information)

    * `sentLen`: Objekt vracející hranice věty ve vstupních a výstupních textech.

      * `srcSentLen`: Celé pole představující délky vět ve vstupním textu. Délka pole je počet vět a hodnoty jsou délka každé věty.

      * `transSentLen`: Celé pole představující délky vět v přeloženém textu. Délka pole je počet vět a hodnoty jsou délka každé věty.

    Hranice věty jsou zahrnuty `includeSentenceLength` `true`pouze v případě, že parametr požadavku je .

  * `sourceText`: Objekt s vlastností `text`jednoho řetězce s názvem , který poskytuje vstupní text ve výchozím skriptu zdrojového jazyka. `sourceText`vlastnost je k dispozici pouze v případě, že vstup je vyjádřen ve skriptu, který není obvyklý skript pro jazyk. Například pokud vstup byl arabština napsaný `sourceText.text` v latince, pak by se stejný arabský text převeden na arabský skript.

Příklad odpovědí JSON jsou uvedeny v části [příklady.](#examples)

## <a name="response-headers"></a>Hlavičky odpovědi

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
    <tr>
    <td>X-RequestId</td>
    <td>Hodnota generovaná službou k identifikaci požadavku. Používá se pro účely řešení potíží.</td>
  </tr>
  <tr>
    <td>X-MT-systém</td>
    <td>Určuje typ systému, který byl použit pro překlad pro každý jazyk do požadovaný pro překlad. Hodnota je seznam řetězců oddělený čárkou. Každý řetězec označuje typ:<br/><ul><li>Vlastní - Požadavek obsahuje vlastní systém a alespoň jeden vlastní systém byl použit během překladu.</li><li>Tým - Všechny ostatní požadavky</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Stavové kódy odpovědi

Následují možné stavové kódy HTTP, které požadavek vrátí. 

<table width="100%">
  <th width="20%">Stavový kód</th>
  <th>Popis</th>
  <tr>
    <td>200</td>
    <td>Úspěch</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Jeden z parametrů dotazu chybí nebo je neplatný. Před opakováním opravte parametry požadavku.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Požadavek nelze ověřit. Zkontrolujte, zda jsou zadána a platná pověření.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Požadavek není autorizován. Zkontrolujte chybovou zprávu podrobností. To často znamená, že všechny bezplatné překlady poskytované zkušební předplatné byly vypovězeny.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>Požadavek nelze splnit, protože chybí prostředek. Zkontrolujte chybovou zprávu podrobností. Při použití <code>category</code>vlastní , to často znamená, že vlastní překladsystém ještě není k dispozici pro poskytování požadavků. Žádost by měla být zopakována po uplynutí čekací doby (např. 1 minuta).</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Server požadavek zamítl, protože klient překročil limity požadavků.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Došlo k neočekávané chybě. Pokud chyba přetrvává, oznamte ji s: datem a <code>X-RequestId</code>časem selhání, <code>X-ClientTraceId</code>identifikátorem požadavku z hlavičky odpovědi a identifikátorem klienta z hlavičky požadavku .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Server je dočasně nedostupný. Opakujte požadavek. Pokud chyba přetrvává, oznamte ji s: datem a <code>X-RequestId</code>časem selhání, <code>X-ClientTraceId</code>identifikátorem požadavku z hlavičky odpovědi a identifikátorem klienta z hlavičky požadavku .</td>
  </tr>
</table> 

Pokud dojde k chybě, požadavek také vrátí odpověď na chybu JSON. Kód chyby je šestimístné číslo kombinující 3místný stavový kód HTTP následovaný třímístným číslem pro další kategorizaci chyby. Běžné kódy chyb naleznete na [referenční stránce v3 Translator Text API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Příklady

### <a name="translate-a-single-input"></a>Překlad jednoho vstupu

Tento příklad ukazuje, jak přeložit jednu větu z angličtiny do zjednodušené čínštiny.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Tělo odpovědi je:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

Pole `translations` obsahuje jeden prvek, který poskytuje překlad jednoho kusu textu ve vstupu.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Překlad jednoho vstupu pomocí automatické detekce jazyka

Tento příklad ukazuje, jak přeložit jednu větu z angličtiny do zjednodušené čínštiny. Požadavek neurčuje vstupní jazyk. Místo toho se používá automatická detekce zdrojového jazyka.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Tělo odpovědi je:

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
Odpověď je podobná odpovědi z předchozího příkladu. Vzhledem k tomu, že byla požadována automatická detekce jazyka, odpověď obsahuje také informace o jazyku zjištěném pro vstupní text. 

### <a name="translate-with-transliteration"></a>Přeložit s přepisem

Pojďme rozšířit předchozí příklad přidáním přepisu. Následující žádost žádá o čínský překlad napsaný v latince.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Tělo odpovědi je:

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

Výsledek překladu `transliteration` nyní obsahuje vlastnost, která poskytuje přeložený text pomocí latinky.

### <a name="translate-multiple-pieces-of-text"></a>Překlad více částí textu

Překlad více řetězců najednou je jednoduše záležitostí určení pole řetězců v těle požadavku.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

Tělo odpovědi je:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Překlad do více jazyků

Tento příklad ukazuje, jak přeložit stejný vstup do několika jazyků v jednom požadavku.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Tělo odpovědi je:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>Zpracování vulgárních výrazů

Za normálních okolností překladatel služby zachová vulgární výrazy, které je k dispozici ve zdroji v překladu. Stupeň vulgárních výrazů a kontext, který činí slova vulgární mise se liší mezi kulturami, a v důsledku toho stupeň vulgárních výrazů v cílovém jazyce může být zesílen nebo snížen.

Pokud se chcete vyhnout vulgárním výrazům v překladu, bez ohledu na přítomnost vulgárních výrazů ve zdrojovém textu, můžete použít možnost filtrování vulgárních výrazů. Tato možnost vám umožní zvolit, zda chcete vidět vulgární výrazy odstraněny, zda chcete označit vulgární výrazy s příslušnými značkami (dává vám možnost přidat vlastní post-zpracování), nebo chcete, aby žádná akce přijata. Přijaté hodnoty `ProfanityAction` jsou `Deleted` `Marked` , `NoAction` a (výchozí).

<table width="100%">
  <th width="20%">Vulgární akce</th>
  <th>Akce</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Toto je výchozí chování. Vulgární výrazy budou přecházet ze zdroje na cíl.<br/><br/>
    <strong>Příklad Zdroj (japonština)</strong>: 中中中中中中中中中中中中中<br/>
    <strong>Příklad Překlad (anglicky)</strong>: On je blbec.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Vulgární slova budou odstraněna z výstupu bez náhrady.<br/><br/>
    <strong>Příklad Zdroj (japonština)</strong>: 中中中中中中中中中中中中中<br/>
    <strong>Příklad překladu (anglicky)</strong>: Je.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Vulgární slova jsou nahrazena značkou ve výstupu. Značka závisí na <code>ProfanityMarker</code> parametru.<br/><br/>
Pro <code>ProfanityMarker=Asterisk</code>vulgární slova jsou nahrazena <code>***</code>:<br/>
    <strong>Příklad Zdroj (japonština)</strong>: 中中中中中中中中中中中中中<br/>\* \* \* 
    <strong>Příklad překladu (anglicky)</strong>: On je .<br/><br/>
Pro <code>ProfanityMarker=Tag</code>, vulgární slova jsou obklopeny &lt;XML tagy vulgárních výrazů&gt; a &lt;/ vulgární výrazy&gt;:<br/>
    <strong>Příklad Zdroj (japonština)</strong>: 中中中中中中中中中中中中中<br/>
    <strong>Příklad Překlad (anglicky)</strong> &lt;: On&gt;je vulgární&lt;blbec /&gt;vulgární výrazy .
  </tr>
</table> 

Například:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```
Příkaz vrací:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Porovnejte s:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```

Poslední požadavek vrátí:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Překlad obsahu pomocí značek a rozhodnutí o přeloženém

Je běžné překládat obsah, který obsahuje značky, jako je obsah ze stránky HTML nebo obsah z dokumentu XML. Při překladu obsahu pomocí značek zahrňte parametr `textType=html` dotazu. Kromě toho je někdy užitečné vyloučit určitý obsah z překladu. Atribut `class=notranslate` můžete použít k určení obsahu, který by měl zůstat v původním jazyce. V následujícím příkladu nebude obsah `div` uvnitř prvního prvku přeložen, zatímco `div` obsah v druhém prvku bude přeložen.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Zde je ukázkový požadavek pro ilustraci.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

Odpověď je:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Získat informace o zarovnání

Chcete-li získat `includeAlignment=true` informace o zarovnání, zadejte na řetězec dotazu.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation.'}]"
```

Odpověď je:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

Informace o zarovnání začínají písmenem `0:2-0:1`, což znamená, že první tři znaky ve zdrojovém textu (`The`) se mapují na první dva znaky v přeloženém textu (`La`).

#### <a name="limitations"></a>Omezení
Získání informací o zarovnání je experimentální funkce, kterou jsme povolili pro výzkum prototypů a zkušenosti s mapováním potenciálních frází. Můžeme se rozhodnout, že to v budoucnu přestaneme podporovat. Zde jsou některé z pozoruhodných omezení, kde nejsou podporovány zarovnání:

* Zarovnání není k dispozici pro text ve formátu HTML, tj.
* Zarovnání je vráceno pouze pro podmnožinu dvojic jazyků:
  - z angličtiny do jiného jazyka;
  - z jakéhokoli jiného jazyka do angličtiny s výjimkou zjednodušené čínštiny, tradiční čínštiny a lotyštiny do angličtiny;
  - z japonštiny na korejštinu nebo z korejštiny do japonštiny.
* Pokud je věta přeložena do konzerv, neobdržíte zarovnání. Příklad konzervovaného překladu je "Toto je test", "Miluji tě" a další vysokofrekvenční věty.
* Zarovnání není k dispozici, pokud použijete některý z přístupů, abyste zabránili [překladu,](../prevent-translation.md) jak je popsáno zde

### <a name="obtain-sentence-boundaries"></a>Získat hranice věty

Chcete-li získat informace o délce věty ve `includeSentenceLength=true` zdrojovém textu a přeloženém textu, zadejte na řetězci dotazu.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

Odpověď je:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>Překlad pomocí dynamického slovníku

Pokud již znáte překlad, který chcete použít u slova nebo fráze, můžete jej zadat jako značku v žádosti. Dynamický slovník je bezpečný pouze pro složená vlastní jména, jako jsou vlastní názvy a názvy produktů.

Značka, která má být zadat, používá následující syntaxi.

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Zvažte například větu z angličtiny "Slovo wordomatic je položka slovníku." Chcete-li zachovat slovo _wordomatic_ v překladu, pošlete žádost:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Výsledkem je:

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Tato funkce funguje stejným `textType=text` způsobem `textType=html`s nebo s . Funkce by měla být používána střídmě. Vhodný a mnohem lepší způsob přizpůsobení překladu je pomocí vlastního překladače. Vlastní Překladač plně využívá kontextové a statistické pravděpodobnosti. Pokud máte nebo si můžete dovolit vytvořit trénovací data, která zobrazují vaši práci nebo frázi v kontextu, získáte mnohem lepší výsledky. [Další informace o vlastním překladači](../customization.md).
