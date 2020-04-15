---
title: Často kladené otázky kontejnerů služby rozpoznávání řeči (FAQ)
titleSuffix: Azure Cognitive Services
description: Nainstalujte a spusťte kontejnery řeči. převod řeči na text přepisuje zvukové proudy na text v reálném čase, který mohou aplikace, nástroje nebo zařízení využívat nebo zobrazovat. Převod textu na řeč převede vstupní text na syntetizovanou řeč podobné člověku.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: aahi
ms.openlocfilehash: 17582244aef173da6ac700c980f7bd7fb0fec307
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383082"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Často kladené otázky kontejnerů služby rozpoznávání řeči (FAQ)

Při použití služby řeči s kontejnery, spoléhat na tuto kolekci často kladené otázky před eskalovat na podporu. Tento článek zachycuje otázky v různé míře, od obecné po technické. Chcete-li rozbalit odpověď, klikněte na otázku.

## <a name="general-questions"></a>Obecné otázky

<details>
<summary>
<b>Jak fungují kontejnery řeči a jak je nastavím?</b>
</summary>

**Odpověď:** Při nastavování produkčního clusteru je třeba zvážit několik věcí. Za prvé, nastavení jednoho jazyka, více kontejnerů, na stejném počítači, by neměl být velký problém. Pokud dochází k problémům, může to být problém související s hardwarem - takže bychom se nejprve podívali na zdroj, to znamená; Specifikace procesoru a paměti.

Zvažte na chvíli `ja-JP` kontejner a nejnovější model. Akustický model je nejnáročnější kus CPU-moudrý, zatímco jazykový model vyžaduje nejvíce paměti. Když jsme srovnávají použití, trvá asi 0,6 cpu jádra ke zpracování jednoho převodu řeči na text, když zvuk proudí v reálném čase (jako z mikrofonu). Pokud krmíte zvuk rychleji než v reálném čase (například ze souboru), může se toto použití zdvojnásobit (1,2x jádra). Mezitím níže uvedená paměť je operační paměť pro dekódování řeči. *Nebere* v úvahu skutečnou plnou velikost jazykového modelu, který bude umístěn v mezipaměti souborů. Pro `ja-JP` to je další 2 GB; může `en-US`být více (6-7 GB).

Pokud máte počítač, kde je nedostatek paměti a pokoušíte se nasadit více jazyků na to, je možné, že mezipaměť souborů je plná a operační systém je nucen stránkovat modely dovnitř a ven. Pro běžící přepis, které by mohly být katastrofální, a může vést ke zpomalení a další důsledky výkonu.

Dále předem zabalíme spustitelné soubory pro stroje s pokročilou indiční sadou [vektorového rozšíření (AVX2).](speech-container-howto.md#advanced-vector-extension-support) Počítač s instrukční sadou AVX512 bude vyžadovat generování kódu pro tento cíl a spuštění 10 kontejnerů pro 10 jazyků může dočasně vyčerpat procesor. Zpráva, jako je tato, se zobrazí v protokolech dockeru:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Nakonec můžete nastavit počet dekodérů, které *single* chcete `DECODER MAX_COUNT` uvnitř jednoho kontejneru pomocí proměnné. Takže v podstatě bychom měli začít s vaší SKU (CPU / paměť), a můžeme navrhnout, jak se dostat to nejlepší z toho. Skvělý výchozí bod odkazuje na doporučené specifikace prostředků hostitelského počítače.

<br>
</details>

<details>
<summary>
<b>Mohl byste pomoci s plánováním kapacity a odhadem nákladů kontejnerů on-prem Speech?</b>
</summary>

**Odpověď:** Pro kapacitu kontejneru v režimu dávkového zpracování může každý dekodér zpracovat 2-3x v reálném čase se dvěma jádry procesoru pro jediné uznání. Nedoporučujeme zachovat více než dvě souběžná rozpoznávání na instanci kontejneru, ale doporučujeme spustit více instancí kontejnerů z důvodů spolehlivosti nebo dostupnosti za nástroj pro vyrovnávání zatížení.

I když bychom mohli mít každou instanci kontejneru spuštěnou s více dekodéry. Například můžeme být schopni nastavit 7 dekodérů na instanci kontejneru na osmijádrovém počítači (při více než 2x), což přináší 15x propustnost. Je tu param, o kterém `DECODER_MAX_COUNT` je třeba vědět. V extrémních případech vznikají problémy se spolehlivostí a latencí, přičemž propustnost se výrazně zvýšila. Pro mikrofon to bude 1x v reálném čase. Celkové využití by mělo být přibližně na jednom jádru pro jedno rozpoznávání.

Pro scénář zpracování 1 K hodin /den v režimu dávkového zpracování v extrémním případě 3 virtuální počítače by to mohly zpracovat do 24 hodin, ale není zaručeno. Chcete-li zpracovat špičaté dny, převzetí služeb při selhání, aktualizovat a poskytnout minimální zálohování/BCP, doporučujeme 4-5 počítačů namísto 3 na cluster a s 2 + clustery.

Pro hardware používáme jako `DS13_v2` referenci standardní virtuální počítač Azure (každé jádro musí být 2,6 GHz nebo lepší, s povolenou instrukovací sadou AVX2).

| Instance  | virtuální procesory | Paměť RAM    | Dočasné skladování | Průběžně s AHB | Roční rezerva s AHB (% úspora) | 3-rok vyhrazena s AHB (% Úspory) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | $0.598/hod.            | $0.3528/hour (~41 %)                 | $0.2333/hour (~61 %)                  |

Na základě odkazu na návrh (dva clustery 5 virtuálních počítačů pro zpracování dávkového zpracování zvuku 1 K/den) budou náklady na hardware za 1 rok:

> 2 (clustery) * 5 (virtuální chod na cluster) * $0.3528/hour * 365 (dny) * 24 (hodiny) = $31K / rok

Při mapování na fyzický počítač je obecný odhad 1 vCPU = 1 fyzické jádro procesoru. Ve skutečnosti je 1vCPU výkonnější než jedno jádro.

Pro on-prem, všechny tyto další faktory vstoupí do hry:

- Na jaký typ fyzickéCPU je a kolik jader na něm
- Kolik procesorů běží společně na stejném boxu/počítači
- Jak se virtuální virtuální mích nastavují
- Jak se používá hyper-threading / multi-threading
- Jak je paměť sdílena
- Operační systém atd.

Normálně není tak dobře vyladěné jako prostředí Azure. Vzhledem k tomu, že ostatní režie, řekl bych, že bezpečný odhad je 10 fyzických procesorových jader = 8 virtuálních procesorů Azure. Ačkoli populární procesory mají pouze osm jader. S nasazením on-prem budou náklady vyšší než při použití virtuálních počítačích Azure. Zvažte také odpisovou sazbu.

Náklady na služby jsou stejné jako u online služby: $1/hod pro převod řeči na text. Náklady na službu Řeči jsou:

> $1 * 1000 * 365 = $365K

Náklady na údržbu hrazené společnosti Microsoft závisí na úrovni služeb a obsahu služby. To se liší od $ 29.99 / měsíc pro základní úroveň na stovky tisíc, pokud na místě služby zapojeny. Hrubé číslo je $ 300 / hodinu pro servis / údržbu. Náklady na osoby nejsou zahrnuty. Ostatní náklady na infrastrukturu (například úložiště, sítě a vyrovnávání zatížení) nejsou zahrnuty.

<br>
</details>

<details>
<summary>
<b>Proč interpunkce chybí v přepisu?</b>
</summary>

**Odpověď:** By `speech_recognition_language=<YOUR_LANGUAGE>` měl být explicitně nakonfigurován v požadavku, pokud používají carbon klienta.

Příklad:

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
Tady je výstup:

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>Můžu s kontejnerem řeči použít vlastní akustický a jazykový model?</b>
</summary>

V současné době jsme schopni předat pouze jedno ID modelu, buď vlastní jazykový model nebo vlastní akustický model.

**Odpověď:** Současně bylo rozhodnuto *nepodporovat* akustické i jazykové modely. To zůstane v platnosti, dokud jednotný identifikátor je vytvořen ke snížení přestávky rozhraní API. Takže, bohužel to není podporováno právě teď.

<br>
</details>

<details>
<summary>
<b>Můžete vysvětlit tyto chyby z vlastního kontejneru řeči na text?</b>
</summary>

**Chyba 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Odpověď 1:** Pokud trénujete s nejnovějším vlastním modelem, v současné době to nepodporujeme. Pokud trénujete se starší verzí, mělo by být možné použít. Stále pracujeme na podpoře nejnovějších verzí.

V podstatě vlastní kontejnery nepodporují Halide nebo ONNX akustické modely (což je výchozí ve vlastním trénovacím portálu). To je způsobeno tím, že vlastní modely nejsou šifrovány a nechceme vystavit modely ONNX; jazykové modely jsou v pořádku. Zákazník bude muset explicitně vybrat starší model bez ONNX pro vlastní školení. Přesnost nebude ovlivněna. Velikost modelu může být větší (o 100 MB).

> Model podpory > 20190220 (v4.5 Unified)

**Chyba 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Odpověď 2:** V požadavku je třeba zadat správný název hlasu, který rozlišuje malá a velká písmena. Podívejte se na mapování názvů celé služby. Musíte použít `en-US-JessaRUS`, `en-US-JessaNeural` jak není k dispozici právě teď v kontejneru verzi převodu textu na řeč.

**Chyba 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Odpověď 3:** Reed vytvořit prostředek řeči, nikoli prostředek cognitive services.


<br>
</details>

<details>
<summary>
<b>Jaké protokoly rozhraní API jsou podporovány, REST nebo WS?</b>
</summary>

**Odpověď:** Pro kontejnery převodu řeči na text a vlastní kontejnery převodu řeči na text v současné době podporujeme pouze protokol založený na websocketu. Sada SDK podporuje pouze volání v WS, ale ne REST. Existuje plán na přidání podpory REST, ale ne ETA pro tuto chvíli. Vždy se podívejte do oficiální dokumentace, viz [koncové body předpovědi dotazu](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>Je CentOS podporován pro kontejnery řeči?</b>
</summary>

**Odpověď:** CentOS 7 ještě není podporován PythonSDK, také Ubuntu 19.04 není podporováno.

Balíček Python Speech SDK je k dispozici pro tyto operační systémy:
- **Windows** - x64 a x86
- **Mac** - macOS X verze 10.12 nebo novější
- **Linux** - Ubuntu 16.04, Ubuntu 18.04, Debian 9 na x64

Další informace o nastavení prostředí naleznete v tématu [Nastavení platformy Pythonu](quickstarts/setup-platform.md?pivots=programming-language-python). Pro tuto chvíli je Ubuntu 18.04 doporučená verze.

<br>
</details>

<details>
<summary>
<b>Proč se při pokusu o volání koncových bodů předpovědi SLUŽBY LUIS zodenují chyby?</b>
</summary>

Používám kontejner LUIS v nasazení IoT Edge a pokouším se volat koncový bod předpověď LUIS z jiného kontejneru. Kontejner SLUŽBY LUIS naslouchá na portu 5001 a používám adresu URL:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

Chyba, kterou dostávám, je:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

Zobrazí požadavek v protokolech kontejneru LUIS a zpráva říká:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Co to znamená? Co mi uniká? Byl jsem po příkladu pro řeč SDK, [odtud](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Scénář je, že jsme detekci zvuku přímo z mikrofonu počítače a snaží se určit záměr, na základě aplikace LUIS jsme trénovali. Příklad jsem souvisí s dělá přesně to. A funguje dobře s cloudovou službou LUIS. Zdálo se, že použití sady Speech SDK nám zachránilo nutnost samostatného explicitního volání rozhraní API pro převod řeči na text a pak druhé volání luis.

Takže vše, co se pokouším udělat, je přepnout ze scénáře použití služby LUIS v cloudu na použití kontejneru LUIS. Neumím si představit, pokud řeč SDK pracuje pro jednoho, nebude fungovat pro ostatní.

**Odpověď:** Sada Speech SDK by neměla být použita proti kontejneru LUIS. Pro použití kontejneru LUIS by měla být použita sada LUIS SDK nebo rozhraní API LUIS REST. Sada SDK řeči by měla být použita proti kontejneru řeči.

Cloud se liší od kontejneru. Cloud se může skládat z více agregovaných kontejnerů (někdy nazývaných mikroslužby). Takže je kontejner LUIS a pak je kontejner řeči – dva samostatné kontejnery. Kontejner řeči pouze řeč. Kontejner LUIS pouze luis. V cloudu, protože oba kontejnery je známo, že se nasadit a je špatný výkon pro vzdáleného klienta přejít do cloudu, do řeči, vrátit se, pak přejít do cloudu znovu a luis, poskytujeme funkci, která umožňuje klientovi přejít na řeč, zůstat v cloudu, přejděte na LUIS pak se vrátit ke klientovi. Proto i v tomto scénáři řeči SDK přejde do kontejneru cloud řeči se zvukem a pak kontejner cloud řeči mluví s cloudový kontejner LUIS s textem. Kontejner LUIS nemá žádnou koncepci přijímání zvuku (nemá smysl, aby kontejner LUIS přijímal streamovaný zvuk – SLUŽBA LUIS je služba založená na textu). S on-prem, nemáme jistotu, že náš zákazník nasadil oba kontejnery, nepředpokládáme orchestraci mezi kontejnery v prostorách našich zákazníků a pokud jsou oba kontejnery nasazeny on-prem, vzhledem k tomu, že jsou pro klienta více místní, není zátěží nejprve přejít na SR, zpět ke klientovi a zákazník pak tento text převezme a přejde na SLUŽBU LUIS.

<br>
</details>

<details>
<summary>
<b>Proč dostáváme chyby s macOS, kontejnerem řeči a sadou Python SDK?</b>
</summary>

Když odešleme soubor *WAV* k přepsání, výsledek se vrátí s:

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

Víme, že websocket je nastaven správně.

**Odpověď:** Pokud tomu tak je, podívejte se na [tento problém GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310). Máme řešení, které [je navrženo zde](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722).

Carbon to opravil ve verzi 1.8.


<br>
</details>

<details>
<summary>
<b>Jaké jsou rozdíly v koncových bodech kontejneru řeči?</b>
</summary>

Mohl byste pomoci vyplnit následující metriky testu, včetně jaké funkce testovat a jak otestovat sdk a rest API? Zejména rozdíly v "interaktivní" a "konverzace", které jsem neviděl z existujícího doc / vzorku.

| Koncový bod                                                | Funkční zkouška                                                   | Sada SDK | REST API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Syntetizovat text (převod textu na řeč)                                  |     | Ano      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Koncový bod websocketu v 1 websocket u společnosti Cognitive Services on-prem        | Ano | Ne       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | Koncový bod websocketu Cognitive Services on-prem interactive v1  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | Koncový bod websocketu konverzace on-prem v1 |     |          |

**Odpověď:** Jedná se o fúzi:
- Lidé se snaží dictation koncový bod pro kontejnery, (Nejsem si jistý, jak se dostali, že URL)
- Koncový bod<sup>první</sup> strany je jeden v kontejneru.
- Koncový bod 1<sup>st</sup> strany vracející zprávy `speech.hypothesis` speech.fragment namísto zpráv, které se vrátí koncovým bodům třetí<sup>části</sup> pro koncový bod diktování.
- Carbon quickstarts všechny `RecognizeOnce` použití (interaktivní režim)
- Carbon s tvrzením, že pro `speech.fragment` zprávy, které vyžadují, nejsou vráceny v interaktivním režimu.
- Uhlík, který má tvrzení oheň v uvolnění staví (zabíjení procesu).

Řešení je buď přepnout na použití průběžného rozpoznávání v kódu, nebo (rychlejší) připojit k interaktivní nebo průběžné koncové body v kontejneru.
Pro váš kód nastavte koncový bod na <hostitele:port>/rozpoznávání řeči/interaktivní/cognitiveservices/v1

Různé režimy viz Režimy řeči – viz níže:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Správná oprava přichází s Sadou SDK 1.8, která má podporu on-prem (vybere správný koncový bod, takže nebudeme horší než online služba). Mezitím je tu vzorek pro neustálé rozpoznávání, proč na něj neukážeme?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Jaký režim mám použít pro různé zvukové soubory?</b>
</summary>

**Odpověď:** Zde je [rychlý start pomocí Pythonu](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-python). Další jazyky propojené na webu docs najdete.

Jen pro objasnění pro interaktivní, konverzace a diktování; jedná se o pokročilý způsob, jak určit konkrétní způsob, jakým bude naše služba zpracovávat žádost o řeč. Bohužel pro kontejnery on-prem musíme zadat úplný identifikátor URI (protože obsahuje místní počítač), takže tyto informace unikly z abstrakce. Spolupracujeme s týmem SDK, aby chomáč tak byl v budoucnu použitelnější.

<br>
</details>

<details>
<summary>
<b>Jak můžeme porovnat hrubé měřítko transakcí / sekundy / jádro?</b>
</summary>

**Odpověď:** Zde jsou některé z hrubých čísel očekávat od stávajícího modelu (změní k lepšímu v jednom budeme loď v GA):

- U souborů bude omezení v sadě SDK řeči 2x. Prvních pět sekund zvuku není omezeno. Dekodér je schopen dělat asi 3x v reálném čase. Za tímto účelem celkové využití procesoru bude blízko 2 jádra pro jedno uznání.
- Pro mikrofon to bude 1x v reálném čase. Celkové využití by mělo být asi 1 jádro pro jediné uznání.

To vše lze ověřit z protokolů dockeru. Jsme vlastně výpis řádku s relace a fráze / utterance statistiky, a to zahrnuje čísla RTF.


<br>
</details>

<details>
<summary>
<b>Je běžné rozdělit zvukové soubory na sklíčidla pro použití kontejneru řeči?</b>
</summary>

Můj současný plán je vzít existující zvukový soubor a rozdělit jej na 10 sekund kusy a poslat ty přes kontejner. Je to přijatelný scénář?  Existuje lepší způsob, jak zpracovat větší zvukové soubory s kontejnerem?

**Odpověď:** Stačí použít řeč SDK a dát mu soubor, bude to dělat správnou věc. Proč potřebujete soubor nakouskovat?


<br>
</details>

<details>
<summary>
<b>Jak lze provést spuštění více kontejnerů na stejném hostiteli?</b>
</summary>

Doc říká vystavit jiný port, což mám dělat, ale kontejner LUIS je stále naslouchána na portu 5000?

**Odpověď:** Zkuste `-p <outside_unique_port>:5000`. Například, `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Technické otázky

<details>
<summary>
<b>Jak mohu získat nedávková api &lt;pro zpracování zvuku o 15 sekundách?</b>
</summary>

**Odpověď:** `RecognizeOnce()` V interaktivním režimu zpracovává pouze až 15 sekund zvuku, jako režim je určen pro rozpoznávání řeči příkazu, kde se očekává, že projevy být krátký. Pokud používáte `StartContinuousRecognition()` pro diktování nebo konverzaci, neexistuje žádný limit 15 sekund.


<br>
</details>

<details>
<summary>
<b>Jaké jsou doporučené zdroje, CPU a RAM; pro 50 souběžných žádostí?</b>
</summary>

Kolik souběžných požadavků bude 4 core, 4 GB RAM zvládnout? Pokud budeme muset sloužit například 50 souběžných požadavků, kolik Core a RAM se doporučuje?

**Odpověď:** V reálném čase, 8 `en-US`s naší nejnovější , takže doporučujeme používat více docker kontejnery nad 6 souběžných požadavků. Dostane bláznivější než 16 jader a stane se nerovnoměrným přístupem k paměti (NUMA) citlivým na uzel. Následující tabulka popisuje minimální a doporučené přidělení prostředků pro každý kontejner řeči.

# <a name="speech-to-text"></a>[Převod řeči na text](#tab/stt)

| Kontejner      | Minimální             | Doporučené         |
|----------------|---------------------|---------------------|
| Převod řeči na text | 2 jádra, 2 GB paměti | 4 jádra, 4 GB paměti |

# <a name="custom-speech-to-text"></a>[Vlastní převod řeči na text](#tab/cstt)

| Kontejner             | Minimální             | Doporučené         |
|-----------------------|---------------------|---------------------|
| Vlastní převod řeči na text | 2 jádra, 2 GB paměti | 4 jádra, 4 GB paměti |

# <a name="text-to-speech"></a>[Převod textu na řeč](#tab/tts)

| Kontejner      | Minimální             | Doporučené         |
|----------------|---------------------|---------------------|
| Převod textu na řeč | 1 jádro, 2 GB paměti | 2 jádra, 3 GB paměti |

# <a name="custom-text-to-speech"></a>[Vlastní převod textu na řeč](#tab/ctts)

| Kontejner             | Minimální             | Doporučené         |
|-----------------------|---------------------|---------------------|
| Vlastní převod textu na řeč | 1 jádro, 2 GB paměti | 2 jádra, 3 GB paměti |

***

- Každé jádro musí být alespoň 2,6 GHz nebo rychlejší.
- U souborů bude omezení v sadě Speech SDK 2x (prvních 5 sekund zvuku není omezeno).
- Dekodér je schopen dělat asi 2-3x v reálném čase. Za tímto účelem celkové využití procesoru bude blízko dvou jader pro jedno rozpoznávání. To je důvod, proč nedoporučujeme udržovat více než dvě aktivní připojení na instanci kontejneru. Extrémní strana by bylo dát asi 10 dekodérů na 2x `DS13_V2`v reálném čase v osmi jádrové stroje, jako je . Pro kontejner verze 1.3 a novější, je tu param můžete zkusit nastavení `DECODER_MAX_COUNT=20`.
- Pro mikrofon to bude 1x v reálném čase. Celkové využití by mělo být přibližně na jednom jádru pro jedno rozpoznávání.

Zvažte celkový počet hodin zvuku, který máte. Pokud je číslo velké, chcete-li zlepšit spolehlivost/dostupnost, doporučujeme spustit více instancí kontejnerů, buď na jednom poli nebo na více krabicích za nástroj pro vyrovnávání zatížení. Orchestrace lze provést pomocí Kubernetes (K8S) a Helm, nebo s Docker compose.

Jako příklad pro zpracování 1000 hodin / 24 hodin jsme se pokusili nastavit 3-4 virtuální chodů s 10 instancemi/dekodéry na virtuální hod.

<br>
</details>

<details>
<summary>
<b>Podporuje kontejner řeči interpunkci?</b>
</summary>

**Odpověď:** V kontejneru on-prem máme k dispozici velká písmena (ITN). Interpunkce je závislá na jazyku a není podporována pro některé jazyky, včetně čínštiny a japonštiny.

Máme *do* implicitní a základní interpunkční podporu pro existující `off` kontejnery, ale je ve výchozím nastavení. Co to znamená, že `.` můžete získat znak v `。` příkladu, ale ne znak. Chcete-li povolit tuto implicitní logiku, tady je příklad, jak to udělat v Pythonu pomocí naší sady Speech SDK (to by bylo podobné v jiných jazycích):

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>Proč se při pokusu o post dat do kontejneru převodu řeči na text zoátáčím o 404 chybách?</b>
</summary>

Zde je příklad HTTP POST:

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**Odpověď:** Nepodporujeme rozhraní REST API v jednom kontejneru řeči na text, podporujeme pouze WebSockets prostřednictvím sady Speech SDK. Vždy se podívejte do oficiální dokumentace, viz [koncové body předpovědi dotazu](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>Proč se při použití služby převodu řeči na text zobrazuje tato chyba?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Odpověď:** K tomu obvykle dochází, když krmíte zvuk rychleji, než může kontejner rozpoznávání řeči převzít. Vyrovnávací paměti klienta se zaplní a zrušení se aktivuje. Je třeba řídit souběžnost a RTF, na kterém odešlete zvuk.

<br>
</details>

<details>
<summary>
<b>Můžete vysvětlit tyto chyby kontejneru pro převod textu na řeč z příkladů jazyka C++?</b>
</summary>

**Odpověď:** Pokud je verze kontejneru starší než 1.3, měl by být použit tento kód:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Starší kontejnery nemají požadovaný koncový bod pro carbon `FromHost` pro práci s rozhraním API. Pokud jsou nádoby použité pro verzi 1.3, měl by být použit tento kód:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Níže je uveden příklad `FromEndpoint` použití rozhraní API:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 Funkce `SetSpeechSynthesisVoiceName` je volána, protože kontejnery s aktualizovaným modulem pro převod textu na řeč vyžadují hlasový název.

<br>
</details>

<details>
<summary>
<b>Jak lze použít v1.7 sady Speech SDK s kontejnerem řeči?</b>
</summary>

**Odpověď:** Existují tři koncové body v kontejneru řeči pro různé použití, jsou definovány jako režimy řeči – viz níže:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Jsou pro různé účely a používají se odlišně.

[Ukázky Pythonu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py):
- Pro jednotné rozpoznávání (interaktivní režim) s vlastním koncovým bodem (to znamená; `SpeechConfig` parametrem koncového bodu), viz `speech_recognize_once_from_file_with_custom_endpoint_parameters()`.
- Pro průběžné rozpoznávání (režim konverzace) a stačí upravit použít `speech_recognize_continuous_from_file()`vlastní koncový bod, jak je uvedeno výše, naleznete v tématu .
- Chcete-li povolit diktování ve vzorcích jako výše (pouze pokud ji opravdu potřebujete), hned po vytvoření `speech_config`přidejte kód `speech_config.enable_dictation()`.

V C# povolit diktování, vyvolat `SpeechConfig.EnableDictation()` funkci.

### <a name="fromendpoint-apis"></a>`FromEndpoint`Rozhraní api
| Jazyk | Podrobnosti rozhraní API |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-java-stable" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Není v současné době podporována, ani není plánována. |

<br>
</details>

<details>
<summary>
<b>Jak lze použít v1.8 sady Speech SDK s kontejnerem řeči?</b>
</summary>

**Odpověď:** Je tu nové `FromHost` API. Tím se nenahradí ani nezmění žádná existující nastavení API. To jen přidává alternativní způsob, jak vytvořit řeč config pomocí vlastního hostitele.

### <a name="fromhost-apis"></a>`FromHost`Rozhraní api

| Jazyk | Podrobnosti rozhraní API |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-java-stable" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Aktuálně se nepodporuje. |

> Parametry: hostitel (povinné), klíč předplatného (volitelné, pokud můžete službu používat bez něj).

Formát pro `protocol://hostname:port` hostitele `:port` je místo, kde je volitelný (viz níže):
- Pokud je kontejner spuštěn místně, název `localhost`hostitele je .
- Pokud je kontejner spuštěn na vzdáleném serveru, použijte název hostitele nebo adresu IPv4 tohoto serveru.

Příklady parametrů hostitele pro převod řeči na text:
- `ws://localhost:5000`- nezabezpečené připojení k místnímu kontejneru pomocí portu 5000
- `ws://some.host.com:5000`- nezabezpečené připojení k kontejneru běžícímu na vzdáleném serveru

Python ukázky shora, ale použít `host` parametr namísto `endpoint`:

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kontejnery služeb Cognitive Services](speech-container-howto.md)
