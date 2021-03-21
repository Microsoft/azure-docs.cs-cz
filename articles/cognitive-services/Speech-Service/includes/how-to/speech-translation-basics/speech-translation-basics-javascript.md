---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 07/14/2020
ms.author: v-demjoh
ms.custom: devx-track-js
ms.openlocfilehash: 58faca3d3b1b3a190432e2000d8619fcac1712d8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102445540"
---
Jednou ze základních funkcí služby Speech je schopnost rozpoznávat lidské rozpoznávání a překládat ho do jiných jazyků. V tomto rychlém startu se naučíte používat sadu Speech SDK ve vašich aplikacích a produktech k provádění vysoce kvalitního překladu řeči. Tento rychlý Start popisuje témata, včetně:

* Převod řeči na text
* Překlad řeči na více cílových jazyků
* Přímá překlady řeči na řeč

## <a name="skip-to-samples-on-github"></a>Přeskočit na ukázky na GitHubu

Pokud chcete přeskočit přímý na vzorový kód, přečtěte si [ukázky rychlý Start JavaScriptu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/translate-speech-to-text) na GitHubu.

## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Předtím, než můžete cokoli udělat, musíte nainstalovat <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">sadu Speech SDK pro JavaScript </a>. V závislosti na vaší platformě postupujte podle následujících pokynů:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webový prohlížeč </a>

V závislosti na cílovém prostředí navíc použijte jednu z následujících možností:

# <a name="script"></a>[pravidel](#tab/script)

Stáhněte a extrahujte <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">sadu Speech SDK pro JavaScript</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* soubor a umístěte ji do složky přístupné pro váš soubor HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Pokud cílíte na webový prohlížeč a použijete `<script>` značku; `sdk` Předpona není nutná. `sdk`Předpona je alias použitý k pojmenování `require` modulu.

# <a name="import"></a>[importovat](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Další informace o najdete v `import` tématu <a href="https://javascript.info/import-export" target="_blank">Export a import </a>.

# <a name="require"></a>[žádá](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Další informace o najdete v `require` tématu <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">co je potřeba? </a>.

---

## <a name="create-a-translation-configuration"></a>Vytvořit konfiguraci překladu

Chcete-li volat službu překladu pomocí sady Speech SDK, je nutné vytvořit [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) . Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token.

> [!NOTE]
> Bez ohledu na to, jestli provádíte rozpoznávání řeči, syntézu řeči, překlad nebo rozpoznávání záměrů, vždy vytvoříte konfiguraci.
Existuje několik způsobů, jak můžete inicializovat [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) :

* S předplatným: předejte klíč a přidruženou oblast.
* S koncovým bodem: předejte koncový bod služby řeči. Klíč nebo autorizační token jsou volitelné.
* S hostitelem: předejte adresu hostitele. Klíč nebo autorizační token jsou volitelné.
* Pomocí autorizačního tokenu: předejte autorizační token a přidruženou oblast.

Pojďme se podívat, jak [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) se vytvoří pomocí klíče a oblasti. Tyto přihlašovací údaje můžete získat podle kroků v [části Vyzkoušejte si službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

```javascript
const speechTranslationConfig = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-translator"></a>Inicializovat překladatele

Po vytvoření [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) je dalším krokem inicializace [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) . Když inicializujete [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) , budete ho muset předat `speechTranslationConfig` . To poskytuje přihlašovací údaje, které služba překladu vyžaduje k ověření vaší žádosti.

Pokud překládáte řeč poskytované prostřednictvím výchozího mikrofonu vašeho zařízení, [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) mělo by to vypadat takto:

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Pokud chcete zadat vstupní zvukové zařízení, budete muset vytvořit [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) a zadat `audioConfig` parametr při inicializaci [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) .

> [!TIP]
> Přečtěte si, [Jak získat ID zařízení pro vstupní zvukové zařízení](../../../how-to-select-audio-input-devices.md).
Odkazujte na `AudioConfig` objekt následujícím způsobem:

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

Pokud chcete místo používání mikrofonu zadat zvukový soubor, budete ho muset ještě zadat `audioConfig` . To však lze provést pouze v případě cílení na **Node.js** a při vytváření [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) namísto volání `fromDefaultMicrophoneInput` volání `fromWavFileOutput` a předání `filename` parametru.

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

## <a name="translate-speech"></a>Překlad řeči

[Třída TranslationRecognizer](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) pro sadu Speech SDK pro JavaScript zpřístupňuje několik metod, které můžete použít pro překlad řeči.

* Překlad s jednou proložením (Async) – provádí převod v neblokujícím (asynchronním) režimu. Tím se přeloží jeden utterance. Konec jednoho utterance se určuje tak, že naslouchá tichému ukončení na konci nebo dokud se nezpracovává po dobu 15 sekund zvuku.
* Průběžný překlad (asynchronní) – asynchronně iniciuje operaci průběžného překladu. Uživatel zaregistruje události do událostí a zpracuje různé stavy aplikací. Pro zastavení asynchronního průběžného překladu volejte [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#stopcontinuousrecognitionasync) .

> [!NOTE]
> Přečtěte si další informace o tom, jak [zvolit režim rozpoznávání řeči](../../../get-started-speech-to-text.md).
### <a name="specify-a-target-language"></a>Zadejte cílový jazyk.

Chcete-li přeložit, je nutné zadat zdrojový jazyk a alespoň jeden cílový jazyk.
Můžete zvolit zdrojový jazyk pomocí národního prostředí uvedeného v [tabulce překladu řeči](../../../language-support.md#speech-translation). Možnosti pro přeložený jazyk najdete na stejném odkazu. Možnosti pro cílové jazyky se liší v případě, že chcete zobrazit text nebo chcete slyšet syntetizované rozpoznávání řeči. Chcete-li přeložit z angličtiny na němčinu, upravte objekt konfigurace překladu:

```javascript
speechTranslationConfig.speechRecognitionLanguage = "en-US";
speechTranslationConfig.addTargetLanguage("de");
```

### <a name="single-shot-recognition"></a>Rozpoznávání s jedním záběrem

Tady je příklad asynchronního jednorázového překladu pomocí [`recognizeOnceAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognizeonceasync) :

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Pro zpracování výsledku budete muset napsat nějaký kód. Tato ukázka vyhodnocuje [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognitionresult) pro překlad do němčiny:

```javascript
recognizer.recognizeOnceAsync(
  function (result) {
    let translation = result.translations.get("de");
    window.console.log(translation);
    recognizer.close();
  },
  function (err) {
    window.console.log(err);
    recognizer.close();
});
```

Váš kód může také zpracovávat aktualizace poskytované během zpracování překladu.
Pomocí těchto aktualizací můžete poskytnout vizuální zpětnou vazbu k průběhu překladu.
Podívejte se na [Tento JavaScript Node.js příklad](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/js/node/translation.js) pro vzorový kód, který zobrazuje aktualizace poskytované během procesu překladu. Následující kód obsahuje také informace vytvářené během procesu překladu.

```javascript
recognizer.recognizing = function (s, e) {
    var str = ("(recognizing) Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " +  e.result.text +
            " Translation:");
    str += e.result.translations.get("de");
    console.log(str);
};
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " + e.result.text +
            " Translation:";
    str += e.result.translations.get("de");
    str += "\r\n";
    console.log(str);
};
```

### <a name="continuous-translation"></a>Průběžný překlad

Průběžný překlad je trochu větší než rozpoznávání s jedním proložením. Pro získání výsledků rozpoznávání se vyžaduje přihlášení k odběru `recognizing` `recognized` událostí, a `canceled` . Chcete-li zastavit převod, je nutné zavolat [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#stopcontinuousrecognitionasync) . Tady je příklad toho, jak se průběžné překlady provádí na vstupním souboru zvukového vstupu.

Pojďme začít definováním vstupu a inicializací [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) :

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Přihlásíme se k odběru událostí odeslaných z [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) .

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognizing): Signál pro události obsahující mezilehlé výsledky překladu.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognized): Signál pro události obsahující konečné výsledky překladu (indikující úspěšný pokus o překlad).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#sessionstopped): Signál pro události indikující konec relace překladu (operace).
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#canceled): Signál pro události obsahující zrušené výsledky překladu (indikující pokus o převod, který se zrušil v důsledku výsledku nebo přímý požadavek na zrušení nebo případně i přenos nebo selhání protokolu).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`TRANSLATING: Text=${e.result.text}`);
};
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`TRANSLATED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be translated.");
    }
};
recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);
    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }
    recognizer.stopContinuousRecognitionAsync();
};
recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

S nastavením všeho můžeme zavolat [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync) .

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();
// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

## <a name="choose-a-source-language"></a>Zvolit zdrojový jazyk

Běžným úkolem překladu řeči je zadání vstupního (nebo zdrojového) jazyka. Pojďme se podívat, jak byste změnili vstupní jazyk na italštinu. V kódu Najděte svůj kód [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) a přidejte následující řádek přímo pod něj.

```javascript
speechTranslationConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig#speechrecognitionlanguage)Vlastnost očekává řetězec formátu národního prostředí jazyka. Můžete zadat libovolnou hodnotu ve sloupci **locale (národní prostředí** ) v seznamu podporovaných [národních prostředí a jazyků](../../../language-support.md).

## <a name="choose-one-or-more-target-languages"></a>Vyberte jeden nebo více cílových jazyků

Sada Speech SDK umožňuje paralelní převod na více cílových jazyků. Dostupné cílové jazyky se poněkud liší od seznamu zdrojového jazyka a místo národního prostředí určíte cílové jazyky pomocí kódu jazyka.
Seznam kódů jazyka pro textové cíle v [tabulce překladu řeči najdete na stránce Podpora jazyků](../../../language-support.md#speech-translation). Můžete také vyhledat podrobnosti o překladu na syntetizované jazyky.

Následující kód přidá němčinu jako cílový jazyk:

```javascript
translationConfig.addTargetLanguage("de");
```

Vzhledem k tomu, že je možné použít více překladů cílového jazyka, váš kód musí při zkoumání výsledku určit cílový jazyk. Následující kód získá výsledky překladu pro němčinu.

```javascript
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " +
            sdk.ResultReason[e.result.reason] +
            " Text: " + e.result.text + " Translations:";
    var language = "de";
    str += " [" + language + "] " + e.result.translations.get(language);
    str += "\r\n";
    // show str somewhere
};
```
