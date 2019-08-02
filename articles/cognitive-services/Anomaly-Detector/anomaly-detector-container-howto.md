---
title: Jak nainstalovat a spustit kontejnery pro použití rozhraní API detektoru anomálií
titleSuffix: Azure Cognitive Services
description: Pomocí pokročilých algoritmů rozhraní API pro detekci anomálií můžete identifikovat anomálie v datech časových řad.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 8d107aed75904c27b9ed231d50c884f96318a324
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321431"
---
# <a name="install-and-run-anomaly-detector-containers"></a>Instalace a spuštění kontejnerů detektoru anomálií

Detektor anomálií má následující kontejner: 

|Funkce|Funkce|
|-|-|
|Detektor anomálií| <li> Detekuje anomálie při jejich výskytu v reálném čase. <li> Detekuje anomálie v celé sadě dat jako dávku. <li> Odvodí očekávaný normální rozsah dat. <li> Podporuje úpravu citlivosti detekce anomálií, aby lépe vyhovovala vašim datům. |

Podrobné informace o rozhraních API najdete v těchto tématech:
* [Další informace o službě rozhraní API detektoru anomálií](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Než začnete používat kontejnery detektoru anomálií, musíte splnit následující předpoklady:

|Požadováno|Účel|
|--|--|
|Modul Docker| Potřebujete modul Docker nainstalovaný na [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které konfigurují prostředí Docker v systémech [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).<br><br> Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure. <br><br> **V systému Windows**musí být Docker taky nakonfigurovaný tak, aby podporoval kontejnery Linux.<br><br>|
|Znalost pomocí Docker | Měli byste mít základní znalosti konceptů Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů, a taky znalosti základních `docker` příkazů.| 
|Prostředek detektoru anomálií |Aby bylo možné tyto kontejnery použít, je nutné mít následující:<br><br>Prostředek _detektoru anomálií_ Azure, který získá přidružený klíč rozhraní API a identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách **** přehled detektoru anomálií Azure Portal a klíče a jsou požadovány ke spuštění kontejneru.<br><br>**{API_KEY}** : Jeden ze dvou dostupných klíčů prostředků na stránce **klíče**<br><br>**{ENDPOINT_URI}** : Koncový bod, jak je uvedený na stránce **Přehled**|

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Aby bylo možné požádat o přístup ke kontejneru, je nutné nejprve dokončit a odeslat [formulář žádosti o kontejner detektoru anomálií](https://aka.ms/adcontainer) .

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

V následující tabulce jsou popsány minimální a doporučené PROCESORové jádra a paměť pro přidělení kontejneru detektoru anomálií.

| QPS (dotazy za sekundu) | Minimální | Doporučené |
|-----------|---------|-------------|
| 10 QPS | 4 jádra, 1 GB paměti | 8 jader 2 – GB paměti |
| 20 QPS | 8 jader, 2 GB paměti | 16 jader 4 – GB paměti |

Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.

Základní a paměť odpovídají `--cpus` nastavení a `--memory` , která se `docker run` používají jako součást příkazu.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru pomocí`docker pull`

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Pomocí příkazu Stáhněte image kontejneru.

| Kontejner | Úložiště |
|-----------|------------|
| cognitive-services-anomaly-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-anomaly-detector-container"></a>Pull Docker pro kontejner detektoru anomálií

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Jak používat kontejner

Jakmile je kontejner na hostitelském [počítači](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run)s požadovaným nastavením fakturace. K [](anomaly-detector-container-configuration.md#example-docker-run-commands) dispozici jsou `docker run` další příklady příkazu. 
1. [Dotazování koncového bodu předpovědi kontejneru](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s`docker run`

Pomocí příkazu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) spusťte kterýkoli ze tří kontejnerů. Příkaz používá následující parametry:

| Zástupný symbol | Value |
|-------------|-------|
|{API_KEY} | Tento klíč se používá ke spuštění kontejneru a je k dispozici na stránce klíčů detektoru anomálií Azure Portal.  |
|{ENDPOINT_URI} | Hodnota identifikátoru URI fakturačního koncového bodu je k dispozici na stránce Přehled rozpoznávání anomálií Azure Portal.|

Tyto parametry nahraďte vlastními hodnotami v následujícím ukázkovém `docker run` příkazu.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí kontejner detektoru anomálií z image kontejneru.
* Přiděluje jedno PROCESORové jádro a 4 gigabajty (GB) paměti.
* Zpřístupňuje TCP port 5000 a přiděluje pseudo-TTY pro kontejner
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači. 

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Spuštění více kontejnerů na stejném hostiteli

Pokud máte v úmyslu spustit více kontejnerů s vystavenými porty, ujistěte se, že každý kontejner spustíte s jiným portem. Například spusťte první kontejner na portu 5000 a druhý kontejner na portu 5001.

Nahraďte `<container-name>` a hodnotou kontejnerů, které používáte. `<container-registry>` Nemusí se jednat o stejný kontejner. Můžete mít kontejner detektoru anomálií a kontejner LUIS běžící na hostiteli společně nebo může být spuštěno více kontejnerů detektoru anomálií. 

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

Každý další kontejner by měl být na jiném portu. 

## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncového bodu předpovědi kontejneru

Kontejner poskytuje rozhraní API koncového bodu předpovědi založené na REST. 

Pro rozhraní API kontejneru https://localhost:5000 použijte hostitele.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spouštíte kontejner s povoleným výstupním [připojením](anomaly-detector-container-configuration.md#mount-settings) a povolíte protokolování, kontejner generuje soubory protokolu, které jsou užitečné při řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru. 

## <a name="billing"></a>Fakturace

Kontejnery detektoru anomálií odesílají informace o fakturaci do Azure __ pomocí prostředku detektoru anomálií ve vašem účtu Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postupy pro stažení, instalaci a spuštění kontejnerů detektoru anomálií. Souhrn:

* Detekce anomálií poskytuje jeden kontejner pro Linux pro Docker, který zapouzdřuje detekci anomálií pomocí služby Batch vs streaming, očekávalo se odvození rozsahu a optimalizace citlivosti.
* Image kontejneru se stáhnou z privátní Azure Container Registry vyhrazené pro kontejnery ve verzi Preview.
* Spuštění imagí kontejnerů v Dockeru.
* Můžete použít REST API nebo SDK pro volání operací v kontejnerech detektoru anomálií zadáním identifikátoru URI hostitele kontejneru.
* Při vytváření instance kontejneru, je nutné zadat fakturační informace.

> [!IMPORTANT]
> Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Kontejnery Cognitive Services neodesílají zákaznická data (např. Analyzovaná data časových řad) do Microsoftu.

## <a name="next-steps"></a>Další kroky

* Kontrola [konfigurace kontejnery](anomaly-detector-container-configuration.md) nastavení konfigurace
* [Další informace o službě rozhraní API detektoru anomálií](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
