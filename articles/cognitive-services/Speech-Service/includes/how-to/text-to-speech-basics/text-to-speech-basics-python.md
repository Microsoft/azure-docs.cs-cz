---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: be60a2f371148fabf73fc7fcdce114295775d71c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986251"
---
## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte účet Azure a předplatné služby Rozpoznávání řeči. Pokud nemáte účet a předplatné, [vyzkoušejte bezplatnou službu Řeč](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Než budete moci něco udělat, budete muset nainstalovat sadu Speech SDK.

```Python
pip install azure-cognitiveservices-speech
```

Pokud jste na macOS a spustit do problémů s instalací, možná budete muset nejprve spustit tento příkaz.

```Python
python3 -m pip install --upgrade pip
```

Po instalaci sady Speech SDK zahrňte do horní části skriptu následující příkazy importu.

```Python
from azure.cognitiveservices.speech import AudioDataStream, SpeechConfig, SpeechSynthesizer, SpeechSynthesisOutputFormat
from azure.cognitiveservices.speech.audio import AudioOutputConfig
```

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Chcete-li volat službu Řeč pomocí sady Speech [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)SDK, je třeba vytvořit . Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token.

> [!NOTE]
> Bez ohledu na to, zda provádíte rozpoznávání řeči, syntézu řeči, překlad nebo záměr rozpoznávání, budete vždy vytvořit konfiguraci.

Existuje několik způsobů, jak můžete inicializovat [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python):

* S předplatným: předat klíč a přidružené oblasti.
* S koncovým bodem: předají v koncovém bodě služby Řeči. Klíč nebo autorizační token je volitelný.
* S hostitelem: předat adresu hostitele. Klíč nebo autorizační token je volitelný.
* S tokenem autorizace: předavte autorizační token a přidruženou oblast.

V tomto příkladu [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) vytvoříte pomocí klíče předplatného a oblasti. Na stránce [podpory oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) najdete identifikátor oblasti.

```python
speech_config = SpeechConfig(subscription="YourSubscriptionKey", region="YourServiceRegion")
```

## <a name="synthesize-speech-to-a-file"></a>Syntetizovat řeč do souboru

Dále vytvoříte [`SpeechSynthesizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python) objekt, který provede převody převodů převodů převodů na řeč a výstupy do reproduktorů, souborů nebo jiných výstupních datových proudů. Přijímá [`SpeechSynthesizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python) jako params [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) objekt vytvořený v předchozím kroku [`AudioOutputConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig?view=azure-python) a objekt, který určuje, jak by měly být zpracovány výstupní výsledky.

Chcete-li začít, vytvořte automaticky `AudioOutputConfig` `.wav` zapisovat `filename` výstup do souboru pomocí param konstruktoru.

```python
audio_config = AudioOutputConfig(filename="path/to/write/file.wav")
```

Dále vytvořte instanci `SpeechSynthesizer` a `speech_config` předáním `audio_config` objektu a objektu jako params. Potom provádění syntézy řeči a zápisu do `speak_text_async()` souboru je stejně jednoduché jako spuštění s řetězcem textu.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
synthesizer.speak_text_async("A simple test to write to a file.")
```

Spusťte program a syntetizovaný `.wav` soubor se zapíše do zadaného umístění. Toto je dobrý příklad nejzákladnější využití, ale dále se podíváte na přizpůsobení výstupu a zpracování výstupní odezvy jako datový proud v paměti pro práci s vlastní scénáře.

## <a name="synthesize-to-speaker-output"></a>Syntetizovat výstup reproduktoru

V některých případech můžete chtít přímo výstup syntetizované řeči přímo do reproduktoru. Chcete-li to provést, použijte příklad v `AudioOutputConfig` předchozí části, ale změňte odebráním `filename` param a nastavte `use_default_speaker=True`. To výstupy na aktuální aktivní výstupní zařízení.

```python
audio_config = AudioOutputConfig(use_default_speaker=True)
```

## <a name="get-result-as-an-in-memory-stream"></a>Získat výsledek jako datový proud v paměti

Pro mnoho scénářů ve vývoji aplikace řeči pravděpodobně budete potřebovat výsledná zvuková data jako datový proud v paměti, nikoli přímo zapisovat do souboru. To vám umožní vytvořit vlastní chování, včetně:

* Abstrahujte výsledné bajtové pole jako datový proud, který lze hledat pro vlastní služby příjem dat.
* Integrujte výsledek s jinými rozhraními API nebo službami.
* Upravte zvuková data, napište vlastní `.wav` záhlaví atd.

Je to jednoduché provést tuto změnu z předchozího příkladu. Nejprve odeberte `AudioConfig`, protože budete spravovat chování výstupu ručně od tohoto okamžiku pro větší kontrolu. Pak `None` předat `AudioConfig` pro `SpeechSynthesizer` v konstruktoru. 

> [!NOTE]
> Předávání `None` pro `AudioConfig`, spíše než vynechat jako v příkladu výstupu reproduktoru výše, nebude přehrávat zvuk ve výchozím nastavení na aktuální aktivní výstupní zařízení.

Tentokrát uložíte výsledek do [`SpeechSynthesisResult`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult?view=azure-python) proměnné. Vlastnost `audio_data` obsahuje `bytes` objekt výstupních dat. S tímto objektem můžete pracovat ručně [`AudioDataStream`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?view=azure-python) nebo můžete použít třídu ke správě datového proudu v paměti. V tomto příkladu `AudioDataStream` použijete konstruktor získat datový proud z výsledku.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = synthesizer.speak_text_async("Getting the response as an in-memory stream.").get()
stream = AudioDataStream(result)
```

Odtud můžete implementovat jakékoli vlastní chování `stream` pomocí výsledného objektu.

## <a name="customize-audio-format"></a>Přizpůsobení formátu zvuku

V následující části je uvedeno, jak přizpůsobit atributy zvukového výstupu, včetně:

* Typ zvukového souboru
* Vzorkovací frekvence
* Bitová hloubka

Chcete-li změnit formát zvuku, `set_speech_synthesis_output_format()` použijte `SpeechConfig` funkci na objektu. Tato funkce očekává `enum` typ [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python), který slouží k výběru výstupního formátu. [Seznam dostupných zvukových formátů](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python) naleznete v referenčních dokumentech.

Existují různé možnosti pro různé typy souborů v závislosti na vašich požadavcích. Všimněte si, že z `Raw24Khz16BitMonoPcm` definice nezpracované formáty jako neobsahují zvukové hlavičky. Nezpracované formáty používejte pouze v případě, že víte, že vaše implementace navazujících dat může dekódovat nezpracovaný bitový tok, nebo pokud plánujete ruční vytváření záhlaví na základě bitové hloubky, vzorkovací rychlosti, počtu kanálů atd.

V tomto příkladu určíte formát `Riff24Khz16BitMonoPcm` RIFF s vysokou `SpeechSynthesisOutputFormat` věrností nastavením na objektu. `SpeechConfig` Podobně jako v příkladu v [`AudioDataStream`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?view=azure-python) předchozí části, můžete použít k získání datového proudu v paměti výsledku a zapisovat jej do souboru.


```python
speech_config.set_speech_synthesis_output_format(SpeechSynthesisOutputFormat["Riff24Khz16BitMonoPcm"])
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

result = synthesizer.speak_text_async("Customizing audio output format.").get()
stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

Spuštění programu znovu zapíše `.wav` vlastní soubor na zadanou cestu.

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

Dále je třeba změnit požadavek na syntézu řeči tak, aby odkazoval na soubor XML. Požadavek je většinou stejný, ale `speak_text_async()` místo použití `speak_ssml_async()`funkce použijete . Tato funkce očekává řetězec XML, takže nejprve přečtete konfiguraci SSML jako řetězec. Odtud je výsledný objekt přesně stejný jako předchozí příklady.

> [!NOTE]
> Pokud `ssml_string` obsahuje `ï»¿` na začátku řetězce, je třeba odstranit formát kusovníku nebo služba vrátí chybu. To provést nastavením `encoding` parametru takto: `open("ssml.xml", "r", encoding="utf-8-sig")`.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

ssml_string = open("ssml.xml", "r").read()
result = synthesizer.speak_ssml_async(ssml_string).get()

stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
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
