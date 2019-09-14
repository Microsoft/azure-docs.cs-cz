---
title: Instalace a spuštění kontejnerů – Analýza textu
titleSuffix: Azure Cognitive Services
description: Postup stažení, instalaci a spouštění kontejnerů v tomto kurzu návod pro analýzu textu.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 3cc61798fb5e5ecba91fef93a743b492dce75211
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966758"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalace a spuštění kontejnerů Analýza textu

Kontejnery umožňují spouštět text analytická rozhraní API ve vašem vlastním prostředí a jsou skvělé pro konkrétní požadavky na zabezpečení a zabezpečení dat. Kontejnery Analýza textu poskytují pokročilé zpracování přirozeného jazyka v nezpracovaném textu a zahrnují tři hlavní funkce: analýzu mínění, extrakci klíčových frází a detekci jazyka. Odkaz na entitu se v kontejneru aktuálně nepodporuje.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li spustit některý z Analýza textu kontejnerů, je nutné mít prostředí hostitelského počítače a kontejneru.

## <a name="preparation"></a>Příprava

Před použitím kontejnerů pro analýzu textu, musí splňovat následující požadavky:

|Požadováno|Účel|
|--|--|
|Modul Docker| Potřebujete modul Docker nainstalovaný na [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které konfigurují prostředí Docker v systémech [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).<br><br> Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure. <br><br> **V systému Windows**musí být Docker taky nakonfigurovaný tak, aby podporoval kontejnery Linux.<br><br>|
|Znalost pomocí Docker | Měli byste mít základní znalosti konceptů Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů, a taky znalosti základních `docker` příkazů.| 
|Prostředek Analýza textu |Aby bylo možné kontejner používat, musíte mít:<br><br>Prostředek služby Azure [Analýza textu](../../cognitive-services-apis-create-account.md) , který získá přidružený klíč rozhraní API a identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách Analýza textu přehledu a klíčů Azure Portal a jsou požadovány ke spuštění kontejneru.<br><br>**{API_KEY}** : Jeden ze dvou dostupných klíčů prostředků na stránce **klíče**<br><br>**{ENDPOINT_URI}** : Koncový bod, jak je uvedený na stránce **Přehled**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

Následující tabulka popisuje minimální a doporučené Procesorových jader a aspoň 2.6 gigahertz (GHz) nebo rychlejší a paměti v gigabajtech (GB), přidělit pro každý kontejner pro analýzu textu.

# <a name="key-phrase-extractiontabkeyphrase"></a>[Extrakce klíčových frází](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-requirements](../includes/key-phrase-extraction-container-requirements.md)]

# <a name="language-detectiontablanguage"></a>[Rozpoznávání jazyka](#tab/language)

[!INCLUDE [language-detection-container-requirements](../includes/language-detection-container-requirements.md)]

# <a name="sentiment-analysistabsentiment"></a>[Analýza subjektivního hodnocení](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-requirements](../includes/sentiment-analysis-container-requirements.md)]

***

* Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.
* TPS-transakcí za sekundu

Základní a paměť odpovídají `--cpus` nastavení a `--memory` , která se `docker run` používají jako součást příkazu.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru pomocí`docker pull`

Image kontejneru pro Analýza textu jsou k dispozici na Container Registry Microsoft.

# <a name="key-phrase-extractiontabkeyphrase"></a>[Extrakce klíčových frází](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-repository](../includes/key-phrase-extraction-container-repository.md)]

# <a name="language-detectiontablanguage"></a>[Rozpoznávání jazyka](#tab/language)

[!INCLUDE [language-detection-container-repository](../includes/language-detection-container-repository.md)]

# <a name="sentiment-analysistabsentiment"></a>[Analýza subjektivního hodnocení](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-repository](../includes/sentiment-analysis-container-repository.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-text-analytics-containers"></a>Vyžádané čtení Docker pro kontejnery Analýza textu

# <a name="key-phrase-extractiontabkeyphrase"></a>[Extrakce klíčových frází](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detectiontablanguage"></a>[Rozpoznávání jazyka](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="sentiment-analysistabsentiment"></a>[Analýza subjektivního hodnocení](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

***

## <a name="how-to-use-the-container"></a>Jak používat kontejner

Jakmile je kontejner na hostitelském [počítači](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run)s požadovaným nastavením fakturace. K [](../text-analytics-resource-container-config.md#example-docker-run-commands) dispozici jsou `docker run` další příklady příkazu.
1. [Dotazování koncového bodu předpovědi kontejneru](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s`docker run`

Pomocí příkazu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) spusťte kterýkoli ze tří kontejnerů. Podrobnosti o tom, jak získat `{Endpoint_URI}` hodnoty a `{API_Key}` , najdete v článku [shromáždění požadovaných parametrů](#gathering-required-parameters) .

K dispozici jsou [Příklady](../text-analytics-resource-container-config.md#example-docker-run-commands) příkazů.`docker run`

# <a name="key-phrase-extractiontabkeyphrase"></a>[Extrakce klíčových frází](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detectiontablanguage"></a>[Rozpoznávání jazyka](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="sentiment-analysistabsentiment"></a>[Analýza subjektivního hodnocení](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

***

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncového bodu předpovědi kontejneru

Kontejner poskytuje rozhraní API koncového bodu předpovědi založené na REST.

Pro rozhraní API kontejneru `http://localhost:5000`použijte hostitele.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spouštíte kontejner s povoleným výstupním [připojením](../text-analytics-resource-container-config.md#mount-settings) a povolíte protokolování, kontejner generuje soubory protokolu, které jsou užitečné při řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru.

## <a name="billing"></a>Fakturace

Kontejnery Analýza textu odesílají informace o fakturaci do Azure pomocí prostředku _Analýza textu_ na účtu Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro stažení, instalaci a používání kontejnerů pro analýzu textu. Souhrn:

* Analýza textu poskytuje pro Docker tři kontejnery pro Linux, které zapouzdřují různé možnosti:
   * *Extrakce klíčových frází*
   * *Rozpoznávání jazyka*
   * *Analýza subjektivního hodnocení*
* Image kontejneru se stáhnou z kontejneru registru Microsoft (MCR) v Azure.
* Spuštění imagí kontejnerů v Dockeru.
* Rozhraní REST API nebo sady SDK můžete použít k volání operací v kontejnerech rozhraní Text Analytics tak, že zadáte identifikátor URI kontejneru hostitele.
* Při vytváření instance kontejneru, je nutné zadat fakturační informace.

> [!IMPORTANT]
> Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Cognitive Services kontejnery Neodesílat data zákazníků (třeba image nebo text, který je analyzován) společnosti Microsoft.

## <a name="next-steps"></a>Další postup

* Kontrola [konfigurace kontejnery](../text-analytics-resource-container-config.md) nastavení konfigurace
* Přečtěte si [Nejčastější dotazy](../text-analytics-resource-faq.md) k řešení problémů souvisejících s funkcemi.
