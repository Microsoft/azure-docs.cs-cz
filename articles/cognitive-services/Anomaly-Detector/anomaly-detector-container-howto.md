---
title: Jak nainstalovat a spustit kontejnery pro použití rozhraní API detektoru anomálií
titleSuffix: Azure Cognitive Services
description: Pomocí pokročilých algoritmů rozhraní API detektoru anomálií můžete identifikovat anomálie v datech časových řad.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 45abd904ea95cf8e68583ba5630a485af59479ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220344"
---
# <a name="install-and-run-anomaly-detector-containers-preview"></a>Instalace a spuštění kontejnerů anomálií detektor (Náhled)

Detektor anomálií má následující funkce funkce kontejneru:

| Funkce | Funkce |
|--|--|
| Detektor anomálií | <li> Detekuje anomálie, jak k nim dochází v reálném čase. <li> Detekuje anomálie v celé sadě dat jako dávku. <li> Odvodí očekávaný normální rozsah dat. <li> Podporuje nastavení citlivosti detekce anomálií tak, aby lépe vyhovovalo vašim datům. |

Podrobné informace o api naleznete v následujících tématech:
* [Další informace o službě API detektoru anomálií](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů detektoru anomálií musíte splnit následující požadavky:

|Požaduje se|Účel|
|--|--|
|Docker Engine| Potřebujete modul Docker Engine nainstalovaný v [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které nakonfigurují prostředí Dockeru v systému [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).<br><br> Docker musí být nakonfigurovaný tak, aby umožňoval kontejnerům připojení k fakturačním datům a odesílání fakturačních dat do Azure. <br><br> **V systému Windows**musí být Docker také nakonfigurován pro podporu kontejnerů Linuxu.<br><br>|
|Znalost Dockeru | Měli byste mít základní znalosti konceptů Dockeru, jako jsou registry, úložiště, kontejnery `docker` a image kontejnerů, stejně jako znalost základních příkazů.| 
|Zdroj detektoru anomálií |Chcete-li používat tyto kontejnery, musíte mít:<br><br>Prostředek _Azure Anomaly Detector_ získat přidružené klíče rozhraní API a identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na webu Azure portal je **detektor anomálií** přehled a klíče stránky a jsou nutné ke spuštění kontejneru.<br><br>**{API_KEY}:** Jeden ze dvou dostupných klíčů prostředků na stránce **Klíče**<br><br>**{ENDPOINT_URI}:** Koncový bod uvedený na stránce **Přehled**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Musíte nejprve vyplnit a odeslat [formulář požadavku kontejneru detektoru anomálií požádat](https://aka.ms/adcontainer) o přístup ke kontejneru.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Požadavky na kontejnery a doporučení

Následující tabulka popisuje minimální a doporučená jádra procesoru a paměť, které mají být přiděleny pro kontejner detektoru anomálií.

| QPS (dotazy za sekundu) | Minimální | Doporučené |
|-----------|---------|-------------|
| 10 QPS | 4 jádra, 1 GB paměti | 8 jádrová 2GB paměť |
| 20 QPS | 8 jádra, 2 GB paměti | 16 jádrová 4GB paměť |

Každé jádro musí být nejméně 2,6 gigahertzů (GHz) nebo rychlejší.

Jádro a paměť `--cpus` odpovídají `--memory` nastavení a, které se `docker run` používají jako součást příkazu.

## <a name="get-the-container-image-with-docker-pull"></a>Získejte obrázek kontejneru pomocí`docker pull`

Pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu stáhněte bitovou kopii kontejneru.

| Kontejner | Repository |
|-----------|------------|
| kognitivní-služby-anomálií-detektor | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Docker vytáhnout pro kontejner detektoranolií

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Jak kontejner používat

Jakmile je kontejner v [hostitelském počítači](#the-host-computer), použijte následující proces pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run)s požadovaným nastavením fakturace. Další [příklady](anomaly-detector-container-configuration.md#example-docker-run-commands) `docker run` příkazu jsou k dispozici.
1. [Dotaz na koncový bod předpověď kontejneru](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Spusťte nádobu s`docker run`

Ke spuštění kontejneru použijte příkaz [spustit docker.](https://docs.docker.com/engine/reference/commandline/run/) Podrobnosti o tom, jak získat hodnoty `{ENDPOINT_URI}` `{API_KEY}` a, naleznete v [části shromažďování požadovaných parametrů.](#gathering-required-parameters)

[Příklady](anomaly-detector-container-configuration.md#example-docker-run-commands) příkazu `docker run` jsou k dispozici.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí kontejner detektoru anomálií z bitové kopie kontejneru
* Přiděluje jedno jádro procesoru a 4 gigabajty (GB) paměti.
* Zpřístupní port TCP 5000 a přidělí pseudo-TTY pro kontejner
* Automaticky odebere kontejner po jeho ukončení. Bitová kopie kontejneru je stále k dispozici v hostitelském počítači. 

> [!IMPORTANT]
> `Eula`, `Billing`a `ApiKey` možnosti musí být zadány ke spuštění kontejneru; v opačném případě se kontejner nespustí.  Další informace naleznete v [tématu Fakturace](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Spuštění více kontejnerů na stejném hostiteli

Pokud máte v úmyslu spustit více kontejnerů s vystavené porty, ujistěte se, že spustit každý kontejner s jiným portem. Například spusťte první kontejner na portu 5000 a druhý kontejner na portu 5001.

`<container-registry>` Nahraďte `<container-name>` a s hodnotami kontejnerů, které používáte. Nemusí se jedná o stejný kontejner. Můžete mít kontejner detektoru anomálií a kontejner LUIS spuštěný na HOSTITELI společně nebo můžete mít spuštěno více kontejnerů detektoru anomálií. 

Spusťte první kontejner na portu 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Spusťte druhý kontejner na portu 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Každý následující kontejner by měl být na jiném portu. 

## <a name="query-the-containers-prediction-endpoint"></a>Dotaz na koncový bod předpovědi kontejneru

Kontejner poskytuje úložiště dat na základě dotazu předpověď koncový bod API. 

Použijte hostitele http://localhost:5000, pro kontejnerová řešení API.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spustíte kontejner s povoleným výstupním [připojením](anomaly-detector-container-configuration.md#mount-settings) a protokolováním, kontejner generuje soubory protokolu, které jsou užitečné k řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturace

Kontejnery detektoranolií odesílají fakturační údaje do Azure pomocí prostředku _detektoru anomálií_ na vašem účtu Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech naleznete v [tématu Konfigurace kontejnerů](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste se naučili koncepty a pracovní postupy pro stahování, instalaci a spouštění kontejnerů detektoru anomálií. Souhrn:

* Detektor anomálií poskytuje jeden kontejner Linuxu pro Docker, zapouzdřuje detekci anomálií s dávkovým vs streamováním, očekávaným odvozením rozsahu a laděním citlivosti.
* Image kontejnerů se stahují z privátního registru kontejnerů Azure vyhrazenépro náhled kontejnerů.
* Image kontejnerů běží v Dockeru.
* Pomocí rozhraní REST API nebo sady SDK můžete volat operace v kontejnerech detektoru anomálií zadáním identifikátoru URI hostitele kontejneru.
* Při vytváření instancí kontejneru je nutné zadat fakturační údaje.

> [!IMPORTANT]
> Kontejnery služeb Cognitive Services nejsou licencovány ke spuštění bez připojení k Azure pro měření. Zákazníci musí povolit kontejnery komunikovat fakturační údaje se službou měření za všech okolností. Kontejnery služeb Cognitive Services neodesílají společnosti Microsoft zákaznická data (např. data časových řad, která se analyzují).

## <a name="next-steps"></a>Další kroky

* Kontrola [Konfigurace kontejnerů](anomaly-detector-container-configuration.md) pro nastavení konfigurace
* [Nasazení kontejneru detektoru anomálií do instancí kontejnerů Azure](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Další informace o službě API detektoru anomálií](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
