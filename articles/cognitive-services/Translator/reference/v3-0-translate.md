---
title: Metoda převede Text Microsoft překladač rozhraní API | Microsoft Docs
titleSuffix: Cognitive Services
description: Použijte metodu převede Microsoft překladač Text rozhraní API.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: d8d5e1e2fac747fa733f1d92c08008b7eac2a1bc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343780"
---
# <a name="text-api-30-translate"></a>Text API 3.0: převede

Přeloží text.

## <a name="request-url"></a>Adresa URL požadavku

Odesílání `POST` požadavek na:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Žádosti jsou parametry předané na řetězec dotazu:

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>verze rozhraní API.</td>
    <td>*Požadovaný parametr*.<br/>Verze rozhraní API požadovaná klientem. Hodnota musí být `3.0`.</td>
  </tr>
  <tr>
    <td>od</td>
    <td>*Volitelný parametr*.<br/>Určuje jazyk, ze vstupního textu. Najít jazyky, které jsou k dispozici pro překlad z vyhledáním [podporované jazyky](.\v3-0-languages.md) pomocí `translation` oboru. Pokud `from` není zadán parametr, automatické zjišťování jazyka se použije k určení jazyka zdroje.</td>
  </tr>
  <tr>
    <td>na</td>
    <td>*Požadovaný parametr*.<br/>Určuje jazyk text výstupu. Cílový jazyk musí mít jednu z [podporované jazyky](.\v3-0-languages.md) součástí `translation` oboru. Například použít `to=de` přeložit němčina.<br/>Je možné přeložit do několika jazyků současně opakováním parametr v řetězci dotazu. Například použít `to=de&to=it` přeložit a němčina, italština.</td>
  </tr>
  <tr>
    <td>textType</td>
    <td>*Volitelný parametr*.<br/>Určuje, zda text se překlad vztahuje prostý text nebo text ve formátu HTML. Musí být ve správném formátu, dokončení element HTML. Možné hodnoty jsou: `plain` (výchozí) nebo `html`.</td>
  </tr>
  <tr>
    <td>category</td>
    <td>*Volitelný parametr*.<br/>Řetězec určující kategorii překlad (domény). Tento parametr se používá k získání překlady z přizpůsobený systém vytvořené s [vlastní překladač](../customization.md). Výchozí hodnota je: `general`.</td>
  </tr>
  <tr>
    <td>ProfanityAction</td>
    <td>*Volitelný parametr*.<br/>Určuje, jakým způsobem má být použit profanities v překladů. Možné hodnoty jsou: `NoAction` (výchozí), `Marked` nebo `Deleted`. Zjistit, jak považovat vulgárnost, najdete v části [vulgárnost zpracování](#handle-profanity).</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td>*Volitelný parametr*.<br/>Určuje, jak by měl být označen profanities v překladů. Možné hodnoty jsou: `Asterisk` (výchozí) nebo `Tag`. Zjistit, jak považovat vulgárnost, najdete v části [vulgárnost zpracování](#handle-profanity).</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td>*Volitelný parametr*.<br/>Určuje, jestli chcete zahrnout zarovnání projekce z zdrojový text na text přeložený. Možné hodnoty jsou: `true` nebo `false` (výchozí). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td>*Volitelný parametr*.<br/>Určuje, jestli se mají zahrnout větu hranice pro vstupního textu a přeložený text. Možné hodnoty jsou: `true` nebo `false` (výchozí).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td>*Volitelný parametr*.<br/>Určuje jazyk, který záložní, pokud nebyl zjištěn jazyk ze vstupního textu. Automatické zjišťování jazyk se použije při `from` je parametr vynechán. Pokud zjišťování selže, `suggestedFrom` považováno za jazyk.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Volitelný parametr*.<br/>Určuje skript ze vstupního textu.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Volitelný parametr*.<br/>Určuje skript přeložený text.</td>
  </tr>
</table> 

Hlavičky požadavku zahrnují:

<table width="100%">
  <th width="20%">Záhlaví</th>
  <th>Popis</th>
  <tr>
    <td>_Jedním autorizačním_<br/>_Záhlaví_</td>
    <td>*Hlavička požadavku požadované*.<br/>V tématu [dostupné možnosti pro ověřování](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Typ obsahu</td>
    <td>*Hlavička požadavku požadované*.<br/>Určuje typ obsahu datové části. Možné hodnoty jsou: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Hlavička požadavku požadované*.<br/>Délka textu požadavku.</td>
  </tr>
  <tr>
    <td>X ClientTraceId</td>
    <td>*Volitelné*.<br/>GUID klientem generovaná k jednoznačné identifikaci požadavku. Tuto hlavičku můžete vynechat, pokud zahrnují ID trasování v řetězci dotazu pomocí parametru dotazu s názvem `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Tělo požadavku

Text žádosti je pole JSON. Každý element pole je objekt JSON s řetězec vlastnost s názvem `Text`, která představuje řetězec přeložit.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Platí následující omezení:

* Pole může obsahovat maximálně 25 elementy.
* Celý text zahrnuté v požadavku nesmí překročit 5 000 znaků včetně mezer.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON s jeden výsledek pro každý řetězec v vstupního pole. Výsledný objekt zahrnuje následující vlastnosti:

  * `detectedLanguage`: Objekt popisující zjištěný jazyk prostřednictvím následujících vlastností:

      * `language`: Řetězec představující kód zjištěného jazyka.

      * `score`Hodnota: float označující spolehlivosti ve výsledku. Je skóre mezi 0 a 1 a nízké skóre označuje nízkou spolehlivosti.

    `detectedLanguage` Vlastnost je přítomen v objektu výsledků, pouze pokud se požaduje automatické rozpoznávání jazyka.

  * `translations`: Pole překlad výsledky. Velikost pole odpovídá počtu cíl jazyků zadaných prostřednictvím `to` parametr dotazu. Každý prvek v poli zahrnuje:

    * `to`: Řetězec představující kód jazyka cílový jazyk.

    * `text`: Řetězec poskytnutí přeložený text.

    * `transliteration`: Poskytnutí přeložený text ve skriptu určeného objektu `toScript` parametr.

      * `script`: Řetězec určující skript cíl.   

      * `text`: Řetězec poskytnutí přeložený text ve skriptu cíl.

    `transliteration` Objekt není vložená, pokud přepis není provedena.

    * `alignment`: Objekt se jeden řetězec vlastnost s názvem `proj`, který je přiřazen zadejte text, který se přeložený text. Zarovnání informace jsou poskytovány, pouze když parametr žádosti `includeAlignment` je `true`. Zarovnání se vrátí jako řetězcovou hodnotu v následujícím formátu: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  Dvojtečkou odděluje počáteční a koncová hodnota Čárka odděluje jazyky a místo odděluje slova. Jedno slovo může zarovnané s nula, jednu nebo více slov v dalších jazyků a může být nesouvislé zarovnaný slova. Pokud nejsou dostupné žádné informace zarovnání, bude prázdný element zarovnání. V tématu [získat informace o zarovnání](#obtain-alignment-information) pro příklad a omezení.

    * `sentLen`: Objekt vrácení ve vstupní a výstupní texty větu hranice.

      * `srcSentLen`: Pole celé číslo představující délek věty v vstupního textu. Délka pole je číslo vět a hodnoty jsou délka jednotlivé věty.

      * `transSentLen`: Pole celé číslo představující délek věty v přeložený text. Délka pole je číslo vět a hodnoty jsou délka jednotlivé věty.

    Věty hranice jsou pouze zahrnuty při parametr žádosti `includeSentenceLength` je `true`.

  * `sourceText`: Objekt se jeden řetězec vlastnost s názvem `text`, což dává vstupního textu ve výchozím nastavení skriptu jazyka zdroje. `sourceText` Vlastnost není k dispozici jenom v případě, že vstup je vyjádřena v skript, který není obvyklé skript pro jazyk. Například, pokud vstupní byly Arabské napsané v latince, pak `sourceText.text` by do skriptu Arabské převést stejné Arabic text.

Příklad odpovědi JSON jsou součástí [příklady](#examples) části.

## <a name="response-status-codes"></a>Kódy stavu odpovědi

Níže jsou uvedeny možné stavové kódy HTTP, které vrací žádost. 

<table width="100%">
  <th width="20%">Stavový kód</th>
  <th>Popis</th>
  <tr>
    <td>200</td>
    <td>Úspěch</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Jeden z parametrů dotazu nebyl nalezen nebo není platný. Před opakováním opravte parametry žádosti.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Žádost nešlo ověřit. Zkontrolujte, zda jsou přihlašovací údaje zadané a platné.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Požadavek není ověřen. Zkontrolujte podrobnosti chybové zprávy. Často to znamená, že všechny volné překlady součástí zkušební předplatné vyčerpání.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Volající odesílá příliš mnoho požadavků.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Došlo k neočekávané chybě. Pokud chyba přetrvává, nahlaste to s: datum a čas selhání identifikátor žádosti z hlavičky odpovědi `X-RequestId`a identifikátor klienta z hlavičky požadavku `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Server není dočasně k dispozici. Opakujte žádost. Pokud chyba přetrvává, nahlaste to s: datum a čas selhání identifikátor žádosti z hlavičky odpovědi `X-RequestId`a identifikátor klienta z hlavičky požadavku `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Příklady

### <a name="translate-a-single-input"></a>Převede jeden vstup

Tento příklad ukazuje postup přeložit jeden větu z Angličtina pro zjednodušenou čínštinu.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Text odpovědi je:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

`translations` Pole obsahuje jeden element, který poskytuje překlad jednu část textu ve vstupu.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Převede jeden vstup pro automatické rozpoznávání jazyka

Tento příklad ukazuje postup přeložit jeden větu z Angličtina pro zjednodušenou čínštinu. Požadavek neurčuje vstupní jazyk. Automatické zjišťování jazyka zdroj bude místo něj použita.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Text odpovědi je:

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
Odpověď je podobná odpověď z předchozího příkladu. Vzhledem k tomu, že byl požadován automatické rozpoznávání jazyka, odpověď také obsahuje informace o jazyce zjištěna vstupního textu. 

### <a name="translate-with-transliteration"></a>Převede s přepis

Předchozí příklad umožňuje rozšířit přidáním přepis. Následující požadavek požádá o čínština překlad napsané v latince.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Text odpovědi je:

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"text":"nǐ hǎo , nǐ jiào shén me míng zì ？","script":"Latn"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

Výsledek překlad nyní zahrnuje `transliteration` vlastnost, která umožňuje přeložený text použití znaky latinky.

### <a name="translate-multiple-pieces-of-text"></a>Převede více částí textu

Najednou překladu vícenásobných řetězců se jednoduše zadávání pole řetězců v textu požadavku.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

---

Text odpovědi je:

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

### <a name="translate-to-multiple-languages"></a>Převede do několika jazyků

Tento příklad ukazuje, jak přeložit stejné vstup několik jazyků v jedné žádosti.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Text odpovědi je:

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

### <a name="handle-profanity"></a>Popisovač vulgárnost

Za normálních okolností službu překladač zachovají vulgárnost, která je k dispozici ve zdroji v překladu. Stupeň vulgárnost a kontext, který umožňuje slova znevažujícího liší pro jednotlivé jazykové verze, a proto může rozšířit nebo snížit stupeň vulgárnost v jazyce cíl.

Pokud chcete, aby se zabránilo získání vulgárnost v překlad, bez ohledu na přítomnost vulgárnost v zdrojový text můžete vulgárnost filtrování možnost. Možnost můžete zvolit, jestli chcete zobrazit vulgárnost odstranit, zda chcete označit profanities s odpovídající značky (poskytující možnost přidat vlastní po zpracování), nebo chcete nebyla provedena žádná akce. Přijaté hodnoty `ProfanityAction` jsou `Deleted`, `Marked` a `NoAction` (výchozí).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Akce</th>
  <tr>
    <td>`NoAction`</td>
    <td>Toto je výchozí chování. Vulgárnost předá ze zdroje k cíli.<br/><br/>
    **Příklad zdroje (japonština)**: 彼はジャッカスです。<br/>
    **Příklad překlad (angličtina)**: se jackass.
    </td>
  </tr>
  <tr>
    <td>`Deleted`</td>
    <td>Znevažujícího slova se odeberou z výstupu bez nahrazení.<br/><br/>
    **Příklad zdroje (japonština)**: 彼はジャッカスです。<br/>
    **Příklad překlad (angličtina)**: se.
    </td>
  </tr>
  <tr>
    <td>`Marked`</td>
    <td>Znevažujícího slova značku ve výstupu. Značky závisí na `ProfanityMarker` parametr.<br/><br/>
Pro `ProfanityMarker=Asterisk`, znevažujícího slova s `***`:<br/>
    **Příklad zdroje (japonština)**: 彼はジャッカスです。<br/>
    **Příklad překlad (angličtina)**: se \* \* \*.<br/><br/>
Pro `ProfanityMarker=Tag`, znevažujícího slova jsou obklopená značky XML &lt;vulgárnost&gt; a &lt;/profanity&gt;:<br/>
    **Příklad zdroje (japonština)**: 彼はジャッカスです。<br/>
    **Příklad překlad (angličtina)**: se &lt;vulgárnost&gt;jackass&lt;/profanity&gt;.
  </tr>
</table> 

Příklad:

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

Tento příkaz vrátí:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Porovnání s:

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

Vrátí poslední žádosti:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Převede obsah pomocí značek a rozhodnout, jaké je přeložen

Je běžné nepřeloží obsah, který obsahuje značky, jako je třeba obsah ze stránky HTML nebo obsah z dokumentu XML. Zahrnout parametr dotazu `textType=html` při překladu obsah pomocí značek. Kromě toho je někdy umožňuje vyloučit konkrétní obsah z překlad. Můžete použít atribut `class=notranslate` k určení obsahu, který by měla zůstat v původním jazyce. V následujícím příkladu, obsah uvnitř první `div` element nebude přeložit, při obsah za sekundu `div` element se přeložit.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Zde je ukázka požadavku pro ilustraci.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

---

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

Pokud chcete získat informace o zarovnání, zadejte `includeAlignment=true` na řetězec dotazu.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation.'}]"
```

---

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

Informace o zarovnání začíná `0:2-0:1`, to znamená, že prvních tří znaků v textu zdroje (`The`) mapu, aby první dva znaky v přeložený text (`La`).

Vezměte na vědomí následující omezení:

* Zarovnání se vrátí jenom pro podmnožinu dvojice jazyků:
  - z angličtina do dalších jazyků;
  - z jakéhokoli jiného jazyka na angličtinu, s výjimkou zjednodušené čínštiny, tradiční čínštině a lotyština na angličtinu;
  - z japonština z korejština japonština nebo korejština.
* Pokud se věta specifických překlad neobdržíte zarovnání. Je třeba specifických překlad "Toto je test", "I rádi je" a dalších – vysoká frekvence věty.

### <a name="obtain-sentence-boundaries"></a>Získat větu hranice

Získat informace o délka věty v zdrojový text a přeložený text, zadejte `includeSentenceLength=true` na řetězec dotazu.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

---

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

### <a name="translate-with-dynamic-dictionary"></a>Převede s dynamický slovník

Pokud už znáte překladu, který chcete použít slovo nebo frázi, můžete je zadat jako značek v rámci žádosti. Je dynamický slovník je pouze bezpečné pro přístup z složená jako vlastní jména a názvy produktů.

Kód k poskytování používá následující syntaxe.

``` 
<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>
```

Představte si třeba anglickou větu "word wordomatic je položky dictionary." Chcete-li zachovat slovo _wordomatic_ v překlad, odeslat požadavek:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Výsledkem je:

```
[
    {
        "translations":[
            {"text":"Das Wort "wordomatic" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Tato funkce funguje stejně jako s `textType=text` nebo s `textType=html`. Funkce měli šetřit. Je vhodné a mnohem lepší způsob přizpůsobení překlad pomocí vlastní překladač. Vlastní překladač využívá úplnou kontextu a statistické pravděpodobnostech. Pokud máte nebo si může dovolit vytvořit Cvičná data, který ukazuje zaměstnavatelem nebo frázi v kontextu, získáte mnohem lepší výsledky. [Další informace o vlastní překladač](../customization.md).
 





