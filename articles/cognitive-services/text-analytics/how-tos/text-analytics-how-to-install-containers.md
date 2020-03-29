---
title: Instalace a spuštění kontejnerů – Analýza textu
titleSuffix: Azure Cognitive Services
description: Jak stáhnout, nainstalovat a spustit kontejnery pro analýzu textu v tomto kurzu návodu.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 65033f9b6599d690b1097b4b78aa01148a40fc39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037508"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalace a spuštění kontejnerů Analýzy textu

Kontejnery umožňují spouštět api pro analýzu textu ve vlastním prostředí a jsou skvělé pro vaše konkrétní požadavky na zabezpečení a zásady správného řízení dat. Kontejnery Text Analytics poskytují pokročilé zpracování přirozeného jazyka přes nezpracovaný text a obsahují tři hlavní funkce: analýzu mínění, extrakci klíčových frází a detekci jazyka. Propojení entit není aktuálně podporováno v kontejneru.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

> [!IMPORTANT]
> Bezplatný účet je omezen na 5 000 transakcí měsíčně a pro <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">kontejnery <span class="docon docon-navigate-external x-hidden-focus"></span> </a> platí pouze cenové úrovně **Free** a **Standard.** Další informace o sazbách žádostí o transakce naleznete v [tématu Data Limits](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits).

## <a name="prerequisites"></a>Požadavky

Chcete-li spustit některý z kontejnerů analýzy textu, musíte mít hostitelský počítač a prostředí kontejneru.

## <a name="preparation"></a>Příprava

Před použitím kontejnerů analýzy textu musíte splnit následující požadavky:

|Požaduje se|Účel|
|--|--|
|Docker Engine| Potřebujete modul Docker Engine nainstalovaný v [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které nakonfigurují prostředí Dockeru v systému [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).<br><br> Docker musí být nakonfigurovaný tak, aby umožňoval kontejnerům připojení k fakturačním datům a odesílání fakturačních dat do Azure. <br><br> **V systému Windows**musí být Docker také nakonfigurován pro podporu kontejnerů Linuxu.<br><br>|
|Znalost Dockeru | Měli byste mít základní znalosti konceptů Dockeru, jako jsou registry, úložiště, kontejnery `docker` a image kontejnerů, stejně jako znalost základních příkazů.| 
|Zdroj analýzy textu |Chcete-li kontejner používat, musíte mít:<br><br>Prostředek Azure [Text Analytics](../../cognitive-services-apis-create-account.md) k získání přidruženého klíče rozhraní API a identifikátoru URI koncového bodu. Obě hodnoty jsou k dispozici na stránce Přehled analýzy textu a Klíče na webu Azure Portal a jsou nutné ke spuštění kontejneru.<br><br>**{API_KEY}:** Jeden ze dvou dostupných klíčů prostředků na stránce **Klíče**<br><br>**{ENDPOINT_URI}:** Koncový bod uvedený na stránce **Přehled**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavky na kontejnery a doporučení

Následující tabulka popisuje minimální a doporučená jádra procesoru, alespoň 2,6 gigahertzů (GHz) nebo rychlejších, a paměť v gigabajtech (GB), která se přidělují pro každý kontejner Analýzy textu.

# <a name="key-phrase-extraction"></a>[Extrakce klíčových frází](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-requirements](../includes/key-phrase-extraction-container-requirements.md)]

# <a name="language-detection"></a>[Rozpoznávání jazyka](#tab/language)

[!INCLUDE [language-detection-container-requirements](../includes/language-detection-container-requirements.md)]

# <a name="sentiment-analysis"></a>[Analýza mínění](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-requirements](../includes/sentiment-analysis-container-requirements.md)]

***

* Každé jádro musí být nejméně 2,6 gigahertzů (GHz) nebo rychlejší.
* TPS - transakce za sekundu

Jádro a paměť `--cpus` odpovídají `--memory` nastavení a, které se `docker run` používají jako součást příkazu.

## <a name="get-the-container-image-with-docker-pull"></a>Získejte obrázek kontejneru pomocí`docker pull`

Image kontejnerů pro analýzu textu jsou k dispozici v registru kontejnerů společnosti Microsoft.

# <a name="key-phrase-extraction"></a>[Extrakce klíčových frází](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-repository](../includes/key-phrase-extraction-container-repository.md)]

# <a name="language-detection"></a>[Rozpoznávání jazyka](#tab/language)

[!INCLUDE [language-detection-container-repository](../includes/language-detection-container-repository.md)]

# <a name="sentiment-analysis"></a>[Analýza mínění](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-repository](../includes/sentiment-analysis-container-repository.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-text-analytics-containers"></a>Docker vytáhnout pro kontejnery analýzy textu

# <a name="key-phrase-extraction"></a>[Extrakce klíčových frází](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Rozpoznávání jazyka](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Analýza mínění](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

***

## <a name="how-to-use-the-container"></a>Jak kontejner používat

Jakmile je kontejner v [hostitelském počítači](#the-host-computer), použijte následující proces pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run)s požadovaným nastavením fakturace. Další [příklady](../text-analytics-resource-container-config.md#example-docker-run-commands) `docker run` příkazu jsou k dispozici.
1. [Dotaz na koncový bod předpověď kontejneru](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Spusťte nádobu s`docker run`

Pomocí příkazu [docker run](https://docs.docker.com/engine/reference/commandline/run/) spusťte některý ze tří kontejnerů. Podrobnosti o tom, jak získat hodnoty `{ENDPOINT_URI}` `{API_KEY}` a, naleznete v části [Shromažďování požadovaných parametrů.](#gathering-required-parameters)

[Příklady](../text-analytics-resource-container-config.md#example-docker-run-commands) příkazu `docker run` jsou k dispozici.

# <a name="key-phrase-extraction"></a>[Extrakce klíčových frází](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Rozpoznávání jazyka](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Analýza mínění](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

***

> [!IMPORTANT]
> `Eula`, `Billing`a `ApiKey` možnosti musí být zadány ke spuštění kontejneru; v opačném případě se kontejner nespustí.  Další informace naleznete v [tématu Fakturace](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Dotaz na koncový bod předpovědi kontejneru

Kontejner poskytuje úložiště dat na základě dotazu předpověď koncový bod API.

Použijte hostitele `http://localhost:5000`, pro kontejnerová řešení API.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spustíte kontejner s povoleným výstupním [připojením](../text-analytics-resource-container-config.md#mount-settings) a protokolováním, kontejner generuje soubory protokolu, které jsou užitečné k řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturace

Kontejnery Text Analytics odesílají fakturační údaje do Azure pomocí prostředku _Textové analýzy_ ve vašem účtu Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech naleznete v [tématu Konfigurace kontejnerů](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste se naučili koncepty a pracovní postupy pro stahování, instalaci a spouštění kontejnerů Analýzy textu. Souhrn:

* Analýza textu poskytuje tři linuxové kontejnery pro Docker, které zapouzdřují různé funkce:
   * *Extrakce klíčových frází*
   * *Rozpoznávání jazyka*
   * *Analýza mínění*
* Image kontejnerů se stahují z registru Microsoft Container Registry (MCR) v Azure.
* Image kontejnerů běží v Dockeru.
* Pomocí rozhraní REST API nebo sady SDK můžete volat operace v kontejnerech analýzy textu zadáním identifikátoru URI hostitele kontejneru.
* Při vytváření instancí kontejneru je nutné zadat fakturační údaje.

> [!IMPORTANT]
> Kontejnery služeb Cognitive Services nejsou licencovány ke spuštění bez připojení k Azure pro měření. Zákazníci musí povolit kontejnery komunikovat fakturační údaje se službou měření za všech okolností. Kontejnery služeb Cognitive Services neodesílají společnosti Microsoft zákaznická data (např. obrázek nebo text, který se analyzuje).

## <a name="next-steps"></a>Další kroky

* Kontrola [Konfigurace kontejnerů](../text-analytics-resource-container-config.md) pro nastavení konfigurace
* Problémy související s funkčností naleznete v [tématu Nejčastější](../text-analytics-resource-faq.md) dotazy.
