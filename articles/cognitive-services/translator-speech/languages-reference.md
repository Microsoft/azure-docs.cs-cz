---
title: Metoda jazyky Microsoft překladač řeči rozhraní API | Microsoft Docs
titleSuffix: Cognitive Services
description: Použijte metodu Microsoft překladač řeči rozhraní API jazyky.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 05/18/18
ms.author: v-jansko
ms.openlocfilehash: 5396e3be17345c3c36197a9b6cbace86e1f574c1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343804"
---
# <a name="speech-api-languages"></a>Rozpoznávání řeči rozhraní API: jazyky

Microsoft Translator průběžně rozšiřuje seznam jazyků podporovaných jeho služby. Toto rozhraní API použijte ke zjištění skupinu jazyků, které jsou aktuálně k dispozici pro použití se službou řeči překlad.

Ukázka použití rozhraní API získat dostupné jazyky ukázky kódu jsou k dispozici na [lokality Microsoft překladač Github](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Poznámky k implementaci

ZÍSKAT /languages 

Celou sadu jazyky je k dispozici pro transcribe rozpoznávání řeči, přeložit přepisována text a k vytvoření řečového překladu.

Klient používá `scope` parametr definovat, jaké sady jazyky činit interested v dotazu.

* **Rozpoznávání řeči na text:** použít parametr dotazu `scope=speech` načíst skupinu jazyků, které jsou k dispozici pro transcribe řeči na text.
* **Překlad textu:** použít parametr dotazu `scope=text` načíst skupinu jazyků, které jsou k dispozici pro překlad přepisována textu.
* **Převod textu na řeč:** použít parametr dotazu `scope=tts` načíst sadu jazyky a hlasy syntetizovat přeložený text zpět do řeči k dispozici.

Klienta můžete načíst více sad současně zadáním seznamu voleb oddělených čárkami. Například, `scope=speech,text,tts`.

Úspěšná odpověď je objekt JSON s vlastností pro každý požadovaná sada.

```
{
    "speech": {
        //... Set of languages supported for speech-to-text
    },
    "text": {
        //... Set of languages supported for text translation
    },
    "tts": {
        //... Set of languages supported for text-to-speech
    }
}
```

Vzhledem k tomu může klient používat `scope` parametr dotazu a vyberte které sady podporované jazyky, které má být vrácen, skutečný odpovědi můžou obsahovat jenom podmnožinu všechny vlastnosti, které jsou uvedené výše.

Zadaná hodnota s každou vlastnost je následujícím způsobem.

### <a name="speech-to-text-speech"></a>Rozpoznávání řeči na text (řeči)

Hodnota přidružená vlastnost řeči na text `speech`, je slovník (klíč a hodnotu) páry. Každý klíč identifikuje podporován pro rozpoznávání řeči na text. Klíč je identifikátor tohoto klienta předá do rozhraní API. Hodnota přidružené ke klíči je objekt s následujícími vlastnostmi:

* `name`: Zobrazovaný název jazyka.
* `language`: Značka jazyka přidruženého zapisovat jazyk. Níže najdete v části "Transakce Text".
Příkladem je:

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Překlad textu (text)

Hodnota přidružená `text` vlastnost je také slovník, kde každý klíč identifikuje podporován pro překlad textu. Hodnota přidružená klíč popisuje jazyka:

* `name`: Zobrazovaný název jazyka.
* `dir`: Orientaci, což je `rtl` jazyků zprava doleva nebo `ltr` jazyků zleva doprava.

Příkladem je:

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Převod textu na řeč

Hodnota přidružená vlastnost převod textu na řeč, převod textu na řeč, je také slovník, kde každý klíč identifikuje podporované hlasu. Atributy objektu hlasové jsou:

* `displayName`: Zobrazovaný název hlasu.
* `gender`: Pohlaví hlasu (mužského nebo ženského).
* `locale`: Značka jazyk hlasového s podznačky primární jazyk a podznačky oblast.
* `language`: Značka jazyka přidruženého zapisovat jazyk.
* `languageName`: Zobrazovaný název jazyka.
* `regionName`: Zobrazovaný název oblasti pro tento jazyk.

Příkladem je:

```
{
    "tts": {
        "en-US-Zira": {
            "displayName": "Zira",
            "gender": "female",
            "locale": "en-US",
            "languageName": "English",
            "regionName": "United States",
            "language": "en"
        }
}
```

### <a name="localization"></a>Lokalizace
Služba vrátí všechny názvy v jazyce hlavičku, Accept-Language"pro všechny jazyky podporované v překlad textu.

### <a name="response-class-status-200"></a>Odpověď – třída (stav 200)
Objekt popisující sadu podporované jazyky.

Hodnota ModelExample: 

Langagues {rozpoznávání řeči (objekt, volitelný), text (objekt, volitelný), převod textu na řeč (objekt, volitelný)}

### <a name="headers"></a>Záhlaví

|Záhlaví|Popis|Typ|
:--|:--|:--|
X-RequestId|Hodnota generovaný serverem svou žádost identifikovat a používat pro účely odstraňování potíží.|řetězec|

### <a name="parameters"></a>Parametry

|Parametr|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|
|verze rozhraní API.    |Verze rozhraní API požadovaná klientem. Povolené hodnoty jsou: `1.0`.|query|řetězec|
|scope  |Nastaví podporované jazyky nebo hlasy se vraťte do klienta. Tento parametr je zadán jako textový soubor s oddělovači seznam klíčových slov. K dispozici jsou následující klíčová slova:<ul><li>`speech`: Poskytuje sadu jazyky podporované pro transcribe řeči.</li><li>`tts`: Poskytuje sadu podporuje pro převod textu na řeč.</li><li>`text`: Poskytuje sadu jazyky podporované pro překlad textu.</li></ul>Pokud zadaná hodnota není hodnota `scope` výchozí `text`.|query|řetězec|
|X ClientTraceId    |Identifikátor GUID klientem generovaná použít ke sledování žádost. Usnadňuje řešení potíží s problémy, musí klienti zadejte novou hodnotu s každou žádostí a jeho přihlášení.|záhlaví|řetězec|
|Přijměte jazyk    |Některá pole v odpovědi jsou názvy jazyků nebo oblasti. Tento parametr použijte k definování jazyk, ve kterém jsou vráceny názvy. Jazyk je určena poskytnutím značku ve správném formátu BCP 47 jazyk. Vyberte ze seznamu identifikátorů jazyk vrátí s značku `text` oboru. Nepodporované jazyků názvy jsou uvedeny v anglickém jazyce.<br/>Například použijte hodnotu `fr` požadovat názvy ve francouzštině, nebo použijte hodnotu `zh-Hant` na žádost o názvy v tradiční čínštině.|záhlaví|řetězec|
    
### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400|Chybný požadavek. Zkontrolujte vstupní parametry zajistit, že jsou platné. Objekt odpovědi obsahuje podrobnější popis chyby.|
|429|Příliš mnoho požadavků.|
|500|Došlo k chybě. Pokud chyba přetrvává, nahlaste to identifikátorem trasování klienta (X-ClientTraceId) nebo požadavku identifikátoru (X-RequestId).|
|503|Server není dočasně k dispozici. Opakujte žádost. Pokud chyba přetrvává, nahlaste to identifikátorem trasování klienta (X-ClientTraceId) nebo požadavku identifikátoru (X-RequestId).|
