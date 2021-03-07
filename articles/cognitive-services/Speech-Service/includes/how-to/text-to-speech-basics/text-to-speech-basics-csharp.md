---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 278bb106789452d14001da5bd0bab6570d114666
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428181"
---
V tomto rychlém startu se naučíte běžné vzory návrhu pro provádění syntézy textu na řeč pomocí sady Speech SDK. Začnete tím, že provádíte základní konfiguraci a shrnutí a přejdete k pokročilejším příkladům pro vývoj vlastních aplikací, včetně:

* Získávání odpovědí jako proudů v paměti
* Přizpůsobení výstupní vzorkovací frekvence a přenosové rychlosti
* Odesílání požadavků na syntézu pomocí SSML (Speech syntézy Markup Language)
* Používání hlasů neuronové

## <a name="skip-to-samples-on-github"></a>Přeskočit na ukázky na GitHubu

Pokud chcete přeskočit přímý na vzorový kód, přečtěte si [ukázky pro rychlý Start v C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/text-to-speech) na GitHubu.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Předtím, než můžete cokoli udělat, musíte nainstalovat sadu Speech SDK. V závislosti na vaší platformě postupujte podle následujících pokynů:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Jednot </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">PODPORUJÍ </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin </a>

## <a name="import-dependencies"></a>Importovat závislosti

Chcete-li spustit příklady v tomto článku, zahrňte do `using` horní části skriptu následující příkazy.

```csharp
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Chcete-li volat službu Speech pomocí sady Speech SDK, je třeba vytvořit [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) . Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token.

> [!NOTE]
> Bez ohledu na to, jestli provádíte rozpoznávání řeči, syntézu řeči, překlad nebo rozpoznávání záměrů, vždy vytvoříte konfiguraci.

Existuje několik způsobů, jak můžete inicializovat [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) :

* S předplatným: předejte klíč a přidruženou oblast.
* S koncovým bodem: předejte koncový bod služby řeči. Klíč nebo autorizační token jsou volitelné.
* S hostitelem: předejte adresu hostitele. Klíč nebo autorizační token jsou volitelné.
* Pomocí autorizačního tokenu: předejte autorizační token a přidruženou oblast.

V tomto příkladu vytvoříte [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) pomocí klíče a oblasti předplatného. Tyto přihlašovací údaje můžete získat podle kroků v [části Vyzkoušejte si službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free). Také můžete vytvořit nějaký základní často používaný kód, který se použije pro zbytek tohoto článku, který můžete upravit pro různá přizpůsobení.

```csharp
public class Program 
{
    static async Task Main()
    {
        await SynthesizeAudioAsync();
    }

    static async Task SynthesizeAudioAsync() 
    {
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>Vysyntetizátorování řeči v souboru

V dalším kroku vytvoříte [`SpeechSynthesizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer) objekt, který provede převody textu na řeč a výstupy na reproduktory, soubory nebo jiné výstupní datové proudy. [`SpeechSynthesizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer)Parametr přijímá jako je [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) objekt vytvořený v předchozím kroku a [`AudioConfig`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig) objekt, který určuje, jak by měly být zpracovány výsledky výstupu.

Chcete-li začít, vytvořte `AudioConfig` pro automatický zápis výstupu do `.wav` souboru pomocí `FromWavFileOutput()` funkce a vytvoření instance pomocí `using` příkazu. `using`Příkaz v tomto kontextu automaticky odstraní nespravované prostředky a způsobí, že objekt přejde mimo obor po vyřazení.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
}
```

Dále vytvořte instanci `SpeechSynthesizer` s jiným `using` příkazem. Předejte `config` objekt a `audioConfig` objekt jako Parai. Pak je provádění syntézy řeči a psaní do souboru jednoduché jako při spuštění `SpeakTextAsync()` s textovým řetězcem.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
    using var synthesizer = new SpeechSynthesizer(config, audioConfig);
    await synthesizer.SpeakTextAsync("A simple test to write to a file.");
}
```

Spusťte program a v `.wav` zadaném umístění se zapíše syntetizující soubor. Toto je dobrý příklad základního využití, ale další se můžete podívat na přizpůsobení výstupu a zpracování výstupní odezvy jako proud v paměti pro práci s vlastními scénáři.

## <a name="synthesize-to-speaker-output"></a>Vysyntetizovat výstup mluvčího

V některých případech můžete chtít přímo vyprogramovat výstup syntetizované řeči přímo na mluvčí. Pokud to chcete provést, jednoduše vynechejte `AudioConfig` PARAT při vytváření `SpeechSynthesizer` v předchozím příkladu. Tento výstup vypíše aktuální aktivní výstupní zařízení.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config);
    await synthesizer.SpeakTextAsync("Synthesizing directly to speaker output.");
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

Tentokrát výsledek uložíte do [`SpeechSynthesisResult`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult) proměnné. `AudioData`Vlastnost obsahuje `byte []` výstupní data. S tímto můžete pracovat `byte []` ručně nebo můžete použít [`AudioDataStream`](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream) třídu ke správě streamu v paměti. V tomto příkladu použijete `AudioDataStream.FromResult()` statickou funkci k získání datového proudu z výsledku.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);
    
    var result = await synthesizer.SpeakTextAsync("Getting the response as an in-memory stream.");
    using var stream = AudioDataStream.FromResult(result);
}
```

Odsud můžete implementovat jakékoli vlastní chování pomocí výsledného `stream` objektu.

## <a name="customize-audio-format"></a>Přizpůsobení formátu zvuku

V následující části se dozvíte, jak přizpůsobit atributy výstupů zvuku, včetně:

* Typ zvukového souboru
* Vzorkovací frekvence
* Bitová hloubka

Chcete-li změnit formát zvuku, použijte `SetSpeechSynthesisOutputFormat()` funkci na `SpeechConfig` objektu. Tato funkce očekává `enum` typ [`SpeechSynthesisOutputFormat`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat) výstupu, který použijete k výběru výstupního formátu. [Seznam zvukových formátů](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat) , které jsou k dispozici, najdete v referenční dokumentaci.

V závislosti na vašich požadavcích máte k dispozici různé možnosti pro různé typy souborů. Všimněte si, že podle definice nezpracované formáty jako neobsahují `Raw24Khz16BitMonoPcm` zvukové hlavičky. Nezpracované formáty použijte jenom v případě, že vaše implementace pro příjem dat může dekódovat nezpracovaný Bitstream, nebo pokud plánujete ruční vytváření hlaviček na základě bitové hloubky, vzorkovací frekvence, počtu kanálů atd.

> [!NOTE]
> Hlasy **en-US-AriaRUS** a **en-US-GuyRUS** se vytvářejí z ukázek kódovaných v `Riff24Khz16BitMonoPcm` vzorkovací frekvenci.

V tomto příkladu zadáte RIFF formát s vysokou přesností nastavením `Riff24Khz16BitMonoPcm` `SpeechSynthesisOutputFormat` `SpeechConfig` objektu na. Podobně jako v předchozím oddílu můžete použít [`AudioDataStream`](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream) k získání streamu v paměti výsledku a pak ho zapsat do souboru.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config.SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    using var synthesizer = new SpeechSynthesizer(config, null);
    var result = await synthesizer.SpeakTextAsync("Customizing audio output format.");

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
}
```

Po opětovném spuštění programu se zapíše `.wav` soubor do zadané cesty.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Přizpůsobení vlastností řeči pomocí SSML

SSML (Speech syntézy Language) umožňuje vyladit rozteč, výslovnost, míru řeči, objem a další výstup textu do mluvené řeči odesláním požadavků ze schématu XML. Tato část obsahuje několik praktických příkladů použití, ale pro podrobnějšího průvodce si přečtěte [článek SSML postupy](../../../speech-synthesis-markup.md).

Chcete-li začít používat SSML k přizpůsobení, provedete jednoduchou změnu, která přepne hlas.
Nejprve vytvořte nový soubor XML pro SSML config v kořenovém adresáři projektu, v tomto příkladu `ssml.xml` . Kořenový element je vždy `<speak>` a zalamování textu v `<voice>` prvku umožňuje změnit hlas pomocí `name` param. Tento příklad změní hlas na hlas v češtině (UK). Všimněte si, že tento hlas je **standardní** hlas, který má různé ceny a dostupnost než **neuronové** hlasy. Podívejte se na [úplný seznam](../../../language-support.md#standard-voices) podporovaných **standardních** hlasů.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Dál je potřeba změnit požadavek na Shrnutí řeči, aby odkazoval na váš soubor XML. Požadavek je většinou stejný, ale namísto použití `SpeakTextAsync()` funkce použijte `SpeakSsmlAsync()` . Tato funkce očekává řetězec XML, takže nejdřív načtěte konfiguraci SSML jako řetězec pomocí `File.ReadAllText()` . Z tohoto místa je objekt výsledku přesně stejný jako předchozí příklady.

> [!NOTE]
> Pokud používáte aplikaci Visual Studio, konfigurace sestavení pravděpodobně nenalezne soubor XML ve výchozím nastavení. Pokud to chcete opravit, klikněte pravým tlačítkem na soubor XML a vyberte **vlastnosti**. Změňte **akci sestavení** na *obsah* a změňte **Kopírovat do výstupního adresáře** na *vždycky kopírovat*.

```csharp
public static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);
    
    var ssml = File.ReadAllText("./ssml.xml");
    var result = await synthesizer.SpeakSsmlAsync(ssml);

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
}
```

Výstup funguje, ale existuje několik jednoduchých změn, které vám pomůžou s tím, aby se lépe staly přirozenější. Celková rychlost speakace je trochu příliš rychlá, takže přidáme `<prosody>` značku a omezíme rychlost na **90%** výchozí sazby. Kromě toho je pozastavení po čárkě ve větě trochu krátké a nepřirozený zvuk. Chcete-li tento problém vyřešit, přidejte `<break>` značku pro zpoždění řeči a nastavte parametr Time na **200ms**. Znovu spusťte syntézu, abyste viděli, jak tato přizpůsobení ovlivnila výstup.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Hlasy neuronové

Hlasy neuronové jsou algoritmy pro syntézu řeči založené na hluboce neuronové sítích. Při použití hlasu neuronové je syntetizované rozpoznávání řeči skoro neodlišitelné od lidských nahrávek. V případě přirozeného Prosody jako přirozeného a jasného kloubování slov, neuronové hlasy významně omezují naslouchat únavu při interakci uživatelů se systémy AI.

Pokud chcete přepnout na neuronové hlas, změňte na `name` jednu z [možností hlasu neuronové](../../../language-support.md#neural-voices). Pak přidejte obor názvů XML pro `mstts` a zabalte text do `<mstts:express-as>` značky. Použijte `style` parametr pro přizpůsobení stylu speaking. Tento příklad používá `cheerful` , ale zkuste ho nastavit na `customerservice` nebo `chat` pro zobrazení rozdílu ve stylu speaking.

> [!IMPORTANT]
> Hlasy neuronové se podporují **jenom** u zdrojů řeči vytvořených v oblastech *Východní USA*, *Jižní východní Asie* a *západní Evropa* .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
