---
title: Instalace a spouštění kontejnerů
titlesuffix: Face - Azure Cognitive Services
description: Jak si stáhnout, nainstalovat a spouštění kontejnerů v tomto kurzu návod pro rozpoznávání tváře.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: article
ms.date: 02/06/2019
ms.author: diberry
ms.openlocfilehash: 93fbcb96415aa2c800a772fdeb925b1513d7512d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863002"
---
# <a name="install-and-run-containers"></a>Instalace a spuštění kontejnerů

Rozpoznávání tváře poskytuje standardizované kontejneru Linuxu pro Docker, s názvem pro rozpoznávání tváře, detekuje lidské tváře v obrázcích, který určuje atributy, včetně orientačních bodů pro rozpoznávání tváře (například ústa a oči), pohlaví, věk a další funkce rozpoznávání obličeje předpovědět počítače. Kromě zjišťování můžete pro rozpoznávání tváře zkontrolujte, jestli dvě tváře na stejnou bitovou kopii nebo jinou Image jsou stejné s použitím skóre spolehlivosti nebo porovnání proti databázi a zjistěte, jestli podobně vypadajících tváří, nebo identické pro rozpoznávání tváře již existuje. Můžete také uspořádat podobných tváří do skupin pomocí sdílené visual vlastností.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů API pro rozpoznávání tváře, musí splňovat následující požadavky:

|Požaduje se|Účel|
|--|--|
|Modul docker| Je nutné modul Docker nainstalovaný na [hostitelský počítač](#the-host-computer). Docker nabízí balíčky, které nakonfigurují prostředí Dockeru na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).<br><br> Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure. <br><br> **Na Windows**, Docker musí být taky nakonfigurovaný pro podporu kontejnerů Linuxu.<br><br>|
|Znalost Dockeru | Byste měli mít základní znalost konceptů Dockeru, jako je registrů, úložiště, kontejnery a Image kontejneru, jakož i znalost basic `docker` příkazy.| 
|Rozhraní API pro rozpoznávání tváře prostředků |Chcete-li použít kontejner, musíte mít:<br><br>A _API pro rozpoznávání tváře_ prostředků Azure můžete získat přidružený klíč účtování a fakturace identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách portálu Azure přehled rozhraní API pro rozpoznávání tváře a klíče a jsou vyžadovány pro spuštění kontejneru.<br><br>**{BILLING_KEY}** : klíč prostředku<br><br>**{BILLING_ENDPOINT_URI}** : Příklad identifikátor URI koncového bodu je: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|


## <a name="request-access-to-the-private-container-registry"></a>Požádat o přístup k registru kontejneru soukromého

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Hostitelském počítači

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

Následující tabulka popisuje minimální a doporučené jader procesoru a paměti k přidělení pro každý kontejner API pro rozpoznávání tváře.

| Kontejner | Minimální | Doporučené |
|-----------|---------|-------------|
|Tvář | 1 jádro, 2 GB paměti | 1 jádro, 4 GB paměti |

Každé jádro, musí být aspoň 2.6 gigahertz (GHz) nebo rychlejší.

Jader a paměti odpovídají `--cpus` a `--memory` nastavení, které se používají jako součást `docker run` příkazu.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru s `docker pull`

Image kontejneru pro rozhraní API pro rozpoznávání tváře jsou k dispozici. 

| Kontejner | Úložiště |
|-----------|------------|
| Tvář | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Operace docker pull pro kontejner pro rozpoznávání tváře

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/face:latest
```

## <a name="how-to-use-the-container"></a>Jak používat kontejneru

Jakmile bude kontejner ve [hostitelský počítač](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spuštění kontejneru](#run-the-container-with-docker-run), s požadovanými fakturace nastavení. Další [příklady](./face-resource-container-config.md#example-docker-run-commands) z `docker run` příkazu jsou k dispozici. 
1. [Dotazování koncový bod kontejneru předpovědi](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s `docker run`

Použití [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkaz ke spuštění tři kontejnery. Příkaz používá následující parametry:

| Zástupný symbol | Hodnota |
|-------------|-------|
|{BILLING_KEY} | Tento klíč se používá ke spuštění kontejneru a je k dispozici na stránce klíče rozhraní API pro rozpoznávání tváře webu Azure portal.  |
|{BILLING_ENDPOINT_URI} | Fakturační koncový bod hodnotu identifikátoru URI je k dispozici na stránce Přehled rozhraní API pro rozpoznávání tváře webu Azure portal.|

Tyto parametry nahraďte vlastními hodnotami v následujícím příkladu `docker run` příkazu.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Tento příkaz:

* Spustí kontejner pro rozpoznávání tváře z image kontejneru
* Přidělí jedno Procesorové jádro a 4 gigabajty (GB) paměti
* Zpřístupňuje TCP port 5000 a přiděluje pseudo-TTY pro kontejner
* Po ukončení automaticky odstraní kontejner. Image kontejneru je stále k dispozici na hostitelském počítači. 

Další [příklady](./face-resource-container-config.md#example-docker-run-commands) z `docker run` příkazu jsou k dispozici. 

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncový bod kontejneru predikcí

Kontejner poskytuje koncový bod předpovědi dotazů založených na REST API. 

Použít hostitele, https://localhost:5000, pro kontejner rozhraní API.

## <a name="stop-the-container"></a>Zastavit kontejner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spouštíte kontejner s výstupem [připojit](./face-resource-container-config.md#mount-settings) a povolení protokolování kontejneru vygeneruje soubory protokolů, které jsou užitečné při řešení potíží, ke kterým dochází při spuštění nebo spuštění kontejneru. 

## <a name="containers-api-documentation"></a>Dokumentace k rozhraní API kontejneru

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>Fakturace

Odeslat kontejnery API pro rozpoznávání tváře fakturační údaje do Azure, pomocí _API pro rozpoznávání tváře_ prostředků v účtu Azure. 

Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Cognitive Services kontejnery Neodesílat data zákazníků společnosti Microsoft. 

`docker run` Příkaz používá následující argumenty pro účely fakturace:

| Možnost | Popis |
|--------|-------------|
| `ApiKey` | Klíč rozhraní API _API pro rozpoznávání tváře_ prostředek, který používá ke sledování fakturační údaje. |
| `Billing` | Koncový bod _API pro rozpoznávání tváře_ prostředek, který používá ke sledování fakturační údaje.|
| `Eula` | Označuje, že jste přijali licenci pro kontejner.<br/>Hodnota této možnosti musí být nastavená na `accept`. |

> [!IMPORTANT]
> Všechny tři možnosti je nutné zadat platnou hodnotou nebo kontejneru se nespustí.

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](./face-resource-container-config.md).

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro stažení, instalaci a používání rozhraní API pro rozpoznávání tváře kontejnery. Souhrn:

* Rozhraní API pro rozpoznávání tváře nabízí tři kontejnery Linuxu pro Docker, zapouzdření extrakce klíčových frází, rozpoznávání jazyka a analýzy subjektivního hodnocení.
* Image kontejneru se stáhnou z kontejneru registru Microsoft (MCR) v Azure.
* Spuštění imagí kontejnerů v Dockeru.
* Rozhraní REST API nebo sady SDK můžete použít k volání operací v rozhraní API pro rozpoznávání tváře kontejnery tak, že zadáte identifikátor URI kontejneru hostitele.
* Při vytváření instance kontejneru, je nutné zadat fakturační informace.

> [!IMPORTANT]
> Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Cognitive Services kontejnery Neodesílat data zákazníků (třeba image nebo text, který je analyzován) společnosti Microsoft.

## <a name="next-steps"></a>Další postup

* Kontrola [konfigurace kontejnery](face-resource-container-config.md) nastavení konfigurace
* Kontrola [přehled pro rozpoznávání tváře](Overview.md) získat další informace o zjištění a identifikaci tváře  
* Odkazovat [API pro rozpoznávání tváře](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) podrobné informace o metodách podporuje kontejneru.
* Odkazovat na [– nejčastější dotazy (FAQ)](FAQ.md) k vyřešení problémů týkajících se funkce rozpoznávání tváře.
* Použití více [kontejnery Cognitive Services](../cognitive-services-container-support.md)
