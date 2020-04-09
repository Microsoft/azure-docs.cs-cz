---
title: Instalace a spuštění kontejnerů - Face
titleSuffix: Azure Cognitive Services
description: Tento článek ukazuje, jak stáhnout, nainstalovat a spustit kontejnery pro plochu v tomto kurzu návodu.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 74465bddb57c14af4d02c1d3bfdc46f3ac25bef3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878540"
---
# <a name="install-and-run-face-containers-preview"></a>Instalace a spuštění kontejnerů plochy (náhled)

Azure Cognitive Services Face poskytuje standardizovaný linuxový kontejner pro Docker, který detekuje lidské tváře v obrazech. Identifikuje také atributy, které zahrnují orientační body obličeje, jako jsou nosy a oči, pohlaví, věk a další strojově předpovídané rysy obličeje. Kromě detekce může tvář zkontrolovat, zda jsou dvě plochy ve stejném obrázku nebo různé obrázky stejné pomocí skóre spolehlivosti. Plocha také můžete porovnat plochy s databází a zjistit, zda již existuje podobně vypadající nebo identická plocha. Může také uspořádat podobné tváře do skupin pomocí sdílených vizuálních vlastností.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů služby Face je nutné splnit následující požadavky.

|Požaduje se|Účel|
|--|--|
|Docker Engine| Modul Docker u v počítači musí být nainstalován v [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které nakonfigurují prostředí Dockeru v systému [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).<br><br> Docker musí být nakonfigurovaný tak, aby umožňoval kontejnerům připojení k fakturačním datům a odesílání fakturačních dat do Azure. <br><br> V systému Windows docker také musí být nakonfigurován pro podporu linuxových kontejnerů.<br><br>|
|Znalost Dockeru | Potřebujete základní znalosti konceptů Dockeru, jako jsou registry, úložiště, kontejnery a image kontejnerů. Potřebujete také znalost `docker` základních příkazů.| 
|Zdroj obličeje |Chcete-li kontejner použít, musíte mít:<br><br>Prostředek Azure **Face** a přidružený klíč rozhraní API a identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránce **Přehled** a **Klíče** pro prostředek. Musí spustit kontejner.<br><br>**{API_KEY}:** Jeden ze dvou dostupných klíčů prostředků na stránce **Klíče**<br><br>**{ENDPOINT_URI}:** Koncový bod uvedený na stránce **Přehled**

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>Požádat o přístup k registru privátního kontejneru

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavky na kontejnery a doporučení

Následující tabulka popisuje minimální a doporučená jádra procesoru a paměť, které mají být přiděleny pro každý kontejner služby Face.

| Kontejner | Minimální | Doporučené | Transakce za sekundu<br>(Minimální, maximální)|
|-----------|---------|-------------|--|
|Tvář | 1 jádro, 2 GB paměti | 1 jádro, 4 GB paměti |10, 20|

* Každé jádro musí být alespoň 2,6 GHz nebo rychlejší.
* Transakce za sekundu (TPS).

Jádro a paměť `--cpus` odpovídají `--memory` nastavení a, které se `docker run` používají jako součást příkazu.

## <a name="get-the-container-image-with-docker-pull"></a>Získání image kontejneru s docker pull

K dispozici jsou ifotky kontejnerů pro službu Face. 

| Kontejner | Repository |
|-----------|------------|
| Tvář | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Docker vytáhnout pro kontejner Plochy

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Použití kontejneru

Po kontejneru je v [hostitelském počítači](#the-host-computer), použijte následující proces pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run) s požadovaným nastavením fakturace. Další [příklady](./face-resource-container-config.md#example-docker-run-commands) `docker run` příkazu jsou k dispozici. 
1. [Dotaz na koncový bod předpověď kontejneru](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Spuštění kontejneru s docker run

Ke spuštění kontejneru použijte příkaz [spustit docker.](https://docs.docker.com/engine/reference/commandline/run/) Podrobnosti o tom, jak získat hodnoty `{ENDPOINT_URI}` `{API_KEY}` a, naleznete v [části shromažďování požadovaných parametrů.](#gathering-required-parameters)

[Příklady](face-resource-container-config.md#example-docker-run-commands) příkazu `docker run` jsou k dispozici.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí kontejner plochy z image kontejneru.
* Přidělí jedno jádro procesoru a 4 GB paměti.
* Zpřístupní port TCP 5000 a přidělí pseudo TTY pro kontejner.
* Automaticky odebere kontejner po jeho ukončení. Bitová kopie kontejneru je stále k dispozici v hostitelském počítači. 

Další [příklady](./face-resource-container-config.md#example-docker-run-commands) `docker run` příkazu jsou k dispozici. 

> [!IMPORTANT]
> `Eula`, `Billing`a `ApiKey` možnosti musí být zadány ke spuštění kontejneru nebo kontejner nespustí. Další informace naleznete v [tématu Fakturace](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Dotaz na koncový bod předpovědi kontejneru

Kontejner poskytuje úložiště dat na základě dotazu předpověď koncový bod API. 

Použijte hostitele `http://localhost:5000`, pro kontejnerová řešení API.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spustíte kontejner s [výstupnípřipojení](./face-resource-container-config.md#mount-settings) a protokolování je povolena, kontejner generuje soubory protokolu, které jsou užitečné pro řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturace

Kontejnery služby Face odesílají fakturační údaje do Azure pomocí prostředku Face na vašem účtu Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech naleznete v [tématu Konfigurace kontejnerů](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste se dozvěděli koncepty a pracovní postupy, jak stáhnout, nainstalovat a spustit kontejnery služby Face. Souhrn:

* Image kontejnerů se stahují z registru kontejnerů Azure.
* Image kontejnerů běží v Dockeru.
* Pomocí rozhraní REST API nebo sady SDK můžete volat operace v kontejnerech služby Face zadáním identifikátoru URI hostitele kontejneru.
* Při vytváření instanitu kontejneru je nutné zadat fakturační údaje.

> [!IMPORTANT]
> Kontejnery služeb Cognitive Services nemají licenci ke spuštění bez připojení k Azure pro měření. Zákazníci musí povolit kontejnery komunikovat fakturační údaje se službou měření za všech okolností. Kontejnery služeb Cognitive Services neodesílají společnosti Microsoft data zákazníků, například obrázek nebo text, který se analyzuje.

## <a name="next-steps"></a>Další kroky

* Nastavení konfigurace naleznete v [tématu Konfigurace kontejnerů](face-resource-container-config.md).
* Další informace o tom, jak rozpoznat a identifikovat tváře, najdete v [tématu Přehled tváří](Overview.md).
* Informace o metodách podporovaných kontejnerem naleznete v [rozhraní API pro rozpoznávání tváře](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Další kontejnery služeb Cognitive Services naleznete v [tématu kontejnery služeb Cognitive Services](../cognitive-services-container-support.md).
