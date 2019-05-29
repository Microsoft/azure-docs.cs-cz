---
title: Instalace a spuštění kontejnerů
titlesuffix: Face - Azure Cognitive Services
description: Stažení, instalaci a spouštění kontejnerů v tomto kurzu návod pro rozpoznávání tváře.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: article
ms.date: 05/28/2019
ms.author: diberry
ms.openlocfilehash: 26ebeb463f42dce06c29a5bd3f69585430a2ee90
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306641"
---
# <a name="install-and-run-face-containers"></a>Instalace a spouštění kontejnerů pro rozpoznávání tváře

Azure Cognitive Services Face poskytuje standardizované kontejneru Linuxu pro Docker, který zjistí lidských tváří na obrázcích. Také určuje atributy, které zahrnují orientačních bodů pro rozpoznávání tváře například ústa a oči, pohlaví, věk a další funkce rozpoznávání obličeje předpovědět počítače. Kromě zjišťování můžete pro rozpoznávání tváře zkontrolujte, jestli dvě tváře na stejnou bitovou kopii nebo jinou Image, jsou stejné pomocí skóre spolehlivosti. Rozpoznávání tváře můžete také porovnat tváří na databázi, pokud podobně vypadajících nebo stejné jako tváře již existuje. Je také můžete uspořádat podobných tváří do skupin pomocí vlastnosti sdílené visual.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů API pro rozpoznávání tváře, musí splňovat následující požadavky.

|Požaduje se|Účel|
|--|--|
|Modul docker| Musí být nainstalovaný modul Docker na [hostitelský počítač](#the-host-computer). Docker nabízí balíčky, které nakonfigurují prostředí Dockeru na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).<br><br> Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure. <br><br> Na Windows Docker také musí být nakonfigurované pro podporu kontejnerů Linuxu.<br><br>|
|Znalost Dockeru | Potřebujete základní znalosti konceptů Dockeru, jako je například registrů, úložiště, kontejnerů a imagí kontejneru. Potřebujete znalost basic `docker` příkazy.| 
|Azure `Cognitive Services` prostředků |Použití kontejneru, musíte mít:<br><br>Prostředek Azure Cognitive Services a přidružený klíč účtování a fakturace identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na **přehled** a **klíče** stránky pro prostředek. Musí se spustit kontejner. Přidat `face/v1.0` směrování na koncový bod identifikátoru URI, jak je znázorněno v následujícím příkladu BILLING_ENDPOINT_URI: <br><br>**{BILLING_KEY}** : klíč prostředku<br><br>**{BILLING_ENDPOINT_URI}** : je například identifikátor URI koncového bodu `https://westus.api.cognitive.microsoft.com/face/v1.0`|

## <a name="request-access-to-the-private-container-registry"></a>Požádat o přístup k registru kontejneru soukromého

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Hostitelském počítači

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

Následující tabulka popisuje minimální a doporučené jader procesoru a paměti k přidělení pro každý kontejner API pro rozpoznávání tváře.

| Kontejner | Minimální | Doporučené | Transakce za sekundu<br>(Minimální, maximální)|
|-----------|---------|-------------|--|
|Tvář | 1 jádro, 2 GB paměti | 1 jádro, 4 GB paměti |10, 20|

* Každé jádro, musí být aspoň 2,6 GHz nebo rychlejší.
* Transakcí za sekundu (TPS).

Jader a paměti odpovídají `--cpus` a `--memory` nastavení, které se používají jako součást `docker run` příkazu.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru s docker o přijetí změn

Image kontejneru pro rozhraní API pro rozpoznávání tváře jsou k dispozici. 

| Kontejner | Úložiště |
|-----------|------------|
| Tvář | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Operace docker pull pro kontejner pro rozpoznávání tváře

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Použití kontejneru

Poté, co je kontejner na [hostitelský počítač](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spuštění kontejneru](#run-the-container-with-docker-run) s požadovanými fakturace nastavení. Další [příklady](./face-resource-container-config.md#example-docker-run-commands) z `docker run` příkazu jsou k dispozici. 
1. [Dotazování koncový bod kontejneru předpovědi](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner pomocí dockeru spustit

Použití [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkaz ke spuštění tři kontejnery. Příkaz používá následující parametry.

| Zástupný symbol | Hodnota |
|-------------|-------|
|{BILLING_KEY} | Tento klíč se používá ke spuštění kontejneru a je k dispozici na Azure `Cognitive Services` **klíče** stránky. |
|{BILLING_ENDPOINT_URI} | Fakturační koncový bod hodnotu identifikátoru URI je k dispozici na Azure `Cognitive Services` **přehled** stránky. Příklad: `https://westus.api.cognitive.microsoft.com/face/v1.0`.|

Přidat `face/v1.0` směrování na koncový bod identifikátoru URI, jak je znázorněno v předchozím příkladu BILLING_ENDPOINT_URI. 

Tyto parametry nahraďte vlastními hodnotami v následujícím `docker run` příklad příkazu:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Tento příkaz:

* Spustí kontejner pro rozpoznávání tváře z image kontejneru.
* Přidělí jedno Procesorové jádro a 4 GB paměti.
* Zpřístupňuje TCP port 5000 a přiděluje pseudo TTY pro kontejner.
* Po ukončení automaticky odstraní kontejner. Image kontejneru je stále k dispozici na hostitelském počítači. 

Další [příklady](./face-resource-container-config.md#example-docker-run-commands) z `docker run` příkazu jsou k dispozici. 

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru nebo kontejneru se nespustí. Další informace najdete v tématu [fakturace](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncový bod kontejneru predikcí

Kontejner poskytuje koncový bod předpovědi dotazů založených na REST API. 

Použít hostitele, `https://localhost:5000`, pro kontejner rozhraní API.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavit kontejner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spouštíte kontejner s výstupem [připojit](./face-resource-container-config.md#mount-settings) a je povoleno protokolování, kontejner vytváří soubory protokolů, které jsou užitečné při řešení potíží, ke kterým dochází při spuštění nebo spuštění kontejneru. 


## <a name="billing"></a>Fakturace

API pro rozpoznávání tváře kontejnery odesílat informace o fakturaci do Azure pomocí rozhraní API pro rozpoznávání tváře prostředků v účtu Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro stažení, instalace a spouštění kontejnerů API pro rozpoznávání tváře. Souhrn:

* Rozhraní API pro rozpoznávání tváře poskytuje tři kontejnery Linuxu pro Docker, které poskytují extrakce klíčových frází, rozpoznávání jazyka a analýzy subjektivního hodnocení.
* Image kontejneru se stahují ze služby Azure Container Registry.
* Spuštění imagí kontejnerů v Dockeru.
* Rozhraní REST API nebo sady SDK můžete použít k volání operací v rozhraní API pro rozpoznávání tváře kontejnery tak, že zadáte identifikátor URI kontejneru hostitele.
* Při vytváření instance kontejneru, je nutné zadat fakturační informace.

> [!IMPORTANT]
> Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníkům musíte povolit kontejnery pro komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Kontejnery služby cognitive Services není zákaznická data, jako je image nebo text, který se právě analyzuje, odeslat společnosti Microsoft.

## <a name="next-steps"></a>Další postup

* Nastavení konfigurace, najdete v části [konfigurace kontejnery](face-resource-container-config.md).
* Další informace o tom, jak detekovat a identifikovat tváří, naleznete v tématu [přehled pro rozpoznávání tváře](Overview.md).
* Informace o metodách podporuje kontejneru najdete v tématu [API pro rozpoznávání tváře](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Použití víc kontejnerů služby Cognitive Services naleznete v části [služeb Cognitive Services kontejnery](../cognitive-services-container-support.md).
