---
title: Jak nainstalovat a spustit kontejner pro nástroj pro rozpoznávání formuláře
titleSuffix: Azure Cognitive Services
description: Naučte se využívat kontejner Rozpoznávání formulářů k analýze dat formulářů a tabulek.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: a251e97d671c4aad0aebb1d6c3349cdc09444308
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718484"
---
# <a name="install-and-run-form-recognizer-containers"></a>Instalace a spouštění kontejnerů Rozlišovač formuláře

Azure pro rozpoznávání formuláře platí technologií pro strojové učení k identifikaci a extrahovat páry klíč hodnota a tabulky z formuláře. Přiřadí hodnoty a položky tabulky s páry klíč hodnota a potom vypíše strukturovanými daty, která obsahuje vztahy v původním souboru. 

Ke snížení složitosti a snadnou integraci vlastního modelu Rozlišovač formuláře do procesu automatizace pracovního postupu nebo jiné aplikace, můžete volat modelu s použitím jednoduchého rozhraní REST API. Zobrazuje se jenom pět formuláře dokumentů (nebo jeden prázdný formulář a dva formuláře vyplněné) jsou potřeba, abyste se mohli výsledky rychle, přesně a přizpůsobená pro váš konkrétní obsah. Žádné náročné ruční zásah nebo odbornými znalostmi o rozsáhlé datové vědy je nezbytné. A nevyžaduje označování dat nebo dat poznámky.

|Funkce|Funkce|
|-|-|
|Rozpoznávání formulářů| <li>Zpracovává soubory PDF, PNG a JPG<li>Trénovat vlastních modelů s minimálně 5 formy se stejné rozvržení <li>Extrahuje páry klíč hodnota a informace o tabulce <li>Pomocí funkce Azure Cognitive Services počítače pro zpracování obrazu rozhraní API rozpoznat Text detekovat a extrahovat tištěný text z obrázků uvnitř formuláře<li>Nevyžaduje, aby poznámky nebo používání popisků|

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů Rozlišovač formuláře, musí splňovat následující požadavky:

|Požadováno|Účel|
|--|--|
|Modul docker| Je nutné modul Docker nainstalovaný na [hostitelský počítač](#the-host-computer). Docker nabízí balíčky, které nakonfigurují prostředí Dockeru na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).<br><br> Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure. <br><br> Na Windows musí být Docker také nakonfigurované pro podporu kontejnery Linuxu.<br><br>|
|Znalost Dockeru | Byste měli mít základní znalost konceptů Dockeru, jako je například registry, úložiště, kontejnery a Image kontejnerů a znalost basic `docker` příkazy.|
|Azure CLI| Nainstalujte [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) na hostiteli.|
|Prostředky počítače rozhraní API pro zpracování obrazu| Zpracovat naskenované dokumenty a obrázky, je třeba prostředek pro počítačové zpracování obrazu. Můžete přístup k funkci rozpoznat Text jako buď prostředek Azure (rozhraní REST API nebo sady SDK) nebo *cognitive services – rozpoznat text* [kontejneru](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull). Použít obvyklé fakturační poplatky. <br><br>Předat klíče a koncových bodů fakturace pro váš prostředek pro počítačové zpracování obrazu (v cloudu Azure nebo kontejner služeb Cognitive Services). Použít tento klíč a fakturační koncový bod jako {COMPUTER_VISION_API_KEY} a {COMPUTER_VISION_BILLING_ENDPOINT_URI}.<br><br> Pokud používáte *cognitive services – rozpoznat text* kontejner, ujistěte se, že:<br><br>Klíče pro počítačové zpracování obrazu na kontejneru formuláře Rozlišovače je klíč zadaný v počítačové zpracování obrazu `docker run` příkazu *cognitive services – rozpoznat text* kontejneru.<br>Váš fakturační koncový bod je koncový bod kontejneru (například `https://localhost:5000`). Pokud používáte kontejneru pro počítačové zpracování obrazu i kontejner formuláře Rozlišovač společně na stejném hostiteli, nemohou i být spuštěny s výchozí port *5000*.  |  
|Prostředek formuláře rozpoznávání rukopisu |Pokud chcete použít tyto kontejnery, musíte mít:<br><br>A _formuláře Rozlišovač_ prostředků Azure můžete získat přidružený klíč účtování a fakturace identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na na webu Azure portal **formuláře Rozlišovač přehled** a **formuláře Rozlišovač přehled klíče** stránky a obě hodnoty, které jsou vyžadovány pro spuštění kontejneru.<br><br>**{BILLING_KEY}** : klíč prostředku<br><br>**{BILLING_ENDPOINT_URI}** : je například identifikátor URI koncového bodu `https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Nejprve musíte vyplňte a odešlete [Cognitive Services formuláře Rozlišovač kontejnery přístup formulář žádosti o](https://aka.ms/FormRecognizerRequestAccess) chcete požádat o přístup ke kontejneru. Tím také přihlásíte pro počítačové zpracování obrazu. Není nutné se zaregistrovat do služby pro počítačové zpracování obrazu formulář žádosti o samostatně. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Hostitelském počítači

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

Minimální a doporučené jader procesoru a paměti k přidělení pro každý kontejner Rozlišovač formuláře jsou popsány v následující tabulce:

| Kontejner | Minimální | Doporučené |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 jádra, 4 GB paměti | 4 jádra, 8 GB paměti |

* Každé jádro, musí být aspoň 2.6 gigahertz (GHz) nebo rychlejší.
* TPS – transakcí za sekundu
* Jader a paměti odpovídají `--cpus` a `--memory` nastavení, které se používají jako součást `docker run` příkazu.

> [!Note]
> Minimální a doporučené hodnoty jsou založeny na limity Dockeru a *není* hostitelského počítače prostředky.

## <a name="get-the-container-image-with-the-docker-pull-command"></a>Získat image kontejneru pomocí příkazu docker o přijetí změn

V následující úložiště jsou dostupné Image kontejneru pro formuláře pro rozpoznávání:

| Kontejner | Úložiště |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

Pokud máte v úmyslu použít `cognitive-services-recognize-text` [kontejneru](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull), místo služby Rozlišovač formuláře, ujistěte se, že používáte `docker pull` příkaz s názvem správným kontejnerem: 

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Operace docker pull Rozlišovač formuláře kontejneru

#### <a name="form-recognizer"></a>Rozpoznávání formulářů

Rozpoznávání formuláře kontejneru získáte pomocí následujícího příkazu:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>Jak používat kontejneru

Poté, co je kontejner na [hostitelský počítač](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spuštění kontejneru](#run-the-container-by-using-the-docker-run-command), s nastavením fakturační povinné, ale nepoužívá se. Další [příklady](form-recognizer-container-configuration.md#example-docker-run-commands) z `docker run` příkazu jsou k dispozici.
1. [Dotazování koncový bod kontejneru předpovědi](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Spusťte kontejner pomocí dockeru, spusťte příkaz

Použití [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkaz ke spuštění tři kontejnery. Příkaz používá následující parametry:

| Zástupný symbol | Hodnota |
|-------------|-------|
|{BILLING_KEY} | Tento klíč se používá ke spuštění kontejneru. Je k dispozici na na webu Azure portal **formuláře rozpoznávání klíče** stránky.  |
|{BILLING_ENDPOINT_URI} | Fakturační koncový bod hodnotu identifikátoru URI je k dispozici na na webu Azure portal **formuláře Rozlišovač přehled** stránky.|
|{COMPUTER_VISION_API_KEY}| Klíč je k dispozici na na webu Azure portal **klíče rozhraní API pro zpracování obrazu počítačů** stránky.|
|{COMPUTER_VISION_ENDPOINT_URI}|Fakturační koncový bod. Pokud používáte cloudové prostředky pro počítačové zpracování obrazu, že hodnota identifikátoru URI je k dispozici na na webu Azure portal **přehled rozhraní API pro zpracování obrazu počítače** stránky. Pokud používáte `cognitive-services-recognize-text` kontejneru, použijte fakturační adresu URL koncového bodu, který je předán do kontejneru v `docker run` příkazu.|

Tyto parametry nahraďte vlastními hodnotami v následujícím příkladu `docker run` příkazu.

### <a name="form-recognizer"></a>Rozpoznávání formulářů

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Tento příkaz:

* Spouští formuláře Rozlišovač kontejner z image kontejneru.
* Přidělí 2 jádra procesoru a 8 gigabajtů (GB) paměti.
* Zpřístupňuje TCP port 5000 a přiděluje pseudo-TTY pro kontejner.
* Po ukončení automaticky odstraní kontejner. Image kontejneru je stále k dispozici na hostitelském počítači.
* Připojí /input a svazek/Output do kontejneru.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Spouští samostatné kontejnery jako samostatné spouštět příkazy dockeru

Kombinace Rozlišovač formuláře a rozpoznávání textu, který je hostovaný místně na stejném hostiteli použijte následující dva příklady příkazů rozhraní příkazového řádku Dockeru:

Spuštění prvního kontejneru na portu 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
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

Kombinace Rozlišovač formuláře a rozpoznávání textu, který je hostovaný místně na stejném hostiteli najdete v následující ukázkový soubor Docker Compose YAML. Rozpoznávání textu `{COMPUTER_VISION_API_KEY}` musí být stejná pro obě `formrecognizer` a `ocr` kontejnery. `{COMPUTER_VISION_ENDPOINT_URI}` Se používá pouze v `ocr` kontejner, protože `formrecognizer` kontejner používá `ocr` název a port. 

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
> `Eula`, `Billing`, A `ApiKey`, jakož i `FormRecognizer:ComputerVisionApiKey` a `FormRecognizer:ComputerVisionEndpointUri` možnosti, musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru. Další informace najdete v tématu [fakturace](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncový bod kontejneru predikcí

|Kontejner|Koncový bod|
|--|--|
|form-recognizer|http://localhost:5000


### <a name="form-recognizer"></a>Rozpoznávání formulářů

Poskytuje kontejner dotazů založených na protokolu websocket koncový bod rozhraní API, které máte přístup prostřednictvím [Rozlišovač formuláře služby dokumentace k sadě SDK](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Ve výchozím nastavení používá sady SDK modulu pro rozpoznávání formuláře služeb online services. Použití kontejneru, budete muset změnit metodu inicializace. Viz následující příklady.

#### <a name="for-c"></a>ProC#

Přejdete od použití toto volání inicializace Azure cloud:

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

na toto volání, která používá koncový bod kontejneru:

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>Pro jazyk Python

Přejdete od použití toto volání inicializace Azure cloud:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

na toto volání, která používá koncový bod kontejneru:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Rozpoznávání formulářů

Poskytuje koncový bod REST API, které můžete vyhledat v kontejneru [rozhraní API pro rozpoznávání formuláře](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel) stránky.


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Zastavit kontejner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Při spuštění kontejneru se kontejner používá **stdout** a **stderr** do výstupu informace, které jsou užitečné pro odstraňování problémů, které vznikají při spuštění nebo spuštění kontejneru.

## <a name="billing"></a>Fakturace

Kontejnery Rozlišovač formuláře odeslat fakturační údaje do Azure s použitím _formuláře Rozlišovač_ prostředků v účtu Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](form-recognizer-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro stažení, instalaci a používání kontejnery Rozlišovač formuláře. Souhrn:

* Rozlišovač formuláře obsahuje jeden kontejner Linuxu pro Docker.
* Image kontejneru se stahují ze soukromého registru kontejnerů v Azure.
* Spuštění imagí kontejnerů v Dockeru.
* Rozhraní REST API nebo REST SDK můžete použít k volání operací v modulu pro rozpoznávání formuláře kontejneru tak, že zadáte identifikátor URI kontejneru hostitele.
* Fakturační údaje musíte zadat, když vytváříte instanci kontejneru.

> [!IMPORTANT]
>  Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Cognitive Services kontejnery Neodesílat data zákazníků (třeba image nebo text, který je analyzován) společnosti Microsoft.

## <a name="next-steps"></a>Další postup

* Kontrola [konfigurace kontejnery](form-recognizer-container-configuration.md) nastavení konfigurace.
* Použití více [Cognitive Services kontejnery](../cognitive-services-container-support.md).
