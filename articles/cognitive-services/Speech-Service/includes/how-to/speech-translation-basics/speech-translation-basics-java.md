---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: 891276f7a37bfb2397101388f430a297bdca0a21
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445676"
---
Jednou ze základních funkcí služby Speech je schopnost rozpoznávat lidské rozpoznávání a překládat ho do jiných jazyků. V tomto rychlém startu se naučíte používat sadu Speech SDK ve vašich aplikacích a produktech k provádění vysoce kvalitního překladu řeči. Tento rychlý Start popisuje témata, včetně:

* Převod řeči na text
* Překlad řeči na více cílových jazyků
* Přímá překlady řeči na řeč

## <a name="skip-to-samples-on-github"></a>Přeskočit na ukázky na GitHubu

Pokud chcete přeskočit přímý na vzorový kód, přečtěte si [ukázky pro rychlý Start Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre/translate-speech-to-text) na GitHubu.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Předtím, než můžete cokoli udělat, musíte nainstalovat sadu Speech SDK. V závislosti na vaší platformě postupujte podle pokynů v části <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">získání sady Speech SDK</a> tématu _o sadě Speech SDK_ .

## <a name="import-dependencies"></a>Importovat závislosti

Chcete-li spustit příklady v tomto článku, zahrňte do `import` horní části stránky * následující příkazy *.* Soubor kódu Java.

```java
package speech;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.*;
import com.microsoft.cognitiveservices.speech.translation.*;
```

## <a name="sensitive-data-and-environment-variables"></a>Citlivá data a proměnné prostředí

Ukázkový zdrojový kód v tomto článku závisí na proměnných prostředí pro ukládání citlivých dat, jako je klíč a oblast předplatného prostředku pro rozpoznávání řeči. Soubor kódu Java obsahuje dvě `static final String` hodnoty, které jsou přiřazené z proměnných prostředí hostitelských počítačů, konkrétně `SPEECH__SUBSCRIPTION__KEY` a `SPEECH__SERVICE__REGION` . Obě tato pole jsou v oboru třídy, takže jsou přístupné v rámci těla metod třídy. Další informace o proměnných prostředí naleznete v tématu [proměnné prostředí a konfigurace aplikace](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SUBSCRIPTION__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) { }
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

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SERVICE__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) {
        try {
            translateSpeech();
            System.exit(0);
        } catch (Exception ex) {
            System.out.println(ex);
            System.exit(1);
        }
    }

    static void translateSpeech() {
        SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription(
            SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Změnit jazyk zdroje

Jedním z běžných úloh překladu řeči je zadání vstupu (nebo zdrojového) jazyka. Pojďme se podívat, jak byste změnili vstupní jazyk na italštinu. V kódu komunikujte s [`SpeechTranslationConfig`][config] instancí, a to voláním `setSpeechRecognitionLanguage` metody.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    // Source (input) language
    translationConfig.setSpeechRecognitionLanguage("it-IT");
}
```

[`setSpeechRecognitionLanguage`][recognitionlang]Funkce očekává řetězec formátu národního prostředí jazyka. Můžete zadat libovolnou hodnotu ve sloupci **locale (národní prostředí** ) v seznamu podporovaných [národních prostředí a jazyků](../../../language-support.md).

## <a name="add-translation-language"></a>Přidat jazyk překladu

Dalším běžným úkolem překladu řeči je určit cílový jazyk překladu, minimálně jeden je povinný, ale podporují se násobení. Následující fragment kódu nastaví francouzštinu i němčinu jako cíle jazyka překladu.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    translationConfig.setSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.addTargetLanguage("fr");
    translationConfig.addTargetLanguage("de");
}
```

Při každém volání na [`addTargetLanguage`][addlang] je určen nový cílový jazyk překladu. Jinými slovy, pokud je rozpoznávání řeči rozpoznáno ze zdrojového jazyka, je každý cílový překlad k dispozici jako součást výsledné operace překladu.

## <a name="initialize-a-translation-recognizer"></a>Inicializovat Nástroj pro rozpoznávání překladu

Po vytvoření [`SpeechTranslationConfig`][config] je dalším krokem inicializace [`TranslationRecognizer`][recognizer] . Když inicializujete [`TranslationRecognizer`][recognizer] , budete ho muset předat `translationConfig` . Objekt konfigurace poskytuje přihlašovací údaje, které služba Speech vyžaduje k ověření vaší žádosti.

Pokud rozpoznávání řeči rozpoznáte pomocí výchozího mikrofonu vašeho zařízení, [`TranslationRecognizer`][recognizer] mělo by to vypadat takto:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
    }
}
```

Pokud chcete zadat vstupní zvukové zařízení, budete muset vytvořit [`AudioConfig`][audioconfig] a zadat `audioConfig` parametr při inicializaci [`TranslationRecognizer`][recognizer] .

> [!TIP]
> Přečtěte si, [Jak získat ID zařízení pro vstupní zvukové zařízení](../../../how-to-select-audio-input-devices.md).

Nejprve na objekt odkazujete následujícím `AudioConfig` způsobem:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

Pokud chcete místo používání mikrofonu zadat zvukový soubor, budete ho muset ještě zadat `audioConfig` . Pokud však vytvoříte [`AudioConfig`][audioconfig] místo volání, `fromDefaultMicrophoneInput` zavoláte `fromWavFileInput` a předáte `filename` parametr.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

## <a name="translate-speech"></a>Překlad řeči

K překladu řeči sada Speech SDK závisí na mikrofonu nebo na vstupu zvukového souboru. K rozpoznávání řeči dojde před překladem řeči. Po inicializaci všech objektů zavolejte funkci rozpoznávání – jednou a získejte výsledek.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                System.out.printf("Translated into '%s': %s\n", pair.getKey(), pair.getValue());
            }
        }
    }
}
```

Další informace o převodu řeči na text najdete v tématu [základy rozpoznávání řeči](../../../get-started-speech-to-text.md).

## <a name="synthesize-translations"></a>Překlady pro syntetizátory

Po úspěšném rozpoznání a překladu řeči výsledek obsahuje všechny překlady ve slovníku. [`getTranslations`][translations]Funkce vrátí slovník s klíčem jako cílovým jazykem překladu a hodnotou je přeložený text. Rozpoznané rozpoznávání řeči je možné přeložit a pak syntetizovat v jiném jazyce (převod řeči na řeč).

### <a name="event-based-synthesis"></a>Shrnutí založené na událostech

`TranslationRecognizer`Objekt zpřístupňuje `synthesizing` událost. Událost je několikrát aktivována a poskytuje mechanismus pro načtení syntetizového zvuku z výsledku rozpoznávání překladu. Pokud překládáte do více jazyků, přečtěte si téma [Ruční syntéza](#manual-synthesis). Pomocí přiřazení [`setVoiceName`][voicename] a poskytněte obslužné rutiny události pro `synthesizing` událost a Stáhněte si zvuk. V následujícím příkladu se přeložený zvuk uloží jako soubor *. wav* .

> [!IMPORTANT]
> Syntéza založená na událostech funguje pouze s jedním překladem **,** nepřidává více cílových jazyků překladu. Kromě toho [`setVoiceName`][voicename] by měl být stejný jazyk jako cílový jazyk překladu, například; `"de"` může namapovat na `"de-DE-Hedda"` .

```java
static void translateSpeech() throws ExecutionException, FileNotFoundException, InterruptedException, IOException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    String fromLanguage = "en-US";
    String toLanguage = "de";
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    translationConfig.addTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.setVoiceName("de-DE-Hedda");

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        recognizer.synthesizing.addEventListener((s, e) -> {
            byte[] audio = e.getResult().getAudio();
            int size = audio.length;
            System.out.println("Audio synthesized: " + size + " byte(s)" + (size == 0 ? "(COMPLETE)" : ""));

            if (size > 0) {
                try (FileOutputStream file = new FileOutputStream("translation.wav")) {
                    file.write(audio);
                } catch (IOException ex) {
                    ex.printStackTrace();
                }
            }
        });

        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);
            }
        }
    }
}
```

### <a name="manual-synthesis"></a>Ruční syntéza

[`getTranslations`][translations]Funkce vrátí slovník, který lze použít k syntetizování zvuku z textu překladu. Iterujte každým překladem a syntetizujte tento překlad. Při vytváření `SpeechSynthesizer` instance `SpeechConfig` musí objekt mít [`setSpeechSynthesisVoiceName`][speechsynthesisvoicename] vlastnost nastavenou na požadovaný hlas. Následující příklad se přeloží na pět jazyků a každý překlad se pak syntetizuje do zvukového souboru v odpovídajícím neuronové jazyce.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
            Map<String, String> languageToVoiceMap = new HashMap<String, String>();
            languageToVoiceMap.put("de", "de-DE-KatjaNeural");
            languageToVoiceMap.put("en", "en-US-AriaNeural");
            languageToVoiceMap.put("it", "it-IT-ElsaNeural");
            languageToVoiceMap.put("pt", "pt-BR-FranciscaNeural");
            languageToVoiceMap.put("zh-Hans", "zh-CN-XiaoxiaoNeural");

            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);

                SpeechConfig speechConfig =
                    SpeechConfig.fromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
                speechConfig.setSpeechSynthesisVoiceName(languageToVoiceMap.get(language));

                AudioConfig audioConfig = AudioConfig.fromWavFileOutput(language + "-translation.wav");
                try (SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig)) {
                    synthesizer.SpeakTextAsync(translation).get();
                }
            }
        }
    }
}
```

Další informace o syntézě řeči najdete [v tématu Základy funkce pro syntézu řeči](../../../get-started-text-to-speech.md).

[config]: /java/api/com.microsoft.cognitiveservices.speech.translation.SpeechTranslationConfig
[audioconfig]: /java/api/com.microsoft.cognitiveservices.speech.audio.AudioConfig
[recognizer]: /java/api/com.microsoft.cognitiveservices.speech.translation.TranslationRecognizer
[recognitionlang]: /java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage
[addlang]: /java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.addtargetlanguage
[translations]: /java/api/com.microsoft.cognitiveservices.speech.translation.translationrecognitionresult.gettranslations
[voicename]: /java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.setvoicename
[speechsynthesisvoicename]: /java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechsynthesisvoicename