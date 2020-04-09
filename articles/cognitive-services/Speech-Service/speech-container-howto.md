---
title: Instalace kontejnerů řeči – služba řeči
titleSuffix: Azure Cognitive Services
description: Nainstalujte a spusťte kontejnery řeči. Převod řeči na text přepne zvukové proudy na text v reálném čase, který mohou aplikace, nástroje nebo zařízení využívat nebo zobrazovat. Převod textu na řeč převede vstupní text na syntetizovanou řeč podobné člověku.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2caae4fecdf13a1833f23cf9423cf3ded67f6f72
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879005"
---
# <a name="install-and-run-speech-service-containers-preview"></a>Instalace a spuštění kontejnerů služby rozpoznávání řeči (náhled)

Kontejnery umožňují spustit některá nastavení API služby Rozpoznávání řeči ve vašem vlastním prostředí. Kontejnery jsou skvělé pro specifické požadavky na zabezpečení a zásadsprávné řízení dat. V tomto článku se dozvíte, jak stáhnout, nainstalovat a spustit kontejner řeči.

Kontejnery řeči umožňují zákazníkům vytvářet architekturu aplikace pro rozpoznávání řeči, která je optimalizovaná pro robustní cloudové funkce i hraniční lokalitu. K dispozici jsou čtyři různé kontejnery. Dva standardní kontejnery jsou **převod řeči na text** a převod textu na **řeč**. Dva vlastní kontejnery jsou **vlastní řeč na text** a vlastní převod textu na **řeč**.

> [!IMPORTANT]
> Všechny kontejnery řeči jsou v současné době nabízeny jako součást [veřejného náhledu "Gated"](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio). Oznámení bude provedeno, když kontejnery řeči postupují do obecné dostupnosti (GA).

| Funkce | Funkce | Latest (Nejnovější) |
|--|--|--|
| Převod řeči na text | Přepisuje souvislé nahrávky řeči nebo dávky v reálném čase do textu s průběžnými výsledky. | 2.1.1 |
| Vlastní převod řeči na text | Pomocí vlastního modelu z [portálu Vlastní řeč](https://speech.microsoft.com/customspeech)přepne souvislé záznamy řeči nebo dávky v reálném čase do textu s průběžnými výsledky. | 2.1.1 |
| Převod textu na řeč | Převede text na přirozeně znějící řeč se vstupem ve formátu prostého textu nebo jazykem značek pro syntézu řeči (SSML). | 1.3.0 |
| Vlastní převod textu na řeč | Pomocí vlastního modelu z [portálu Vlastní hlas](https://aka.ms/custom-voice-portal)převede text na přirozeně znějící řeč se vstupem ve formátu prostého textu nebo jazykem s označením hlasové syntézy (SSML). | 1.3.0 |

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů řeči jsou následující požadavky:

| Požaduje se | Účel |
|--|--|
| Docker Engine | Potřebujete modul Docker Engine nainstalovaný v [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které nakonfigurují prostředí Dockeru v systému [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).<br><br> Docker musí být nakonfigurovaný tak, aby umožňoval kontejnerům připojení k fakturačním datům a odesílání fakturačních dat do Azure. <br><br> **V systému Windows**musí být Docker také nakonfigurován pro podporu kontejnerů Linuxu.<br><br> |
| Znalost Dockeru | Měli byste mít základní znalosti konceptů Dockeru, jako jsou registry, úložiště, kontejnery `docker` a image kontejnerů, stejně jako znalost základních příkazů. |
| Zdroj řeči | Chcete-li používat tyto kontejnery, musíte mít:<br><br>Prostředek _Azure Speech_ získat přidružený klíč rozhraní API a identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránce Přehled **řeči** a Klíče na webu Azure Portal. Oba jsou nutné ke spuštění kontejneru.<br><br>**{API_KEY}:** Jeden ze dvou dostupných klíčů prostředků na stránce **Klíče**<br><br>**{ENDPOINT_URI}:** Koncový bod uvedený na stránce **Přehled** |

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Vyplňte a odešlete [formulář žádosti o žádost o kontejnery řeči služeb Cognitive Services,](https://aka.ms/speechcontainerspreview/) abyste požádali o přístup ke kontejneru. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Podpora pro Advanced Vector Extension

**Hostitel** je počítač, který spouští kontejner dockeru. Hostitel *musí podporovat* [rozšíření Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Podporu AVX2 můžete zkontrolovat na hostitelích Linuxu pomocí následujícího příkazu:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Pro podporu AVX2 je *vyžadován* hostitelský počítač. Kontejner *nebude* fungovat správně bez podpory AVX2.

### <a name="container-requirements-and-recommendations"></a>Požadavky na kontejnery a doporučení

Následující tabulka popisuje minimální a doporučené přidělení prostředků pro každý kontejner řeči.

# <a name="speech-to-text"></a>[Převod řeči na text](#tab/stt)

| Kontejner | Minimální | Doporučené |
|-----------|---------|-------------|
| Převod řeči na text | 2 jádra, 2 GB paměti | 4 jádra, 4 GB paměti |

# <a name="custom-speech-to-text"></a>[Vlastní převod řeči na text](#tab/cstt)

| Kontejner | Minimální | Doporučené |
|-----------|---------|-------------|
| Vlastní převod řeči na text | 2 jádra, 2 GB paměti | 4 jádra, 4 GB paměti |

# <a name="text-to-speech"></a>[Převod textu na řeč](#tab/tts)

| Kontejner | Minimální | Doporučené |
|-----------|---------|-------------|
| Převod textu na řeč | 1 jádro, 2 GB paměti | 2 jádra, 3 GB paměti |

# <a name="custom-text-to-speech"></a>[Vlastní převod textu na řeč](#tab/ctts)

| Kontejner | Minimální | Doporučené |
|-----------|---------|-------------|
| Vlastní převod textu na řeč | 1 jádro, 2 GB paměti | 2 jádra, 3 GB paměti |

***

* Každé jádro musí být nejméně 2,6 gigahertzů (GHz) nebo rychlejší.

Jádro a paměť `--cpus` odpovídají `--memory` nastavení a, které se `docker run` používají jako součást příkazu.

> [!NOTE]
> Minimální a doporučené jsou založeny mimo omezení Dockeru, *nikoli* prostředky hostitelského počítače. Kontejnery převodu řeči na text například mapují části velkého jazykového modelu a *doporučuje se, aby* se celý soubor vejde do paměti, což je další 4–6 GB. Také první spuštění buď kontejneru může trvat déle, protože modely jsou stránkovány do paměti.

## <a name="get-the-container-image-with-docker-pull"></a>Získejte obrázek kontejneru pomocí`docker pull`

Image kontejnerů pro rozpoznávání řeči jsou k dispozici v následujícím registru kontejnerů.

# <a name="speech-to-text"></a>[Převod řeči na text](#tab/stt)

| Kontejner | Repository |
|-----------|------------|
| Převod řeči na text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Vlastní převod řeči na text](#tab/cstt)

| Kontejner | Repository |
|-----------|------------|
| Vlastní převod řeči na text | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Převod textu na řeč](#tab/tts)

| Kontejner | Repository |
|-----------|------------|
| Převod textu na řeč | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Vlastní převod textu na řeč](#tab/ctts)

| Kontejner | Repository |
|-----------|------------|
| Vlastní převod textu na řeč | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Docker vytáhnout pro kontejnery řeči

# <a name="speech-to-text"></a>[Převod řeči na text](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Docker vyžádat pro kontejner řeč na text

Pomocí příkazu [pro vyžádat docker](https://docs.docker.com/engine/reference/commandline/pull/) stáhněte bitovou kopii kontejneru z registru Náhled kontejneru.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> Značka `latest` vytáhne `en-US` národní prostředí. Další národní prostředí viz [Národní prostředí převodu řeči na text](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Národní prostředí převodu řeči na text

Všechny značky, `latest` s výjimkou mají následující formát a rozlišují malá a velká písmena:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

Následující značka je příkladem formátu:

```
2.1.1-amd64-en-us-preview
```

U všech podporovaných národních prostředí kontejneru **převodu řeči na text** naleznete [v tématu značky obrázků převodřeči na text](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-text"></a>[Vlastní převod řeči na text](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Docker vytáhnout pro vlastní kontejner řeči na text

Pomocí příkazu [pro vyžádat docker](https://docs.docker.com/engine/reference/commandline/pull/) stáhněte bitovou kopii kontejneru z registru Náhled kontejneru.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> A `locale` `voice` pro vlastní kontejnery řeči je určena vlastní model ingestované kontejneru.

# <a name="text-to-speech"></a>[Převod textu na řeč](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker vytáhnout pro kontejner převod textu na řeč

Pomocí příkazu [pro vyžádat docker](https://docs.docker.com/engine/reference/commandline/pull/) stáhněte bitovou kopii kontejneru z registru Náhled kontejneru.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> Značka `latest` vytáhne `en-US` národní prostředí `jessarus` a hlas. Další národní prostředí viz [Místní prostředí převodu textu na řeč](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Národní prostředí pro převod textu na řeč

Všechny značky, `latest` s výjimkou mají následující formát a rozlišují malá a velká písmena:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

Následující značka je příkladem formátu:

```
1.3.0-amd64-en-us-jessarus-preview
```

Pro všechna podporovaná národní prostředí a odpovídající hlasy kontejneru **pro převod textu na řeč** naleznete v [tématu značky obrázků převodu textu na řeč](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> Při vytváření *standardní text na řeč* HTTP POST, [zpráva řeči synthesis markup jazyka (SSML)](speech-synthesis-markup.md) zpráva vyžaduje `voice` prvek s atributem. `name` Hodnota je odpovídající národní prostředí kontejneru a hlas, také známý jako ["krátký název"](language-support.md#standard-voices). `latest` Značka by například měla název `en-US-JessaRUS`hlasu .

# <a name="custom-text-to-speech"></a>[Vlastní převod textu na řeč](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Docker vytáhnout pro vlastní kontejner převodtextu na řeč

Pomocí příkazu [pro vyžádat docker](https://docs.docker.com/engine/reference/commandline/pull/) stáhněte bitovou kopii kontejneru z registru Náhled kontejneru.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> A `locale` `voice` pro vlastní kontejnery řeči je určena vlastní model ingestované kontejneru.

***

## <a name="how-to-use-the-container"></a>Jak kontejner používat

Jakmile je kontejner v [hostitelském počítači](#the-host-computer), použijte následující proces pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run)s požadovaným nastavením fakturace. Další [příklady](speech-container-configuration.md#example-docker-run-commands) `docker run` příkazu jsou k dispozici.
1. [Dotaz na koncový bod předpověď kontejneru](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Spusťte nádobu s`docker run`

Ke spuštění kontejneru použijte příkaz [spustit docker.](https://docs.docker.com/engine/reference/commandline/run/) Podrobnosti o tom, jak získat hodnoty `{Endpoint_URI}` `{API_Key}` a, naleznete v [části shromažďování požadovaných parametrů.](#gathering-required-parameters) Další [příklady](speech-container-configuration.md#example-docker-run-commands) `docker run` příkazu jsou také k dispozici.

# <a name="speech-to-text"></a>[Převod řeči na text](#tab/stt)

Chcete-li spustit kontejner *převodu řeči na text,* proveďte následující `docker run` příkaz.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí kontejner *převodřeči na text* z image kontejneru.
* Přiděluje 4 jádra procesoru a 4 gigabajty (GB) paměti.
* Zpřístupní port TCP 5000 a přidělí pseudo-TTY pro kontejner.
* Automaticky odebere kontejner po jeho ukončení. Bitová kopie kontejneru je stále k dispozici v hostitelském počítači.

# <a name="custom-speech-to-text"></a>[Vlastní převod řeči na text](#tab/cstt)

Vlastní kontejner *řeči na text* závisí na vlastním modelu řeči. Vlastní model musí být [trénován](how-to-custom-speech-train-model.md) pomocí [vlastního řečového portálu](https://speech.microsoft.com/customspeech).

> [!IMPORTANT]
> Vlastní řečový model musí být trénován z jedné z následujících verzí modelu:
> * **20181201 (v3.3 Sjednocený)**
> * **20190520 (v4.14 Sjednocený)**
> * **20190701 (v4.17 Sjednocený)**<br>
> ![Vlastní model kontejneru vlaku řeči](media/custom-speech/custom-speech-train-model-container-scoped.png)

Vlastní řeči **ID modelu** je nutné spustit kontejner. Najdete ji na stránce **Školení** vlastního řečového portálu. Na vlastním portálu řeči přejděte na stránku **Školení** a vyberte model.
<br>

![Stránka vlastního tréninku řeči](media/custom-speech/custom-speech-model-training.png)

Získejte **ID modelu,** které chcete `ModelId` použít `docker run` jako argument k parametru příkazu.
<br>

![Podrobnosti vlastního modelu řeči](media/custom-speech/custom-speech-model-details.png)

Následující tabulka představuje `docker run` různé parametry a jejich odpovídající popisy:

| Parametr | Popis |
|---------|---------|
| `{VOLUME_MOUNT}` | [Připojení svazku](https://docs.docker.com/storage/volumes/)hostitelského počítače , které docker používá k uchování vlastního modelu. Například *C:\CustomSpeech,* kde je *jednotka C* umístěna v hostitelském počítači. |
| `{MODEL_ID}` | **ID vlastního modelu** řeči na stránce **Školení** vlastního řečového portálu. |
| `{ENDPOINT_URI}` | Koncový bod je vyžadován pro měření a fakturaci. Další informace naleznete v [tématu shromažďování požadovaných parametrů](#gathering-required-parameters). |
| `{API_KEY}` | Je vyžadován klíč rozhraní API. Další informace naleznete v [tématu shromažďování požadovaných parametrů](#gathering-required-parameters). |

Chcete-li spustit vlastní kontejner *převodu řeči na text,* proveďte následující `docker run` příkaz:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí *vlastní* kontejner řeči na text z image kontejneru.
* Přiděluje 4 jádra procesoru a 4 gigabajty (GB) paměti.
* Načte vlastní model *převodu řeči na text* ze vstupního připojení svazku, například *C:\CustomSpeech*.
* Zpřístupní port TCP 5000 a přidělí pseudo-TTY pro kontejner.
* Stáhne model daný `ModelId` (pokud není nalezen na svazku připojit).
* Pokud byl vlastní model dříve `ModelId` stažen, je ignorován.
* Automaticky odebere kontejner po jeho ukončení. Bitová kopie kontejneru je stále k dispozici v hostitelském počítači.

# <a name="text-to-speech"></a>[Převod textu na řeč](#tab/tts)

Chcete-li spustit kontejner *převodu textu na řeč,* proveďte následující `docker run` příkaz.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí kontejner *převodu textu na řeč* z image kontejneru.
* Přiděluje 2 jádra procesoru a jeden gigabajt (GB) paměti.
* Zpřístupní port TCP 5000 a přidělí pseudo-TTY pro kontejner.
* Automaticky odebere kontejner po jeho ukončení. Bitová kopie kontejneru je stále k dispozici v hostitelském počítači.

# <a name="custom-text-to-speech"></a>[Vlastní převod textu na řeč](#tab/ctts)

*Kontejner vlastní převod textu na řeč* závisí na vlastním hlasovém modelu. Vlastní model musí být [trénován](how-to-custom-voice-create-voice.md) pomocí [vlastního hlasového portálu](https://aka.ms/custom-voice-portal). Vlastní hlasové **ID modelu** je nutné spustit kontejner. Najdete ji na stránce **Školení** vlastního hlasového portálu. Na vlastním hlasovém portálu přejděte na stránku **Školení** a vyberte model.
<br>

![Vlastní stránka hlasového školení](media/custom-voice/custom-voice-model-training.png)

Získejte **ID modelu,** které chcete `ModelId` použít jako argument pro parametr příkazu docker run.
<br>

![Podrobnosti vlastního hlasového modelu](media/custom-voice/custom-voice-model-details.png)

Následující tabulka představuje `docker run` různé parametry a jejich odpovídající popisy:

| Parametr | Popis |
|---------|---------|
| `{VOLUME_MOUNT}` | [Připojení svazku](https://docs.docker.com/storage/volumes/)hostitelského počítače , které docker používá k uchování vlastního modelu. Například *C:\CustomSpeech,* kde je *jednotka C* umístěna v hostitelském počítači. |
| `{MODEL_ID}` | **ID vlastního modelu** řeči na stránce **Školení** vlastního hlasového portálu. |
| `{ENDPOINT_URI}` | Koncový bod je vyžadován pro měření a fakturaci. Další informace naleznete v [tématu shromažďování požadovaných parametrů](#gathering-required-parameters). |
| `{API_KEY}` | Je vyžadován klíč rozhraní API. Další informace naleznete v [tématu shromažďování požadovaných parametrů](#gathering-required-parameters). |

Chcete-li spustit vlastní kontejner *převodu textu na řeč,* proveďte následující `docker run` příkaz:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí vlastní kontejner *převodu textu na řeč* z image kontejneru.
* Přiděluje 2 jádra procesoru a jeden gigabajt (GB) paměti.
* Načte *model Vlastní převod textu na řeč* z připojení pro vstup svazku, například *C:\CustomVoice*.
* Zpřístupní port TCP 5000 a přidělí pseudo-TTY pro kontejner.
* Stáhne model daný `ModelId` (pokud není nalezen na svazku připojit).
* Pokud byl vlastní model dříve `ModelId` stažen, je ignorován.
* Automaticky odebere kontejner po jeho ukončení. Bitová kopie kontejneru je stále k dispozici v hostitelském počítači.

***

> [!IMPORTANT]
> `Eula`, `Billing`a `ApiKey` možnosti musí být zadány ke spuštění kontejneru; v opačném případě se kontejner nespustí.  Další informace naleznete v [tématu Fakturace](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Dotaz na koncový bod předpovědi kontejneru

| Containers | Adresa URL hostitele sady SDK | Protocol (Protokol) |
|--|--|--|
| Převod řeči na text a vlastní převod řeči na text | `ws://localhost:5000` | WS |
| Převod textu na řeč a Vlastní převod textu na řeč | `http://localhost:5000` | HTTP |

Další informace o použití protokolů WSS a HTTPS naleznete v [tématu zabezpečení kontejneru](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>Převod textu na řeč nebo Vlastní převod textu na řeč

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Spuštění více kontejnerů na stejném hostiteli

Pokud máte v úmyslu spustit více kontejnerů s vystavené porty, ujistěte se, že spustit každý kontejner s jiným vystavený port. Například spusťte první kontejner na portu 5000 a druhý kontejner na portu 5001.

Tento kontejner a jiný kontejner Azure Cognitive Services můžete mít spuštěné na HOST společně. Můžete mít také více kontejnerů stejného kontejneru služby Cognitive Services spuštěna.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Při spuštění nebo spuštění kontejneru může dojít k problémům. Použijte výstupní [připojení](speech-container-configuration.md#mount-settings) a povolte protokolování. To umožní kontejneru generovat soubory protokolu, které jsou užitečné při řešení problémů.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturace

Kontejnery řeči odesílají fakturační informace do Azure pomocí prostředku *řeči* na vašem účtu Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech naleznete v [tématu Konfigurace kontejnerů](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste se naučili koncepty a pracovní postupy pro stahování, instalaci a spouštění kontejnerů řeči. Souhrn:

* Funkce Speech poskytuje čtyři linuxové kontejnery pro Docker a zapouzdřila různé funkce:
  * *Převod řeči na text*
  * *Vlastní převod řeči na text*
  * *Převod textu na řeč*
  * *Vlastní převod textu na řeč*
* Image kontejnerů se stahují z registru kontejnerů v Azure.
* Image kontejnerů běží v Dockeru.
* Bez ohledu na to, zda používáte rozhraní REST API (pouze převod textu na řeč) nebo sadu SDK (převod řeči na text nebo převod textu na řeč), zadáte identifikátor URI hostitele kontejneru. 
* Při vytváření instancí kontejneru musíte poskytnout fakturační údaje.

> [!IMPORTANT]
>  Kontejnery služeb Cognitive Services nejsou licencovány ke spuštění bez připojení k Azure pro měření. Zákazníci musí povolit kontejnery komunikovat fakturační údaje se službou měření za všech okolností. Kontejnery služeb Cognitive Services neodesílají společnosti Microsoft zákaznická data (např. obrázek nebo text, který se analyzuje).

## <a name="next-steps"></a>Další kroky

* Kontrola [konfigurace kontejnerů](speech-container-configuration.md) pro nastavení konfigurace
* Naučte se [používat kontejnery služby Speech service s Kubernetes a Helm](speech-container-howto-on-premises.md)
* Použití [dalších kontejnerů služeb Cognitive Services](../cognitive-services-container-support.md)
