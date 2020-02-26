---
title: Jak nainstalovat a spustit kontejner pro rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak pomocí kontejneru pro rozpoznávání formulářů Azure Cognitive Services analyzovat data formuláře a tabulky.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: dapine
ms.openlocfilehash: 64bd6bb0a1a064f38eae472cb889acb6df7ae4b1
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605144"
---
# <a name="install-and-run-form-recognizer-containers-preview"></a>Instalace a spuštění kontejnerů pro rozpoznávání formulářů (Preview)

Nástroj pro rozpoznávání formulářů Azure používá technologii strojového učení k identifikaci a extrakci párů klíč-hodnota a tabulek z formulářů. Přidružuje hodnoty a položky tabulky s páry klíč-hodnota a pak vypíše strukturovaná data, která zahrnují relace v původním souboru. 

Chcete-li omezit složitost a snadno integrovat model rozpoznávání vlastního formuláře do procesu automatizace pracovního postupu nebo jiné aplikace, můžete model volat pomocí jednoduchého REST API. Je potřeba jenom pět dokumentů formuláře (nebo jedné prázdné formy a dvou vyplněných formulářů), takže můžete získat výsledky rychle, přesně a přizpůsobit konkrétnímu obsahu. K dispozici není nutné provádět těžké Ruční zásahy nebo rozsáhlé odborné znalosti datových věd. A nevyžaduje popisky dat ani datová anotace.

> [!IMPORTANT]
> Kontejnery pro rozpoznávání formulářů aktuálně používají rozhraní API pro rozpoznávání formulářů verze 1,0. K nejnovější verzi rozhraní API můžete přistupovat pomocí spravované služby místo toho.

|Funkce|Funkce|
|-|-|
|Rozpoznávání formulářů| <li>Zpracovává soubory PDF, PNG a JPG.<li>Vlastní modely vlaků s minimálně pěti formuláři stejného rozložení <li>Extrahuje páry klíč-hodnota a informace o tabulce. <li>K detekci a extrakci vytištěného textu z obrázků uvnitř formulářů používá funkci Azure Cognitive Services rozhraní API pro počítačové zpracování obrazu Rozpoznávání textu.<li>Nevyžaduje poznámku nebo popisky|

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Než začnete používat kontejnery pro rozpoznávání formulářů, musíte splnit následující předpoklady:

|Požadováno|Účel|
|--|--|
|Modul Docker| Potřebujete modul Docker nainstalovaný na [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které konfigurují prostředí Docker v systémech [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Informace o úvodu k Docker a kontejneru najdete v tématu [Přehled Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure. <br><br> V systému Windows musí být Docker taky nakonfigurovaný tak, aby podporoval kontejnery Linux.<br><br>|
|Znalost pomocí Docker | Měli byste mít základní znalosti konceptů Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů, a znalosti základních `docker`ch příkazů.|
|Rozhraní příkazového řádku Azure| Na hostitele nainstalujte rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) .|
|Prostředek rozhraní API pro počítačové zpracování obrazu| Chcete-li zpracovat naskenované dokumenty a obrázky, potřebujete Počítačové zpracování obrazu prostředek. K funkci Rozpoznávání textu můžete přistupovat buď jako prostředek Azure (REST API nebo SDK), nebo jako *textový* [kontejner](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)pro rozpoznávání služeb. Použijí se obvyklé fakturační poplatky. <br><br>Předejte klíč rozhraní API a koncové body pro váš prostředek Počítačové zpracování obrazu (cloud Azure nebo kontejner Cognitive Services). Použijte tento klíč rozhraní API a koncový bod jako **{COMPUTER_VISION_API_KEY}** a **{COMPUTER_VISION_ENDPOINT_URI}** .<br><br> Použijete-li kontejner *vnímání-Services-rozpoznávání-text* , ujistěte se, že:<br><br>Váš Počítačové zpracování obrazu klíč pro kontejner pro rozpoznávání formulářů je klíč zadaný v příkazu Počítačové zpracování obrazu `docker run` pro kontejner rozpoznávání *-Services-rozpoznávání-text* .<br>Koncový bod fakturace je koncový bod kontejneru (například `http://localhost:5000`). Použijete-li kontejner Počítačové zpracování obrazu kontejner a rozpoznávání formulářů společně na stejném hostiteli, nelze současně spustit výchozí port *5000*. |
|Prostředek pro rozpoznávání formulářů |Chcete-li použít tyto kontejnery, musíte mít:<br><br>Prostředek pro **rozpoznávání formulářů** Azure, který získá přidružený klíč rozhraní API a identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách přehled a klíče pro **rozpoznávání formulářů** Azure Portal a pro spuštění kontejneru jsou nutné obě hodnoty.<br><br>**{FORM_RECOGNIZER_API_KEY}** : jeden ze dvou dostupných klíčů prostředků na stránce klíče<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}** : koncový bod uvedený na stránce Přehled|

## <a name="gathering-required-parameters"></a>Shromažďují se požadované parametry.

Existují tři primární parametry pro všechny požadované kontejnery Cognitive Services. Licenční smlouvu s koncovým uživatelem (EULA) se musí nacházet s hodnotou `accept`. Kromě toho je potřeba adresa URL koncového bodu i klíč rozhraní API.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>Identifikátor URI koncového bodu `{COMPUTER_VISION_ENDPOINT_URI}` a `{FORM_RECOGNIZER_ENDPOINT_URI}`

Hodnota identifikátoru URI **koncového bodu** je k dispozici na stránce *Přehled* Azure Portal odpovídajícího prostředku služby pro rozpoznávání. Přejděte na stránku *Přehled* , najeďte myší na koncový bod a zobrazí se <span class="docon docon-edit-copy x-hidden-focus"></span> ikona `Copy to clipboard`. Zkopírujte a použijte tam, kde je to potřeba.

![Shromáždit identifikátor URI koncového bodu pro pozdější použití](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Klíče `{COMPUTER_VISION_API_KEY}` a `{FORM_RECOGNIZER_API_KEY}`

Tento klíč se používá ke spuštění kontejneru a je k dispozici na stránce klíčů Azure Portal odpovídajícího prostředku služby pro rozpoznávání. Přejděte na stránku *klíče* a klikněte na ikonu `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> .

![Získat jeden ze dvou klíčů pro pozdější použití](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> Tyto klíče předplatného se používají pro přístup ke službě rozpoznávání rozhraní API. Nesdílejte své klíče. Bezpečně je uložte, například pomocí Azure Key Vault. Tyto klíče doporučujeme také pravidelně obnovovat. K volání rozhraní API je potřeba jenom jeden klíč. Při opětovném generování prvního klíče můžete použít druhý klíč pro pokračování přístupu ke službě.

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Aby bylo možné požádat o přístup ke kontejneru, musíte nejprve dokončit a odeslat [formulář žádosti o přístup kontejnerů pro rozpoznávání formulářů Cognitive Services](https://aka.ms/FormRecognizerContainerRequestAccess) . Tím se také zaregistruje Počítačové zpracování obrazu. Nemusíte se do formuláře žádosti o Počítačové zpracování obrazu zaregistrovat samostatně. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

Minimální a doporučené PROCESORové jádro a paměť k přidělení pro každý kontejner pro rozpoznávání formulářů jsou popsány v následující tabulce:

| Kontejner | Minimální | Doporučené |
|-----------|---------|-------------|
| Rozpoznávání formulářů | 2 jádra, 4 GB paměti | 4 jádra, 8 GB paměti |
| Rozpoznávání textu | 1 jádro, 8 GB paměti | 2 jádra, 8 GB paměti |

* Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.
* Základní a paměť odpovídají nastavení `--cpus` a `--memory`, které se používají jako součást příkazu `docker run`.

> [!Note]
> Minimální a doporučené hodnoty jsou založené na omezeních Docker, a *ne* na zdrojích hostitelských počítačů.

## <a name="get-the-container-images-with-the-docker-pull-command"></a>Získání imagí kontejneru pomocí příkazu docker pull

Image kontejneru pro **rozpoznávání formulářů** a **rozpoznávání textu** nabídky jsou k dispozici v následujícím registru kontejneru:

| Kontejner | Plně kvalifikovaný název bitové kopie |
|-----------|------------|
| Rozpoznávání formulářů | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| Rozpoznávání textu | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Budete potřebovat oba kontejnery, Upozorňujeme, že textový kontejner **pro rozpoznávání** se [podrobně popisuje mimo tento článek.](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Funkce Docker Pull pro kontejner pro rozpoznávání formulářů

#### <a name="form-recognizer"></a>Rozpoznávání formulářů

Chcete-li získat kontejner pro rozpoznávání formulářů, použijte následující příkaz:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>Vyžádané čtení Docker pro kontejner Rozpoznávání textu

#### <a name="recognize-text"></a>Rozpoznávání textu

Chcete-li získat kontejner Rozpoznávání textu, použijte následující příkaz:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>Jak používat kontejner

Po dokončení kontejneru v [hostitelském počítači](#the-host-computer)použijte následující postup pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-by-using-the-docker-run-command)s požadovaným nastavením fakturace. K dispozici jsou další [příklady](form-recognizer-container-configuration.md#example-docker-run-commands) `docker run` příkazu.
1. [Dotazování koncového bodu předpovědi kontejneru](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-by-using-the-docker-run-command"></a>Spuštění kontejneru pomocí příkazu Docker run

Ke spuštění kontejneru použijte příkaz [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) . Podrobnosti o tom, jak získat `{COMPUTER_VISION_ENDPOINT_URI}`, `{COMPUTER_VISION_API_KEY}`, `{FORM_RECOGNIZER_ENDPOINT_URI}` a `{FORM_RECOGNIZER_API_KEY}` hodnot najdete v tématu [shromáždění požadovaných parametrů](#gathering-required-parameters) .

K dispozici jsou [příklady](form-recognizer-container-configuration.md#example-docker-run-commands) `docker run` příkazu.

### <a name="form-recognizer"></a>Rozpoznávání formulářů

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Tento příkaz:

* Spustí kontejner pro rozpoznávání formulářů z image kontejneru.
* Přiděluje 2 jádra procesoru a 8 GB paměti.
* Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.
* Připojí/Input a svazek/Output ke kontejneru.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Spustit samostatné kontejnery jako samostatné příkazy pro spuštění Docker

V případě kombinace formuláře a kombinace pro rozpoznávání textu, která je hostována místně na stejném hostiteli, použijte následující dva příklady příkazů Docker CLI:

Spusťte první kontejner na portu 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Spusťte druhý kontejner na portu 5001.

```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Každý další kontejner by měl být na jiném portu. 

### <a name="run-separate-containers-with-docker-compose"></a>Spustit samostatné kontejnery pomocí Docker Compose

Pro rozpoznávání formulářů a kombinaci pro rozpoznávání textu, která je hostována místně na stejném hostiteli, si přečtěte následující příklad Docker Compose souboru YAML. `{COMPUTER_VISION_API_KEY}` pro rozpoznávání textu musí být pro kontejnery `formrecognizer` i `ocr` stejné. `{COMPUTER_VISION_ENDPOINT_URI}` se používá pouze v kontejneru `ocr`, protože kontejner `formrecognizer` používá `ocr` název a port. 

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
      apikey: "{COMPUTER_VISION_API_KEY}"

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
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
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
> `Eula`, `Billing`a `ApiKey`, a také možnosti `FormRecognizer:ComputerVisionApiKey` a `FormRecognizer:ComputerVisionEndpointUri`, je nutné zadat pro spuštění kontejneru. v opačném případě se kontejner nespustí. Další informace najdete v tématu [fakturace](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncového bodu předpovědi kontejneru

|Kontejner|Koncový bod|
|--|--|
|formulář – rozpoznávání|http://localhost:5000

### <a name="form-recognizer"></a>Rozpoznávání formulářů

Kontejner poskytuje rozhraní API koncových bodů dotazů založených na protokolu WebSocket, ke kterým přistupujete v [dokumentaci k sadě SDK služby pro rozpoznávání formulářů](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Ve výchozím nastavení používá sada SDK pro rozpoznávání formulářů online služby. Chcete-li použít kontejner, je nutné změnit inicializační metodu. Podívejte se na následující příklady.

#### <a name="for-c"></a>ForC#

Změnit z použití tohoto volání inicializace Azure-Cloud:

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
do tohoto volání, které používá koncový bod kontejneru:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>Pro Python

Změnit z použití tohoto volání inicializace Azure-Cloud:

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

do tohoto volání, které používá koncový bod kontejneru:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Rozpoznávání formulářů

Kontejner poskytuje rozhraní API koncového bodu REST, které můžete najít na stránce [rozhraní API pro rozpoznávání formulářů](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel) .


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Odstraňování potíží

Pokud spouštíte kontejner s povoleným výstupním [připojením](form-recognizer-container-configuration.md#mount-settings) a povolíte protokolování, kontejner generuje soubory protokolu, které jsou užitečné při řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturace

Kontejnery pro rozpoznávání formulářů odesílají informace o fakturaci do Azure pomocí prostředku _pro rozpoznávání formulářů_ v účtu Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnerů](form-recognizer-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postupy pro stažení, instalaci a spuštění kontejnerů pro rozpoznávání formulářů. Souhrn:

* Nástroj pro rozpoznávání formulářů poskytuje pro Docker jeden kontejner Linux.
* Image kontejneru se stáhnou z privátního registru kontejnerů v Azure.
* Spuštění imagí kontejnerů v Dockeru.
* Můžete použít buď REST API, nebo sadu SDK REST k volání operací v kontejneru pro rozpoznávání formulářů zadáním identifikátoru URI hostitele kontejneru.
* Fakturační údaje je nutné zadat při vytváření instance kontejneru.

> [!IMPORTANT]
>  Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Kontejnery Cognitive Services neodesílají zákaznická data (například obrázek nebo analyzovaný text) společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [konfigurace kontejnerů](form-recognizer-container-configuration.md) pro nastavení konfigurace.
* Použijte více [Cognitive Services kontejnerů](../cognitive-services-container-support.md).
