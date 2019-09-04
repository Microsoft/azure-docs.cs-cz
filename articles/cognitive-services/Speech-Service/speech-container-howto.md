---
title: Instalace funkcí Speech Containers – Speech Service
titleSuffix: Azure Cognitive Services
description: Instalace a spuštění kontejnerů řeči. Převod řeči na text transcribes zvukové streamy na text v reálném čase, které mohou aplikace, nástroje nebo zařízení spotřebovat nebo zobrazit. Převod textu na řeč převede vstupní text na syntetizované řeč podobné člověku.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 7708133fcba0d594ecd420afd8da1b2881055aa7
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241018"
---
# <a name="install-and-run-speech-service-containers"></a>Instalace a spuštění kontejnerů služby Speech

Díky kontejnerům pro rozpoznávání řeči můžou zákazníci vytvářet jednu architekturu aplikace pro rozpoznávání řeči, která je optimalizovaná tak, aby využila výhod robustních cloudových funkcí i možností Edge. 

Dva kontejnery řeči jsou **Převod řeči na text** a **Převod textu na řeč**. 

|Funkce|Funkce|Latest (Nejnovější)|
|-|-|--|
|Převod řeči na text| <li>Transcribes v reálném čase nepřetržité audio nebo zvukové nahrávky do textu s mezilehlé výsledky.|1.2.0|
|Převod textu na řeč| <li>Převádí text do přirozeně znějící řeči. s prostým textem Input nebo SSML (Speech syntézy Language). |1.2.0|

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů řeči musíte splnit následující předpoklady:

|Požadováno|Účel|
|--|--|
|Modul Docker| Potřebujete modul Docker nainstalovaný na [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které konfigurují prostředí Docker v systémech [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).<br><br> Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure. <br><br> **V systému Windows**musí být Docker taky nakonfigurovaný tak, aby podporoval kontejnery Linux.<br><br>|
|Znalost pomocí Docker | Měli byste mít základní znalosti konceptů Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů, a taky znalosti základních `docker` příkazů.| 
|Prostředek řeči |Aby bylo možné tyto kontejnery použít, je nutné mít následující:<br><br>Prostředek Azure _Speech_ pro získání přidruženého klíče rozhraní API a identifikátoru URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách s přehledem a klíči pro Azure Portal **řeči** . Oba jsou nutné ke spuštění kontejneru.<br><br>**{API_KEY}** : Jeden ze dvou dostupných klíčů prostředků na stránce **klíče**<br><br>**{ENDPOINT_URI}** : Koncový bod, jak je uvedený na stránce **Přehled**|

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Aby bylo možné požádat o přístup ke kontejneru, je nutné nejprve dokončit a odeslat [formulář žádosti o Cognitive Services kontejnerech řeči](https://aka.ms/speechcontainerspreview/) . 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Rozšířená podpora rozšíření Vector

**Hostitel** je počítač, který spouští kontejner Docker. Hostitel musí podporovat [Rozšířená rozšíření Vector](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Tuto podporu můžete na hostitelích se systémem Linux ověřit pomocí následujícího příkazu: 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

V následující tabulce jsou popsány minimální a doporučené PROCESORy a paměťová jádra, která se mají přidělit každému kontejneru řeči.

| Kontejner | Minimální | Doporučené |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | 2 jádra<br>2 GB paměti  | 4 jádra<br>4 GB paměti  |
|cognitive-services-text-to-speech | 1 jádro, 0,5 GB paměti| 2 jádra, 1 GB paměti |

* Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.

Základní a paměť odpovídají `--cpus` nastavení a `--memory` , která se `docker run` používají jako součást příkazu.

**Poznámka:** Minimum a doporučené jsou založeny na omezeních Docker, *nikoli* na prostředky hostitelského počítače. Například kontejnery Speech-to-text namapují část velkých jazykových modelů a _doporučuje_ se, aby se celý soubor vešel do paměti, což je dalších 4-6 GB. První spuštění obou kontejnerů může trvat delší dobu, protože modely jsou stránkované v paměti.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru pomocí`docker pull`

K dispozici jsou image kontejneru pro řeč.

| Kontejner | Úložiště |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitive-services-text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>Jazykové prostředí je ve značce kontejneru.

Značka vyžádá `en-us` národní prostředí a `jessarus` hlas. `latest`

#### <a name="speech-to-text-locales"></a>Převod řeči na textová národní prostředí

Všechny značky s výjimkou `latest` jsou v následujícím formátu, `<culture>` kde označuje kontejner národního prostředí:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

Následující značka je příkladem formátu:

```
1.2.0-amd64-en-us-preview
```

V následující tabulce jsou uvedeny podporované národní prostředí pro **Převod řeči na text** ve verzi 1.2.0 kontejneru:

|Jazykové prostředí|Tags|
|--|--|
|Čínština|`zh-cn`|
|Angličtina |`en-us`<br>`en-gb`<br>`en-au`<br>`en-in`|
|Francouzština |`fr-ca`<br>`fr-fr`|
|Němčina|`de-de`|
|italština|`it-it`|
|Japonština|`ja-jp`|
|Korejština|`ko-kr`|
|Portugalština|`pt-br`|
|Španělština|`es-es`<br>`es-mx`|

#### <a name="text-to-speech-locales"></a>Textová prostředí pro převod textu na řeč

Všechny značky s výjimkou `latest` jsou v následujícím formátu, `<culture>` kde `<voice>` označuje národní prostředí a označuje hlas kontejneru:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

Následující značka je příkladem formátu:

```
1.2.0-amd64-en-us-jessarus-preview
```

V následující tabulce jsou uvedeny podporované národní prostředí pro **Převod textu na řeč** v 1.2.0 verzi kontejneru:

|Jazykové prostředí|Tags|Podporované hlasy|
|--|--|--|
|Čínština|`zh-cn`|huihuirus<br>kangkang-apollo<br>yaoyao-apollo|
|Angličtina |`en-au`|catherine<br>hayleyrus|
|Angličtina |`en-gb`|george-apollo<br>hazelrus<br>susan-apollo|
|Angličtina |`en-in`|heera-apollo<br>priyarus<br>ravi-apollo<br>|
|Angličtina |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|Francouzština|`fr-ca`|caroline<br>harmonierus|
|Francouzština|`fr-fr`|hortenserus<br>julie-apollo<br>paul-apollo|
|Němčina|`de-de`|hedda<br>heddarus<br>stefan-apollo|
|italština|`it-it`|cosimo-apollo<br>luciarus|
|Japonština|`ja-jp`|ayumi-apollo<br>harukarus<br>ichiro-apollo|
|Korejština|`ko-kr`|heamirus|
|Portugalština|`pt-br`|Daniel – Apollo<br>heloisarus|
|Španělština|`es-es`|elenarus<br>laura-apollo<br>pablo-apollo<br>|
|Španělština|`es-mx`|hildarus<br>raul-apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Vyžádání obsahu Docker pro kontejnery řeči

#### <a name="speech-to-text"></a>Převod řeči na text

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>Převod textu na řeč

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>Jak používat kontejner

Jakmile je kontejner na [hostitelském počítači](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run)s požadovaným nastavením fakturace. K [](speech-container-configuration.md#example-docker-run-commands) dispozici jsou `docker run` další příklady příkazu.
1. [Dotazování koncového bodu předpovědi kontejneru](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s`docker run`

Pomocí příkazu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) spusťte kterýkoli ze tří kontejnerů. Příkaz používá následující parametry:

**Během období Preview**musí být nastavení fakturace platná pro spuštění kontejneru, ale neúčtují se za využití.

| Zástupný symbol | Value |
|-------------|-------|
|{API_KEY} | Tento klíč se používá ke spuštění kontejneru a je k dispozici na stránce klíče pro rozpoznávání řeči v Azure Portal.  |
|{ENDPOINT_URI} | Hodnota identifikátoru URI fakturačního koncového bodu je k dispozici na stránce Přehled řeči Azure Portal.|

Tyto parametry nahraďte vlastními hodnotami v následujícím ukázkovém `docker run` příkazu.

### <a name="text-to-speech"></a>Převod textu na řeč

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="speech-to-text"></a>Převod řeči na text

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí kontejner řeči z image kontejneru.
* Přiděluje 2 jádra procesoru a 2 gigabajty (GB) paměti.
* Zpřístupňuje TCP port 5000 a přiděluje pseudo-TTY pro kontejner
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncového bodu předpovědi kontejneru

|Kontejner|Koncový bod|
|--|--|
|Převod řeči na text|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|Převod textu na řeč|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>Převod řeči na text

Kontejner poskytuje rozhraní API koncových bodů dotazů založených na protokolu WebSocket, která jsou k dispozici prostřednictvím [sady Speech SDK](index.yml).

Sada Speech SDK standardně používá online hlasové služby. Chcete-li použít kontejner, je nutné změnit inicializační metodu. Podívejte se na následující příklady.

#### <a name="for-c"></a>ForC#

Změnit z použití tohoto volání inicializace Azure-Cloud:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

do tohoto volání pomocí koncového bodu kontejneru:

```csharp
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"),
    "YourSubscriptionKey");
```

#### <a name="for-python"></a>Pro Python

Změnit z použití tohoto volání inicializace Azure-Cloud

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

do tohoto volání pomocí koncového bodu kontejneru:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>Převod textu na řeč

Kontejner poskytuje rozhraní API koncového bodu REST, které najdete [tady](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech) a příklady najdete [tady](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/).

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Když kontejner spustíte, kontejner použije **stdout** a **stderr** k výstupu informací, které jsou užitečné při řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru.

## <a name="billing"></a>Fakturace

Kontejnery řeči odesílají informace o fakturaci do Azure pomocí prostředku _řeči_ ve vašem účtu Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](speech-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postupy pro stažení, instalaci a spuštění kontejnerů řeči. Souhrn:

* Speech poskytuje pro Docker dva kontejnery pro Linux, které zapouzdří řeč na text a převod textu na řeč.
* Image kontejneru se stáhnou z privátního registru kontejnerů v Azure.
* Spuštění imagí kontejnerů v Dockeru.
* Pomocí REST API nebo sady SDK můžete volat operace v kontejnerech řeči zadáním identifikátoru URI hostitele kontejneru.
* Při vytváření instance kontejneru budete muset zadat fakturační údaje.

> [!IMPORTANT]
>  Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Cognitive Services kontejnery Neodesílat data zákazníků (třeba image nebo text, který je analyzován) společnosti Microsoft.

## <a name="next-steps"></a>Další postup

* Kontrola [konfigurace kontejnery](speech-container-configuration.md) nastavení konfigurace
* Použít více [Cognitive Servicesch kontejnerů](../cognitive-services-container-support.md)
