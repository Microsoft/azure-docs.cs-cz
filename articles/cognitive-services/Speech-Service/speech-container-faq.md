---
title: Nejčastější dotazy ke kontejnerům služby Speech Service (FAQ)
titleSuffix: Azure Cognitive Services
description: Instalace a spuštění kontejnerů řeči. Převod řeči na text transcribes zvukové streamy na text v reálném čase, které mohou aplikace, nástroje nebo zařízení spotřebovat nebo zobrazit. Převod textu na řeč převede vstupní text na syntetizované řeč podobné člověku.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: b13a6944290f58f5ede239dee60610d67fff8b1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88918464"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Nejčastější dotazy ke kontejnerům služby Speech Service (FAQ)

Pokud používáte službu Speech s kontejnery, využijte tuto kolekci nejčastějších dotazů, než předáte podporu. Tento článek zachycuje otázky různého stupně, od obecných až po technický. Chcete-li rozbalit odpověď, klikněte na otázku.

## <a name="general-questions"></a>Obecné otázky

<details>
<summary>
<b>Jak fungují kontejnery řeči a jak je mám nastavit?</b>
</summary>

**Odpověď:** Při nastavování produkčního clusteru je potřeba zvážit několik věcí. První, nastavení jednoho jazyka, více kontejnerů, na stejném počítači by nemělo být velký problém. Pokud dochází k potížím, může se jednat o problém související s hardwarem, takže se nejprve podíváme na zdroj, tj. Specifikace procesoru a paměti.

Vezměte v úvahu okamžik, `ja-JP` kontejner a poslední model. Akustický model je nejnáročnějším PROCESORem, zatímco model jazyka vyžaduje nejvíce paměti. Když jsme používali srovnávací testy, při zpracování zvuku v reálném čase (například z mikrofonu) bere v souvislosti s 0,6 PROCESORovým jádrům zpracování jediné žádosti o převod řeči na text. Pokud budete zvuk podávat rychleji než v reálném čase (například ze souboru), může toto využití zdvojnásobit (1,2 × jader). Níže uvedené množství paměti je provozní paměť pro dekódování řeči. Nebere *v úvahu skutečnou* úplnou velikost jazykového modelu, který se nachází v souborové mezipaměti. To `ja-JP` je další 2 GB. `en-US` může to být více (6-7 GB).

Pokud máte počítač, ve kterém je paměť omezených a pokoušíte se do něj nasadit více jazyků, je možné, že je mezipaměť souborů plná a operační systém je nucen k vynechání modelů stránek a. Pro spuštění přepisu, které by mohlo být katastrofální důsledky a může vést k zpomalení a dalším dopadům na výkon.

Kromě toho jsme představili spustitelné soubory pro počítače se sadou instrukcí [Rozšířené vektorové rozšíření (AVX2)](speech-container-howto.md#advanced-vector-extension-support) . Počítač se sadou instrukcí AVX512 bude vyžadovat generování kódu pro tento cíl a od deseti kontejnerů se může dočasně vyčerpat procesor. V protokolech Docker se zobrazí zpráva, jako je tato:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Nakonec můžete nastavit počet dekodérů, které chcete v *jednom* kontejneru, pomocí `DECODER MAX_COUNT` proměnné. Proto byste měli začít s vaší jednotkou SKU (procesor/paměť) a můžeme navrhnout, jak to máme nejlépe. Skvělý výchozí bod odkazuje na Doporučené specifikace prostředků hostitelského počítače.

<br>
</details>

<details>
<summary>
<b>Mohli byste pomáhat s plánováním kapacity a odhadem nákladů na kontejnery Prem řeči na text?</b>
</summary>

**Odpověď:** V případě kapacity kontejneru v režimu dávkového zpracování by každý dekodér mohl zvládnout 2 – 3x v reálném čase se dvěma jádry PROCESORU pro jedno rozpoznání. Nedoporučujeme udržovat pro jednu instanci kontejneru více než dva souběžné rozpoznávání, ale doporučujeme spouštět více instancí kontejnerů z důvodů spolehlivosti/dostupnosti za nástrojem pro vyrovnávání zatížení.

I když můžeme mít každou instanci kontejneru spuštěnou s dalšími dekodéry. V osmi základních počítačích může být například možné nastavit 7 dekodérů na instanci kontejneru (při více než dvojnásobku každého), což vede k 15x propustnosti. Existuje parametr, `DECODER_MAX_COUNT` na který je třeba vědět. V případě problémů s vysokým případem, spolehlivosti a latence dojde k výraznému nárůstu propustnosti. V případě mikrofonu se bude nacházet v čase 1x v reálném čase. Celkové využití by mělo být v souvislosti s jedním jádrem pro jedno rozpoznávání.

Scénář zpracování 1 K hodinám za den v režimu dávkového zpracování v extrémním případě by mohl tyto 3 virtuální počítače zpracovat během 24 hodin, ale nezaručuje se jim. Pro řízení dní špičky, převzetí služeb při selhání, aktualizace a zajištění minimálního zálohování/BCP doporučujeme 4-5 počítačů místo 3 na cluster a s 2 + clustery.

V případě hardwaru používáme jako referenci standardní virtuální počítač Azure `DS13_v2` (každý jádro musí být 2,6 GHz nebo vyšší, přičemž je povolená sada instrukcí AVX2).

| Instance  | vCPU (celkem) | Paměť RAM    | Dočasné úložiště | Průběžné platby s AHB | Rezerva na 1 rok s AHB (úspory v%) | za 3 roky rezervované s AHB (úspory v%) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | $0.598 za hodinu            | $0.3528 za hodinu (~ 41%)                 | $0.2333 za hodinu (~ 61%)                  |

Na základě referenčních informací o návrhu (dva clustery s 5 virtuálními počítači, které budou zpracovávat 1 – hodiny za den zpracování zvukových dávek), budou mít roční náklady na hardware na 1 rok:

> 2 (clustery) * 5 (počet virtuálních počítačů na cluster) * $0.3528/hod * 365 (dny) * 24 (hodiny) = $31K/rok

Při mapování na fyzický počítač je obecný odhad 1 vCPU = 1 jádro fyzického procesoru. Ve skutečnosti je 1vCPU výkonnější než jedna jádro.

Pro Prem všechny tyto další faktory přicházejí do hry:

- Informace o tom, jaký typ fyzického procesoru je a kolik jader je v něm k disřadě
- Kolik procesorů běží dohromady ve stejném poli/počítači
- Jak se nastavují virtuální počítače
- Jak se používá technologie Hyper-Threading/Multithreading s více vlákny
- Způsob sdílení paměti
- Operační systém atd.

Obvykle se nejedná o prostředí Azure. S ohledem na jinou režii jsem řekněme, že bezpečný odhad je 10 fyzických jader procesoru = 8 Azure vCPU. I když populární procesory mají jenom osm jader. Při nasazení on-Prem budou náklady vyšší než používání virtuálních počítačů Azure. Zvažte také odpisovou sazbu.

Náklady na službu jsou stejné jako online služba: $1/hodina pro převod řeči na text. Náklady na službu řeči:

> $1 * 1000 * 365 = $365K

Náklady na údržbu placené Microsoftu závisí na úrovni služby a obsahu služby. Pro základní úroveň na stovky tisíc v případě, že je zapojená služba na pracovišti, se v různých měsících 29.99 měsíčně. Hrubý počet je 300 USD za hodinu pro servis a údržbu. Náklady na lidi nejsou zahrnuté. Jiné náklady na infrastrukturu (například úložiště, sítě a nástroje pro vyrovnávání zatížení) nejsou zahrnuty.

<br>
</details>

<details>
<summary>
<b>Proč v přepisu chybí interpunkční znaménka?</b>
</summary>

**Odpověď:** V `speech_recognition_language=<YOUR_LANGUAGE>` případě, že klient používá klienta, musí být explicitně nakonfigurován v žádosti.

Například:

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
<b>Můžu používat vlastní akustický model a jazykový model s kontejnerem řeči?</b>
</summary>

V současné době je možné předat pouze jedno ID modelu, vlastní jazykový model nebo vlastní akustický model.

**Odpověď:** Rozhodnutí, že *se současně nepodporují současně* akustické i jazykové modely. Tato akce zůstane v platnosti, dokud se nevytvoří jednotný identifikátor, aby se snížilo rozdělení rozhraní API. To se ale bohužel v tuto chvíli nepodporuje.

<br>
</details>

<details>
<summary>
<b>Mohli byste tyto chyby vysvětlit z vlastního kontejneru řeči do textu?</b>
</summary>

**Chyba 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Odpověď 1:** Pokud budete školení používat nejnovější vlastní model, momentálně to nepodporujeme. Pokud budete používat starší verzi, měla by být možná. Pořád pracujeme na podpoře nejnovějších verzí.

V podstatě vlastní kontejnery nepodporují zvukové modely založené na Halide nebo ONNX (což je výchozí nastavení na portálu Custom Training). Je to kvůli tomu, že se vlastní modely nešifrují a nechceme vystavovat modely ONNX, ale jazykové modely jsou přesné. Zákazník bude muset explicitně vybrat starší model, který není ONNX, pro vlastní školení. Tato přesnost nebude ovlivněna. Velikost modelu může být větší (o 100 MB).

> Model podpory > 20190220 (sjednocená verze 4.5)

**Chyba 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Odpověď 2:** V požadavku musíte zadat správný název hlasu, což rozlišuje velká a malá písmena. Přečtěte si úplné mapování názvu služby. Je nutné použít `en-US-JessaRUS` , protože není `en-US-JessaNeural` nyní k dispozici ve verzi kontejneru převodu textu na řeč.

**Chyba 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Odpověď 3:** Reed se vytvořit prostředek pro rozpoznávání řeči, ne prostředek Cognitive Services.


<br>
</details>

<details>
<summary>
<b>Jaké protokoly rozhraní API jsou podporovány, REST nebo WS?</b>
</summary>

**Odpověď:** V případě převodů řeči na text a vlastních kontejnerů řeči na text momentálně podporujeme jenom protokol založený na protokolu WebSocket. Sada SDK podporuje pouze volání v WS, ale ne REST. Je k dispozici plán pro přidání podpory REST, ale ne ETA. Vždy se podívejte na oficiální dokumentaci, přečtěte si téma [koncové body předpovědi dotazů](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>Je CentOS podporováno pro kontejnery řeči?</b>
</summary>

**Odpověď:** Sada Python SDK zatím nepodporuje CentOS 7, ani Ubuntu 19,04 se nepodporuje.

Balíček python Speech SDK je k dispozici pro tyto operační systémy:
- **Windows** – x64 a x86
- **Mac** – MacOS X verze 10,12 nebo novější
- **Linux** -Ubuntu 16,04, Ubuntu 18,04, Debian 9 na platformě x64

Další informace o nastavení prostředí najdete v tématu [instalace platformy Python](quickstarts/setup-platform.md?pivots=programming-language-python). V současnosti je doporučená verze Ubuntu 18,04.

<br>
</details>

<details>
<summary>
<b>Proč se při pokusu o volání koncových bodů předpovědi LUIS zobrazují chyby?</b>
</summary>

Používám kontejner LUIS ve IoT Edge nasazení a snaží se volat koncový bod předpovědi LUIS z jiného kontejneru. Kontejner LUIS naslouchá na portu 5001 a adresa URL, kterou používám, je:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

Zobrazuje se chyba:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

Zobrazila se žádost v protokolech kontejneru LUIS a zpráva říká:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Co to znamená? Co mi chybí? Používal (a) jsem za ukázku pro sadu Speech SDK [.](https://github.com/Azure-Samples/cognitive-services-speech-sdk) Scénář je, že detekujeme zvuk přímo z PC Microphone a snažíte se určit záměr na základě vámi vyškolené aplikace LUIS. Příkladem, na který odkazuje, je přesně to. A funguje dobře s cloudovou službou LUIS. Díky sadě Speech SDK se můžete s tím, že si nemusíte udělat samostatné explicitní volání rozhraní API pro převod řeči na text a pak druhé volání LUIS.

A to vše, co se snažím udělat, je přepnout ze scénáře použití LUIS v cloudu na používání kontejneru LUIS. Nemůžu si představit, že sada Speech SDK funguje pro jednu, nebude fungovat pro ostatní.

**Odpověď:** Sada Speech SDK by se neměla používat pro LUIS kontejner. Pro použití kontejneru LUIS by měla být použita sada LUIS SDK nebo LUIS REST API. Sada Speech SDK by měla být použita proti kontejneru řeči.

Cloud se liší od kontejneru. Cloud se může skládat z několika agregovaných kontejnerů (někdy označovaných jako mikroslužby). Proto je k dispozici kontejner LUIS a potom je kontejner řeči – dva samostatné kontejnery. Kontejner řeči funguje pouze v řeči. Kontejner LUIS provádí pouze LUIS. Vzhledem k tomu, že je známo, že jsou oba kontejnery nasazené a že se jedná o špatný výkon vzdáleného klienta pro přechod do cloudu, rozpoznávání řeči, vrácení zpět a následné přechod do cloudu a LUIS, poskytujeme funkci, která klientovi umožňuje přejít na řeč, zůstat v cloudu, přejít na LUIS a pak se vrátit ke klientovi. I když v tomto scénáři sada Speech SDK přejde do cloudového kontejneru řeči se zvukem, a potom hlasové rozhovory cloudového kontejneru LUIS do cloudového kontejneru s textem. Kontejner LUIS nemá žádný koncept přijmout zvuk (není smyslem, aby kontejner LUIS přijímal zvukové streamování – LUIS je textová služba). V Prem jsme nemuseli mít jistotu, že zákazník nasadil oba kontejnery, a pokud jsou oba kontejnery nasazené v místním prostředí, a pokud jsou oba kontejnery nasazeny v-Prem, nejedná se o nezatíženou službu SR, vraťte se ke klientovi a poznáte ho tak, aby se dalo na LUIS.

<br>
</details>

<details>
<summary>
<b>Proč dochází k chybám pomocí macOS, kontejneru řeči a sady Python SDK?</b>
</summary>

Když pošleme soubor *. wav* , který se má přepisu, vrátí se výsledek:

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

Víme, že je WebSocket správně nastavený.

**Odpověď:** Pokud se jedná o tento případ, podívejte se na [Tento problém GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310). [Tady je navržený](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)problém.

Uhlík byl opraven na verzi 1,8.


<br>
</details>

<details>
<summary>
<b>Jaké jsou rozdíly v koncových bodech kontejneru řeči?</b>
</summary>

Mohli byste vám pomáhat s plněním následujících metrik testu, včetně toho, které funkce testovat a jak testovat sadu SDK a rozhraní REST API? Obzvláště rozdíly v "interaktivních" a "konverzacích", které jsem nevidí od existující dokumentace/ukázky.

| Koncový bod                                                | Funkční test                                                   | Sada SDK | REST API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Text z syntezátoru (převod textu na řeč)                                  |     | Yes      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Cognitive Services koncový bod protokolu WebSocket pro diktování Prem v1        | Yes | No       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | Koncový bod Prem Interactive v1 WebSocket v Cognitive Services  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | Koncový bod WebSocket v Prem konverzaci v1 služby vnímání |     |          |

**Odpověď:** Toto je Fusion:
- Lidé, kteří se pokoušejí koncový bod diktování pro kontejnery, (nevím, jak si tuto adresu URL dostali)
- 1 koncový<sup>bod strany,</sup> který je v kontejneru.
- <sup>1 koncový</sup> bod koncového bodu vrací Speech. fragment zprávy namísto `speech.hypothesis` zpráv. 3 koncové body částí<sup>vzdálené plochy</sup> se vrátí pro koncový bod diktování.
- Uhlí Starter všechny použití `RecognizeOnce` (interaktivní režim)
- Uhlík s kontrolním výrazem, který pro `speech.fragment` zprávy vyžadující, že nejsou vraceny v interaktivním režimu.
- Uhlík, který má kontrolní výraz v sestavení vydaných verzí (ukončuje se proces)

Alternativní řešení je buď přepnout na použití průběžného rozpoznávání v kódu, nebo (rychlejší) připojit se k interaktivním nebo souvislým koncovým bodům v kontejneru.
Pro váš kód nastavte koncový bod na `host:port` /Speech/Recognition/Interactive/cognitiveservices/v1

Různé režimy najdete v tématu režimy řeči – viz níže:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Správná Oprava se koná v sadě SDK 1,8, která má podporu on-Prem (vybírá správný koncový bod, takže nebudeme se od online služby lišit). Do té doby existuje ukázka pro průběžné rozpoznávání, proč na ni neodkazujeme?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Jaký režim mám použít pro různé zvukové soubory?</b>
</summary>

**Odpověď:** Tady je [rychlý Start s použitím Pythonu](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-python). Další jazyky, které jsou propojené na webu Docs, najdete v části.

Jenom pro objasnění interaktivních, konverzací a diktování; Toto je pokročilý způsob určení konkrétního způsobu, jakým bude naše služba zpracovávat požadavky na řeč. Pro kontejnery on-Prem je ale nutné zadat úplný identifikátor URI (protože zahrnuje místní počítač), proto tyto informace nedošlo k úniku z abstrakce. Spolupracujeme s týmem sady SDK, aby bylo možné ho v budoucnu využít.

<br>
</details>

<details>
<summary>
<b>Jak můžeme v rámci srovnávacích testů určit hrubou míru transakcí za sekundu/jader?</b>
</summary>

**Odpověď:** Tady jsou některá z hrubých čísel, která je potřeba z existujícího modelu očekávat (v případě, že se to bude lépe nadáme v GA):

- V případě souborů se omezuje omezení v sadě Speech SDK na 2x. Prvních pět sekund zvukového zvuku není omezeno. Dekodér je schopný v 3x v reálném čase. V takovém případě se celkové využití CPU blíží k 2 jádrům pro jedno rozpoznávání.
- Pro MIC se bude nacházet v čase 1x v reálném čase. Celkové využití by mělo být v přibližně 1 jádru pro jedno rozpoznávání.

To se dá ověřit z protokolů Docker. Ve skutečnosti vypíšeme řádek s údaji o relaci a frázi/utterance, který obsahuje čísla ve formátu RTF.


<br>
</details>

<details>
<summary>
<b>Je běžné rozdělit zvukové soubory do Chucks pro použití kontejneru řeči?</b>
</summary>

V mém aktuálním plánu si můžete vzít existující zvukový soubor a rozdělit ho do 10 sekund bloků dat a odeslat je prostřednictvím kontejneru. Je to přijatelný scénář?  Existuje lepší způsob, jak zpracovat větší zvukové soubory pomocí kontejneru?

**Odpověď:** Stačí použít sadu Speech SDK a dát jí soubor, který bude mít správnou věc. Proč potřebujete soubor zablokovat?


<br>
</details>

<details>
<summary>
<b>Návody nastavit více kontejnerů na stejném hostiteli?</b>
</summary>

V dokumentu se říká, jak vystavit jiný port, který mám dělat, ale kontejner LUIS pořád naslouchá na portu 5000?

**Odpověď:** Zkuste `-p <outside_unique_port>:5000` . Například, `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Technické dotazy

<details>
<summary>
<b>Jak získám jiná než dávková rozhraní API pro zpracování zvuku &lt; 15 sekund?</b>
</summary>

**Odpověď:** `RecognizeOnce()` v interaktivním režimu se jenom zpracovávají až 15 sekund, protože režim je určený pro příkazy řeči, u kterých se očekává, že projevy bude krátká. Pokud použijete funkci `StartContinuousRecognition()` pro diktování nebo konverzaci, není limit 15 sekund.


<br>
</details>

<details>
<summary>
<b>Jaké jsou doporučené prostředky, procesor a paměť RAM; pro 50 souběžných požadavků?</b>
</summary>

Kolik souběžných požadavků povede k 4 jádrům, 4 GB popisovače RAM? Pokud budeme muset zajišťovat například 50 souběžných požadavků, kolik jader a paměti RAM se doporučuje?

**Odpověď:** V reálném čase 8 s naší nejnovější `en-US` , doporučujeme použít více kontejnerů Docker nad rámec 6 souběžných požadavků. Získá crazier nad 16 jádry a stane se neuniformní přístupem k paměti (NUMA), který je citlivý na uzel. Následující tabulka popisuje minimální a doporučené přidělení prostředků pro každý kontejner řeči.

# <a name="speech-to-text"></a>[Převod řeči na text](#tab/stt)

| Kontejner      | Minimum             | Doporučeno         |
|----------------|---------------------|---------------------|
| Převod řeči na text | 2 jádra, 2 GB paměti | 4 jádra, 4 GB paměti |

# <a name="custom-speech-to-text"></a>[Custom Speech na text](#tab/cstt)

| Kontejner             | Minimum             | Doporučeno         |
|-----------------------|---------------------|---------------------|
| Custom Speech na text | 2 jádra, 2 GB paměti | 4 jádra, 4 GB paměti |

# <a name="text-to-speech"></a>[Převod textu na řeč](#tab/tts)

| Kontejner      | Minimum             | Doporučeno         |
|----------------|---------------------|---------------------|
| Převod textu na řeč | 1 jádro, 2 GB paměti | 2 jádra, 3 GB paměti |

# <a name="custom-text-to-speech"></a>[Vlastní převod textu na řeč](#tab/ctts)

| Kontejner             | Minimum             | Doporučeno         |
|-----------------------|---------------------|---------------------|
| Vlastní převod textu na řeč | 1 jádro, 2 GB paměti | 2 jádra, 3 GB paměti |

***

- Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.
- V případě souborů se omezuje omezení v sadě Speech SDK na 2x (prvních 5 sekund zvukového zvuku se neomezuje).
- Dekodér je schopný o 2 – 3x v reálném čase. V tomto případě bude celkové využití procesoru blízko dvou jader pro jedno rozpoznávání. Proto nedoporučujeme udržovat více než dvě aktivní připojení na jednu instanci kontejneru. Extrémní strana by měla být o 10 dekodérech v dvojnásobné reálné době v osmi základních počítačích, jako je `DS13_V2` . Pro kontejner verze 1,3 a novější je k dispozici parametr, který byste si mohli zkusit nastavit `DECODER_MAX_COUNT=20` .
- V případě mikrofonu se bude nacházet v čase 1x v reálném čase. Celkové využití by mělo být v souvislosti s jedním jádrem pro jedno rozpoznávání.

Vezměte v úvahu celkový počet hodin zvukového zvuku. Pokud je toto číslo velké, aby se zlepšila spolehlivost a dostupnost, doporučujeme, abyste spustili více instancí kontejnerů, a to buď v jednom poli, nebo ve více polích za nástrojem pro vyrovnávání zatížení. Orchestraci můžete provést pomocí Kubernetes (K8S) a Helm nebo pomocí Docker.

Například pro zpracování 1000 hodin za 24 hodin jsme zkusili nastavit virtuální počítače na 3-4 s 10 instancemi/dekodéry na jeden virtuální počítač.

<br>
</details>

<details>
<summary>
<b>Podporuje kontejner řeči interpunkční znaménka?</b>
</summary>

**Odpověď:** V kontejneru on-Prem je k dispozici velká a malá písmena (vytvořené). Interpunkce je závislá na jazyku a není podporovaná pro některé jazyky, včetně čínských a japonských.

Pro existující *kontejnery máme podporu* implicitních a základních interpunkčních znamének, ale `off` ve výchozím nastavení je to. To znamená, že můžete získat `.` znak v příkladu, ale ne `。` znak. Pokud chcete povolit tuto implicitní logiku, tady je příklad, jak to udělat v Pythonu pomocí naší sady Speech SDK (ta by byla podobná v jiných jazycích):

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
<b>Proč dochází k chybám 404 při pokusu o odeslání dat do kontejneru převodu řeči na text?</b>
</summary>

Tady je příklad HTTP POST:

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

**Odpověď:** Nepodporujeme REST API v kontejneru pro převod řeči na text, podporujeme jenom WebSockets prostřednictvím sady Speech SDK. Vždy se podívejte na oficiální dokumentaci, přečtěte si téma [koncové body předpovědi dotazů](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>Proč se při používání služby Speech-to-text zobrazuje tato chyba?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Odpověď:** K tomu obvykle dochází, když zvuk podáváte rychleji, než je kontejner rozpoznávání řeči může převzít. Vyrovnávací paměti klienta se naplní a zrušení se aktivuje. Je nutné řídit souběžnost a RTF, na kterých se zvuk posílá.

<br>
</details>

<details>
<summary>
<b>Mohli byste tyto chyby kontejneru textu na řeč vysvětlit z příkladů jazyka C++?</b>
</summary>

**Odpověď:** Pokud je verze kontejneru starší než 1,3, měl by se tento kód použít:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Starší kontejnery nemají požadovaný koncový bod pro uhlí pro práci s `FromHost` rozhraním API. Pokud jsou kontejnery používané pro verzi 1,3, je třeba použít tento kód:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Níže je příklad použití `FromEndpoint` rozhraní API:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 `SetSpeechSynthesisVoiceName`Funkce je volána, protože kontejnery s aktualizovaným modulem pro převod textu na řeč vyžadují hlasový název.

<br>
</details>

<details>
<summary>
<b>Jak můžu použít sadu Speech SDK v 1.7 pomocí kontejneru řeči?</b>
</summary>

**Odpověď:** Existují tři koncové body kontejneru řeči pro různá použití, jsou definovány jako režimy řeči – viz níže:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Jsou určené pro různé účely a jsou používány odlišně.

[Ukázky](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)Pythonu:
- Pro jednoduché rozpoznávání (interaktivní režim) s vlastním koncovým bodem (tj. `SpeechConfig` s parametrem koncového bodu) naleznete v tématu `speech_recognize_once_from_file_with_custom_endpoint_parameters()` .
- V případě nepřetržitého rozpoznávání (režimu konverzace) a pouhým úpravou použití vlastního koncového bodu, který je uveden výše, přečtěte si téma `speech_recognize_continuous_from_file()` .
- Chcete-li povolit diktování v ukázkách, jako jsou výše (pouze pokud to opravdu potřebujete), hned po vytvoření `speech_config` přidejte kód `speech_config.enable_dictation()` .

V jazyce C# Chcete-li povolit diktování, volejte `SpeechConfig.EnableDictation()` funkci.

### <a name="fromendpoint-apis"></a>`FromEndpoint` Třídy
| Jazyk | Podrobnosti rozhraní API |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-java-stable" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | V současné době není podporována, ani není plánována. |

<br>
</details>

<details>
<summary>
<b>Jak můžu použít sadu Speech SDK pro sadu Speech v kontejneru řeči?</b>
</summary>

**Odpověď:** Existuje nové `FromHost` rozhraní API. Nenahrazuje ani nemění žádná existující rozhraní API. Jenom přidá alternativní způsob, jak vytvořit konfiguraci řeči pomocí vlastního hostitele.

### <a name="fromhost-apis"></a>`FromHost` Třídy

| Jazyk | Podrobnosti rozhraní API |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-java-stable" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Aktuálně se nepodporuje. |

> Parametry: hostitel (povinný), klíč předplatného (volitelné, pokud můžete službu použít bez ní).

Formát pro hostitele je `protocol://hostname:port` `:port` volitelné (viz níže):
- Pokud je kontejner spuštěn místně, název hostitele je `localhost` .
- Pokud je kontejner spuštěn na vzdáleném serveru, použijte název hostitele nebo adresu IPv4 tohoto serveru.

Příklady parametrů hostitele pro převod řeči na text:
- `ws://localhost:5000` – nezabezpečené připojení k místnímu kontejneru pomocí portu 5000
- `ws://some.host.com:5000` – nezabezpečené připojení k kontejneru běžícímu na vzdáleném serveru

Ukázky Pythonu výše, ale použijte `host` parametr místo `endpoint` :

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kontejnery Cognitive Services](speech-container-howto.md)
