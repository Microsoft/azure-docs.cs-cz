---
title: Translator Speech API jazyků – metoda
titleSuffix: Azure Cognitive Services
description: Použijte metodu Translator Speech API jazyků.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 05/18/18
ms.author: v-jansko
ms.openlocfilehash: 12f989137c3aea57bdcde0d50315ad157898cd28
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55862747"
---
# <a name="translator-speech-api-languages"></a>Translator Speech API: Languages

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Translator Speech neustále rozšiřuje seznam jazyků podporovaných jeho služeb. Zjistit skupinu jazyků, které jsou aktuálně k dispozici pro použití ve službě Translator Speech pomocí tohoto rozhraní API.

Ukázky kódu pro demonstraci použití rozhraní API za účelem získání dostupné jazyky jsou k dispozici [webu Microsoft Translator GitHub](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Poznámky k implementaci

### <a name="get-languages"></a>ZÍSKAT /languages 

Široké škále jazycích je k dispozici pro zpracování řeči, můžete přeložit text přepisu a k vytvoření řečového překladu přepisy.

Klient použije `scope` parametr k definování, který nastaví jazyky ho zajímají dotazu.

* **Převod řeči na text:** Použijte parametr dotazu `scope=speech` načíst skupinu jazyků, které jsou k dispozici pro přepisy řeči na text.
* **Překlad textu:** Použijte parametr dotazu `scope=text` načíst sadu jazyků můžete přeložit text přepisu.
* **Převod textu na řeč:**  Použijte parametr dotazu `scope=tts` načíst sadu jazyků a hlasy tak, aby odpovídaly přeloženého textu zpátky na řeč k dispozici.

Klienta můžete současně načíst více sad zadáním čárkou oddělený seznam voleb. Například, `scope=speech,text,tts`.

Úspěšná odpověď je objekt JSON s vlastností pro každou požadovaná nastavení.

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

Vzhledem k tomu může klient používat `scope` parametr dotazu, který nastaví podporované jazyky vybrat by měla být vrácena, skutečné odpovědi může obsahovat pouze podmnožinu všech vlastností, které jsou uvedené výše.

Zadaná hodnota s každou vlastnost je následujícím způsobem.

### <a name="speech-to-text-speech"></a>Převod řeči na text (řeči)

Hodnota přidružená vlastnost speech to text `speech`, je slovník (klíče, hodnota) dvojice. Každý klíč identifikuje podporován pro převod řeči na text. Klíč je identifikátor tohoto klienta předá do rozhraní API. Hodnotu přiřazenou klíči je objekt s následujícími vlastnostmi:

* `name`: Zobrazovaný název jazyka.
* `language`: Značka jazyka přidruženého napsané jazyka. Níže naleznete v tématu "Transakce Text".
Příkladem je:

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Text translation (text)

Hodnota přidružená k `text` vlastnost je také slovníku, kde každý klíč identifikuje jazyk pro překlad textu podporovány. Hodnotu přiřazenou klíči popisuje jazyk:

* `name`: Zobrazovaný název jazyka.
* `dir`: Text, který je `rtl` pro jazyky zprava doleva nebo `ltr` jazyků zleva doprava.

Příkladem je:

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Převod textu na řeč

Hodnota přidružená vlastnost převod textu na řeč, převodu textu na řeč, je také slovník, ve kterém každý klíč identifikuje podporované hlasový vstup. Atributy objektu hlasové jsou:

* `displayName`: Zobrazovaný název hlasu.
* `gender`: Pohlaví hlasu (kolík nebo Ženský).
* `locale`: Značka jazyka hlasu podznačku primárního jazyka a oblasti podznačku.
* `language`: Značka jazyka přidruženého napsané jazyka.
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
Tato služba vrátí všechny názvy v jazyce, který hlavičku 'Accept-Language' pro všechny jazyky podporované v překlady textů.

### <a name="response-class-status-200"></a>Třída odpovědi (stav 200)
Objekt, který popisuje sadu podporované jazyky.

Hodnota ModelExample: 

Langagues {řeči (object, volitelné), text (object, volitelné), převod textu na řeč (object, volitelné)}

### <a name="headers"></a>Hlavičky

|Hlavička|Popis|Type|
:--|:--|:--|
X-RequestId|Hodnota generován serverem k identifikaci žádosti a používá pro účely odstraňování potíží.|řetězec|

### <a name="parameters"></a>Parametry

|Parametr|Popis|Typ parametru|Typ dat|
|:--|:--|:--|:--|
|verze API-version    |Verze rozhraní API požadovaná klientem. Povolené hodnoty jsou: `1.0`.|query|řetězec|
|scope  |Nastaví podporované jazyky nebo hlasy vrácena klientovi. Tento parametr zadán jako čárkou oddělený seznam klíčových slov. Následující klíčová slova jsou k dispozici:<ul><li>`speech`: Poskytuje sadu jazyky podporované pro přepisy řeči.</li><li>`tts`: Poskytuje sadu podporuje pro převod textu na řeč.</li><li>`text`: Poskytuje sadu jazyky podporované pro překlad textu.</li></ul>Pokud není zadána hodnota, hodnota `scope` výchozí hodnota je `text`.|query|řetězec|
|X-ClientTraceId    |Identifikátor GUID klientem generovaná použít ke sledování požadavku. Usnadňuje řešení potíží se musí klienti zadejte novou hodnotu s každou žádostí a zaznamenejte ho.|záhlaví|řetězec|
|Přijměte jazyka    |Některá pole v odpovědi jsou názvy jazyků nebo oblastech. Použijte tento parametr se definuje jazyk, ve kterém se vrátí názvy. Jazyk je zadán zadáním značky jazyka ve správném formátu BCP 47. Vyberte značku ze seznamu identifikátorů jazyka se vrátil s `text` oboru. U nepodporovaných jazyků názvy jsou k dispozici v angličtině.<br/>Například použijte hodnotu `fr` žádosti názvy ve francouzštině, nebo použijte hodnotu `zh-Hant` požadavek názvy v tradiční čínštině.|záhlaví|řetězec|
    
### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|
|:--|:--|
|400|Chybný požadavek. Zkontrolujte vstupní parametry k zajištění, že jsou platné. Objekt odpovědi obsahuje podrobnější popis chyby.|
|429|Příliš mnoho požadavků.|
|500|Došlo k chybě. Pokud potíže potrvají, zprávu s identifikátorem trasování klienta (X-ClientTraceId) nebo identifikátor (X-RequestId) žádosti.|
|503|Server je dočasně nedostupný. Zkuste prosím požadavek. Pokud potíže potrvají, zprávu s identifikátorem trasování klienta (X-ClientTraceId) nebo identifikátor (X-RequestId) žádosti.|
