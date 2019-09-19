---
title: Instalace a spuštění kontejnerů – rozhraní API pro rozpoznávání tváře
titleSuffix: Azure Cognitive Services
description: Stažení, instalace a spuštění kontejnerů pro tvář v tomto výukovém kurzu.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: fbfc3f48bed5a4772573dcf2ab168cd3498a4cac
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102011"
---
# <a name="install-and-run-face-containers"></a>Instalace a spuštění kontejnerů obličeje

Azure Cognitive Services Face poskytuje standardizovaný kontejner pro Linux pro Docker, který detekuje lidské obličeje na obrázcích. Také identifikuje atributy, které zahrnují orientační orientační prvky, jako jsou například nos a oči, pohlaví, věk a další funkce obličeje v počítači. Kromě detekce může ploška kontrolovat, jestli dvě plošky ve stejné imagi nebo v různých imagích jsou stejné pomocí skóre spolehlivosti. Ploška také může porovnat plošky s databází a zjistit, zda již existuje podobný vzhled nebo identický obličej. Můžete také uspořádat podobné plošky do skupin pomocí sdílených vizuálních vlastností.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Než začnete používat kontejnery Face API, musíte splnit následující předpoklady.

|Požadováno|Účel|
|--|--|
|Modul Docker| Modul Docker musí být nainstalovaný na hostitelském [počítači](#the-host-computer). Docker poskytuje balíčky, které konfigurují prostředí Docker v systémech [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).<br><br> Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure. <br><br> V systému Windows musí být Docker taky nakonfigurovaný tak, aby podporoval kontejnery Linux.<br><br>|
|Znalost pomocí Docker | Potřebujete základní porozumění konceptům Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů. Potřebujete také znalosti základních `docker` příkazů.| 
|Prostředek pro tvář |Chcete-li použít kontejner, je nutné mít:<br><br>Prostředek Azure **Face** a přidružený klíč rozhraní API a identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách **Přehled** a **klíče** pro daný prostředek. Jsou nutné ke spuštění kontejneru.<br><br>**{API_KEY}** : Jeden ze dvou dostupných klíčů prostředků na stránce **klíče**<br><br>**{ENDPOINT_URI}** : Koncový bod, jak je uvedený na stránce **Přehled**

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>Požádat o přístup k registru kontejneru soukromého

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

V následující tabulce jsou popsány minimální a doporučené PROCESORy a paměťová jádra, která se mají přidělit pro každý Face API kontejner.

| Kontejner | Minimální | Doporučené | Transakce za sekundu<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|Tvář | 1 jádro, 2 GB paměti | 1 jádro, 4 GB paměti |10, 20|

* Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.
* Transakcí za sekundu (TPS).

Základní a paměť odpovídají `--cpus` nastavení a `--memory` , která se `docker run` používají jako součást příkazu.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru pomocí docker pull

K dispozici jsou image kontejneru pro Face API. 

| Kontejner | Úložiště |
|-----------|------------|
| Tvář | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Získání Docker pro kontejner obličeje

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Použití kontejneru

Po dokončení kontejneru v hostitelském [počítači](#the-host-computer)použijte následující postup pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run) s požadovaným nastavením fakturace. K [](./face-resource-container-config.md#example-docker-run-commands) dispozici jsou `docker run` další příklady příkazu. 
1. [Dotazování koncového bodu předpovědi kontejneru](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Spuštění kontejneru pomocí Docker run

Ke spuštění kontejneru použijte příkaz [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) . Podrobnosti o tom, jak získat `{ENDPOINT_URI}` hodnoty a `{API_KEY}` , najdete v článku [shromáždění požadovaných parametrů](#gathering-required-parameters) .

K dispozici jsou [Příklady](face-resource-container-config.md#example-docker-run-commands) příkazů.`docker run`

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí kontejner obličeje z image kontejneru.
* Přidělí jeden procesor a 4 GB paměti.
* Zveřejňuje port TCP 5000 a přiděluje pseudo TTY pro kontejner.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači. 

K [](./face-resource-container-config.md#example-docker-run-commands) dispozici jsou `docker run` další příklady příkazu. 

> [!IMPORTANT]
> Aby bylo `Billing`možné spustit `ApiKey` kontejner ,nebosekontejnernespustí,musíbýtzadánymožnosti,a.`Eula` Další informace najdete v tématu [fakturace](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncového bodu předpovědi kontejneru

Kontejner poskytuje rozhraní API koncového bodu předpovědi založené na REST. 

Pro rozhraní API kontejneru `http://localhost:5000`použijte hostitele.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spouštíte kontejner s výstupním [připojením](./face-resource-container-config.md#mount-settings) a je povoleno protokolování, kontejner generuje soubory protokolu, které jsou užitečné k řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru. 


## <a name="billing"></a>Fakturace

Kontejnery Face API odesílají informace o fakturaci do Azure pomocí prostředku Face API na účtu Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postupy, jak stahovat, instalovat a spouštět kontejnery Face API. Souhrn:

* Image kontejneru se stáhnou z Azure Container Registry.
* Spuštění imagí kontejnerů v Dockeru.
* Pomocí REST API nebo sady SDK můžete volat operace v kontejnerech Face API tím, že zadáte identifikátor URI hostitele kontejneru.
* Při vytváření instance kontejneru je nutné zadat informace o fakturaci.

> [!IMPORTANT]
> U Cognitive Servicesch kontejnerů nejsou licencovány ke spuštění bez připojení k Azure pro měření. Zákazníci musí kontejnery povolit, aby ve všech časech komunikovaly fakturační údaje se službou měření. Kontejnery Cognitive Services neodesílají zákaznická data, jako je například analyzovaný obrázek nebo text, do společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

* Nastavení konfigurace najdete v tématu [konfigurace kontejnerů](face-resource-container-config.md).
* Další informace o tom, jak detekovat a identifikovat plošky, najdete v tématu [Přehled obličeje](Overview.md).
* Informace o metodách podporovaných kontejnerem naleznete v [Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Chcete-li použít více Cognitive Services kontejnerů, přečtěte si téma [Cognitive Services kontejnery](../cognitive-services-container-support.md).
