---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: cb1c6588dc6c770c809b786982ece1c9f0b1b1b5
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108844"
---
V tomto rychlém startu se naučíte běžné vzory návrhu pro provádění syntézy textu na řeč pomocí sady Speech SDK. Začnete tím, že provádíte základní konfiguraci a shrnutí a přejdete k pokročilejším příkladům pro vývoj vlastních aplikací, včetně:

* Získávání odpovědí jako proudů v paměti
* Přizpůsobení výstupní vzorkovací frekvence a přenosové rychlosti
* Odesílání požadavků na syntézu pomocí SSML (Speech syntézy Markup Language)
* Používání hlasů neuronové

## <a name="skip-to-samples-on-github"></a>Přeskočit na ukázky na GitHubu

Pokud chcete přeskočit přímý na vzorový kód, přečtěte si [ukázky pro rychlý Start Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre/text-to-speech) na GitHubu.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Předtím, než můžete cokoli udělat, musíte nainstalovat sadu Speech SDK. V závislosti na vaší platformě postupujte podle následujících pokynů:

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-java&tabs=jre" target="_blank">Java Runtime </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-java&tabs=android" target="_blank">Svém </a>

## <a name="import-dependencies"></a>Importovat závislosti

Chcete-li spustit příklady v tomto článku, zahrňte do horní části skriptu následující příkazy importu.

```java
import com.microsoft.cognitiveservices.speech.AudioDataStream;
import com.microsoft.cognitiveservices.speech.SpeechConfig;
import com.microsoft.cognitiveservices.speech.SpeechSynthesizer;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisOutputFormat;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisResult;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;

import java.io.*;
import java.util.Scanner;
```

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Chcete-li volat službu Speech pomocí sady Speech SDK, je třeba vytvořit [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) . Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token.

> [!NOTE]
> Bez ohledu na to, jestli provádíte rozpoznávání řeči, syntézu řeči, překlad nebo rozpoznávání záměrů, vždy vytvoříte konfiguraci.

Existuje několik způsobů, jak můžete inicializovat [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) :

* S předplatným: předejte klíč a přidruženou oblast.
* S koncovým bodem: předejte koncový bod služby řeči. Klíč nebo autorizační token jsou volitelné.
* S hostitelem: předejte adresu hostitele. Klíč nebo autorizační token jsou volitelné.
* Pomocí autorizačního tokenu: předejte autorizační token a přidruženou oblast.

V tomto příkladu vytvoříte [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) pomocí klíče a oblasti předplatného. Tyto přihlašovací údaje můžete získat podle kroků v [části Vyzkoušejte si službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free). Také můžete vytvořit nějaký základní často používaný kód, který se použije pro zbytek tohoto článku, který můžete upravit pro různá přizpůsobení.

```java
public class Program
{
    public static void main(String[] args) {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>Vysyntetizátorování řeči v souboru

V dalším kroku vytvoříte [`SpeechSynthesizer`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer) objekt, který provede převody textu na řeč a výstupy na reproduktory, soubory nebo jiné výstupní datové proudy. [`SpeechSynthesizer`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer)Parametr přijímá jako je [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) objekt vytvořený v předchozím kroku a [`AudioConfig`](/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig) objekt, který určuje, jak by měly být zpracovány výsledky výstupu.

Začněte tím, že vytvoříte `AudioConfig` a automaticky zapíšete výstup do `.wav` souboru pomocí `fromWavFileOutput()` statické funkce.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");
}
```

Dále vytvořte instanci `SpeechSynthesizer` předání `speechConfig` objektu a `audioConfig` objektu jako Paras. Pak je provádění syntézy řeči a psaní do souboru jednoduché jako při spuštění `SpeakText()` s textovým řetězcem.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("A simple test to write to a file.");
}
```

Spusťte program a v `.wav` zadaném umístění se zapíše syntetizující soubor. Toto je dobrý příklad základního využití, ale další se můžete podívat na přizpůsobení výstupu a zpracování výstupní odezvy jako proud v paměti pro práci s vlastními scénáři.

## <a name="synthesize-to-speaker-output"></a>Vysyntetizovat výstup mluvčího

V některých případech můžete chtít přímo vyprogramovat výstup syntetizované řeči přímo na mluvčí. Chcete-li to provést, vytvořte instanci `AudioConfig` pomocí `fromDefaultSpeakerOutput()` funkce static. Tento výstup vypíše aktuální aktivní výstupní zařízení.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Získat výsledek jako proud v paměti

Pro mnoho scénářů ve vývoji aplikací pro rozpoznávání řeči pravděpodobně budete potřebovat výsledná zvuková data jako proud v paměti, nikoli přímo zapisovat do souboru. To vám umožní vytvořit vlastní chování, včetně:

* Vyabstrakcte výsledné pole bajtů jako datový proud, který je možné vyhledat pro vlastní služby pro příjem dat.
* Integrujte výsledek s jinými službami nebo rozhraními API.
* Úprava zvukových dat, psaní vlastních `.wav` hlaviček atd.

Tuto změnu je jednoduché provést v předchozím příkladu. Nejprve odeberte `AudioConfig` blok, protože budete spravovat chování výstupu ručně z tohoto bodu dále pro zvýšené řízení. Pak předejte `null` `AudioConfig` v `SpeechSynthesizer` konstruktoru.

> [!NOTE]
> Předání `null` pro `AudioConfig` místo toho, aby ho nemuseli vynechat jako v příkladu výstupu mluvčího, ve výchozím nastavení nebude přehrávat zvuk na aktuálním aktivním výstupním zařízení.

Tentokrát výsledek uložíte do [`SpeechSynthesisResult`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult) proměnné. `SpeechSynthesisResult.getAudioData()`Funkce vrátí `byte []` výstupní data. S tímto můžete pracovat `byte []` ručně nebo můžete použít [`AudioDataStream`](/java/api/com.microsoft.cognitiveservices.speech.audiodatastream) třídu ke správě streamu v paměti. V tomto příkladu použijete `AudioDataStream.fromResult()` statickou funkci k získání datového proudu z výsledku.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);

    SpeechSynthesisResult result = synthesizer.SpeakText("Getting the response as an in-memory stream.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    System.out.print(stream.getStatus());
}
```

Odsud můžete implementovat jakékoli vlastní chování pomocí výsledného `stream` objektu.

## <a name="customize-audio-format"></a>Přizpůsobení formátu zvuku

V následující části se dozvíte, jak přizpůsobit atributy výstupů zvuku, včetně:

* Typ zvukového souboru
* Vzorkovací frekvence
* Bitová hloubka

Chcete-li změnit formát zvuku, použijte `setSpeechSynthesisOutputFormat()` funkci na `SpeechConfig` objektu. Tato funkce očekává `enum` typ [`SpeechSynthesisOutputFormat`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat) výstupu, který použijete k výběru výstupního formátu. [Seznam zvukových formátů](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat) , které jsou k dispozici, najdete v referenční dokumentaci.

V závislosti na vašich požadavcích máte k dispozici různé možnosti pro různé typy souborů. Všimněte si, že podle definice nezpracované formáty jako neobsahují `Raw24Khz16BitMonoPcm` zvukové hlavičky. Nezpracované formáty použijte jenom v případě, že vaše implementace pro příjem dat může dekódovat nezpracovaný Bitstream, nebo pokud plánujete ruční vytváření hlaviček na základě bitové hloubky, vzorkovací frekvence, počtu kanálů atd.

V tomto příkladu zadáte RIFF formát s vysokou přesností nastavením `Riff24Khz16BitMonoPcm` `SpeechSynthesisOutputFormat` `SpeechConfig` objektu na. Podobně jako v předchozím oddílu můžete použít [`AudioDataStream`](/java/api/com.microsoft.cognitiveservices.speech.audiodatastream) k získání streamu v paměti výsledku a pak ho zapsat do souboru.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // set the output format
    speechConfig.setSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);
    SpeechSynthesisResult result = synthesizer.SpeakText("Customizing audio output format.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
}
```

Po opětovném spuštění programu se zapíše `.wav` soubor do zadané cesty.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Přizpůsobení vlastností řeči pomocí SSML

SSML (Speech syntézy Language) umožňuje vyladit rozteč, výslovnost, míru řeči, objem a další výstup textu do mluvené řeči odesláním požadavků ze schématu XML. Tato část ukazuje příklad změny hlasu, ale podrobnější příručku najdete v [článku SSML](../../../speech-synthesis-markup.md).

Chcete-li začít používat SSML k přizpůsobení, provedete jednoduchou změnu, která přepne hlas.
Nejprve vytvořte nový soubor XML pro SSML config v kořenovém adresáři projektu, v tomto příkladu `ssml.xml` . Kořenový element je vždy `<speak>` a zalamování textu v `<voice>` prvku umožňuje změnit hlas pomocí `name` param. Podívejte se na [úplný seznam](../../../language-support.md#neural-voices) podporovaných **neuronové** hlasů.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    When you're on the freeway, it's a good idea to use a GPS.
  </voice>
</speak>
```

Dál je potřeba změnit požadavek na Shrnutí řeči, aby odkazoval na váš soubor XML. Požadavek je většinou stejný, ale namísto použití `SpeakText()` funkce použijte `SpeakSsml()` . Tato funkce očekává řetězec XML, takže nejprve vytvoříte funkci pro načtení souboru XML a vrátíte ji jako řetězec.

```java
private static String xmlToString(String filePath) {
    File file = new File(filePath);
    StringBuilder fileContents = new StringBuilder((int)file.length());

    try (Scanner scanner = new Scanner(file)) {
        while(scanner.hasNextLine()) {
            fileContents.append(scanner.nextLine() + System.lineSeparator());
        }
        return fileContents.toString().trim();
    } catch (FileNotFoundException ex) {
        return "File not found.";
    }
}
```

Z tohoto místa je objekt výsledku přesně stejný jako předchozí příklady.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);

    String ssml = xmlToString("ssml.xml");
    SpeechSynthesisResult result = synthesizer.SpeakSsml(ssml);
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
}
```

> [!NOTE]
> Chcete-li změnit hlas bez použití SSML, můžete nastavit vlastnost `SpeechConfig` na pomocí `SpeechConfig.setSpeechSynthesisVoiceName("en-US-AriaNeural");`

## <a name="get-facial-pose-events"></a>Získat události pozice obličeje

Řeč může být dobrým způsobem, jak řídit animaci výrazů obličeje.
Často se [visemes](../../../how-to-speech-synthesis-viseme.md) používají k reprezentování klíčových pozic v pozorovaném řeči, jako je například pozice sad LIP, vidlice a jazyka při vytváření konkrétního foném.
Můžete se přihlásit k odběru událostí viseme v sadě Speech SDK, abyste získali data animace obličeje, a pak data použít na znak během animace obličeje.
Naučte [se, jak získat události viseme](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk).
