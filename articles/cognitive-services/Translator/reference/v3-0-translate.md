---
title: Translator Text API Translate – metoda
titleSuffix: Azure Cognitive Services
description: Použijte metodu Translator Text API přeložit.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: a096bd2f23910eb2eb3bc4aa36e34400ccfbb701
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853400"
---
# <a name="translator-text-api-30-translate"></a>Translator Text API 3.0: překlad

Přeloží text.

## <a name="request-url"></a>Adresa URL požadavku

Odeslat `POST` požadavek na:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Žádosti jsou parametry předané v řetězci dotazu:

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>verze API-version</td>
    <td>*Povinný parametr*.<br/>Verze rozhraní API požadovaná klientem. Hodnota musí být `3.0`.</td>
  </tr>
  <tr>
    <td>od</td>
    <td>*Volitelný parametr*.<br/>Určuje jazyk, který vstupního textu. Najít jazyky, které jsou k dispozici pro převod z vyhledáním [podporované jazyky](.\v3-0-languages.md) pomocí `translation` oboru. Pokud `from` parametr není zadán, automatické rozpoznávání jazyka se použije k určení zdrojového jazyka.</td>
  </tr>
  <tr>
    <td>na</td>
    <td>*Povinný parametr*.<br/>Určuje jazyk text výstupu. Cílový jazyk musí být jedna z [podporované jazyky](.\v3-0-languages.md) součástí `translation` oboru. Například použít `to=de` pro převod na němčinu.<br/>Je možné převést na různých jazycích současně opakováním parametr v řetězci dotazu. Například použít `to=de&to=it` pro převod a němčina, italština.</td>
  </tr>
  <tr>
    <td>textType</td>
    <td>*Volitelný parametr*.<br/>Definuje, zda je text, který je překládán prostý text nebo text ve formátu HTML. Musí být ve správném formátu, dokončení element veškeré kódování HTML. Možné hodnoty jsou: `plain` (výchozí) nebo `html`.</td>
  </tr>
  <tr>
    <td>category</td>
    <td>*Volitelný parametr*.<br/>Řetězec určující kategorie překladu (domény). Tento parametr slouží k získání překlady z přizpůsobené systému vytvořených pomocí [vlastní Translator](../customization.md). Výchozí hodnota je: `general`.</td>
  </tr>
  <tr>
    <td>ProfanityAction</td>
    <td>*Volitelný parametr*.<br/>Určuje, jak bude pracovat profanities překlady. Možné hodnoty jsou: `NoAction` (výchozí), `Marked` nebo `Deleted`. Způsoby, jak nakládat s vulgárních výrazů najdete v tématu [vulgárních výrazů zpracování](#handle-profanity).</td>
  </tr>
  <tr>
    <td>ProfanityMarker</td>
    <td>*Volitelný parametr*.<br/>Určuje, jak by měla být označena profanities v překlady. Možné hodnoty jsou: `Asterisk` (výchozí) nebo `Tag`. Způsoby, jak nakládat s vulgárních výrazů najdete v tématu [vulgárních výrazů zpracování](#handle-profanity).</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td>*Volitelný parametr*.<br/>Určuje, jestli se mají zahrnout zarovnání projekce ze zdrojového textu na přeložený text. Možné hodnoty jsou: `true` nebo `false` (výchozí). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td>*Volitelný parametr*.<br/>Určuje, jestli se má zahrnout věty hranice pro vstupní text a přeloženým textem. Možné hodnoty jsou: `true` nebo `false` (výchozí).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td>*Volitelný parametr*.<br/>Určuje jazyk, který použití náhradní lokality, pokud nelze určit jazyk vstupního textu. Automatické rozpoznávání jazyka se použije při `from` parametr se vynechá. Pokud se nezdaří detekce, `suggestedFrom` předpokládá se jazyk.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Volitelný parametr*.<br/>Určuje skript vstupního textu.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Volitelný parametr*.<br/>Určuje skript přeloženého textu.</td>
  </tr>
  <tr>
    <td>AllowFallback</td>
    <td>*Volitelný parametr*.<br/>Určuje, že služba může nouzového řešení ověření pomocí obecného systému při vlastní systému neexistuje. Možné hodnoty jsou: `true` (výchozí) nebo `false`.<br/><br/>`AllowFallback=false` Určuje, zda překlad by měl používat pouze systémy trénovaných ke zpracování `category` určeném požadavkem. Pokud překlad pro jazyk X jazyk Y vyžaduje řetězení prostřednictvím jazyka pivot E, pak všechny systémy v řetězci (X -> E a E -> Y) bude nutné mít vlastní a mají stejné kategorie. Pokud je nalezen žádný systém s konkrétní kategorií, požadavek vrátí stavový kód 400. `AllowFallback=true` Určuje, že služba může nouzového řešení ověření pomocí obecného systému při vlastní systému neexistuje.
</td>
  </tr>
</table> 

Hlavičky žádosti patří:

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
  <tr>
    <td>_Jedním autorizačním_<br/>_Záhlaví_</td>
    <td>*Hlavička požadavku požadované*.<br/>Zobrazit [dostupné možnosti pro ověřování](./v3-0-reference.md#authentication).</td>
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
    <td>X-ClientTraceId</td>
    <td>*Volitelné*.<br/>Klientem generovaná identifikátor GUID k jednoznačné identifikaci požadavku. Tato hlavička můžete vynechat, pokud zahrnete ID trasování v řetězci dotazu pomocí parametru dotazu s názvem `ClientTraceId`.</td>
  </tr>
  <tr>
    <td>Systém X MT</td>
    <td>*Volitelné*.<br/>Určuje typ systému, který se použil pro překlad pro každý "k" Jazyk požadovaný pro překlad. Hodnota je čárkou oddělený seznam řetězců. Každý řetězec označuje typ:<br/><ul><li>Vlastní - žádost obsahuje vlastní systém a aspoň jeden vlastní systém se použil při překladu.</li><li>Tým – všechny ostatní žádosti</li></ul>
</td>
  </tr>
</table> 

## <a name="request-body"></a>Text požadavku

Text požadavku je pole JSON. Každý prvek pole je objekt JSON s řetězcovou vlastnost s názvem `Text`, která představuje řetězec, který má přeložit.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Platí následující omezení:

* Pole může mít maximálně 25 prvků.
* Celý text zahrnutý v požadavku nemůže být delší než 5 000 znaků včetně mezer.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON se jeden výsledek pro každý řetězec vstupního pole. Výsledný objekt zahrnuje následující vlastnosti:

  * `detectedLanguage`: Objekt popisující zjištěný jazyk prostřednictvím následující vlastnosti:

      * `language`: Řetězec představující kód zjištěný jazyk.

      * `score`: Hodnoty typu float označující důvěru ve výsledky. Je skóre mezi 0 a 1 a nízké skóre označuje s nízkou spolehlivostí.

    `detectedLanguage` Vlastnost je k dispozici v objektu výsledků pouze pokud se požaduje automatické rozpoznávání jazyka.

  * `translations`: Pole překlad výsledky. Velikost pole odpovídá počtu cílové jazyky zadané prostřednictvím `to` parametr dotazu. Každý prvek v poli zahrnuje:

    * `to`: Řetězec představující kód jazyka v cílovém jazyce.

    * `text`: Řetězec poskytující přeložený text.

    * `transliteration`: Objekt poskytující přeložený text ve skriptu určené `toScript` parametru.

      * `script`: Řetězec určující skript cíl.   

      * `text`: Řetězec poskytující přeložený text ve skriptu cíl.

    `transliteration` Objektu není zahrnuta, pokud transkripci není provedena.

    * `alignment`: Objekt s vlastností jednoho řetězce s názvem `proj`, namapovaná vstupní text, který se přeložený text. Informace o zarovnání je zadat, pouze pokud parametr žádosti `includeAlignment` je `true`. Zarovnání se vrátí jako hodnotu řetězce v následujícím formátu: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  Dvojtečka odděluje počáteční a koncová hodnota, pomlčku odděluje jazyky a slova jsou odděleny mezerou. O jedno slovo může bylo v souladu s žádným, jedním nebo více slov v jiném jazyce, a může být nesouvislé zarovnané slova. Pokud je k dispozici žádné informace o zarovnání, zarovnání element bude prázdný. Zobrazit [získat informace o zarovnání](#obtain-alignment-information) příklad a omezení.

    * `sentLen`: Objekt vrácení věty hranice ve vstupní a výstupní texty.

      * `srcSentLen`: Pole celé číslo představující délku věty ve vstupním textu. Délka pole je počet věty a hodnoty jsou délka jednotlivé věty.

      * `transSentLen`: Pole celé číslo představující délku vět přeloženého textu. Délka pole je počet věty a hodnoty jsou délka jednotlivé věty.

    Věty hranice jsou pouze zahrnuty při parametr žádosti `includeSentenceLength` je `true`.

  * `sourceText`: Objekt s vlastností jednoho řetězce s názvem `text`, což dává vstupního textu ve výchozím nastavení skriptu zdrojového jazyka. `sourceText` Vlastnost je k dispozici pouze v případě, že vstup je vyjádřena ve skriptu, který není obvykle skript pro jazyk. Například, pokud vstupní byly arabština napsaná v latince, pak `sourceText.text` by stejný Arabské text převést na arabské skriptu.

Jsou k dispozici v příkladu odpověďmi ve formátu JSON [příklady](#examples) oddílu.

## <a name="response-status-codes"></a>Stavové kódy odezvy

Tady jsou možné stavové kódy HTTP, které vrátí žádost o. 

<table width="100%">
  <th width="20%">Stavový kód</th>
  <th>Popis</th>
  <tr>
    <td>200</td>
    <td>Úspěch</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Jeden z parametrů dotazu je chybí nebo není platný. Opravte parametry požadavku než to zkusíte znovu.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Žádost nešlo ověřit. Zkontrolujte, zda jsou pověření zadaná a je platný.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Požadavek není autorizovaný. Najdete podrobnosti o chybové zprávě. To často určuje, že se využilo všechny bezplatné překlady zkušebního předplatného k dispozici.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>Požadavek nelze splnit, protože chybí prostředek. Najdete podrobnosti o chybové zprávě. Při použití vlastního `category`, to často určuje, že vlastní překladový systém ještě není k dispozici pro požadavků. Po určité době čekání (např. 10 minut) neúspěšného požadavku.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Volající odesílá příliš mnoho požadavků.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Došlo k neočekávané chybě. Pokud chyba přetrvává, nahlaste to s: datum a čas selhání žádost s identifikátorem v hlavičce odpovědi `X-RequestId`a identifikátor klienta v hlavičce požadavku `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Server je dočasně nedostupný. Zkuste požadavek. Pokud chyba přetrvává, nahlaste to s: datum a čas selhání žádost s identifikátorem v hlavičce odpovědi `X-RequestId`a identifikátor klienta v hlavičce požadavku `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Příklady

### <a name="translate-a-single-input"></a>Převede jeden vstup

Tento příklad ukazuje, jak pro převod jedné větě z angličtiny do zjednodušené čínštiny.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

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

`translations` Pole obsahuje jeden element, který zajišťuje překlad jednu část textu ve vstupu.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Převede jeden vstup pomocí automatického rozpoznávání jazyka

Tento příklad ukazuje, jak pro převod jedné větě z angličtiny do zjednodušené čínštiny. Jazyk není určen požadavek. Automatická detekce Zdrojový jazyk používá místo toho.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

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
Odpověď je podobný odpovědi z předchozího příkladu. Protože automatické rozpoznávání jazyka byl vyžádán, odpověď obsahuje také informace o jazyce nedetekovali vstupního textu. 

### <a name="translate-with-transliteration"></a>Přeložit pomocí přepis

Můžeme rozšířit předchozí příklad, tak, že přidáte transkripci. Vyzve k zadání čínské překlad napsané v latince následující požadavek.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
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
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

Teď obsahuje výsledek překladu `transliteration` vlastnost, poskytující přeložený text pomocí znaků latinky.

### <a name="translate-multiple-pieces-of-text"></a>Přeložit více částí textu

Překlad najednou více řetězců stačí zadat pole řetězců v textu požadavku.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

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

### <a name="translate-to-multiple-languages"></a>Přeloží do více jazyků

Tento příklad ukazuje, jak převést na stejný vstup do několika jazyků v jedné žádosti.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

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

### <a name="handle-profanity"></a>Zpracování vulgárních výrazů

Službu Translator obvykle zachová vulgárních výrazů, který je k dispozici ve zdroji v překladu. Stupeň vulgárních výrazů a kontext, který umožňuje neslušná slova se liší mezi jazykové verze, a díky tomu mohou rozšířit nebo nižší stupeň vulgárních výrazů v cílovém jazyce.

Pokud chcete předejít vulgárních výrazů v překladu, bez ohledu na přítomnost vulgární výrazy ve zdrojovém textu, můžete použít vulgárních výrazů, možnost filtrování. Možnost můžete zvolit, jestli chcete zobrazit vulgárních výrazů odstraněn, zda chcete označit profanities s odpovídající značky (díky tomu získáte možnost přidávat po zpracování), nebo chcete, aby nebyla provedena žádná akce. Přijímané hodnoty `ProfanityAction` jsou `Deleted`, `Marked` a `NoAction` (výchozí).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Akce</th>
  <tr>
    <td>`NoAction`</td>
    <td>Toto je výchozí chování. Vulgárních výrazů předá ze zdroje do cíle.<br/><br/>
    **Příklad zdroje (japonština)**: 彼はジャッカスです。<br/>
    **Příklad překladu (v angličtině)**: je jackass.
    </td>
  </tr>
  <tr>
    <td>`Deleted`</td>
    <td>Urážlivá slova se odebere z výstupu bez nahrazení.<br/><br/>
    **Příklad zdroje (japonština)**: 彼はジャッカスです。<br/>
    **Příklad překladu (v angličtině)**: pracuje.
    </td>
  </tr>
  <tr>
    <td>`Marked`</td>
    <td>Neslušná slova značky ve výstupu. Značky závisí `ProfanityMarker` parametru.<br/><br/>
Pro `ProfanityMarker=Asterisk`, jsou nahrazeny urážlivá slova `***`:<br/>
    **Příklad zdroje (japonština)**: 彼はジャッカスです。<br/>
    **Příklad překladu (v angličtině)**: je \* \* \*.<br/><br/>
Pro `ProfanityMarker=Tag`, urážlivá slova jsou ohraničeny značky XML &lt;vulgárních výrazů&gt; a &lt;/profanity&gt;:<br/>
    **Příklad zdroje (japonština)**: 彼はジャッカスです。<br/>
    **Příklad překladu (v angličtině)**: je &lt;vulgárních výrazů&gt;jackass&lt;/profanity&gt;.
  </tr>
</table> 

Příklad:

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

Vrátí:

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

# <a name="curltabcurl"></a>[Curl](#tab/curl)

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

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Převede obsah pomocí značek a rozhodnout, co je přeloženo

Je běžné překládat obsah, který obsahuje značky, jako je třeba obsah ze stránky HTML nebo obsahu z dokumentu XML. Zahrnout parametr dotazu `textType=html` při překladu obsah s klíčovými slovy. Kromě toho je někdy užitečné chcete vyloučit z překladu konkrétní obsah. Můžete použít atribut `class=notranslate` k určení obsahu, který by měla zůstat v původním jazyce. V následujícím příkladu je obsah uvnitř první `div` element nebude přenesen, při obsah ve druhém `div` element bude fungovat.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Následuje ukázkový požadavek pro ilustraci.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

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

Zobrazí informace o zarovnání, zadejte `includeAlignment=true` v řetězci dotazu.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

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

Informace o zarovnání začíná `0:2-0:1`, což znamená, že první tři znaky ve zdrojovém textu (`The`) mapovat na přeložený text v prvních dvou znacích (`La`).

Mějte na paměti následující omezení:

* Zarovnání se vrátí jenom pro podmnožinu dvojice jazyků:
  - z angličtiny do jakéhokoli jiného jazyka;
  - z jakéhokoli jiného jazyka na angličtinu, s výjimkou zjednodušené čínštiny, tradiční čínštině a lotyština na angličtinu;
  - z japonština, korejština nebo korejština, japonština.
* Pokud se věta uvedeny překlad neobdržíte zarovnání. Příklad uvedeny překladu je "Toto je test", "Mám rád je" a další vysokou frekvencí věty.

### <a name="obtain-sentence-boundaries"></a>Získat hranice větu

Chcete-li získat informace o délka věty zdrojový text a přeložený text, zadejte `includeSentenceLength=true` v řetězci dotazu.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

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

### <a name="translate-with-dynamic-dictionary"></a>Přeložit pomocí dynamický slovník

Pokud již znáte překlad, kterou chcete použít pro slovo nebo frázi, můžete je zadat jako kód v rámci požadavku. Je dynamický slovník je jen bezpečný pro složená, jako jsou vlastní názvy a názvy produktů.

Kód slouží k poskytování používá následující syntaxi.

``` 
<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>
```

Představte si třeba angličtina větu "word wordomatic je ke slovníku." Chcete-li zachovat slovo _wordomatic_ v překladu, odeslat požadavek:

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

Tato funkce funguje stejným způsobem jako s `textType=text` nebo s `textType=html`. Tato funkce by měly používat střídmě. Odpovídající a mnohem lepší způsob přizpůsobení překladu je s použitím překladač vlastní. Vlastní Translator umožňuje plně využívat kontextu a statistické pravděpodobnosti. Pokud máte nebo můžete dovolit vytvoření trénovací data, která zobrazuje vaše firma nebo frázi v kontextu, získáte mnohem lepší výsledky. [Další informace o vlastní Translator](../customization.md).
 





