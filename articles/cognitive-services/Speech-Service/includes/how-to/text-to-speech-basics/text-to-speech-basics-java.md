---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: e0c2554162d54af6c0a483e26f708838c3045a03
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986244"
---
## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte účet Azure a předplatné služby Rozpoznávání řeči. Pokud nemáte účet a předplatné, [vyzkoušejte bezplatnou službu Řeč](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Než budete moci něco udělat, budete muset nainstalovat sadu Speech SDK. V závislosti na platformě postupujte podle následujících pokynů:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java Runtime<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Importovat závislosti

Chcete-li spustit příklady v tomto článku, uveďte následující příkazy importu v horní části skriptu.

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

Chcete-li volat službu Řeč pomocí sady Speech [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)SDK, je třeba vytvořit . Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token.

> [!NOTE]
> Bez ohledu na to, zda provádíte rozpoznávání řeči, syntézu řeči, překlad nebo záměr rozpoznávání, budete vždy vytvořit konfiguraci.

Existuje několik způsobů, jak můžete inicializovat [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable):

* S předplatným: předat klíč a přidružené oblasti.
* S koncovým bodem: předají v koncovém bodě služby Řeči. Klíč nebo autorizační token je volitelný.
* S hostitelem: předat adresu hostitele. Klíč nebo autorizační token je volitelný.
* S tokenem autorizace: předavte autorizační token a přidruženou oblast.

V tomto příkladu [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) vytvoříte pomocí klíče předplatného a oblasti. Na stránce [podpory oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) najdete identifikátor oblasti. Můžete také vytvořit některé základní často používaný kód pro zbytek tohoto článku, který upravíte pro různé vlastní nastavení.

```java
public class Program 
{
    public static void main(String[] args) {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>Syntetizovat řeč do souboru

Dále vytvoříte [`SpeechSynthesizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-java-stable) objekt, který provede převody převodů převodů převodů na řeč a výstupy do reproduktorů, souborů nebo jiných výstupních datových proudů. Přijímá [`SpeechSynthesizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-java-stable) jako params [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) objekt vytvořený v předchozím kroku [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable) a objekt, který určuje, jak by měly být zpracovány výstupní výsledky.

Chcete-li začít, vytvořte pro `AudioConfig` `.wav` automatické zápis `fromWavFileOutput()` výstupu do souboru pomocí statické funkce.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");
}
```

Dále vytvořte instanci `SpeechSynthesizer` `speechConfig` procházející objekt `audioConfig` a objekt jako params. Potom provádění syntézy řeči a zápisu do `SpeakText()` souboru je stejně jednoduché jako spuštění s řetězcem textu.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("A simple test to write to a file.");
}
```

Spusťte program a syntetizovaný `.wav` soubor se zapíše do zadaného umístění. Toto je dobrý příklad nejzákladnější využití, ale dále se podíváte na přizpůsobení výstupu a zpracování výstupní odezvy jako datový proud v paměti pro práci s vlastní scénáře.

## <a name="synthesize-to-speaker-output"></a>Syntetizovat výstup reproduktoru

V některých případech můžete chtít přímo výstup syntetizované řeči přímo do reproduktoru. Chcete-li to provést, `AudioConfig` vytvořte `fromDefaultSpeakerOutput()` konkretizovat pomocí statické funkce. To výstupy na aktuální aktivní výstupní zařízení.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Získat výsledek jako datový proud v paměti

Pro mnoho scénářů ve vývoji aplikace řeči pravděpodobně budete potřebovat výsledná zvuková data jako datový proud v paměti, nikoli přímo zapisovat do souboru. To vám umožní vytvořit vlastní chování, včetně:

* Abstrahujte výsledné bajtové pole jako datový proud, který lze hledat pro vlastní služby příjem dat.
* Integrujte výsledek s jinými rozhraními API nebo službami.
* Upravte zvuková data, napište vlastní `.wav` záhlaví atd.

Je to jednoduché provést tuto změnu z předchozího příkladu. Nejprve odstraňte `AudioConfig` blok, protože budete spravovat chování výstupu ručně od tohoto okamžiku pro větší kontrolu. Pak `null` předat `AudioConfig` pro `SpeechSynthesizer` v konstruktoru. 

> [!NOTE]
> Předávání `null` pro `AudioConfig`, spíše než vynechat jako v příkladu výstupu reproduktoru výše, nebude přehrávat zvuk ve výchozím nastavení na aktuální aktivní výstupní zařízení.

Tentokrát uložíte výsledek do [`SpeechSynthesisResult`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult?view=azure-java-stable) proměnné. Funkce `SpeechSynthesisResult.getAudioData()` vrátí `byte []` a výstupních dat. Můžete pracovat s `byte []` tímto ručně nebo [`AudioDataStream`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audiodatastream?view=azure-java-stable) můžete použít třídu ke správě datového proudu v paměti. V tomto příkladu `AudioDataStream.fromResult()` použijete statickou funkci k získání datového proudu z výsledku.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);
    
    SpeechSynthesisResult result = synthesizer.SpeakText("Getting the response as an in-memory stream.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    System.out.print(stream.getStatus());
}
```

Odtud můžete implementovat jakékoli vlastní chování `stream` pomocí výsledného objektu.

## <a name="customize-audio-format"></a>Přizpůsobení formátu zvuku

V následující části je uvedeno, jak přizpůsobit atributy zvukového výstupu, včetně:

* Typ zvukového souboru
* Vzorkovací frekvence
* Bitová hloubka

Chcete-li změnit formát zvuku, `setSpeechSynthesisOutputFormat()` použijte `SpeechConfig` funkci na objektu. Tato funkce očekává `enum` typ [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-java-stable), který slouží k výběru výstupního formátu. [Seznam dostupných zvukových formátů](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet) naleznete v referenčních dokumentech.

Existují různé možnosti pro různé typy souborů v závislosti na vašich požadavcích. Všimněte si, že z `Raw24Khz16BitMonoPcm` definice nezpracované formáty jako neobsahují zvukové hlavičky. Nezpracované formáty používejte pouze v případě, že víte, že vaše implementace navazujících dat může dekódovat nezpracovaný bitový tok, nebo pokud plánujete ruční vytváření záhlaví na základě bitové hloubky, vzorkovací rychlosti, počtu kanálů atd.

V tomto příkladu určíte formát `Riff24Khz16BitMonoPcm` RIFF s vysokou `SpeechSynthesisOutputFormat` věrností nastavením na objektu. `SpeechConfig` Podobně jako v příkladu v [`AudioDataStream`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audiodatastream?view=azure-java-stable) předchozí části, můžete použít k získání datového proudu v paměti výsledku a zapisovat jej do souboru.

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

Dále je třeba změnit požadavek na syntézu řeči tak, aby odkazoval na soubor XML. Požadavek je většinou stejný, ale `SpeakText()` místo použití `SpeakSsml()`funkce použijete . Tato funkce očekává řetězec XML, takže nejprve vytvoříte funkci pro načtení souboru XML a jeho vrácení jako řetězec.

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

Odtud je výsledný objekt přesně stejný jako předchozí příklady.

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
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
