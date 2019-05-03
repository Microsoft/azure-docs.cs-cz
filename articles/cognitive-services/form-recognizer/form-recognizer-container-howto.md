---
title: Nainstalujte a spusťte kontejner – nástroj pro rozpoznávání formuláře
titleSuffix: Azure Cognitive Services
description: Další informace o použití kontejneru formuláře Rozlišovač analyzovat data formuláře a tabulek.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: pafarley
ms.openlocfilehash: 5d4374b329049e2e55966a28567c5232be77abda
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027065"
---
# <a name="install-and-run-form-recognizer-containers"></a>Instalace a spouštění kontejnerů Rozlišovač formuláře
Nástroj pro rozpoznávání formuláře platí technologií pro strojové učení k identifikaci a extrahovat páry klíč hodnota a tabulky z formuláře. Přiřadí hodnoty a položky tabulky k nim a potom vypíše strukturovanými daty, která obsahuje vztahy v původním souboru. Můžete zavolat vlastní formulář Rozlišovač modelu pomocí jednoduchých rozhraní REST API k zjednodušení a snadno ji integrovat v procesu pracovního postupu služby automation nebo jiné aplikace. Zobrazuje se jenom pět dokumentů (nebo prázdný formulář) jsou potřeba, abyste se mohli výsledky rychle, přesně a přizpůsobená pro váš konkrétní obsah, bez náročné ruční zásah nebo odbornými znalostmi o rozsáhlé datové vědy. Nevyžaduje se popisky dat nebo dat poznámky.

|Funkce|Funkce|
|-|-|
|Rozpoznávání formulářů| <li>Soubory procesy zadejte PDF, PNG a JPG.<li>Trénovat vlastních modelů s minimální 5 formy se stejné rozvržení. <li>Extrahuje páry klíč hodnota a informace o tabulce. <li>Kognitivní služby počítače Vision API RecognizeText používá ke zjišťování a extrakci tištěný text z obrázků, uvnitř formuláře.<li>Nevyžaduje poznámky nebo používání popisků.|

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů formuláře pro rozpoznávání, musí splňovat následující požadavky:

|Požaduje se|Účel|
|--|--|
|Modul docker| Je nutné modul Docker nainstalovaný na [hostitelský počítač](#the-host-computer). Docker nabízí balíčky, které nakonfigurují prostředí Dockeru na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).<br><br> Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure. <br><br> **Na Windows**, Docker musí být taky nakonfigurovaný pro podporu kontejnerů Linuxu.<br><br>|
|Znalost Dockeru | Byste měli mít základní znalost konceptů Dockeru, jako je registrů, úložiště, kontejnery a Image kontejneru, jakož i znalost basic `docker` příkazy.|
|Azure CLI| Je potřeba nainstalovat [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) na hostiteli.|
|Prostředky počítače rozhraní API pro zpracování obrazu| Aby bylo možné zpracovat naskenované dokumenty a obrázky, **prostředků pro počítačové zpracování obrazu** je povinný. Můžete přistupovat **rozpoznat Text** funkcí jako buď prostředek Azure (rozhraní REST API nebo sady SDK) nebo `cognitive-services-recognize-text` kontejneru. Použít obvyklé fakturační poplatky. <br><br>Musíte předat jí klíč a fakturační koncový bod pro konkrétní prostředek pro počítačové zpracování obrazu (v cloudu Azure nebo kontejner služeb Cognitive Services). Použít tento klíč a fakturační koncový bod jako {COMPUTER_VISION_API_KEY} a {COMPUTER_VISION_BILLING_ENDPOINT_URI}.<br><br> Pokud používáte  **`cognitive-services-recognize-text` kontejneru**, ujistěte se, že:<br><br>* Klíče pro počítačové zpracování obrazu na kontejneru formuláře Rozlišovače je klíč zadaný v počítačové zpracování obrazu `docker run` příkazu `cognitive-services-recognize-text` kontejneru.<br>* Váš fakturační koncový bod je koncový bod kontejneru, jako například `https://localhost:5000`. Pokud používáte kontejnery pro počítačové zpracování obrazu i formuláře Rozlišovač společně na stejném hostiteli, nemohou i být spuštěny s výchozí port `5000`.  |  
|Prostředek formuláře rozpoznávání rukopisu |Chcete-li použít tyto kontejnery, musíte mít:<br><br>A _formuláře Rozlišovač_ prostředků Azure můžete získat přidružený klíč účtování a fakturace identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na webu Azure portal **formuláře Rozlišovač** stránky přehled a klíče a jsou potřebná ke spuštění kontejneru.<br><br>**{BILLING_KEY}** : klíč prostředku<br><br>**{BILLING_ENDPOINT_URI}** : Příklad identifikátor URI koncového bodu je: `https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Nejprve musíte vyplňte a odešlete [Cognitive Services formuláře Rozlišovač kontejnery formulář žádosti o](https://aka.ms/FormRecognizerRequestAccess) chcete požádat o přístup ke kontejneru. To se také zaregistrujte je pro počítačové zpracování obrazu. Není nutné se zaregistrovat do služby pro počítačové zpracování obrazu formulář žádosti o samostatně. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Hostitelském počítači

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

Následující tabulka popisuje minimální a doporučené jader procesoru a paměti k přidělení pro každý kontejner Rozlišovač formuláře.

| Kontejner | Minimální | Doporučené |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 jádra, 4 GB paměti | 4 jádra, 8 GB paměti |

* Každé jádro, musí být aspoň 2.6 gigahertz (GHz) nebo rychlejší.
* TPS – transakcí za sekundu

Jader a paměti odpovídají `--cpus` a `--memory` nastavení, které se používají jako součást `docker run` příkazu.

> [!Note]
> Minimální a doporučené hodnoty vycházejí z Dockeru omezení a *není* hostitelského počítače prostředky.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru s `docker pull`

Image kontejneru pro nástroj pro rozpoznávání formuláře jsou k dispozici.

| Kontejner | Úložiště |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-form-recognizer-container"></a>Operace docker pull Rozlišovač formuláře kontejneru

#### <a name="form-recognizer"></a>Rozpoznávání formulářů

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>Jak používat kontejneru

Jakmile bude kontejner ve [hostitelský počítač](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spuštění kontejneru](#run-the-container-with-docker-run), s nastavením fakturační povinné, ale nepoužívá se. Další [příklady](form-recognizer-container-configuration.md#example-docker-run-commands) z `docker run` příkazu jsou k dispozici.
1. [Dotazování koncový bod kontejneru předpovědi](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s `docker run`

Použití [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkaz ke spuštění tři kontejnery. Příkaz používá následující parametry:

| Zástupný symbol | Hodnota |
|-------------|-------|
|{BILLING_KEY} | Tento klíč se používá ke spuštění kontejneru a je k dispozici na stránce klíče modulu pro rozpoznávání formuláře webu Azure portal.  |
|{BILLING_ENDPOINT_URI} | Fakturační koncový bod hodnotu identifikátoru URI je k dispozici na stránce s přehledem Rozlišovač formuláře webu Azure portal.|
|{COMPUTER_VISION_API_KEY}| Klíč je k dispozici na stránce klíče rozhraní API pro zpracování obrazu počítačů webu Azure portal.|
|{COMPUTER_VISION_ENDPOINT_URI}|Fakturační koncový bod. Pokud používáte cloudové prostředky pro počítačové zpracování obrazu, hodnotu identifikátoru URI je k dispozici na stránce Přehled rozhraní API pro zpracování obrazu počítače webu Azure portal. Pokud používáte `cognitive-services-recognize-text` kontejner, použijte fakturační adresu URL koncového bodu předaný do kontejneru v `docker run` příkazu.|

Tyto parametry nahraďte vlastními hodnotami v následujícím příkladu `docker run` příkazu.

### <a name="form-recognizer"></a>Rozpoznávání formulářů

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Tento příkaz:

* Spouští formuláře Rozlišovač kontejner z image kontejneru
* Přidělí 2 jádra procesoru a 8 gigabajtů (GB) paměti
* Zpřístupňuje TCP port 5000 a přiděluje pseudo-TTY pro kontejner
* Po ukončení automaticky odstraní kontejner. Image kontejneru je stále k dispozici na hostitelském počítači.
* Připojí /input a svazek/Output do kontejneru

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Spouští samostatné kontejnery jako samostatné spouštět příkazy dockeru

Pro nástroj pro rozpoznávání formuláře a rozpoznávání textu kombinaci místně hostované na stejném hostiteli postupujte podle dva příklady příkazů rozhraní příkazového řádku Dockeru.

Spuštění prvního kontejneru na portu 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Spusťte na portu 5001 druhý kontejner.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Každé následné kontejner by měl být na jiném portu. 

### <a name="run-separate-containers-with-docker-compose"></a>Spusťte samostatnou kontejnery pomocí Docker Compose

Pro nástroj pro rozpoznávání formuláře a rozpoznávání textu kombinaci místně hostované na stejném hostiteli následuje příklad souboru Docker Compose YAML. Rozpoznávání textu `{COMPUTER_VISION_API_KEY}` musí být stejná pro obě `formrecognizer` a `ocr` kontejnery. `{COMPUTER_VISION_ENDPOINT_URI}` Používá jen v `ocr` kontejneru protože `formrecognizer` kontejner používá `ocr` název a port. 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: {COMPUTER_VISION_API_KEY}  

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{BILLING_ENDPOINT_URI}"
      apikey: {BILLING_KEY}
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"  
```


> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` stejně jako `FormRecognizer:ComputerVisionApiKey` a `FormRecognizer:ComputerVisionEndpointUri` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncový bod kontejneru predikcí

|Kontejner|Koncový bod|
|--|--|
|form-recognizer|http://localhost:5000


### <a name="form-recognizer"></a>Rozpoznávání formulářů

Poskytuje kontejner dotazů založených na protokolu websocket koncový bod rozhraní API, které jsou přístupné prostřednictvím [Rozlišovač formuláře služby dokumentace k sadě SDK](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Ve výchozím nastavení používá sady SDK modulu pro rozpoznávání formuláře služeb online services. Použití kontejneru, budete muset změnit metodu inicializace. Viz následující příklady.

#### <a name="for-c"></a>ProC#

Přejdete od použití toto volání inicializace Azure cloud:

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

na toto volání pomocí koncový bod kontejneru:

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>Pro jazyk Python

Přejdete od použití toto volání inicializace cloudu Azure

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

na toto volání pomocí koncový bod kontejneru:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Rozpoznávání formulářů

Kontejner poskytuje koncový bod REST API, který se nachází [tady](https://docs.microsoft.com/azure/cognitive-services/formrecognizer-service/rest-apis#formrecognier-api) najdete ukázky [tady](https://azure.microsoft.com/resources/samples/cognitive-formrecognizer).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Zastavit kontejner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Při spuštění kontejneru se kontejner používá **stdout** a **stderr** do výstupu informace, které jsou užitečné při řešení potíží, ke kterým dochází při spuštění nebo spuštění kontejneru.

## <a name="billing"></a>Fakturace

Kontejnery odeslání formuláře Rozlišovač fakturační údaje do Azure, pomocí _formuláře Rozlišovač_ prostředků v účtu Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](form-recognizer-container-configuration.md).

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro stažení, instalaci a používání kontejnery Rozlišovač formuláře. Souhrn:

* Rozlišovač formuláře obsahuje jeden kontejner Linuxu pro Docker.
* Image kontejneru se stahují ze soukromého registru kontejnerů v Azure.
* Spuštění imagí kontejnerů v Dockeru.
* Rozhraní REST API nebo sady SDK můžete použít k volání operací v modulu pro rozpoznávání formuláře kontejneru tak, že zadáte identifikátor URI kontejneru hostitele.
* Při vytváření instance kontejneru, je nutné zadat fakturační informace.

> [!IMPORTANT]
>  Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Cognitive Services kontejnery Neodesílat data zákazníků (třeba image nebo text, který je analyzován) společnosti Microsoft.

## <a name="next-steps"></a>Další postup

* Kontrola [konfigurace kontejnery](form-recognizer-container-configuration.md) nastavení konfigurace
* Použití více [kontejnery Cognitive Services](../cognitive-services-container-support.md)
