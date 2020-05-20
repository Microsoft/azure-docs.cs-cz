---
title: Instalace a spuštění kontejnerů – Analýza textu
titleSuffix: Azure Cognitive Services
description: Postup stažení, instalace a spuštění kontejnerů pro Analýza textu v tomto výukovém kurzu.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: efe76323b4159af01f1eaf470d9c1833edd0a186
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83702133"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalace a spuštění kontejnerů Analýzy textu

> [!NOTE]
> * Kontejner pro Analýza mínění v3 je teď všeobecně dostupný. Extrakce klíčových frází a kontejnery pro detekci jazyka jsou k dispozici jako nebraná verze Public Preview.
> * Entity Linking a NER nejsou aktuálně k dispozici jako kontejner.

Kontejnery umožňují spouštět text analytická rozhraní API ve vašem vlastním prostředí a jsou skvělé pro konkrétní požadavky na zabezpečení a zabezpečení dat. Kontejnery Analýza textu poskytují pokročilé zpracování přirozeného jazyka v nezpracovaném textu a zahrnují tři hlavní funkce: analýzu mínění, extrakci klíčových frází a detekci jazyka. 

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

> [!IMPORTANT]
> Bezplatný účet je omezený na 5 000 transakcí za měsíc a pro kontejnery jsou platné jenom **Standard** <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">cenové úrovně <span class="docon docon-navigate-external x-hidden-focus"></span> </a> **Free** a Standard. Další informace o sazbách požadavků na transakce najdete v tématu [omezení pro data](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits).

## <a name="prerequisites"></a>Požadavky

Chcete-li spustit některý z Analýza textu kontejnerů, je nutné mít prostředí hostitelského počítače a kontejneru.

## <a name="preparation"></a>Příprava

Před použitím Analýza textu kontejnerů musíte splnit následující předpoklady:

|Vyžadováno|Účel|
|--|--|
|Docker Engine| Potřebujete modul Docker nainstalovaný na [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které nakonfigurují prostředí Dockeru v systému [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).<br><br> Docker musí být nakonfigurovaný tak, aby umožňoval kontejnerům připojit se a odeslat fakturační data do Azure. <br><br> **V systému Windows**musí být Docker taky nakonfigurovaný tak, aby podporoval kontejnery Linux.<br><br>|
|Znalost pomocí Docker | Měli byste mít základní znalosti konceptů Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů, a taky znalosti základních `docker` příkazů.| 
|Prostředek Analýza textu |Aby bylo možné kontejner používat, musíte mít:<br><br>Prostředek služby Azure [Analýza textu](../../cognitive-services-apis-create-account.md) , který získá přidružený klíč rozhraní API a identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách Analýza textu přehledu a klíčů Azure Portal a jsou požadovány ke spuštění kontejneru.<br><br>**{API_KEY}**: jeden ze dvou dostupných klíčů prostředků na stránce **klíče**<br><br>**{ENDPOINT_URI}**: koncový bod uvedený na stránce **Přehled**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavky na kontejner a doporučení

V následující tabulce jsou popsány minimální a doporučené specifikace pro kontejnery Analýza textu. Vyžaduje se minimálně 2 gigabajty (GB) paměti a každý jádro procesoru musí být minimálně 2,6 GHz nebo rychlejší. Jsou uvedeny také povolené transakce na oddíl (TPS).

|  | Minimální specifikace hostitele | Doporučené specifikace hostitele | Minimální TPS | Maximální TPS|
|---|---------|-------------|--|--|
| **Detekce jazyka, extrakce klíčových frází**   | 1 jádro, 2 GB paměti | 1 jádro, 4GB paměti |15 | 30|
| **Analýza mínění V3**   | 1 jádro, 2 GB paměti | 4 jádra, 8 GB paměti |15 | 30|

Jádro procesoru a paměť odpovídají `--cpus` `--memory` nastavení a, která se používají jako součást `docker run` příkazu.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru pomocí`docker pull`

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

Image kontejneru pro Analýza textu jsou k dispozici na Container Registry Microsoft.

# <a name="sentiment-analysis-v3"></a>[Analýza mínění V3](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extrakce klíčových frází (Preview)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection-preview"></a>[Rozpoznávání jazyka (Preview)](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

***

## <a name="how-to-use-the-container"></a>Jak používat kontejner

Jakmile je kontejner na [hostitelském počítači](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run)s požadovaným nastavením fakturace.
1. [Dotazování koncového bodu předpovědi kontejneru](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s`docker run`

Pomocí příkazu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) spusťte kontejnery. Kontejner bude nadále běžet, dokud jej nezastavíte.

Nahraďte níže uvedené zástupné symboly vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
| **{API_KEY}** | Klíč pro prostředek Analýza textu. Můžete ji najít na stránce **klíč a koncový bod** prostředku na Azure Portal. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Koncový bod pro přístup k rozhraní API pro analýzu textu. Můžete ji najít na stránce **klíč a koncový bod** prostředku na Azure Portal. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

> [!IMPORTANT]
> * Příkazy Docker v následujících částech používají zpětné lomítko, `\` jako znak pro pokračování řádku. Tuto položku nahraďte nebo odeberte na základě požadavků vašich hostitelských operačních systémů. 
> * `Eula` `Billing` `ApiKey` Aby bylo možné spustit kontejner, musí být zadány možnosti, a. v opačném případě se kontejner nespustí.  Další informace najdete v tématu [fakturace](#billing).
> * Kontejner mínění Analysis v3 je teď všeobecně dostupný, což vrátí [popisky mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) v odpovědi. Extrakce klíčových frází a kontejnery pro detekci jazyka používají v2 rozhraní API a jsou ve verzi Preview.

# <a name="sentiment-analysis-v3"></a>[Analýza mínění V3](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extrakce klíčových frází (Preview)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection-preview"></a>[Rozpoznávání jazyka (Preview)](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncového bodu předpovědi kontejneru

Kontejner poskytuje rozhraní API koncového bodu předpovědi založené na REST.

`http://localhost:5000`Pro rozhraní API kontejneru použijte hostitele.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spouštíte kontejner s povoleným výstupním [připojením](../text-analytics-resource-container-config.md#mount-settings) a povolíte protokolování, kontejner generuje soubory protokolu, které jsou užitečné při řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturace

Kontejnery Analýza textu odesílají informace o fakturaci do Azure pomocí prostředku _Analýza textu_ na účtu Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnerů](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postupy pro stažení, instalaci a spuštění kontejnerů Analýza textu. Souhrn:

* Analýza textu poskytuje pro Docker tři kontejnery pro Linux, které zapouzdřují různé možnosti:
   * *Analýza mínění*
   * *Extrakce klíčových frází (Preview)* 
   * *Rozpoznávání jazyka (Preview)*
   
* Image kontejneru se stáhnou z Microsoft Container Registry (MCR) v Azure.
* Image kontejneru se spouštějí v Docker.
* Můžete použít REST API nebo SDK pro volání operací v kontejnerech Analýza textu zadáním identifikátoru URI hostitele kontejneru.
* Při vytváření instance kontejneru je nutné zadat informace o fakturaci.

> [!IMPORTANT]
> Nemusíte spouštět kontejnery Cognitive Services bez připojení k Azure pro měření. Zákazníci musí povolit kontejnerům, aby ve všech časech komunikovaly informace o fakturaci. Kontejnery Cognitive Services neodesílají zákaznická data (např. analyzovaný text) do Microsoftu.

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [konfigurace kontejnerů](../text-analytics-resource-container-config.md) pro nastavení konfigurace
* Přečtěte si [Nejčastější dotazy](../text-analytics-resource-faq.md) k řešení problémů souvisejících s funkcemi.
