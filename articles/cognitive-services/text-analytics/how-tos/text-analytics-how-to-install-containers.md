---
title: Instalace a spuštění kontejnerů Docker pro rozhraní API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: Kontejnery Docker pro rozhraní API pro analýzu textu slouží k provádění přirozeného jazyka, jako je například analýza mínění, v místním prostředí.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
keywords: místní, Docker, kontejner, analýza mínění, zpracování přirozeného jazyka
ms.openlocfilehash: ac82781ed4a05fbbca7f5b16edb0b5349dfc400a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102432056"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalace a spuštění kontejnerů Analýzy textu

> [!NOTE]
> * Kontejner pro Analýza mínění a rozpoznávání jazyka jsou teď všeobecně dostupné. Kontejner extrakce klíčových frází je k dispozici jako nebraná verze Public Preview.
> * Entity Linking a NER nejsou aktuálně k dispozici jako kontejner.
> * Přístup k Analýza textu pro kontejner stavu vyžaduje [formulář žádosti](https://aka.ms/csgate). V současné době se vám nebude účtovat využití.
> * Nedávno se změnila umístění imagí kontejneru. Přečtěte si tento článek, kde najdete aktualizované umístění pro tento kontejner.

Kontejnery umožňují spouštět rozhraní API Analýzy textu ve vlastním prostředí a jsou skvělé pro splnění určitých požadavků na zabezpečení a zásady správného řízení dat. Kontejnery Analýza textu poskytují pokročilé zpracování přirozeného jazyka v nezpracovaném textu a zahrnují tři hlavní funkce: analýzu mínění, extrakci klíčových frází a detekci jazyka. 

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).

> [!IMPORTANT]
> Bezplatný účet je omezený na 5 000 transakcí za měsíc a pro kontejnery jsou platné jenom  <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">cenové úrovně</a> **Free** a Standard. Další informace o sazbách požadavků na transakce najdete v tématu [omezení pro data](../overview.md#data-limits).

## <a name="prerequisites"></a>Předpoklady

Chcete-li spustit některý z Analýza textu kontejnerů, je nutné mít prostředí hostitelského počítače a kontejneru.

## <a name="preparation"></a>Příprava

Před použitím Analýza textu kontejnerů musíte splnit následující předpoklady:

|Vyžadováno|Účel|
|--|--|
|Docker Engine| Potřebujete modul Docker nainstalovaný na [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které nakonfigurují prostředí Dockeru v systému [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).<br><br> Docker musí být nakonfigurovaný tak, aby umožňoval kontejnerům připojit se a odeslat fakturační data do Azure. <br><br> **V systému Windows** musí být Docker taky nakonfigurovaný tak, aby podporoval kontejnery Linux.<br><br>|
|Znalost pomocí Docker | Měli byste mít základní znalosti konceptů Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů, a taky znalosti základních `docker` příkazů.| 
|Prostředek Analýza textu |Aby bylo možné kontejner používat, musíte mít:<br><br>Prostředek Azure [Analýza textu](../../cognitive-services-apis-create-account.md) s [cenovou úrovní](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)Free (F0) nebo Standard (y). K získání klíče rozhraní API a identifikátoru URI koncového bodu budete potřebovat přejít na stránku **klíč a koncový bod** prostředku v Azure Portal. <br><br>**{API_KEY}**: jeden ze dvou dostupných klíčů prostředků. <br><br>**{ENDPOINT_URI}**: koncový bod pro váš prostředek. |

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavky na kontejner a doporučení

V následující tabulce jsou popsány minimální a doporučené specifikace pro kontejnery Analýza textu. Vyžaduje se minimálně 2 gigabajty (GB) paměti a každý jádro procesoru musí být minimálně 2,6 GHz nebo rychlejší. Jsou uvedeny také povolené transakce na oddíl (TPS).

|  | Minimální specifikace hostitele | Doporučené specifikace hostitele | Minimální TPS | Maximální TPS|
|---|---------|-------------|--|--|
| **Detekce jazyka, extrakce klíčových frází**   | 1 jádro, 2 GB paměti | 1 jádro, 4GB paměti |15 | 30|
| **Analýza mínění**   | 1 jádro, 2 GB paměti | 4 jádra, 8 GB paměti |15 | 30|
| **Analýza textu pro stav-1 dokument/požadavek**   |  4 jádra, 10 GB paměti | 6 jader, 12GB paměť |15 | 30|
| **Analýza textu pro stav – 10 dokumentů/žádostí**   |  6 jader, 16GB paměť | 8 jader, 20 GB paměť |15 | 30|

Jádro procesoru a paměť odpovídají `--cpus` `--memory` nastavení a, která se používají jako součást `docker run` příkazu.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru pomocí `docker pull`

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

Image kontejneru pro Analýza textu jsou k dispozici na Container Registry Microsoft.

# <a name="sentiment-analysis"></a>[Analýza mínění ](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extrakce klíčových frází (Preview)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Rozpoznávání jazyka](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Analýza textu pro stav (Preview)](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

***

## <a name="how-to-use-the-container"></a>Jak používat kontejner

Jakmile je kontejner na [hostitelském počítači](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run)s požadovaným nastavením fakturace.
1. [Dotazování koncového bodu předpovědi kontejneru](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s `docker run`

Pomocí příkazu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) spusťte kontejnery. Kontejner bude nadále běžet, dokud jej nezastavíte.

> [!IMPORTANT]
> * Příkazy Docker v následujících částech používají zpětné lomítko, `\` jako znak pro pokračování řádku. Tuto položku nahraďte nebo odeberte na základě požadavků vašich hostitelských operačních systémů. 
> * `Eula` `Billing` `ApiKey` Aby bylo možné spustit kontejner, musí být zadány možnosti, a. v opačném případě se kontejner nespustí.  Další informace najdete v tématu [fakturace](#billing).
> * Jsou všeobecně dostupné kontejnery pro analýzu mínění a rozpoznávání jazyka. Kontejner extrakce klíčových frází používá v2 rozhraní API a je ve verzi Preview.

# <a name="sentiment-analysis"></a>[Analýza mínění](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extrakce klíčových frází (Preview)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Rozpoznávání jazyka](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Analýza textu pro stav (Preview)](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Zadání dotazu do prediktivního koncového bodu kontejneru

Kontejner poskytuje rozhraní API prediktivního koncového bodu pro dotazy založené na REST.

Pro rozhraní API kontejneru použijte hostitele `http://localhost:5000`.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Poradce při potížích

Pokud spouštíte kontejner s povoleným výstupním [připojením](../text-analytics-resource-container-config.md#mount-settings) a povolíte protokolování, kontejner generuje soubory protokolu, které jsou užitečné při řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturace

Kontejnery Analýza textu odesílají informace o fakturaci do Azure pomocí prostředku _Analýza textu_ na účtu Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnerů](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postupy pro stažení, instalaci a spuštění kontejnerů Analýza textu. Souhrn:

* Analýza textu poskytuje pro Docker tři kontejnery pro Linux, které zapouzdřují různé možnosti:
   * *Analýza mínění*
   * *Extrakce klíčových frází (Preview)* 
   * *Rozpoznávání jazyka*
   * *Analýza textu pro stav (Preview)*
* Image kontejneru se stáhnou ze služby Microsoft Container Registry (MCR) nebo z úložiště kontejnerů ve verzi Preview.
* Image kontejneru se spouštějí v Docker.
* Můžete použít REST API nebo SDK pro volání operací v kontejnerech Analýza textu zadáním identifikátoru URI hostitele kontejneru.
* Při vytváření instance kontejneru je nutné zadat informace o fakturaci.

> [!IMPORTANT]
> Nemusíte spouštět kontejnery Cognitive Services bez připojení k Azure pro měření. Zákazníci musí povolit kontejnerům, aby ve všech časech komunikovaly informace o fakturaci. Kontejnery Cognitive Services neodesílají zákaznická data (např. analyzovaný text) do Microsoftu.

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [konfigurace kontejnerů](../text-analytics-resource-container-config.md) pro nastavení konfigurace
* Přečtěte si [Nejčastější dotazy](../text-analytics-resource-faq.md) k řešení problémů souvisejících s funkcemi.
