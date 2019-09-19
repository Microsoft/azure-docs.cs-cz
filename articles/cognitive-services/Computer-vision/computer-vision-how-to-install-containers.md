---
title: Jak nainstalovat a spustit kontejnery – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Jak si stáhnout, nainstalovat a spouštění kontejnerů v tomto kurzu návod pro počítačové zpracování obrazu.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: d3a36615109383074833e9af634eb611fb863339
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103653"
---
# <a name="install-and-run-recognize-text-containers"></a>Instalace a spuštění kontejnerů Rozpoznávání textu

Rozpoznávání textu část pro počítačové zpracování obrazu je také k dispozici jako kontejner Dockeru. Umožňuje detekovat a extrahovat tištěný text z obrázků s různými povrchy a pozadími, jako je potvrzení a plakáty nebo vizitky různé objekty.

> [!IMPORTANT]
> Kontejner rozpoznat Text v současné době používá pouze angličtinu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Před použitím Rozpoznávání textu kontejnerů musíte splnit následující předpoklady:

|Požadováno|Účel|
|--|--|
|Modul Docker| Potřebujete modul Docker nainstalovaný na [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které konfigurují prostředí Docker v systémech [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).<br><br> Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure. <br><br> **V systému Windows**musí být Docker taky nakonfigurovaný tak, aby podporoval kontejnery Linux.<br><br>|
|Znalost pomocí Docker | Měli byste mít základní znalosti konceptů Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů, a taky znalosti základních `docker` příkazů.| 
|Prostředek Počítačové zpracování obrazu |Aby bylo možné kontejner používat, musíte mít:<br><br>Prostředek Azure **počítačové zpracování obrazu** a přidružený klíč rozhraní API identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách přehledu a klíčů pro daný prostředek a jsou požadovány ke spuštění kontejneru.<br><br>**{API_KEY}** : Jeden ze dvou dostupných klíčů prostředků na stránce **klíče**<br><br>**{ENDPOINT_URI}** : Koncový bod, jak je uvedený na stránce **Přehled**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>Požádat o přístup k registru kontejneru soukromého

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru pomocí`docker pull`

K dispozici jsou image kontejneru pro Rozpoznávání textu. 

| Kontejner | Úložiště |
|-----------|------------|
|Rozpoznávání textu | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Pomocí příkazu Stáhněte image kontejneru.


### <a name="docker-pull-for-the-recognize-text-container"></a>Vyžádané čtení Docker pro kontejner Rozpoznávání textu

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak používat kontejner

Jakmile je kontejner na hostitelském [počítači](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run)s požadovaným nastavením fakturace. K [](computer-vision-resource-container-config.md) dispozici jsou `docker run` další příklady příkazu. 
1. [Dotazování koncového bodu předpovědi kontejneru](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s`docker run`

Ke spuštění kontejneru použijte příkaz [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) . Podrobnosti o tom, jak získat `{ENDPOINT_URI}` hodnoty a `{API_KEY}` , najdete v článku [shromáždění požadovaných parametrů](#gathering-required-parameters) .

K dispozici jsou [Příklady](computer-vision-resource-container-config.md#example-docker-run-commands) příkazů.`docker run`

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí rozpoznávání kontejneru z image kontejneru.
* Přiděluje jedno PROCESORové jádro a 4 gigabajty (GB) paměti.
* Zpřístupňuje TCP port 5000 a přiděluje pseudo-TTY pro kontejner
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači. 

K [](./computer-vision-resource-container-config.md#example-docker-run-commands) dispozici jsou `docker run` další příklady příkazu. 

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncového bodu předpovědi kontejneru

Kontejner poskytuje rozhraní API koncového bodu předpovědi založené na REST. 

Pro rozhraní API kontejneru `http://localhost:5000`použijte hostitele.

### <a name="asynchronous-text-recognition"></a>Rozpoznávání asynchronní textu

Můžete použít `POST /vision/v2.0/recognizeText` a `GET /vision/v2.0/textOperations/*{id}*` operací ve vzájemné součinnosti asynchronně rozpoznat tištěný text v obrázku, podobně jako na používání těchto odpovídající operace REST služby pro počítačové zpracování obrazu. Zásobník rozpoznání textu rozpozná pouze tištěný text, ne rukou psaný text v současné době proto `mode` parametr obvykle zadaný pro operaci služby pro počítačové zpracování obrazu je ignorován v kontejneru rozpoznat Text.

### <a name="synchronous-text-recognition"></a>Rozpoznávání synchronní textu

Můžete použít `POST /vision/v2.0/recognizeTextDirect` operace synchronně rozpoznat tištěný text v obrázku. Vzhledem k tomu, že je tato operace synchronní, je text žádosti pro tuto operaci stejný `POST /vision/v2.0/recognizeText` jako operace, ale tělo odpovědi pro tuto operaci je stejné jako operace, kterou vrátila `GET /vision/v2.0/textOperations/*{id}*` operace.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spouštíte kontejner s povoleným výstupním [připojením](./computer-vision-resource-container-config.md#mount-settings) a povolíte protokolování, kontejner generuje soubory protokolu, které jsou užitečné při řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru. 


## <a name="billing"></a>Fakturace

Kontejnery Rozpoznávání textu odesílají informace o fakturaci do Azure pomocí prostředku _rozpoznávání textu_ na účtu Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postupy pro stažení, instalaci a spuštění kontejnerů Rozpoznávání textu. Souhrn:

* Rozpoznávání textu poskytuje kontejner pro Linux pro Docker, který zapouzdřuje rozpoznávání textu.
* Image kontejneru se stáhnou z kontejneru registru Microsoft (MCR) v Azure.
* Spuštění imagí kontejnerů v Dockeru.
* Můžete použít REST API nebo SDK pro volání operací v kontejnerech Rozpoznávání textu zadáním identifikátoru URI hostitele kontejneru.
* Při vytváření instance kontejneru, je nutné zadat fakturační informace.

> [!IMPORTANT]
> Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Kontejnery Cognitive Services neodesílají zákaznická data (například obrázek nebo analyzovaný text) společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

* Kontrola [konfigurace kontejnery](computer-vision-resource-container-config.md) nastavení konfigurace
* Kontrola [přehled pro počítačové zpracování obrazu](Home.md) Další informace o rozpoznávání tištěné a rukou psaný text  
* Odkazovat [rozhraní API pro počítačové zpracování obrazu](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) podrobné informace o metodách podporuje kontejneru.
* Odkazovat na [– nejčastější dotazy (FAQ)](FAQ.md) k vyřešení problémů týkajících se funkce pro počítačové zpracování obrazu.
* Použít více [Cognitive Servicesch kontejnerů](../cognitive-services-container-support.md)
