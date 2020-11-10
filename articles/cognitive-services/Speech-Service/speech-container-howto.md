---
title: Instalace a spuštění kontejnerů Docker pro rozhraní API služby pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Pomocí kontejnerů Docker pro službu Speech můžete provádět rozpoznávání řeči, přepis, generaci a další místní prostředí.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: aahi
ms.custom: cog-serv-seo-aug-2020
keywords: místní, Docker, kontejner
ms.openlocfilehash: f91d96732c872c6f93ee2de4c5c3eba5fe5ffbc4
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412234"
---
# <a name="install-and-run-docker-containers-for-the-speech-service-apis"></a>Instalace a spuštění kontejnerů Docker pro rozhraní API služby pro rozpoznávání řeči 

Kontejnery umožňují spouštět některá z rozhraní API služby Speech ve vlastním prostředí. Kontejnery jsou skvělé pro splnění určitých požadavků na zabezpečení a zásady správného řízení dat. V tomto článku se dozvíte, jak stáhnout, nainstalovat a spustit kontejner služby Speech.

Kontejnery služby Speech umožňují zákazníkům vytvořit architekturu aplikace pro zpracování řeči, která je optimalizovaná pro robustní cloudové funkce i umístění v hraničních zařízeních. K dispozici je několik kontejnerů, které používají stejné [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) jako cloudové služby Azure Speech.


> [!IMPORTANT]
> K dispozici jsou nyní obecně dostupné následující kontejnery řeči:
> * Standardní převod řeči na text
> * Custom Speech na text
> * Standardní převod textu na řeč
> 
> Následující kontejnery řeči jsou v ověřovaném náhledu.
> * Vlastní převod textu na řeč
> * Rozpoznávání jazyka řeči 
> * Neuronové převodu textu na řeč
>
> Chcete-li použít kontejnery řeči, musíte odeslat online žádost a nechat ji schválit. Další informace najdete v části **schválení žádosti v níže uvedeném kontejneru spustit kontejner** .

| Kontejner | Funkce | Latest (Nejnovější) |
|--|--|--|
| Převod řeči na text | Analyzuje mínění a transcribes nepřetržité zvukové nahrávky v reálném čase s využitím mezilehlého výsledku.  | 2.6.0 |
| Custom Speech na text | Pomocí vlastního modelu z [Custom Speechového portálu](https://speech.microsoft.com/customspeech)transcribes hlasové nahrávky v reálném čase nebo zvukové nahrávky do textu s mezilehlé výsledky. | 2.6.0 |
| Převod textu na řeč | Převede text na přirozený zvuk řeči pomocí prostého textu nebo jazyka SSML (Speech syntézy). | 1.8.0 |
| Vlastní převod textu na řeč | Pomocí vlastního modelu z [vlastního hlasového portálu](https://aka.ms/custom-voice-portal)převede převod textu na přirozený zvuk hlasu pomocí formátu prostého textu nebo jazyka SSML (Speech syntézy). | 1.8.0 |
| Rozpoznávání jazyka řeči | Zjišťuje jazyk používaný v zvukových souborech. | 1,0 |
| Neuronové převodu textu na řeč | Převede text na přirozený zvuk hlasu pomocí vysoce neuronové síťové technologie a umožní vám tak více přirozeného řeči. | 1.2.0 |

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů řeči je nutné splnit následující předpoklady:

| Vyžadováno | Účel |
|--|--|
| Docker Engine | Potřebujete modul Docker nainstalovaný na [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které nakonfigurují prostředí Dockeru v systému [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).<br><br> Docker musí být nakonfigurovaný tak, aby umožňoval kontejnerům připojit se a odeslat fakturační data do Azure. <br><br> **V systému Windows** musí být Docker taky nakonfigurovaný tak, aby podporoval kontejnery Linux.<br><br> |
| Znalost pomocí Docker | Měli byste mít základní znalosti konceptů Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů, a taky znalosti základních `docker` příkazů. |
| Prostředek řeči | Aby bylo možné tyto kontejnery použít, je nutné mít následující:<br><br>Prostředek Azure _Speech_ pro získání přidruženého klíče rozhraní API a identifikátoru URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách s přehledem a klíči pro Azure Portal **řeči** . Oba jsou nutné ke spuštění kontejneru.<br><br>**{API_KEY}** : jeden ze dvou dostupných klíčů prostředků na stránce **klíče**<br><br>**{ENDPOINT_URI}** : koncový bod uvedený na stránce **Přehled** |

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Rozšířená podpora rozšíření Vector

**Hostitel** je počítač, který spouští kontejner Docker. Hostitel *musí podporovat* [Rozšířená rozšíření Vector](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Podporu AVX2 pro hostitele se systémem Linux můžete vyhledat pomocí následujícího příkazu:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> K podpoře AVX2 se *vyžaduje* hostitelský počítač. *Kontejner nebude* správně fungovat bez podpory AVX2.

### <a name="container-requirements-and-recommendations"></a>Požadavky na kontejner a doporučení

Následující tabulka popisuje minimální a doporučené přidělení prostředků pro každý kontejner řeči.

| Kontejner | Minimum | Doporučeno |
|-----------|---------|-------------|
| Převod řeči na text | 2 jádra, 2 GB paměti | 4 jádra, 4 GB paměti |
| Custom Speech na text | 2 jádra, 2 GB paměti | 4 jádra, 4 GB paměti |
| Převod textu na řeč | 1 jádro, 2 GB paměti | 2 jádra, 3 GB paměti |
| Vlastní převod textu na řeč | 1 jádro, 2 GB paměti | 2 jádra, 3 GB paměti |
| Rozpoznávání jazyka řeči | 1 jádro, 1 GB paměti | 1 jádro, 1 GB paměti |
| Neuronové převodu textu na řeč | 6 jader, 12 GB paměti | 8 jader, 16 GB paměti |

* Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.

Základní a paměť odpovídají `--cpus` `--memory` nastavení a, která se používají jako součást `docker run` příkazu.

> [!NOTE]
> Minimum a doporučené jsou založeny na omezeních Docker, *nikoli* na prostředky hostitelského počítače. Například kontejnery Speech-to-text namapují část velkých jazykových modelů a *doporučuje* se, aby se celý soubor vešel do paměti, což je dalších 4-6 GB. První spuštění obou kontejnerů může trvat delší dobu, protože modely jsou stránkované v paměti.

## <a name="request-approval-to-the-run-the-container"></a>Žádost o schválení ke spuštění kontejneru

Vyplňte a odešlete [formulář žádosti](https://aka.ms/csgate) pro vyžádání přístupu ke kontejneru. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]


## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru pomocí `docker pull`

Obrázky kontejneru pro řeč jsou k dispozici v následujících Container Registry.

# <a name="speech-to-text"></a>[Převod řeči na text](#tab/stt)

| Kontejner | Repository |
|-----------|------------|
| Převod řeči na text | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Custom Speech na text](#tab/cstt)

| Kontejner | Repository |
|-----------|------------|
| Custom Speech na text | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Převod textu na řeč](#tab/tts)

| Kontejner | Repository |
|-----------|------------|
| Převod textu na řeč | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest` |

# <a name="neural-text-to-speech"></a>[Neuronové převodu textu na řeč](#tab/ntts)

| Kontejner | Repository |
|-----------|------------|
| Neuronové převodu textu na řeč | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Vlastní převod textu na řeč](#tab/ctts)

| Kontejner | Repository |
|-----------|------------|
| Vlastní převod textu na řeč | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest` |

# <a name="speech-language-detection"></a>[Rozpoznávání jazyka řeči](#tab/lid)

> [!TIP]
> Chcete-li získat nejužitečnější výsledky, doporučujeme použít kontejner rozpoznávání jazyka rozpoznávání řeči s vlastními kontejnery řeči a textu. 

| Kontejner | Repository |
|-----------|------------|
| Rozpoznávání jazyka řeči | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Vyžádání obsahu Docker pro kontejnery řeči

# <a name="speech-to-text"></a>[Převod řeči na text](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Funkce Docker Pull pro kontejner převodu řeči na text

Pomocí příkazu [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) Stáhněte image kontejneru z registru Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest
```

> [!IMPORTANT]
> `latest`Značka vyžádá `en-US` národní prostředí. Pro další národní prostředí viz [národní prostředí pro převod řeči na text](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Národní prostředí pro převod textu na text

Všechny značky s výjimkou `latest` jsou v následujícím formátu a rozlišují velká a malá písmena:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

Následující značka je příkladem formátu:

```
2.6.0-amd64-en-us
```

Pro všechna podporovaná národní prostředí kontejneru **převodů řeči** na text se podívejte na [tagy pro obrázky typu převod řeči na text](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-text"></a>[Custom Speech na text](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Vyžádání obsahu Docker pro kontejner Custom Speech-to-text

Pomocí příkazu [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) Stáhněte image kontejneru z registru Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest
```

> [!NOTE]
> `locale` `voice` Vlastní kontejnery řeči a jsou určeny vlastním modelem ingestované kontejnerem.

# <a name="text-to-speech"></a>[Převod textu na řeč](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Vyžádání obsahu Docker pro kontejner převodu textu na řeč

Pomocí příkazu [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) Stáhněte image kontejneru z registru Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest
```

> [!IMPORTANT]
> `latest`Značka vyžádá `en-US` národní prostředí a `ariarus` hlas. Pro další národní prostředí viz [národní prostředí pro převod textu na mluvené slovo](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Národní prostředí pro převod textu na řeč

Všechny značky s výjimkou `latest` jsou v následujícím formátu a rozlišují velká a malá písmena:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

Následující značka je příkladem formátu:

```
1.8.0-amd64-en-us-ariarus
```

U všech podporovaných národních prostředí a odpovídajících hlasů kontejneru **textu na řeč** se podívejte na [značky obrázku pro převod textu na mluvené slovo](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> Při vytváření příspěvku http převodu *textu na řeč* vyžaduje zpráva [SSML (Speech syntézy Language)](speech-synthesis-markup.md) , která má `voice` element s `name` atributem. Hodnota je odpovídající národní prostředí a hlas kontejneru, označovaný také jako ["krátký název"](language-support.md#standard-voices). Například `latest` značka by měla název hlasu `en-US-AriaRUS` .

# <a name="neural-text-to-speech"></a>[Neuronové převodu textu na řeč](#tab/ntts)

#### <a name="docker-pull-for-the-neural-text-to-speech-container"></a>Vyžádané čtení Docker pro kontejner neuronové textu na řeč

Pomocí příkazu [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) Stáhněte image kontejneru z registru Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest
```

> [!IMPORTANT]
> `latest`Značka vyžádá `en-US` národní prostředí a `arianeural` hlas. Další národní prostředí najdete v tématu [neuronové národních prostředí pro převod textu na mluvené slovo](#neural-text-to-speech-locales).

#### <a name="neural-text-to-speech-locales"></a>Neuronové textová prostředí pro převod textu na řeč

Všechny značky s výjimkou `latest` jsou v následujícím formátu a rozlišují velká a malá písmena:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

Následující značka je příkladem formátu:

```
1.2.0-amd64-en-us-arianeural-preview
```

Pro všechna podporovaná národní prostředí a odpovídající hlasy kontejneru **text-řeč neuronové** se podívejte na [značky obrázků neuronové pro převod textu na](../containers/container-image-tags.md#neural-text-to-speech)řeč.

> [!IMPORTANT]
> Při sestavování neuronové příspěvku http pro [Převod](speech-synthesis-markup.md) *textu na řeč* vyžaduje `voice` element s `name` atributem. Hodnota je odpovídající národní prostředí a hlas kontejneru, označovaný také jako ["krátký název"](language-support.md#neural-voices). Například `latest` značka by měla název hlasu `en-US-AriaNeural` .

# <a name="custom-text-to-speech"></a>[Vlastní převod textu na řeč](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Vyžádání obsahu Docker pro vlastní kontejner převodu textu na řeč

Pomocí příkazu [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) Stáhněte image kontejneru z registru Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest
```

> [!NOTE]
> `locale` `voice` Vlastní kontejnery řeči a jsou určeny vlastním modelem ingestované kontejnerem.

# <a name="speech-language-detection"></a>[Rozpoznávání jazyka řeči](#tab/lid)

#### <a name="docker-pull-for-the-speech-language-detection-container"></a>Stažení Docker pro kontejner Rozpoznávání jazyka řeči

Pomocí příkazu [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) Stáhněte image kontejneru z registru Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest
```

***

## <a name="how-to-use-the-container"></a>Jak používat kontejner

Jakmile je kontejner na [hostitelském počítači](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run)s požadovaným nastavením fakturace. [examples](speech-container-configuration.md#example-docker-run-commands) `docker run` K dispozici jsou další příklady příkazu.
1. [Dotazování koncového bodu předpovědi kontejneru](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s `docker run`

Ke spuštění kontejneru použijte příkaz [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) . Podrobnosti o tom, jak získat hodnoty a, najdete v článku [shromáždění požadovaných parametrů](#gathering-required-parameters) `{Endpoint_URI}` `{API_Key}` . [examples](speech-container-configuration.md#example-docker-run-commands) `docker run` K dispozici jsou také další příklady příkazu.

# <a name="speech-to-text"></a>[Převod řeči na text](#tab/stt)

Chcete-li spustit standardní kontejner *řeči na text* , spusťte následující `docker run` příkaz.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spouští kontejner *řeči na text* z image kontejneru.
* Přiděluje 4 jádra procesoru a 4 gigabajty (GB) paměti.
* Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>Analýza mínění na výstup řeči na text 
Počínaje v v 2.6.0 kontejneru převodů textu na text byste měli místo verze Preview použít koncový bod rozhraní TextAnalytics 3,0 API. Například
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0/sentiment`
* `https://localhost:5000/text/analytics/v3.0/sentiment`

> [!NOTE]
> Rozhraní Analýza textu `v3.0` API není zpětně kompatibilní s analýza textu `v3.0-preview.1` . Pokud chcete získat nejnovější podporu funkcí mínění, použijte `v2.6.0` Image kontejneru převodu řeči na text a analýza textu `v3.0` .

Počínaje v v 2.2.0 kontejneru převodu řeči na text můžete zavolat [rozhraní mínění Analysis V3 API](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) na výstup. K volání analýzy mínění budete potřebovat koncový bod prostředku rozhraní API pro analýzu textu. Zde je příklad: 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

Pokud máte přístup ke koncovému bodu služby Text Analytics v cloudu, budete potřebovat klíč. Pokud používáte Analýza textu místně, možná ho nebudete muset zadávat.

Klíč a koncový bod jsou předány do kontejneru řeči jako argumenty, jak je uvedeno v následujícím příkladu.

```bash
docker run -it --rm -p 5000:5000 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

Tento příkaz:

* Provede stejné kroky jako v příkazu výše.
* Ukládá rozhraní API pro analýzu textu koncový bod a klíč pro posílání žádostí o analýzu mínění. 

#### <a name="phraselist-v2-on-the-speech-to-text-output"></a>PhraseList v2 na výstupu z převodu řeči na text 

Počínaje v v 2.6.0 kontejneru převodu řeči na text můžete získat výstup s vašimi vlastními větami – buď celou větu, nebo frázi uprostřed. Například *vyšší muž* v následující větě:

* "Toto je věta **, kterou vysoké je, že** je to další věta."

Chcete-li nakonfigurovat seznam frází, je nutné přidat vlastní fráze při volání. Zde je příklad:

```python
    phrase="the tall man"
    recognizer = speechsdk.SpeechRecognizer(
        speech_config=dict_speech_config,
        audio_config=audio_config)
    phrase_list_grammer = speechsdk.PhraseListGrammar.from_recognizer(recognizer)
    phrase_list_grammer.addPhrase(phrase)

```

Pokud máte více frází pro přidání, zavolejte `.addPhrase()` každou frázi a přidejte ji do seznamu frází. 


# <a name="custom-speech-to-text"></a>[Custom Speech na text](#tab/cstt)

Kontejner *Custom Speech-to-text* spoléhá na vlastní model řeči. Vlastní model se musí [vyškolet](how-to-custom-speech-train-model.md) pomocí [vlastního portálu pro rozpoznávání řeči](https://speech.microsoft.com/customspeech).

> [!IMPORTANT]
> Model Custom Speech musí být vyškolený z jedné z následujících verzí modelů:
> * **20181201 (sjednocené v 3.3)**
> * **20190520 (v 4.14 Unified)**
> * **20190701 (v 4.17 Unified)**<br>
> ![Custom Speech model kontejneru vlaků](media/custom-speech/custom-speech-train-model-container-scoped.png)

Pro spuštění kontejneru je vyžadováno **ID vlastního modelu** řeči. Najdete ho na stránce **školení** na vlastním portálu pro rozpoznávání řeči. Z vlastního portálu pro rozpoznávání řeči přejděte na stránku **školení** a vyberte model.
<br>

![Stránka s vlastním školením pro rozpoznávání řeči](media/custom-speech/custom-speech-model-training.png)

Získejte **ID modelu** , které chcete použít jako argument `ModelId` parametru `docker run` příkazu.
<br>

![Podrobnosti o vlastním modelu řeči](media/custom-speech/custom-speech-model-details.png)

Následující tabulka představuje různé `docker run` parametry a jejich odpovídající popisy:

| Parametr | Popis |
|---------|---------|
| `{VOLUME_MOUNT}` | [Připojení svazku](https://docs.docker.com/storage/volumes/)hostitelského počítače, které Docker používá k trvalému uložení vlastního modelu. Například *C:\CustomSpeech* , kde se *jednotka C* nachází na hostitelském počítači. |
| `{MODEL_ID}` | Custom Speech **ID modelu** ze stránky **školení** na vlastním portálu pro rozpoznávání řeči. |
| `{ENDPOINT_URI}` | Koncový bod je vyžadován pro měření a fakturaci. Další informace najdete v tématu [shromažďování požadovaných parametrů](#gathering-required-parameters). |
| `{API_KEY}` | Klíč rozhraní API je povinný. Další informace najdete v tématu [shromažďování požadovaných parametrů](#gathering-required-parameters). |

Chcete-li spustit kontejner *Custom Speech-text* , spusťte následující `docker run` příkaz:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí kontejner *Custom Speech-to-text* z image kontejneru.
* Přiděluje 4 jádra procesoru a 4 gigabajty (GB) paměti.
* Načte model *Custom Speech-to-text* ze vstupního připojení svazku, například *C:\CustomSpeech*.
* Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
* Stáhne model na základě `ModelId` (pokud nebyl nalezen v připojení svazku).
* Pokud se vlastní model stáhl dříve, `ModelId` ignoruje se.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.


#### <a name="base-model-download-on-the-custom-speech-to-text-container"></a>Stažení základního modelu na vlastní kontejner řeči na text  
Počínaje v v 2.6.0 vlastního kontejneru řeči a textu můžete získat dostupné informace o základním modelu pomocí možnosti `BaseModelLocale=<locale>` . Tato možnost vám poskytne seznam dostupných základních modelů v tomto národním prostředí v rámci vašeho fakturačního účtu. Zde je příklad:

```bash
docker run --rm -it \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
BaseModelLocale={LOCALE} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí kontejner *Custom Speech-to-text* z image kontejneru.
* Zkontroluje a vrátí dostupné základní modely cílového národního prostředí.

Výstup poskytuje seznam základních modelů s informacemi o národním prostředí, ID modelu a datum a čas vytvoření. Pomocí ID modelu můžete stáhnout a používat konkrétní základní model, který dáváte přednost. Zde je příklad:
```
Checking available base model for en-us
2020/10/30 21:54:20 [Info] Searching available base models for en-us
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T08:23:42Z, Id: a3d8aab9-6f36-44cd-9904-b37389ce2bfa
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T12:01:02Z, Id: cc7826ac-5355-471d-9bc6-a54673d06e45
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2017-08-17T12:00:00Z, Id: a1f8db59-40ff-4f0e-b011-37629c3a1a53
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-04-16T11:55:00Z, Id: c7a69da3-27de-4a4b-ab75-b6716f6321e5
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-09-21T15:18:43Z, Id: da494a53-0dad-4158-b15f-8f9daca7a412
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-10-19T11:28:54Z, Id: 84ec130b-d047-44bf-a46d-58c1ac292ca7
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T07:59:09Z, Id: ee5c100f-152f-4ae5-9e9d-014af3c01c56
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T09:21:55Z, Id: d04959a6-71da-4913-9997-836793e3c115
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-01-11T10:04:19Z, Id: 488e5f23-8bc5-46f8-9ad8-ea9a49a8efda
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-02-18T14:37:57Z, Id: 0207b3e6-92a8-4363-8c0e-361114cdd719
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-03-03T17:34:10Z, Id: 198d9b79-2950-4609-b6ec-f52254074a05
2020/10/30 21:54:21 [Fatal] Please run this tool again and assign --modelId '<one above base model id>'. If no model id listed above, it means currently there is no available base model for en-us
```

#### <a name="custom-pronunciation-on-the-custom-speech-to-text-container"></a>Vlastní výslovnost na vlastním kontejneru řeči na text 
Počínaje v v 2.5.0 vlastního kontejneru Speech-to-text můžete získat vlastní výsledek výslovnosti ve výstupu. Stačí udělat, abyste si nastavili vlastní pravidla pro výslovnost ve vašem vlastním modelu a model přiřadíte do vlastního kontejneru řeči-text.


# <a name="text-to-speech"></a>[Převod textu na řeč](#tab/tts)

Pokud chcete spustit standardní kontejner převodu *textu na řeč* , spusťte následující `docker run` příkaz.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí standardní kontejner převodu *textu na řeč* z image kontejneru.
* Přidělí 1 jádro procesoru a 2 gigabajty (GB) paměti.
* Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.

# <a name="neural-text-to-speech"></a>[Neuronové převodu textu na řeč](#tab/ntts)

Pokud chcete spustit kontejner *neuronové textu na řeč* , spusťte následující `docker run` příkaz.

```bash
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí z image kontejneru *neuronovéý kontejner pro převod textu na řeč* .
* Přiděluje 6 procesorových jader a 12 gigabajtů (GB) paměti.
* Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.

# <a name="custom-text-to-speech"></a>[Vlastní převod textu na řeč](#tab/ctts)

Vlastní kontejner převodu *textu na řeč* spoléhá na vlastní hlasový model. Vlastní model se musí [vyškolet](how-to-custom-voice-create-voice.md) pomocí [vlastního hlasového portálu](https://aka.ms/custom-voice-portal). Pro spuštění kontejneru je vyžadováno **ID vlastního modelu** hlasu. Najdete ho na stránce **školení** na vlastním hlasovém portálu. Z vlastního hlasového portálu přejděte na stránku **školení** a vyberte model.
<br>

![Stránka pro vlastní výuku hlasu](media/custom-voice/custom-voice-model-training.png)

Získejte **ID modelu** , které chcete použít jako argument pro `ModelId` parametr příkazu Docker run.
<br>

![Podrobnosti o vlastním hlasovém modelu](media/custom-voice/custom-voice-model-details.png)

Následující tabulka představuje různé `docker run` parametry a jejich odpovídající popisy:

| Parametr | Popis |
|---------|---------|
| `{VOLUME_MOUNT}` | [Připojení svazku](https://docs.docker.com/storage/volumes/)hostitelského počítače, které Docker používá k trvalému uložení vlastního modelu. Například *C:\CustomSpeech* , kde se *jednotka C* nachází na hostitelském počítači. |
| `{MODEL_ID}` | **ID modelu** Custom Speech ze stránky **školení** vlastního hlasového portálu. |
| `{ENDPOINT_URI}` | Koncový bod je vyžadován pro měření a fakturaci. Další informace najdete v tématu [shromažďování požadovaných parametrů](#gathering-required-parameters). |
| `{API_KEY}` | Klíč rozhraní API je povinný. Další informace najdete v tématu [shromažďování požadovaných parametrů](#gathering-required-parameters). |

Chcete-li spustit vlastní kontejner převodu *textu na řeč* , spusťte následující `docker run` příkaz:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí z image kontejneru vlastní kontejner převodu *textu na řeč* .
* Přidělí 1 jádro procesoru a 2 gigabajty (GB) paměti.
* Načte vlastní model převodu *textu na řeč* ze vstupního připojení svazku, například *C:\CustomVoice*.
* Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
* Stáhne model na základě `ModelId` (pokud nebyl nalezen v připojení svazku).
* Pokud se vlastní model stáhl dříve, `ModelId` ignoruje se.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.

# <a name="speech-language-detection"></a>[Rozpoznávání jazyka řeči](#tab/lid)

Pokud chcete spustit *rozpoznávání jazyka kontejneru řeči* , spusťte následující `docker run` příkaz.

```bash
docker run --rm -it -p 5003:5003 --memory 1g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz: 

* Spustí kontejner rozpoznávání jazyka řeči z image kontejneru. V současné době se vám neúčtují za běhu této image.
* Přiděluje 1 jádra procesoru a 1 gigabajt (GB) paměti.
* Zveřejňuje port TCP 5003 a přiděluje pro kontejner pseudo TTY.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.

Pokud posíláte jenom požadavky na řeč Rozpoznávání jazyka, budete muset nastavit hodnotu klienta řeči `phraseDetection` na `None` .  

```python
speech_config.set_service_property(
      name='speechcontext-phraseDetection.Mode',
      value='None',
      channel=speechsdk.ServicePropertyChannel.UriQueryParameter
   )
```

Pokud chcete tento kontejner spustit s kontejnerem převod řeči na text, můžete použít tuto [bitovou kopii Docker](https://hub.docker.com/r/antsu/on-prem-client). Po spuštění obou kontejnerů použijte tento příkaz Docker Run ke spuštění `speech-to-text-with-languagedetection-client` .

```Docker
docker run --rm -v ${HOME}:/root -ti antsu/on-prem-client:latest ./speech-to-text-with-languagedetection-client ./audio/LanguageDetection_en-us.wav --host localhost --lport 5003 --sport 5000
```

> [!NOTE]
> Zvýšení počtu souběžných volání může mít vliv na spolehlivost a latenci. Pro detekci jazyka doporučujeme maximálně 4 souběžných volání s využitím 1 CPU a s a 1 GB paměti. Pro hostitele, kteří mají 2 procesory a 2 GB paměti, doporučujeme maximálně 6 souběžných volání.

***

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby bylo možné spustit kontejner, musí být zadány možnosti, a. v opačném případě se kontejner nespustí.  Další informace najdete v tématu [fakturace](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Zadání dotazu do prediktivního koncového bodu kontejneru

> [!NOTE]
> Pokud spouštíte více kontejnerů, použijte jedinečné číslo portu.

| Kontejnery | Adresa URL hostitele sady SDK | Protokol |
|--|--|--|
| Standardní převod řeči na text a Custom Speech na text | `ws://localhost:5000` | WS |
| Převod textu na řeč (včetně standardních, vlastních a neuronové), detekce jazyka řeči | `http://localhost:5000` | HTTP |

Další informace o používání protokolů WSS a HTTPS najdete v tématu [zabezpečení kontejnerů](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

### <a name="speech-to-text-standard-and-custom"></a>Převod řeči na text (standardní a vlastní)

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>Analýza mínění

Pokud jste zadali přihlašovací údaje rozhraní API pro analýzu textu [do kontejneru](#analyze-sentiment-on-the-speech-to-text-output), můžete použít sadu Speech SDK k posílání požadavků rozpoznávání řeči s analýzou mínění. Odezvy rozhraní API můžete nakonfigurovat tak, aby používaly buď *jednoduchý* nebo *podrobný* formát.
> [!NOTE]
> v 1.13 sady Speech SDK pro hlasové služby je zjištěn problém s analýzou mínění. Pokud používáte analýzu mínění v sadě Speech SDK pro Python Service, použijte prosím 1.12. x nebo starší.

# <a name="simple-format"></a>[Jednoduchý formát](#tab/simple-format)

Chcete-li nakonfigurovat klienta řeči tak, aby používal jednoduchý formát, přidejte `"Sentiment"` jako hodnotu pro `Simple.Extensions` . Pokud chcete zvolit konkrétní verzi modelu Analýza textu, nahraďte `'latest'` ji v `speechcontext-phraseDetection.sentimentAnalysis.modelversion` konfiguraci vlastností.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions` Vrátí výsledek mínění v kořenové vrstvě odpovědi.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[Podrobný formát](#tab/detailed-format)

Chcete-li nakonfigurovat klienta řeči tak, aby používal podrobný formát, přidejte `"Sentiment"` jako hodnotu pro `Detailed.Extensions` , `Detailed.Options` nebo obojí. Pokud chcete zvolit konkrétní verzi modelu Analýza textu, nahraďte `'latest'` ji v `speechcontext-phraseDetection.sentimentAnalysis.modelversion` konfiguraci vlastností.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions` poskytuje výsledek mínění v kořenové vrstvě odpovědi. `Detailed.Options` poskytuje výsledek ve `NBest` vrstvě odpovědi. Dají se použít samostatně nebo společně.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

Pokud chcete zcela zakázat analýzu mínění, přidejte `false` hodnotu do `sentimentanalysis.enabled` .

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-standard-neural-and-custom"></a>Převod textu na řeč (Standard, neuronové a vlastní)

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Spuštění více kontejnerů na stejném hostiteli

Pokud máte v úmyslu spustit více kontejnerů s vystavenými porty, ujistěte se, že každý kontejner spustíte s jiným vystaveným portem. Například spusťte první kontejner na portu 5000 a druhý kontejner na portu 5001.

Můžete mít tento kontejner a jiný kontejner Azure Cognitive Services běžící na hostiteli společně. Můžete mít také více kontejnerů stejného Cognitive Services kontejneru se systémem.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Při spuštění nebo spuštění kontejneru může docházet k problémům. Použijte výstupní [připojení](speech-container-configuration.md#mount-settings) a povolte protokolování. Tím umožníte, aby kontejner generoval soubory protokolu, které jsou užitečné při řešení problémů.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturace

Kontejnery řeči odesílají informace o fakturaci do Azure pomocí prostředku *řeči* ve vašem účtu Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnerů](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Shrnutí

V tomto článku jste zjistili koncepty a pracovní postupy pro stažení, instalaci a spuštění kontejnerů řeči. Souhrn:

* Speech poskytuje pro Docker čtyři kontejnery pro Linux, které zapouzdřují různé možnosti:
  * *Převod řeči na text*
  * *Custom Speech na text*
  * *Převod textu na řeč*
  * *Vlastní převod textu na řeč*
  * *Neuronové převodu textu na řeč*
  * *Rozpoznávání jazyka řeči*
* Image kontejneru se stáhnou z registru kontejneru v Azure.
* Image kontejneru se spouštějí v Docker.
* Bez ohledu na to, jestli se používá REST API (jenom pro převod textu na řeč) nebo sadu SDK (převod řeči na text nebo převod textu na řeč), zadáváte identifikátor URI hostitele kontejneru. 
* Při vytváření instance kontejneru budete muset zadat fakturační údaje.

> [!IMPORTANT]
>  Nemusíte spouštět kontejnery Cognitive Services bez připojení k Azure pro měření. Zákazníci musí povolit kontejnerům, aby ve všech časech komunikovaly informace o fakturaci. Kontejnery Cognitive Services neodesílají zákaznická data (např. obrázek nebo analyzovaný text) do Microsoftu.

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [konfigurace kontejnerů](speech-container-configuration.md) pro nastavení konfigurace
* Naučte [se používat kontejnery služby Speech s Kubernetes a Helm](speech-container-howto-on-premises.md)
* Použít více [Cognitive Servicesch kontejnerů](../cognitive-services-container-support.md)
