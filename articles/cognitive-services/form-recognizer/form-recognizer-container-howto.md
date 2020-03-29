---
title: Jak nainstalovat a spustit kontejner pro rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: Tento článek vám vysvětlí, jak pomocí kontejneru Nástroje pro rozpoznávání formulářů Azure Cognitive Services k analýzě dat formuláře a tabulky.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: dapine
ms.openlocfilehash: c8ce4b913548429ff83e0b8aa3cb65455fc9b4c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474692"
---
# <a name="install-and-run-form-recognizer-containers-preview"></a>Instalace a spuštění kontejnerů pro rozpoznávání formulářů (náhled)

Azure Form Recognizer používá technologii strojového učení k identifikaci a extrahování párů klíč-hodnota a tabulek z formulářů. Přidruží hodnoty a položky tabulky k párům klíč-hodnota a poté vynese strukturovaná data, která zahrnují relace v původním souboru. 

Chcete-li snížit složitost a snadno integrovat vlastní model rozpoznávání formulářů do procesu automatizace pracovního postupu nebo jiné aplikace, můžete volat model pomocí jednoduchého rozhraní REST API. Je zapotřebí pouze pět dokumentů formuláře (nebo jeden prázdný formulář a dva vyplněné formuláře), takže můžete rychle, přesně a přizpůsobené konkrétnímu obsahu. Není nutný žádný těžký ruční zásah nebo rozsáhlé odborné znalosti v oblasti datových věd. A nevyžaduje popisdat nebo anotaci dat.

> [!IMPORTANT]
> Kontejnery nástroje pro rozpoznávání formulářů aktuálně používají verzi 1.0 rozhraní API pro rozpoznávání formulářů. K nejnovější verzi rozhraní API můžete získat přístup pomocí spravované služby.

| Funkce | Funkce |
|----------|----------|
| Rozpoznávání formulářů | <li>Zpracovává soubory PDF, PNG a JPG<li>Trénuje vlastní modely s minimálně pěti formami stejného uspořádání <li>Extrahuje dvojice klíč-hodnota a informace o tabulce. <li>Používá funkci Azure Cognitive Services Computer Vision API Recognize Text k detekci a extrahování tištěného textu z obrázků ve formulářích<li>Nevyžaduje poznámky nebo popisky |

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů nástroje pro rozpoznávání formulářů musíte splnit následující požadavky:

| Požaduje se | Účel |
|----------|---------|
| Docker Engine | Potřebujete modul Docker Engine nainstalovaný v [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které nakonfigurují prostředí Dockeru v systému [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).<br><br> Docker musí být nakonfigurovaný tak, aby umožňoval kontejnerům připojení k fakturačním datům a odesílání fakturačních dat do Azure. <br><br> V systému Windows musí být Docker také nakonfigurován pro podporu kontejnerů Linuxu.<br><br> |
| Znalost Dockeru | Měli byste mít základní znalosti konceptů Dockeru, jako jsou registry, úložiště, kontejnery a image kontejnerů a znalost základních `docker` příkazů. |
| Rozhraní příkazového řádku Azure | Nainstalujte si na hostitele [nastavení příkazového příkazu Konazure.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) |
| Prostředek rozhraní API pro počítačové zpracování obrazu | Ke zpracování naskenovaných dokumentů a obrázků potřebujete zdroj počítačového vidění. K funkci Rozpoznat text můžete přistupovat buď jako prostředek Azure (rozhraní REST API nebo Sada SDK) nebo [kontejner](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull) *rozpoznávání kognitivních služeb* . Platí se obvyklé poplatky za fakturaci. <br><br>Předejte klíč rozhraní API i koncové body pro prostředek počítačového zpracování (cloud Azure nebo kontejner kognitivních služeb). Použijte tento klíč rozhraní API a koncový bod jako **{COMPUTER_VISION_API_KEY}** a **{COMPUTER_VISION_ENDPOINT_URI}**.<br><br> Pokud používáte kontejner *cognitive-services-recognize-text,* ujistěte se, že:<br><br>Klíč Počítačové vidění pro kontejner nástroje pro rozpoznávání formulářů `docker run` je klíč určený v příkazu Počítačové vidění pro kontejner *kognitivních služeb rozpoznávací text.*<br>Koncový bod fakturace je koncový bod kontejneru `http://localhost:5000`(například). Pokud používáte kontejner počítačového vidění i kontejner nástroje pro rozpoznávání formulářů společně na stejném hostiteli, nelze je spustit s výchozím portem *5000*. |
| Prostředek nástroje pro rozpoznávání formulářů | Chcete-li použít tyto kontejnery, musíte mít:<br><br>Prostředek **nástroje Pro rozpoznávání formulářů** Azure, který získá přidružený klíč rozhraní API a identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na webu Azure portal **Form Recognizer** Overview a Keys a obě hodnoty jsou potřeba ke spuštění kontejneru.<br><br>**{FORM_RECOGNIZER_API_KEY}:** Jeden ze dvou dostupných klíčů prostředků na stránce Klíče<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}:** Koncový bod uvedený na stránce Přehled |

> [!NOTE]
> Název prostředku počítačového vidění by měl být `-` jedno slovo bez pomlčky nebo jiných speciálních znaků. Toto omezení je na místě k zajištění form recognizer a rozpoznat kompatibilitu kontejneru textu.

## <a name="gathering-required-parameters"></a>Shromažďování požadovaných parametrů

Existují tři primární parametry pro všechny kontejnery cognitive services, které jsou požadovány. Licenční smlouva s koncovým uživatelem (EULA) `accept`musí být přítomna s hodnotou . Kromě toho jsou potřeba adresu URL koncového bodu a klíč rozhraní API.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>Identifikátor URI `{COMPUTER_VISION_ENDPOINT_URI}` koncového bodu a`{FORM_RECOGNIZER_ENDPOINT_URI}`

Hodnota IDENTIFIKÁTORU URI **koncového bodu** je k dispozici na stránce *Přehled* portálu Azure odpovídajícího prostředku služby Cognitive Service. Přejděte na stránku *Přehled,* najeďte `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> na koncový bod a zobrazí se ikona. V případě potřeby kopírujte a používejte.

![Shromáždit identifikátor uri koncového bodu pro pozdější použití](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Klíče `{COMPUTER_VISION_API_KEY}` a`{FORM_RECOGNIZER_API_KEY}`

Tento klíč se používá ke spuštění kontejneru a je k dispozici na stránce klíče portálu Azure odpovídající prostředek kognitivní služby. Přejděte na stránku *Klíče* `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> a klikněte na ikonu.

![Získejte jeden ze dvou klíčů pro pozdější použití](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> Tyto klíče předplatného se používají pro přístup k rozhraní API služby Cognitive Service. Nesdílejte své klíče. Uklápěte je bezpečně, například pomocí azure key vaultu. Doporučujeme také pravidelně regenerovat tyto klíče. K volání rozhraní API je nutný pouze jeden klíč. Při obnově prvního klíče můžete použít druhý klíč pro trvalý přístup ke službě.

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Musíte nejprve vyplnit a odeslat [formulář pro rozpoznávání formulářů Cognitive Services Kontejnery přístup formulář požádat](https://aka.ms/FormRecognizerContainerRequestAccess) o přístup ke kontejneru. Přitom také podepíše vás na počítačové vidění. Není nutné se přihlásit do formuláře žádosti o počítačové vize samostatně. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavky na kontejnery a doporučení

Minimální a doporučená jádra procesoru a paměť, která mají být přidělena pro každý kontejner nástroje pro rozpoznávání formulářů, jsou popsány v následující tabulce:

| Kontejner | Minimální | Doporučené |
|-----------|---------|-------------|
| Rozpoznávání formulářů | 2 jádra, 4 GB paměti | 4 jádra, 8 GB paměti |
| Rozpoznat text | 1 jádro, 8 GB paměti | 2 jádra, 8 GB paměti |

* Každé jádro musí být nejméně 2,6 gigahertzů (GHz) nebo rychlejší.
* Jádro a paměť `--cpus` odpovídají `--memory` nastavení a, které se `docker run` používají jako součást příkazu.

> [!Note]
> Minimální a doporučené hodnoty jsou založeny na omezení dockeru a *nikoli* na prostředcích hostitelského počítače.

## <a name="get-the-container-images-with-the-docker-pull-command"></a>Získání imitace kontejnerů pomocí příkazu docker pull

Image kontejnerů pro **nabídky rozpoznávání formulářů** i **rozpoznávání textu** jsou k dispozici v následujícím registru kontejnerů:

| Kontejner | Plně kvalifikovaný název obrázku |
|-----------|------------|
| Rozpoznávání formulářů | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| Rozpoznat text | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Budete potřebovat oba kontejnery, upozorňujeme, že kontejner **textu rozpoznávání** je podrobně mimo [tento článek.](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Docker vytáhnout pro kontejner rozpoznávání formulářů

#### <a name="form-recognizer"></a>Rozpoznávání formulářů

Chcete-li získat kontejner nástroje pro rozpoznávání formulářů, použijte následující příkaz:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>Docker vytáhnout pro kontejner Rozpoznat text

#### <a name="recognize-text"></a>Rozpoznat text

Chcete-li získat kontejner Rozpoznat text, použijte následující příkaz:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>Jak kontejner používat

Po kontejneru je v [hostitelském počítači](#the-host-computer), použijte následující proces pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-by-using-the-docker-run-command)s požadovaným nastavením fakturace. Další [příklady](form-recognizer-container-configuration.md#example-docker-run-commands) `docker run` příkazu jsou k dispozici.
1. [Dotaz na koncový bod předpověď kontejneru](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Spuštění kontejneru pomocí příkazu docker run

Ke spuštění kontejneru použijte příkaz [spustit docker.](https://docs.docker.com/engine/reference/commandline/run/) Podrobnosti o `{COMPUTER_VISION_ENDPOINT_URI}`tom, jak získat hodnoty `{COMPUTER_VISION_API_KEY}`, `{FORM_RECOGNIZER_ENDPOINT_URI}` `{FORM_RECOGNIZER_API_KEY}` , naleznete v části [Shromažďování požadovaných parametrů.](#gathering-required-parameters)

[Příklady](form-recognizer-container-configuration.md#example-docker-run-commands) příkazu `docker run` jsou k dispozici.

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

* Spustí kontejner nástroje pro rozpoznávání formulářů z image kontejneru.
* Přiděluje 2 jádra procesoru a 8 gigabajtů (GB) paměti.
* Zpřístupní port TCP 5000 a přidělí pseudo-TTY pro kontejner.
* Automaticky odebere kontejner po jeho ukončení. Bitová kopie kontejneru je stále k dispozici v hostitelském počítači.
* Připojí /input a /output volume do kontejneru.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Spuštění samostatných kontejnerů jako samostatných příkazů spuštění dockeru

Pro kombinaci nástroje pro rozpoznávání formulářů a rozpoznávání textu, která je hostována místně na stejném hostiteli, použijte následující dva příklady příkazů Příkazu k onba dockeru:

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
Každý následující kontejner by měl být na jiném portu. 

### <a name="run-separate-containers-with-docker-compose"></a>Spuštění samostatných kontejnerů pomocí Docker Compose

Pro kombinaci nástroje pro rozpoznávání formulářů a rozpoznávání textu, která je hostována místně na stejném hostiteli, najdete v následujícím příkladu souboru YAML v Dockeru Compose. Rozpoznávání textu `{COMPUTER_VISION_API_KEY}` musí být stejné pro `formrecognizer` `ocr` kontejnery i kontejnery. Používá `{COMPUTER_VISION_ENDPOINT_URI}` se pouze `ocr` v kontejneru, `formrecognizer` protože `ocr` kontejner používá název a port. 

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
> Chcete-li `Eula`spustit kontejner, `Billing`musí být zadány možnosti , a `ApiKey`, a `FormRecognizer:ComputerVisionApiKey` také možnosti a; `FormRecognizer:ComputerVisionEndpointUri` v opačném případě se kontejner nespustí. Další informace naleznete v [tématu Fakturace](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Dotaz na koncový bod předpovědi kontejneru

|Kontejner|Koncový bod|
|--|--|
|nástroj pro rozpoznávání formulářů|http://localhost:5000

### <a name="form-recognizer"></a>Rozpoznávání formulářů

Kontejner poskytuje rozhraní API koncového bodu dotazu založené na websocket, ke kterým přistupujete prostřednictvím [dokumentace sady SDK služby nástroje Pro rozpoznávání formulářů](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Ve výchozím nastavení používá sada SDK pro rozpoznávání formulářů online služby. Chcete-li použít kontejner, je třeba změnit metodu inicializace. Podívejte se na následující příklad:

#### <a name="for-c"></a>Pro C #

Změna z použití tohoto volání inicializace Azure cloudu:

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
k tomuto volání, který používá koncový bod kontejneru:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>Pro Python

Změna z použití tohoto volání inicializace Azure cloudu:

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

k tomuto volání, který používá koncový bod kontejneru:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Rozpoznávání formulářů

Kontejner poskytuje rozhraní API koncového bodu REST, které najdete na stránce [rozhraní API nástroje PRO ROZPOZNÁVÁNÍ FORMULÁŘŮ.](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel)


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spustíte kontejner s povoleným výstupním [připojením](form-recognizer-container-configuration.md#mount-settings) a protokolováním, kontejner generuje soubory protokolu, které jsou užitečné k řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturace

Kontejnery nástroje pro rozpoznávání formulářů odesílají fakturační údaje do Azure pomocí prostředku _nástroje pro rozpoznávání formulářů_ ve vašem účtu Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech naleznete v [tématu Konfigurace kontejnerů](form-recognizer-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste se naučili koncepty a pracovní postupy pro stahování, instalaci a spouštění kontejnerů nástroje pro rozpoznávání formulářů. Souhrn:

* Nástroj pro rozpoznávání formulářů poskytuje jeden kontejner Linuxu pro Docker.
* Image kontejnerů se stahují z registru privátního kontejneru v Azure.
* Image kontejnerů běží v Dockeru.
* Pomocí rozhraní REST API nebo sady REST SDK můžete volat operace v kontejneru nástroje pro rozpoznávání formulářů zadáním identifikátoru URI hostitele kontejneru.
* Při vytváření instanitu kontejneru je nutné zadat fakturační údaje.

> [!IMPORTANT]
>  Kontejnery služeb Cognitive Services nejsou licencovány ke spuštění bez připojení k Azure pro měření. Zákazníci musí povolit kontejnery komunikovat fakturační údaje se službou měření za všech okolností. Kontejnery služeb Cognitive Services neodesílají data zákazníků (například obrázek nebo text, který se analyzuje) společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

* Přečtěte [si panel Konfigurace kontejnerů](form-recognizer-container-configuration.md) pro nastavení konfigurace.
* Použijte více [kontejnerů služeb Cognitive Services](../cognitive-services-container-support.md).
