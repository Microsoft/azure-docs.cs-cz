---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.openlocfilehash: d91082ae3c0ae5e501675a06b02e2f55f20ce236
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399569"
---
## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte účet Azure a předplatné služby Rozpoznávání řeči. Pokud nemáte účet a předplatné, [vyzkoušejte bezplatnou službu Řeč](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Než budete moci něco udělat, budete muset nainstalovat <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">sadu <span class="docon docon-navigate-external x-hidden-focus"> </span>JavaScript Speech SDK </a>. V závislosti na platformě postupujte podle následujících pokynů:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js<span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webový prohlížeč<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Navíc v závislosti na cílovém prostředí použijte jednu z následujících možností:

# <a name="import"></a>[Import](#tab/import)

```javascript
import { readFileSync } from "fs";
import {
    AudioConfig,
    SpeechConfig,
    SpeechSynthesisOutputFormat,
    SpeechSynthesizer 
} from "microsoft-cognitiveservices-speech-sdk";
```

Další informace `import`naleznete v tématu <a href="https://javascript.info/import-export" target="_blank">Export a import <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

# <a name="require"></a>[Vyžadují](#tab/require)

```javascript
const readFileSync = require("fs").readFileSync;
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Další informace `require`o tématu naleznete v tématu <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Co je potřeba? <span class="docon docon-navigate-external x-hidden-focus"></span></a>.


# <a name="script"></a>[Skript](#tab/script)

Stáhněte a extrahujte sadu <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">JavaScript Speech <span class="docon docon-navigate-external x-hidden-focus"></span> SDK</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* a umístěte ji do složky přístupné souboru HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Pokud cílíte na webový prohlížeč `<script>` a používáte značku; `sdk` předpona není potřeba. Předpona `sdk` je alias používaný `require` k pojmenování modulu.

---

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Chcete-li volat službu Řeč pomocí sady Speech [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)SDK, je třeba vytvořit . Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token.

> [!NOTE]
> Bez ohledu na to, zda provádíte rozpoznávání řeči, syntézu řeči, překlad nebo záměr rozpoznávání, budete vždy vytvořit konfiguraci.

Existuje několik způsobů, jak můžete inicializovat [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest):

* S předplatným: předat klíč a přidružené oblasti.
* S koncovým bodem: předají v koncovém bodě služby Řeči. Klíč nebo autorizační token je volitelný.
* S hostitelem: předat adresu hostitele. Klíč nebo autorizační token je volitelný.
* S tokenem autorizace: předavte autorizační token a přidruženou oblast.

V tomto příkladu [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) vytvoříte pomocí klíče předplatného a oblasti. Na stránce [podpory oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) najdete identifikátor oblasti. Můžete také vytvořit některé základní často používaný kód pro zbytek tohoto článku, který upravíte pro různé vlastní nastavení.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>Syntetizovat řeč do souboru

Dále vytvoříte [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) objekt, který provede převody převodů převodů převodů na řeč a výstupy do reproduktorů, souborů nebo jiných výstupních datových proudů. Přijímá [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) jako params [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) objekt vytvořený v předchozím kroku [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) a objekt, který určuje, jak by měly být zpracovány výstupní výsledky.

Chcete-li začít, vytvořte pro `AudioConfig` `.wav` automatické zápis `fromAudioFileOutput()` výstupu do souboru pomocí statické funkce.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

Dále vytvořte instanci `SpeechSynthesizer` `speechConfig` procházející objekt `audioConfig` a objekt jako params. Potom provádění syntézy řeči a zápisu do `speakTextAsync()` souboru je stejně jednoduché jako spuštění s řetězcem textu. Výsledek zpětného volání je skvělé `synthesizer.close()`místo pro volání , ve skutečnosti - toto volání je potřeba, aby syntéza fungovat správně.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        }
    },
    error => {
        console.log(error);
        synthesizer.close();
    });
}
```

Spusťte program a syntetizovaný `.wav` soubor se zapíše do zadaného umístění. Toto je dobrý příklad nejzákladnější využití, ale dále se podíváte na přizpůsobení výstupu a zpracování výstupní odezvy jako datový proud v paměti pro práci s vlastní scénáře.

## <a name="synthesize-to-speaker-output"></a>Syntetizovat výstup reproduktoru

V některých případech můžete chtít přímo výstup syntetizované řeči přímo do reproduktoru. Chcete-li to provést, `AudioConfig` vytvořte `fromDefaultSpeakerOutput()` konkretizovat pomocí statické funkce. To výstupy na aktuální aktivní výstupní zařízení.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Získat výsledek jako datový proud v paměti

Pro mnoho scénářů ve vývoji aplikace řeči pravděpodobně budete potřebovat výsledná zvuková data jako datový proud v paměti, nikoli přímo zapisovat do souboru. To vám umožní vytvořit vlastní chování, včetně:

* Abstrahujte výsledné bajtové pole jako datový proud, který lze hledat pro vlastní služby příjem dat.
* Integrujte výsledek s jinými rozhraními API nebo službami.
* Upravte zvuková data, napište vlastní `.wav` záhlaví atd.

Je to jednoduché provést tuto změnu z předchozího příkladu. Nejprve odstraňte `AudioConfig` blok, protože budete spravovat chování výstupu ručně od tohoto okamžiku pro větší kontrolu. Pak `undefined` předat `AudioConfig` pro `SpeechSynthesizer` v konstruktoru. 

> [!NOTE]
> Předávání `undefined` pro `AudioConfig`, spíše než vynechat jako v příkladu výstupu reproduktoru výše, nebude přehrávat zvuk ve výchozím nastavení na aktuální aktivní výstupní zařízení.

Tentokrát uložíte výsledek do [`SpeechSynthesisResult`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult?view=azure-node-latest) proměnné. Vlastnost `SpeechSynthesisResult.audioData` vrátí `ArrayBuffer` výstupní data. S tímto `ArrayBuffer` úkolem můžete pracovat ručně.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Odtud můžete implementovat jakékoli vlastní chování `ArrayBuffer` pomocí výsledného objektu.

## <a name="customize-audio-format"></a>Přizpůsobení formátu zvuku

V následující části je uvedeno, jak přizpůsobit atributy zvukového výstupu, včetně:

* Typ zvukového souboru
* Vzorkovací frekvence
* Bitová hloubka

Chcete-li změnit formát zvuku, `speechSynthesisOutputFormat` použijte `SpeechConfig` vlastnost objektu. Tato vlastnost očekává `enum` typ [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest), který slouží k výběru výstupního formátu. [Seznam dostupných zvukových formátů](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest) naleznete v referenčních dokumentech.

Existují různé možnosti pro různé typy souborů v závislosti na vašich požadavcích. Všimněte si, že z `Raw24Khz16BitMonoPcm` definice nezpracované formáty jako neobsahují zvukové hlavičky. Nezpracované formáty používejte pouze v případě, že víte, že vaše implementace navazujících dat může dekódovat nezpracovaný bitový tok, nebo pokud plánujete ruční vytváření záhlaví na základě bitové hloubky, vzorkovací rychlosti, počtu kanálů atd.

V tomto příkladu určíte formát `Riff24Khz16BitMonoPcm` RIFF s vysokou `speechSynthesisOutputFormat` věrností nastavením na objektu. `SpeechConfig` Podobně jako v předchozím příkladu získáte `ArrayBuffer` zvuková data a pracujte s nimi.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Spuštění programu znovu zapíše `.wav` soubor do zadané cesty.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Přizpůsobení charakteristik řeči pomocí ssml

Jazyk s poznámkami pro syntézu řeči (SSML) umožňuje doladit výšku, výslovnost, rychlost mluvení, hlasitost a další informace o výstupu převodu textu na řeč odesláním požadavků ze schématu XML. Tato část ukazuje několik příkladů praktické použití, ale podrobnější průvodce naleznete v [článku s návody SSML](../../../speech-synthesis-markup.md).

Chcete-li začít používat SSML pro přizpůsobení, provedete jednoduchou změnu, která přepne hlas.
Nejprve vytvořte nový soubor XML pro konfiguraci SSML v `ssml.xml`adresáři kořenového projektu v tomto příkladu . Kořenový prvek `<speak>`je vždy a zabalení textu v elementu `<voice>` `name` umožňuje změnit hlas pomocí paramu. Tento příklad změní hlas na mužský hlas angličtiny (UK). Všimněte si, že tento hlas je **standardní** hlas, který má různé ceny a dostupnost než **nervové** hlasy. Podívejte se na [úplný seznam](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices) podporovaných **standardních** hlasů.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Dále je třeba změnit požadavek na syntézu řeči tak, aby odkazoval na soubor XML. Požadavek je většinou stejný, ale `speakTextAsync()` místo použití `speakSsmlAsync()`funkce použijete . Tato funkce očekává řetězec XML, takže nejprve vytvoříte funkci pro načtení souboru XML a jeho vrácení jako řetězec.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

Další informace `readFileSync`naleznete v <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">souborovém<span class="docon docon-navigate-external x-hidden-focus"></span>systému Node.js</a>. Odtud je výsledný objekt přesně stejný jako předchozí příklady.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Výstup funguje, ale existuje několik jednoduchých dalších změn, které můžete udělat, aby to znělo přirozeněji. Celková rychlost mluvení je příliš rychlá, takže přidáme `<prosody>` značku a snížíme rychlost na **90%** výchozí rychlosti. Kromě toho pauza po čárce ve větě je trochu příliš krátká a nepřirozené znějící. Chcete-li tento problém `<break>` vyřešit, přidejte značku zpoždění řeči a nastavte čas param na **200ms**. Znovu spusťte syntézu a zjistěte, jak tato vlastní nastavení ovlivnila výstup.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Nervové hlasy

Nervové hlasy jsou algoritmy syntézy řeči poháněné hlubokými neuronovými sítěmi. Při použití nervového hlasu je syntetizovaná řeč téměř k nerozeznání od lidských nahrávek. S lidskou přirozenou prozodomií a jasnou artikulací slov nervové hlasy výrazně snižují únavu z poslechu, když uživatelé interagují se systémy AI.

Chcete-li přepnout na `name` neurální hlas, změňte na jednu z [možností nervového hlasu](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices). Potom přidejte obor názvů `mstts`XML pro aplikaci `<mstts:express-as>` a zalomte text do značky. Použijte `style` param přizpůsobit styl mluvení. Tento příklad `cheerful`používá , ale `customerservice` `chat` zkuste jej nastavit nebo zobrazit rozdíl ve stylu mluvení.

> [!IMPORTANT]
> Neurální hlasy jsou podporovány **pouze** pro prostředky řeči vytvořené v oblastech *východní USA*, *jihovýchodní Asie*a *západní Evropy.*

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
    xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
