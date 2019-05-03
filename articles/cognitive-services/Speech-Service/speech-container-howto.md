---
title: Nainstalujte kontejnery řeči
titleSuffix: Azure Cognitive Services
description: Nainstalujte a používejte řeč kontejnery. Převod řeči na text transcribes audiostreamy na text v reálném čase, který může využívat nebo zobrazení vaší aplikace, nástroje nebo zařízení. Převod textu na řeč převede vstupní text na řeč syntetizovaný podobnou té lidské.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: e000c034d10efc652f328fa8d1db8d1902fac693
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026114"
---
# <a name="install-and-run-speech-service-containers"></a>Instalace a spouštění kontejnerů Speech Service

Kontejnery řeči zákazníkům umožní vytvářet jeden architektura řeči aplikace, které je optimalizováno pro využití možnosti robustní Cloudová a hraniční umístění. Jsou dva řeči kontejnery nyní podporujeme **speech to text** a **převod textu na řeč**. 

Jsou dva řeči kontejnery **speech to text** a **převod textu na řeč**. 

|Funkce|Funkce|Nejnovější|
|-|-|--|
|Převod řeči na text| <li>Přepisuje plynulou řeč v reálném čase do textové podoby.<li>Dokáže dávkově přepisovat řeč ze zvukových nahrávek. <li>Podporuje okamžité výsledky, detekci konce řeči, automatické formátování textu a maskování vulgarismů. <li>Může volat službu [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS), která z přepsané řeči odvodí záměr uživatele.\*|1.1.1|
|Převod textu na řeč| <li>Převádí text do přirozeně znějící řeči. <li>Nabízí několik pohlaví a dialektů pro celou řadu podporovaných jazyků. <li>Podporuje vstup v podobě prostého textu nebo jazyk SSML (Speech Synthesis Markup Language). |1.1.0|

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů řeči, musí splňovat následující požadavky:

|Požaduje se|Účel|
|--|--|
|Modul docker| Je nutné modul Docker nainstalovaný na [hostitelský počítač](#the-host-computer). Docker nabízí balíčky, které nakonfigurují prostředí Dockeru na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).<br><br> Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure. <br><br> **Na Windows**, Docker musí být taky nakonfigurovaný pro podporu kontejnerů Linuxu.<br><br>|
|Znalost Dockeru | Byste měli mít základní znalost konceptů Dockeru, jako je registrů, úložiště, kontejnery a Image kontejneru, jakož i znalost basic `docker` příkazy.| 
|Prostředek řeči |Chcete-li použít tyto kontejnery, musíte mít:<br><br>A _řeči_ prostředků Azure můžete získat přidružený klíč účtování a fakturace identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na webu Azure portal **řeči** stránky přehled a klíče a jsou potřebná ke spuštění kontejneru.<br><br>**{BILLING_KEY}** : klíč prostředku<br><br>**{BILLING_ENDPOINT_URI}** : Příklad identifikátor URI koncového bodu je: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Nejprve musíte vyplňte a odešlete [formulář žádosti o kontejnerech řeči Cognitive Services](https://aka.ms/speechcontainerspreview/) chcete požádat o přístup ke kontejneru. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Hostitelském počítači

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Rozšířená podpora vektoru rozšíření

**Hostitele** je počítač, který spustí kontejner dockeru. Hostitel musí podporovat [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Můžete zkontrolovat tuto podporu na hostitelé s Linuxem pomocí následujícího příkazu: 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

Následující tabulka popisuje minimální a doporučené jader procesoru a paměti k přidělení pro každý kontejner řeči.

| Kontejner | Minimální | Doporučené |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | 2 jádra<br>2 GB paměti  | 4 jádra<br>4 GB paměti  |
|cognitive-services-text-to-speech | 1 jádro, 0,5 GB paměti| 2 jádra, 1 GB paměti |

* Každé jádro, musí být aspoň 2.6 gigahertz (GHz) nebo rychlejší.


Jader a paměti odpovídají `--cpus` a `--memory` nastavení, které se používají jako součást `docker run` příkazu.

**Poznámka:**; Minimální a doporučené vychází z Dockeru omezení *není* hostitelského počítače prostředky. Například kontejnery speech to text paměti namapovat části velké jazykový model a je _doporučuje_ , který vyhovuje celý soubor v paměti, což je o 4 až 6 GB. První spuštění kontejneru buď navíc může trvat déle, protože modely jsou stránkování do paměti.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru s `docker pull`

Image kontejneru pro zpracování řeči, jsou k dispozici. 

| Kontejner | Úložiště |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitive-services-text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>Jazyk národního prostředí je ve značce kontejneru

`latest` Označte si `en-us` národní prostředí a `jessarus` hlasu. 

#### <a name="speech-to-text-locales"></a>Převod řeči na text národních prostředí

Všechny značky, s výjimkou `latest` jsou v následujícím formátu, kde `<culture>` Určuje národní prostředí kontejneru:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

Následující značku je příklad formátu:

```
1.0.0-amd64-en-us-preview
```

V následující tabulce jsou uvedeny podporované národní prostředí pro **speech to text** 1.1.1 verzi kontejneru:

|Jazyk národního prostředí|Značky|
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


#### <a name="text-to-speech-locales"></a>Národní prostředí pro převod textu na řeč

Všechny značky, s výjimkou `latest` jsou v následujícím formátu, kde `<culture>` Určuje národní prostředí a `<voice>` označuje hlasu kontejneru:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

Následující značku je příklad formátu:

```
1.0.0-amd64-en-us-jessarus-preview
```

V následující tabulce jsou uvedeny podporované národní prostředí pro **převod textu na řeč** v 1.1.0 verzi kontejneru:

|Jazyk národního prostředí|Značky|Podporované hlasů|
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
|Portugalština|`pt-br`|daniel-apollo<br>heloisarus|
|Španělština|`es-es`|elenarus<br>laura-apollo<br>pablo-apollo<br>|
|Španělština|`es-mx`|hildarus<br>raul-apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Operace docker pull pro kontejnery řeči

#### <a name="speech-to-text"></a>Převod řeči na text

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>Převod textu na řeč

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>Jak používat kontejneru

Jakmile bude kontejner ve [hostitelský počítač](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spuštění kontejneru](#run-the-container-with-docker-run), s nastavením fakturační povinné, ale nepoužívá se. Další [příklady](speech-container-configuration.md#example-docker-run-commands) z `docker run` příkazu jsou k dispozici. 
1. [Dotazování koncový bod kontejneru předpovědi](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s `docker run`

Použití [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkaz ke spuštění tři kontejnery. Příkaz používá následující parametry:

**Během fáze private preview**, fakturace nastavení musí být platné pro spuštění kontejneru, ale za využití se nic neúčtuje.

| Zástupný symbol | Hodnota |
|-------------|-------|
|{BILLING_KEY} | Tento klíč se používá ke spuštění kontejneru a je k dispozici na stránce klíče řeči webu Azure portal.  |
|{BILLING_ENDPOINT_URI} | Fakturační koncový bod hodnotu identifikátoru URI je k dispozici na stránce s přehledem řeči webu Azure portal.|

Tyto parametry nahraďte vlastními hodnotami v následujícím příkladu `docker run` příkazu.

### <a name="text-to-speech"></a>Převod textu na řeč

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} 
```

### <a name="speech-to-text"></a>Převod řeči na text

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} 
```

Tento příkaz:

* Spouští řeči kontejner z image kontejneru
* Přidělí 2 jádra procesoru a paměti 2 gigabajty (GB)
* Zpřístupňuje TCP port 5000 a přiděluje pseudo-TTY pro kontejner
* Po ukončení automaticky odstraní kontejner. Image kontejneru je stále k dispozici na hostitelském počítači. 

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncový bod kontejneru predikcí

|Kontejner|Koncový bod|
|--|--|
|Převod řeči na text|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|Převod textu na řeč|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>Převod řeči na text

Poskytuje kontejner dotazů založených na protokolu websocket koncový bod rozhraní API, které jsou přístupné prostřednictvím [dokumentace ke službě SDK services řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

Ve výchozím nastavení používá sadou SDK pro řeč online hlasové služby. Použití kontejneru, budete muset změnit metodu inicializace. Viz následující příklady.

#### <a name="for-c"></a>ProC#

Přejdete od použití toto volání inicializace Azure cloud:

```C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

na toto volání pomocí koncový bod kontejneru:

```C#
var config = SpeechConfig.FromEndpoint("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1", "YourSubscriptionKey");
```

#### <a name="for-python"></a>Pro jazyk Python

Přejdete od použití toto volání inicializace cloudu Azure

```python
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

na toto volání pomocí koncový bod kontejneru:

```python
speech_config = speechsdk.SpeechConfig(subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>Převod textu na řeč

Kontejner poskytuje koncový bod REST API, který se nachází [tady](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) najdete ukázky [tady](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Zastavit kontejner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Při spuštění kontejneru se kontejner používá **stdout** a **stderr** do výstupu informace, které jsou užitečné při řešení potíží, ke kterým dochází při spuštění nebo spuštění kontejneru. 

## <a name="billing"></a>Fakturace

Odeslat kontejnery řeči fakturační údaje do Azure, pomocí _řeči_ prostředků v účtu Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](speech-container-configuration.md).

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro stažení, instalaci a používání kontejnerů řeči. Souhrn:

* Řeč poskytuje dva kontejnery Linuxu pro Docker, zapouzdření převod řeči na text a převod textu na řeč.
* Image kontejneru se stahují ze soukromého registru kontejnerů v Azure.
* Spuštění imagí kontejnerů v Dockeru.
* Rozhraní REST API nebo sady SDK můžete použít k volání operací v kontejnerech řeči tak, že zadáte identifikátor URI kontejneru hostitele.
* Při vytváření instance kontejneru, je nutné zadat fakturační informace.

> [!IMPORTANT]
>  Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Cognitive Services kontejnery Neodesílat data zákazníků (třeba image nebo text, který je analyzován) společnosti Microsoft.

## <a name="next-steps"></a>Další postup

* Kontrola [konfigurace kontejnery](speech-container-configuration.md) nastavení konfigurace
* Použití více [kontejnery Cognitive Services](../cognitive-services-container-support.md)
