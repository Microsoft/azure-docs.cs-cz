---
title: Metoda Translator Text APIho překladu
titleSuffix: Azure Cognitive Services
description: Použijte metodu Translator Text API přeložit.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 10/16/2019
ms.author: swmachan
ms.openlocfilehash: b809171549a8f3cbbbb6ccad1553608598afa345
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161714"
---
# <a name="translator-text-api-30-translate"></a>Translator Text API 3,0: přeložit

Přeloží text.

## <a name="request-url"></a>Adresa URL požadavku

Odeslat požadavek `POST` do:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Parametry žádosti předané řetězci dotazu jsou:

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>verze API-Version</td>
    <td><em>Povinný parametr</em>.<br/>Verze rozhraní API, kterou klient požaduje. Hodnota musí být <code>3.0</code>.</td>
  </tr>
  <tr>
    <td>Výsledkem</td>
    <td><em>Volitelný parametr</em>.<br/>Určuje jazyk vstupního textu. Vyhledáním <a href="./v3-0-languages.md">podporovaných jazyků</a> pomocí oboru <code>translation</code> zjistíte, které jazyky jsou k dispozici pro překlad. Pokud není zadán parametr <code>from</code>, je pro určení zdrojového jazyka použito automatické rozpoznávání jazyka. <br/><br/>Při použití funkce <a href="https://docs.microsoft.com/azure/cognitive-services/translator/dynamic-dictionary">dynamického slovníku</a> je nutné použít parametr <code>from</code> místo automatického zjišťování.</td>
  </tr>
  <tr>
    <td>na</td>
    <td><em>Povinný parametr</em>.<br/>Určuje jazyk výstupního textu. Cílový jazyk musí být jeden z <a href="./v3-0-languages.md">podporovaných jazyků</a> obsažených v oboru <code>translation</code>. Použijte například <code>to=de</code> pro převod na němčinu.<br/>Je možné překládat na více jazyků současně opakováním parametru v řetězci dotazu. Použijte například <code>to=de&to=it</code> pro překlad do němčiny a italštiny.</td>
  </tr>
  <tr>
    <td>textType</td>
    <td><em>Volitelný parametr</em>.<br/>Definuje, zda je převáděný text prostý text nebo text HTML. Libovolný kód HTML musí být dobře vytvořený, kompletní element. Možné hodnoty jsou: <code>plain</code> (výchozí) nebo <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Volitelný parametr</em>.<br/>Řetězec určující kategorii (doménu) překladu. Tento parametr slouží k získání překladu z přizpůsobeného systému vytvořeného pomocí <a href="../customization.md">vlastního překladatele</a>. Přidejte ID kategorie z <a href="https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">podrobností projektu</a> vlastního překladatele do tohoto parametru pro použití nasazeného přizpůsobeného systému. Výchozí hodnota je: <code>general</code>.</td>
  </tr>
  <tr>
    <td>ProfanityAction</td>
    <td><em>Volitelný parametr</em>.<br/>Určuje, jak by měly být v překladech ošetřeny vulgární výrazy. Možné hodnoty jsou: <code>NoAction</code> (výchozí), <code>Marked</code> nebo <code>Deleted</code>. Pro pochopení způsobů, jak považovat vulgární výrazy, se podívejte na téma <a href="#handle-profanity">zpracování vulgárních výrazů</a>.</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td><em>Volitelný parametr</em>.<br/>Určuje, jak by měly být v překlady označeny vulgární výrazy. Možné hodnoty jsou: <code>Asterisk</code> (výchozí) nebo <code>Tag</code>. Pro pochopení způsobů, jak považovat vulgární výrazy, se podívejte na téma <a href="#handle-profanity">zpracování vulgárních výrazů</a>.</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>Volitelný parametr</em>.<br/>Určuje, zda se má zahrnout projekce zarovnání ze zdrojového textu do přeloženého textu. Možné hodnoty jsou: <code>true</code> nebo <code>false</code> (výchozí). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>Volitelný parametr</em>.<br/>Určuje, zda se mají zahrnout hranice věty pro vstupní text a přeložený text. Možné hodnoty jsou: <code>true</code> nebo <code>false</code> (výchozí).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>Volitelný parametr</em>.<br/>Určuje záložní jazyk, pokud nelze identifikovat jazyk vstupního textu. Automatické zjišťování jazyka se použije, když se vynechá parametr <code>from</code>. Pokud se detekce nezdaří, předpokládá se jazyk <code>suggestedFrom</code>.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>Volitelný parametr</em>.<br/>Určuje skript vstupního textu.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>Volitelný parametr</em>.<br/>Určuje skript přeloženého textu.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>Volitelný parametr</em>.<br/>Určuje, že služba může přejít do obecného systému, pokud vlastní systém neexistuje. Možné hodnoty jsou: <code>true</code> (výchozí) nebo <code>false</code>.<br/><br/><code>allowFallback=false</code> určuje, že by překlad měl používat pouze systémy vyškolené pro <code>category</code> určené požadavkem. Pokud překlad pro jazyk X na jazyk Y vyžaduje řetězení pomocí jazyka pivotu E, pak všechny systémy v řetězu (X-> E a E-> Y) budou muset být vlastní a musí mít stejnou kategorii. Pokud se s konkrétní kategorií nenajde žádný systém, bude požadavek vracet kód stavu 400. <code>allowFallback=true</code> určuje, že služba může přejít do obecného systému, pokud vlastní systém neexistuje.
</td>
  </tr>
</table> 

Hlavičky požadavku zahrnují:

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
  <tr>
    <td>Ověřovací hlavičky (y)</td>
    <td><em>Požadovaná hlavička žádosti</em><br/>Podívejte se <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">na dostupné možnosti ověřování</a>.</td>
  </tr>
  <tr>
    <td>Typ obsahu</td>
    <td><em>Požadovaná hlavička žádosti</em><br/>Určuje typ obsahu datové části.<br/> Přijatá hodnota je <code>application/json; charset=UTF-8</code>.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td><em>Požadovaná hlavička žádosti</em><br/>Délka textu žádosti</td>
  </tr>
  <tr>
    <td>X – ClientTraceId</td>
    <td><em>Volitelné</em>.<br/>Identifikátor GUID generovaný klientem pro jednoznačnou identifikaci požadavku. Tuto hlavičku můžete vynechat, pokud zahrnete ID trasování do řetězce dotazu pomocí parametru dotazu s názvem <code>ClientTraceId</code>.</td>
  </tr>
</table> 

## <a name="request-body"></a>Text požadavku

Tělo požadavku je pole JSON. Každý prvek pole je objekt JSON s vlastností řetězce s názvem `Text`, který představuje řetězec, který chcete přeložit.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Platí následující omezení:

* Pole může mít maximálně 100 prvků.
* Celý text zahrnutý v požadavku nesmí být delší než 5 000 znaků včetně mezer.

## <a name="response-body"></a>Tělo odpovědi

Úspěšná odpověď je pole JSON s jedním výsledkem pro každý řetězec ve vstupním poli. Objekt výsledku obsahuje následující vlastnosti:

  * `detectedLanguage`: objekt popisující zjištěný jazyk prostřednictvím následujících vlastností:

      * `language`: řetězec představující kód zjištěného jazyka.

      * `score`: hodnota typu float upozorňující na jistotu ve výsledku. Skóre je v rozsahu 0 až 1 a nízké skóre indikuje nízkou důvěru.

    Vlastnost `detectedLanguage` je přítomna pouze v objektu Result, pokud je požadováno automatické zjišťování jazyka.

  * `translations`: pole výsledků překladu. Velikost pole odpovídá počtu cílových jazyků, které jsou zadány prostřednictvím parametru dotazu `to`. Každý prvek v poli obsahuje:

    * `to`: řetězec představující kód jazyka cílového jazyka.

    * `text`: řetězec, který poskytuje přeložený text.

    * `transliteration`: objekt, který poskytuje přeložený text ve skriptu určeném parametrem `toScript`.

      * `script`: řetězec určující cílový skript.   

      * `text`: řetězec, který poskytuje přeložený text v cílovém skriptu.

    Objekt `transliteration` není zahrnutý v případě, že se nepřekoná přemístění.

    * `alignment`: objekt s jedinou řetězcovou vlastností nazvanou `proj`, která mapuje vstupní text na přeložený text. Informace o zarovnání jsou k dispozici pouze v případě, že parametr žádosti `includeAlignment` je `true`. Zarovnání je vráceno jako řetězcová hodnota v následujícím formátu: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  Dvojtečka odděluje počáteční a koncový index, čárku odděluje jazyky a mezeru odděluje slova. Jedno slovo může být zarovnáno s nula, jedním nebo více slovy v jiném jazyce a zarovnaná slova mohou být nesouvislá. Pokud nejsou k dispozici žádné informace o zarovnání, element zarovnání bude prázdný. Příklady a omezení najdete v tématu [získání informací o zarovnání](#obtain-alignment-information) .

    * `sentLen`: objekt vracející hranice věty ve vstupních a výstupních textech.

      * `srcSentLen`: celočíselné pole představující délky vět ve vstupním textu. Délka pole je počet vět a hodnoty jsou délkou jednotlivých vět.

      * `transSentLen`: celočíselné pole představující délky vět v přeloženém textu. Délka pole je počet vět a hodnoty jsou délkou jednotlivých vět.

    Hranice věty jsou zahrnuté jenom v případě, že parametr Request `includeSentenceLength` je `true`.

  * `sourceText`: objekt s jedinou řetězcovou vlastností nazvanou `text`, která poskytuje vstupní text ve výchozím skriptu zdrojového jazyka. vlastnost `sourceText` je přítomna pouze v případě, že vstup je vyjádřen ve skriptu, který není obvyklým skriptem pro daný jazyk. Například pokud byl vstup ve skriptu latinky napsaný arabskou, pak `sourceText.text` bude stejný arabský text převedený na Arabský skript.

Příklad odpovědí JSON je uveden v části [Příklady](#examples) .

## <a name="response-headers"></a>Hlavičky odpovědi

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
    <tr>
    <td>X-RequestId</td>
    <td>Hodnota, kterou služba vygenerovala k identifikaci požadavku. Používá se pro účely řešení potíží.</td>
  </tr>
  <tr>
    <td>X-MT-System</td>
    <td>Určuje typ systému, který se použil pro překlad pro každý jazyk, který se požaduje pro překlad. Hodnota je čárkami oddělený seznam řetězců. Každý řetězec označuje typ:<br/><ul><li>Vlastní požadavek obsahuje vlastní systém a v průběhu překladu byl použit alespoň jeden vlastní systém.</li><li>Team – všechny ostatní požadavky</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Stavové kódy odpovědí

Níže jsou uvedené možné stavové kódy HTTP, které požadavek vrátí. 

<table width="100%">
  <th width="20%">Stavový kód</th>
  <th>Popis</th>
  <tr>
    <td>200</td>
    <td>Úspěch</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Jeden z parametrů dotazu chybí nebo je neplatný. Před opakováním pokusu proveďte správné parametry žádosti.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Požadavek nebylo možné ověřit. Ověřte, jestli jsou přihlašovací údaje zadané a platné.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Požadavek není autorizovaný. Podívejte se na podrobnosti chybové zprávy. To často znamená, že se využívaly všechny bezplatné překlady, které jsou součástí zkušebního předplatného.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>Požadavek nebylo možné splnit, protože chybí prostředek. Podívejte se na podrobnosti chybové zprávy. Při použití vlastního <code>category</code> to často znamená, že vlastní překladový systém ještě není k dispozici pro poskytování požadavků. Žádost by se měla opakovat po dobu čekání (např. 1 minuta).</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Server odmítl požadavek, protože klient překročil omezení požadavků.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Došlo k neočekávané chybě. Pokud chyba přetrvává, ohlaste ji pomocí: datum a čas selhání, identifikátor požadavku z hlavičky odpovědi <code>X-RequestId</code> a identifikátor klienta z hlavičky žádosti <code>X-ClientTraceId</code>.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Server je dočasně nedostupný. Opakujte požadavek. Pokud chyba přetrvává, ohlaste ji pomocí: datum a čas selhání, identifikátor požadavku z hlavičky odpovědi <code>X-RequestId</code> a identifikátor klienta z hlavičky žádosti <code>X-ClientTraceId</code>.</td>
  </tr>
</table> 

Pokud dojde k chybě, požadavek vrátí také odpověď na chybu JSON. Kód chyby je číslo na 6 číslic, ve kterém se kombinují stavový kód HTTP s kódem, za nímž následuje 3 číslice a další kategorizace chyby. Běžné kódy chyb najdete na [stránce s referenčními Translator text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Příklady

### <a name="translate-a-single-input"></a>Přeložit jeden vstup

Tento příklad ukazuje, jak přeložit jednu větu z angličtiny na zjednodušenou čínštinu.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Tělo odpovědi:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

Pole `translations` obsahuje jeden prvek, který poskytuje překlad jediného textu ve vstupu.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Překlad jednoho vstupu s použitím jazyka pro automatické zjišťování

Tento příklad ukazuje, jak přeložit jednu větu z angličtiny na zjednodušenou čínštinu. Požadavek neurčuje vstupní jazyk. Místo toho se použije automatické zjišťování zdrojového jazyka.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Tělo odpovědi:

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
Odpověď je podobná odpovědi z předchozího příkladu. Vzhledem k tomu, že se požadovalo automatické zjišťování jazyka, odpověď obsahuje také informace o jazyce zjištěném pro vstupní text. 

### <a name="translate-with-transliteration"></a>Převod s využitím převodu na více než litry

Předchozí příklad si rozšíříme tak, že přidáte předávaného litru. Následující požadavek se zeptá na čínský překlad napsaný ve skriptu latinky.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Tělo odpovědi:

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

Výsledek překladu nyní obsahuje vlastnost `transliteration`, která poskytuje přeložený text pomocí znaků latinky.

### <a name="translate-multiple-pieces-of-text"></a>Přeložit více částí textu

Překlad více řetězců najednou je pouhým určením pole řetězců v textu žádosti.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

Tělo odpovědi:

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

### <a name="translate-to-multiple-languages"></a>Převést na více jazyků

Tento příklad ukazuje, jak přeložit stejný vstup do několika jazyků v jednom požadavku.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Tělo odpovědi:

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

### <a name="handle-profanity"></a>Vulgární výrazy

Služba Translator (normálně) zachovává vulgární výrazy, které se nacházejí ve zdroji v překladu. Stupeň vulgárních výrazů a kontext, který dává slova neslušně odlišně mezi kulturami a v důsledku toho může být stupeň vulgárních výrazů v cílovém jazyku rozšířen nebo snížen.

Pokud se chcete vyhnout používání vulgárních výrazů v překladu bez ohledu na přítomnost vulgárních výrazů ve zdrojovém textu, můžete použít možnost filtrování vulgárních výrazů. Tato možnost umožňuje zvolit, zda chcete odstranit vulgární výrazy, bez ohledu na to, zda chcete označit vulgární výrazy vhodnými značkami (což vám dává možnost přidat vlastní následné zpracování), nebo chcete provést žádnou akci. Přijaté hodnoty `ProfanityAction` jsou `Deleted`, `Marked` a `NoAction` (výchozí).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Akce</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Toto je výchozí chování. Vulgární výrazy přecházejí ze zdroje do cíle.<br/><br/>
    <strong>Příklad zdroje (japonština)</strong>: 彼はジャッカスです Marketplace.<br/>
    <strong>Příklad překladu (angličtina)</strong>: je to Jackass.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Slova v vulgárních textech se z výstupu odeberou bez náhrady.<br/><br/>
    <strong>Příklad zdroje (japonština)</strong>: 彼はジャッカスです Marketplace.<br/>
    <strong>Příklad překladu (angličtina)</strong>: je.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Slova v vulgárních výrazech jsou nahrazena značkou ve výstupu. Tato značka závisí na parametru <code>ProfanityMarker</code>.<br/><br/>
U <code>ProfanityMarker=Asterisk</code> jsou slova v vulgárních slovech nahrazena <code>***</code>:<br/>
    <strong>Příklad zdroje (japonština)</strong>: 彼はジャッカスです Marketplace.<br/>
    <strong>Příklad překladu (angličtina)</strong>: je \* \* \*.<br/><br/>
V případě <code>ProfanityMarker=Tag</code> jsou slova ohraničená značkami XML &lt;profanity &gt; a &lt;/Profanity &gt;:<br/>
    <strong>Příklad zdroje (japonština)</strong>: 彼はジャッカスです Marketplace.<br/>
    <strong>Příklad překladu (angličtina)</strong>: je &lt;profanity &gt;jackass &lt;/Profanity &gt;.
  </tr>
</table> 

Například:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```
Tato akce vrátí:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Porovnat s:

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

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Překladu obsahu pomocí značek a rozhodnutí o tom, co se má přeložit

Je běžné přeložit obsah, který obsahuje značky, jako je například obsah ze stránky HTML nebo obsahu z dokumentu XML. Při překladu obsahu pomocí značek zahrnout parametr dotazu `textType=html` Kromě toho je někdy užitečné vyloučit konkrétní obsah z překladu. Pomocí atributu `class=notranslate` můžete zadat obsah, který by měl zůstat v původním jazyce. V následujícím příkladu obsah uvnitř prvního prvku `div` nebude přeložen, zatímco obsah druhého prvku `div` bude přeložen.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Tady je ukázkový požadavek, který se má ilustrovat.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

Odpověď:

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

Chcete-li získat informace o zarovnání, zadejte v řetězci dotazu `includeAlignment=true`.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation.'}]"
```

Odpověď:

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

Informace o zarovnání začínají `0:2-0:1`, což znamená, že první tři znaky ve zdrojovém textu (`The`) se mapují k prvním dvěma znakům v přeloženém textu (`La`).

#### <a name="limitations"></a>Omezení
Pamatujte na následující omezení:

* Zarovnání není pro text ve formátu HTML k dispozici, tj., textType = HTML.
* Zarovnání se vrátí jenom pro podmnožinu párů jazyků:
  - z angličtiny do jakéhokoli jiného jazyka;
  - z jakéhokoli jiného jazyka až po angličtinu, s výjimkou čínského zjednodušené čínštiny, tradiční čínštiny a lotyšské angličtiny;
  - z japonštiny do korejštiny nebo z korejštiny do japonštiny.
* Pokud je větu zarovnaným překladem, nebudete přicházet. Příkladem překonzervovaného překladu je "Toto je test", "Miluji vás" a dalšími větami s vysokou frekvencí.

### <a name="obtain-sentence-boundaries"></a>Získat hranice věty

Chcete-li získat informace o délce věty ve zdrojovém textu a přeloženém textu, zadejte v řetězci dotazu `includeSentenceLength=true`.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

Odpověď:

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

### <a name="translate-with-dynamic-dictionary"></a>Přeložit pomocí dynamického slovníku

Pokud už znáte překlad, který chcete použít pro slovo nebo frázi, můžete ho v rámci žádosti dodat jako značku. Dynamický slovník je bezpečný jenom pro složená podstatná jména, jako jsou správné názvy a názvy produktů.

Značka, která se má dodat, používá následující syntaxi.

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Například je třeba vzít v úvahu anglickou větu "slovo wordomatic je položka slovníku". Pokud chcete zachovat slovo _wordomatic_ v překladu, pošlete žádost:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Výsledek je následující:

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Tato funkce funguje stejně jako u `textType=text` nebo s `textType=html`. Funkce by se měla používat zřídka. Vhodný a mnohem lepší způsob přizpůsobení překladu je použití vlastního překladatele. Vlastní Překladatel umožňuje plné využití kontextových a statistických pravděpodobností. Pokud máte nebo máte možnost chtít vytvořit školicí údaje, které ukazují vaši práci nebo frázi v kontextu, získáte mnohem lepší výsledky. [Přečtěte si další informace o vlastním překladateli](../customization.md).
