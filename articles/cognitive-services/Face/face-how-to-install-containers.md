---
title: Instalace a spuštění kontejnerů Docker pro Face API
titleSuffix: Azure Cognitive Services
description: Pomocí kontejneru Docker pro Face API můžete detekovat a identifikovat lidské plošky na obrázcích.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
keywords: místní, Docker, kontejner, identifikace
ms.openlocfilehash: 0f6807f771510f85c5a20cfb2a160cfe1e8726a3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000538"
---
# <a name="install-and-run-face-containers-preview"></a>Instalace a spuštění kontejnerů obličeje (Preview)

> [!IMPORTANT]
> Došlo k dosažení limitu počtu uživatelů kontejnerů Rozpoznávání tváře. V současné době nepřijímáme nové přihlášky ke kontejnerům Rozpoznávání tváře.

Azure Cognitive Services Face API poskytuje kontejner pro Linux Docker, který detekuje a analyzuje lidské obličeje na obrázcích. Také identifikuje atributy, které zahrnují orientační orientační prvky, jako jsou například nos a oči, pohlaví, věk a další funkce obličeje v počítači. Kromě detekce může ploška kontrolovat, jestli dvě plošky ve stejné imagi nebo v různých imagích jsou stejné pomocí skóre spolehlivosti. Ploška také může porovnat plošky s databází a zjistit, zda již existuje podobný vzhled nebo identický obličej. Můžete také uspořádat podobné plošky do skupin pomocí sdílených vizuálních vlastností.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

Než začnete používat kontejnery služby Face, musíte splnit následující předpoklady.

|Vyžadováno|Účel|
|--|--|
|Docker Engine| Modul Docker musí být nainstalovaný na [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které nakonfigurují prostředí Dockeru v systému [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).<br><br> Docker musí být nakonfigurovaný tak, aby umožňoval kontejnerům připojit se a odeslat fakturační data do Azure. <br><br> V systému Windows musí být Docker taky nakonfigurovaný tak, aby podporoval kontejnery Linux.<br><br>|
|Znalost pomocí Docker | Potřebujete základní porozumění konceptům Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů. Potřebujete také znalosti základních `docker` příkazů.| 
|Prostředek pro tvář |Chcete-li použít kontejner, je nutné mít:<br><br>Prostředek Azure **Face** a přidružený klíč rozhraní API a identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách **Přehled** a **klíče** pro daný prostředek. Jsou nutné ke spuštění kontejneru.<br><br>**{API_KEY}**: jeden ze dvou dostupných klíčů prostředků na stránce **klíče**<br><br>**{ENDPOINT_URI}**: koncový bod uvedený na stránce **Přehled**

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavky na kontejner a doporučení

V následující tabulce jsou popsány minimální a doporučené PROCESORy a paměťová jádra, která se mají přidělit pro každý kontejner služby obličeje.

| Kontejner | Minimum | Doporučeno | Transakcí za sekundu<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|Rozpoznávání tváře | 1 jádro, 2 GB paměti | 1 jádro, 4 GB paměti |10, 20|

* Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.
* Transakcí za sekundu (TPS).

Základní a paměť odpovídají `--cpus` `--memory` nastavení a, která se používají jako součást `docker run` příkazu.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru pomocí docker pull

K dispozici jsou image kontejneru pro službu obličeje. 

| Kontejner | Repository |
|-----------|------------|
| Rozpoznávání tváře | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Získání Docker pro kontejner obličeje

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Použití kontejneru

Po dokončení kontejneru v [hostitelském počítači](#the-host-computer)použijte následující postup pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run) s požadovaným nastavením fakturace. [examples](./face-resource-container-config.md#example-docker-run-commands) `docker run` K dispozici jsou další příklady příkazu. 
1. [Dotazování koncového bodu předpovědi kontejneru](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Spuštění kontejneru pomocí Docker run

Ke spuštění kontejneru použijte příkaz [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) . Podrobnosti o tom, jak získat hodnoty a, najdete v článku [shromáždění požadovaných parametrů](#gathering-required-parameters) `{ENDPOINT_URI}` `{API_KEY}` .

[Examples](face-resource-container-config.md#example-docker-run-commands) `docker run` K dispozici jsou příklady příkazů.

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

[examples](./face-resource-container-config.md#example-docker-run-commands) `docker run` K dispozici jsou další příklady příkazu. 

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby bylo možné spustit kontejner, nebo se kontejner nespustí, musí být zadány možnosti, a. Další informace najdete v tématu [fakturace](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Zadání dotazu do prediktivního koncového bodu kontejneru

Kontejner poskytuje rozhraní API prediktivního koncového bodu pro dotazy založené na REST. 

Pro rozhraní API kontejneru použijte hostitele `http://localhost:5000`.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spouštíte kontejner s výstupním [připojením](./face-resource-container-config.md#mount-settings) a je povoleno protokolování, kontejner generuje soubory protokolu, které jsou užitečné k řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturace

Kontejnery služby obličeje odesílají informace o fakturaci do Azure pomocí prostředku obličeje ve vašem účtu Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnerů](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postupy pro stažení, instalaci a spuštění kontejnerů služeb obličeje. Souhrn:

* Image kontejneru se stáhnou z Azure Container Registry.
* Image kontejneru se spouštějí v Docker.
* Pomocí REST API nebo sady SDK můžete volat operace v kontejnerech služby obličeje zadáním identifikátoru URI hostitele kontejneru.
* Při vytváření instance kontejneru je nutné zadat informace o fakturaci.

> [!IMPORTANT]
> U Cognitive Servicesch kontejnerů nejsou licencovány ke spuštění bez připojení k Azure pro měření. Zákazníci musí kontejnery povolit, aby ve všech časech komunikovaly fakturační údaje se službou měření. Kontejnery Cognitive Services neodesílají zákaznická data, jako je například analyzovaný obrázek nebo text, do společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

* Nastavení konfigurace najdete v tématu [konfigurace kontejnerů](face-resource-container-config.md).
* Další informace o tom, jak detekovat a identifikovat plošky, najdete v tématu [Přehled obličeje](Overview.md).
* Informace o metodách podporovaných kontejnerem naleznete v [Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Chcete-li použít více Cognitive Services kontejnerů, přečtěte si téma [Cognitive Services kontejnery](../cognitive-services-container-support.md).
