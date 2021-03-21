---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 5efe6e9cf3227857300d649a8c2ecb4973627335
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102444582"
---
Jednou ze základních funkcí služby Speech je schopnost rozpoznávat lidské rozpoznávání a překládat ho do jiných jazyků. V tomto rychlém startu se naučíte používat sadu Speech SDK ve vašich aplikacích a produktech k provádění vysoce kvalitního překladu řeči. Tento rychlý Start popisuje témata, včetně:

* Převod řeči na text
* Překlad řeči na více cílových jazyků
* Přímá překlady řeči na řeč

## <a name="skip-to-samples-on-github"></a>Přeskočit na ukázky na GitHubu

Pokud chcete přeskočit přímý na vzorový kód, přečtěte si [ukázky pro rychlý Start v C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/translate-speech-to-text) na GitHubu.

## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Předtím, než můžete cokoli udělat, musíte nainstalovat sadu Speech SDK. V závislosti na vaší platformě postupujte podle pokynů v části <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">získání sady Speech SDK</a> tématu _o sadě Speech SDK_ .

## <a name="import-dependencies"></a>Importovat závislosti

Chcete-li spustit příklady v tomto článku, zahrňte do `using` horní části souboru *program. cs* následující příkazy.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Citlivá data a proměnné prostředí

Ukázkový zdrojový kód v tomto článku závisí na proměnných prostředí pro ukládání citlivých dat, jako je klíč a oblast předplatného prostředku pro rozpoznávání řeči. `Program`Třída obsahuje dvě `static readonly string` hodnoty, které jsou přiřazeny z proměnných prostředí hostitelských počítačů, konkrétně `SPEECH__SUBSCRIPTION__KEY` a `SPEECH__SERVICE__REGION` . Obě tato pole jsou v oboru třídy, takže jsou přístupné v rámci těla metod třídy. Další informace o proměnných prostředí naleznete v tématu [proměnné prostředí a konfigurace aplikace](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => Task.CompletedTask;
}
```

## <a name="create-a-speech-translation-configuration"></a>Vytvoření konfigurace překladu řeči

Chcete-li volat službu Speech pomocí sady Speech SDK, je třeba vytvořit [`SpeechTranslationConfig`][config] . Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token.

> [!TIP]
> Bez ohledu na to, jestli provádíte rozpoznávání řeči, syntézu řeči, překlad nebo rozpoznávání záměrů, vždy vytvoříte konfiguraci.

Existuje několik způsobů, jak můžete inicializovat [`SpeechTranslationConfig`][config] :

* S předplatným: předejte klíč a přidruženou oblast.
* S koncovým bodem: předejte koncový bod služby řeči. Klíč nebo autorizační token jsou volitelné.
* S hostitelem: předejte adresu hostitele. Klíč nebo autorizační token jsou volitelné.
* Pomocí autorizačního tokenu: předejte autorizační token a přidruženou oblast.

Pojďme se podívat, jak [`SpeechTranslationConfig`][config] se vytvoří pomocí klíče a oblasti. Tyto přihlašovací údaje můžete získat podle kroků v [části Vyzkoušejte si službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => TranslateSpeechAsync();

    static async Task TranslateSpeechAsync()
    {
        var translationConfig =
            SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Změnit jazyk zdroje

Jedním z běžných úloh překladu řeči je zadání vstupu (nebo zdrojového) jazyka. Pojďme se podívat, jak byste změnili vstupní jazyk na italštinu. Ve vašem kódu můžete pracovat s [`SpeechTranslationConfig`][config] instancí a přiřazovat k `SpeechRecognitionLanguage` Vlastnosti.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

[`SpeechRecognitionLanguage`][recognitionlang]Vlastnost očekává řetězec formátu národního prostředí jazyka. Můžete zadat libovolnou hodnotu ve sloupci **locale (národní prostředí** ) v seznamu podporovaných [národních prostředí a jazyků](../../../language-support.md).

## <a name="add-translation-language"></a>Přidat jazyk překladu

Dalším běžným úkolem překladu řeči je určit cílový jazyk překladu, minimálně jeden je povinný, ale podporují se násobení. Následující fragment kódu nastaví francouzštinu i němčinu jako cíle jazyka překladu.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig.SpeechRecognitionLanguage = "it-IT";
    
    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("de");
}
```

Při každém volání na [`AddTargetLanguage`][addlang] je určen nový cílový jazyk překladu. Jinými slovy, pokud je rozpoznávání řeči rozpoznáno ze zdrojového jazyka, je každý cílový překlad k dispozici jako součást výsledné operace překladu.

## <a name="initialize-a-translation-recognizer"></a>Inicializovat Nástroj pro rozpoznávání překladu

Po vytvoření [`SpeechTranslationConfig`][config] je dalším krokem inicializace [`TranslationRecognizer`][recognizer] . Když inicializujete [`TranslationRecognizer`][recognizer] , budete ho muset předat `translationConfig` . Objekt konfigurace poskytuje přihlašovací údaje, které služba Speech vyžaduje k ověření vaší žádosti.

Pokud rozpoznávání řeči rozpoznáte pomocí výchozího mikrofonu vašeho zařízení, [`TranslationRecognizer`][recognizer] mělo by to vypadat takto:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);
}
```

Pokud chcete zadat vstupní zvukové zařízení, budete muset vytvořit [`AudioConfig`][audioconfig] a zadat `audioConfig` parametr při inicializaci [`TranslationRecognizer`][recognizer] .

> [!TIP]
> Přečtěte si, [Jak získat ID zařízení pro vstupní zvukové zařízení](../../../how-to-select-audio-input-devices.md).

Nejprve na objekt odkazujete následujícím `AudioConfig` způsobem:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

Pokud chcete místo používání mikrofonu zadat zvukový soubor, budete ho muset ještě zadat `audioConfig` . Pokud však vytvoříte [`AudioConfig`][audioconfig] místo volání, `FromDefaultMicrophoneInput` zavoláte `FromWavFileInput` a předáte `filename` parametr.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Překlad řeči

K překladu řeči sada Speech SDK závisí na mikrofonu nebo na vstupu zvukového souboru. K rozpoznávání řeči dojde před překladem řeči. Po inicializaci všech objektů zavolejte funkci rozpoznávání – jednou a získejte výsledek.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");
    
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\":");
        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");
        }
    }
}
```

Další informace o převodu řeči na text najdete v tématu [základy rozpoznávání řeči](../../../get-started-speech-to-text.md).

## <a name="synthesize-translations"></a>Překlady pro syntetizátory

Po úspěšném rozpoznání a překladu řeči výsledek obsahuje všechny překlady ve slovníku. [`Translations`][translations]Klíč slovníku je cílový jazyk překladu a hodnota je přeložený text. Rozpoznané rozpoznávání řeči je možné přeložit a pak syntetizovat v jiném jazyce (převod řeči na řeč).

### <a name="event-based-synthesis"></a>Shrnutí založené na událostech

`TranslationRecognizer`Objekt zpřístupňuje `Synthesizing` událost. Událost je několikrát aktivována a poskytuje mechanismus pro načtení syntetizového zvuku z výsledku rozpoznávání překladu. Pokud překládáte do více jazyků, přečtěte si téma [Ruční syntéza](#manual-synthesis). Pomocí přiřazení [`VoiceName`][voicename] a poskytněte obslužné rutiny události pro `Synthesizing` událost a Stáhněte si zvuk. V následujícím příkladu se přeložený zvuk uloží jako soubor *. wav* .

> [!IMPORTANT]
> Syntéza založená na událostech funguje pouze s jedním překladem **,** nepřidává více cílových jazyků překladu. Kromě toho [`VoiceName`][voicename] by měl být stejný jazyk jako cílový jazyk překladu, například; `"de"` může namapovat na `"de-DE-Hedda"` .

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguage = "de";
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    translationConfig.AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.VoiceName = "de-DE-Hedda";

    using var recognizer = new TranslationRecognizer(translationConfig);

    recognizer.Synthesizing += (_, e) =>
    {
        var audio = e.Result.GetAudio();
        Console.WriteLine($"Audio synthesized: {audio.Length:#,0} byte(s) {(audio.Length == 0 ? "(Complete)" : "")}");

        if (audio.Length > 0)
        {
            File.WriteAllBytes("YourAudioFile.wav", audio);
        }
    };

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{toLanguage}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\"");
        Console.WriteLine($"Translated into '{toLanguage}': {result.Translations[toLanguage]}");
    }
}
```

### <a name="manual-synthesis"></a>Ruční syntéza

[`Translations`][translations]Slovník lze použít k syntetizování zvuku z textu překladu. Iterujte každým překladem a syntetizujte tento překlad. Při vytváření `SpeechSynthesizer` instance `SpeechConfig` musí objekt mít [`SpeechSynthesisVoiceName`][speechsynthesisvoicename] vlastnost nastavenou na požadovaný hlas. Následující příklad se přeloží na pět jazyků a každý překlad se pak syntetizuje do zvukového souboru v odpovídajícím neuronové jazyce.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        var languageToVoiceMap = new Dictionary<string, string>
        {
            ["de"] = "de-DE-KatjaNeural",
            ["en"] = "en-US-AriaNeural",
            ["it"] = "it-IT-ElsaNeural",
            ["pt"] = "pt-BR-FranciscaNeural",
            ["zh-Hans"] = "zh-CN-XiaoxiaoNeural"
        };

        Console.WriteLine($"Recognized: \"{result.Text}\"");

        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");

            var speechConfig =
                SpeechConfig.FromSubscription(
                    SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);
            speechConfig.SpeechSynthesisVoiceName = languageToVoiceMap[language];

            using var audioConfig = AudioConfig.FromWavFileOutput($"{language}-translation.wav");
            using var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
            
            await synthesizer.SpeakTextAsync(translation);
        }
    }
}
```

Další informace o syntézě řeči najdete [v tématu Základy funkce pro syntézu řeči](../../../get-started-text-to-speech.md).

[config]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig
[audioconfig]: /dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig
[recognizer]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer
[recognitionlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage
[addlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage
[translations]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations
[voicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename
[speechsynthesisvoicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename