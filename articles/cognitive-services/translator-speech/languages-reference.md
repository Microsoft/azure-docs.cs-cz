---
title: Metoda Translator Speech API jazyků
titleSuffix: Azure Cognitive Services
description: Použijte metodu Translator Speech API jazyky.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 46ac3928238382f56db5a799226bd3d9243b7ca2
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966410"
---
# <a name="translator-speech-api-languages"></a>Translator Speech API: Languages

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Translator Speech průběžně rozšiřuje seznam jazyků podporovaných jeho službami. Pomocí tohoto rozhraní API můžete zjistit sadu jazyků, které jsou aktuálně k dispozici pro použití se službou Translator Speech.

Ukázky kódu, které demonstrují použití rozhraní API k získání dostupných jazyků, jsou k dispozici na [webu Microsoft Translator GitHub](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Poznámky k implementaci

### <a name="get-languages"></a>ZÍSKAT/languages

K dispozici je celá sada jazyků pro přepisovat rozpoznávání řeči, překladu přepisu textu a k tvorbě syntetizového řeči překladu.

Klient používá `scope` parametr dotazu k definování množiny jazyků, které vás zajímají.

* **Převod řeči na text:** Pomocí parametru `scope=speech` dotazu načtěte sadu jazyků, které jsou k dispozici pro přepisovat řeči na text.
* **Překlad textu:** Pomocí parametru `scope=text` dotazu načtěte sadu jazyků, které jsou k dispozici pro překlad textu přepisu.
* **Převod textu na řeč:**  Pomocí parametru `scope=tts` dotazu můžete načíst sadu jazyků a hlasy, které jsou k dispozici pro syntetizování přeloženého textu zpět na řeč.

Klient může získat více sad současně tak, že určí čárkami oddělený seznam voleb. Například, `scope=speech,text,tts`.

Úspěšná odpověď je objekt JSON s vlastností pro každou požadovanou sadu.

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

Vzhledem k tomu, že klient `scope` může použít parametr dotazu k výběru, které sady podporovaných jazyků by měly být vráceny, může skutečná odpověď zahrnovat pouze podmnožinu všech výše uvedených vlastností.

Hodnota zadaná u jednotlivých vlastností je následující.

### <a name="speech-to-text-speech"></a>Převod řeči na text (řeč)

Hodnota přidružená k vlastnosti `speech`převod řeči na text je slovník dvojic (klíč, hodnota). Každý klíč identifikuje jazyk podporovaný pro převod řeči na text. Klíč je identifikátor, který klient předává do rozhraní API. Hodnota přidružená ke klíči je objekt s následujícími vlastnostmi:

* `name`: Zobrazovaný název jazyka
* `language`: Značka jazyka přidruženého napsaného jazyka. Viz "textová transakce" níže.
Příklad:

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Překlad textu (text)

Hodnota přidružená `text` k vlastnosti je také slovník, ve kterém každý klíč identifikuje jazyk podporovaný pro překlad textu. Hodnota přidružená ke klíči popisuje jazyk:

* `name`: Zobrazovaný název jazyka
* `dir`: Směrová orientace, `rtl` která je určena pro jazyky zprava doleva `ltr` nebo pro jazyky zleva doprava.

Příklad:

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Převod textu na řeč

Hodnota přidružená k vlastnosti převod textu na řeč je také slovník, ve kterém každý klíč identifikuje podporovaný hlas. Atributy hlasového objektu jsou:

* `displayName`: Zobrazovaný název hlasu
* `gender`: Pohlaví hlasu (muž nebo žena).
* `locale`: Značka jazyka hlasu s primárním jazykem případě a region případě
* `language`: Značka jazyka přidruženého napsaného jazyka.
* `languageName`: Zobrazovaný název jazyka
* `regionName`: Zobrazovaný název oblasti pro tento jazyk

Příklad:

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
Služba vrátí všechny názvy v jazyce hlavičky ' Accept-Language ' pro všechny jazyky, které jsou v překladu textu podporovány.

### <a name="response-class-status-200"></a>Response – třída (stav 200)
Objekt popisující sadu podporovaných jazyků.

ModelExample hodnota:

Langagues {Speech (Object, Optional); text (Object; Optional); TTS (Object, Optional)}

### <a name="headers"></a>Záhlaví

|Záhlaví|Popis|type|
:--|:--|:--|
X-RequestId|Hodnota vygenerovaná serverem k identifikaci žádosti a používaná pro účely řešení potíží.|řetězec|

### <a name="parameters"></a>Parametry

|Parametr|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|
|api-version    |Verze rozhraní API, kterou klient požaduje. Povolené hodnoty jsou: `1.0`.|query|řetězec|
|scope  |Sady podporovaných jazyků nebo hlasů, které se mají vrátit do klienta. Tento parametr je zadán jako čárkami oddělený seznam klíčových slov. K dispozici jsou následující klíčová slova:<ul><li>`speech`: Poskytuje sadu jazyků podporovaných pro přepisovat řeč.</li><li>`tts`: Poskytuje sadu hlasů podporovaných pro převod textu na řeč.</li><li>`text`: Poskytuje sadu jazyků podporovaných pro překlad textu.</li></ul>Není-li zadána hodnota, je `scope` `text`použita výchozí hodnota.|query|řetězec|
|X-ClientTraceId    |Identifikátor GUID generovaný klientem, který se používá pro trasování požadavku. Aby se usnadnilo řešení potíží, klienti by měli pro každou žádost zadat novou hodnotu a zaznamenat ji.|záhlaví|řetězec|
|Přijmout – jazyk    |Některá pole v odpovědi jsou názvy jazyků nebo oblastí. Pomocí tohoto parametru můžete definovat jazyk, ve kterém se mají vracet názvy. Jazyk je určen tak, že poskytuje značku jazyka BCP 47 ve správném formátu. Vyberte značku ze seznamu identifikátorů jazyka vrácených s `text` oborem. Pro nepodporované jazyky jsou názvy k dispozici v anglickém jazyce.<br/>Použijte například hodnotu `fr` pro vyžádání názvů ve francouzštině nebo použijte hodnotu `zh-Hant` pro vyžádání názvů v tradiční čínštině.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Reason|
|:--|:--|
|400|Chybný požadavek. Zkontrolujte vstupní parametry a ujistěte se, že jsou platné. Objekt Response obsahuje podrobnější popis chyby.|
|429|Příliš mnoho požadavků.|
|500|Došlo k chybě. Pokud chyba přetrvává, ohlaste ji pomocí identifikátoru trasování klienta (X-ClientTraceId) nebo identifikátoru žádosti (X-RequestId).|
|503|Server je dočasně nedostupný. Opakujte prosím požadavek. Pokud chyba přetrvává, ohlaste ji pomocí identifikátoru trasování klienta (X-ClientTraceId) nebo identifikátoru žádosti (X-RequestId).|
