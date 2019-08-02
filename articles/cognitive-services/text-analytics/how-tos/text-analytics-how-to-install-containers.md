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
ms.date: 07/30/2019
ms.author: dapine
ms.openlocfilehash: f658e8d0f820ccec513b5665fc1ce94c083c3b3e
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703527"
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

### <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

Následující tabulka popisuje minimální a doporučené Procesorových jader a aspoň 2.6 gigahertz (GHz) nebo rychlejší a paměti v gigabajtech (GB), přidělit pro každý kontejner pro analýzu textu.

| Kontejner | Minimální | Doporučené | TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|Extrakce klíčových frází | 1 jádro, 2 GB paměti | 1 jádro, 4 GB paměti |15, 30|
|Rozpoznávání jazyka | 1 jádro, 2 GB paměti | 1 jádro, 4 GB paměti |15, 30|
|Analýza mínění 2. x | 1 jádro, 2 GB paměti | 1 jádro, 4 GB paměti |15, 30|
|Analýza mínění 3. x | 1 jádro, 2 GB paměti | 4 jádra, 4 GB paměti |15, 30|

* Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.
* TPS-transakcí za sekundu

Základní a paměť odpovídají `--cpus` nastavení a `--memory` , která se `docker run` používají jako součást příkazu.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru pomocí`docker pull`

Ze služby Microsoft Container Registry jsou dostupné Image kontejneru pro analýzu textu.

| Kontejner | Úložiště |
|-----------|------------|
|Extrakce klíčových frází | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Rozpoznávání jazyka | `mcr.microsoft.com/azure-cognitive-services/language` |
|Analýza mínění 2. x| `mcr.microsoft.com/azure-cognitive-services/sentiment` |
|Analýza mínění 3. x| `containerpreview.azurecr.io/microsoft/cognitive-services-sentiment-v3.0` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Pomocí příkazu si stáhněte image kontejneru z Microsoft Container Registry.

Úplný popis dostupných značek pro kontejnery pro analýzu textu naleznete v následujících kontejnerech na úložiště Docker Hub:

* [Extrakce klíčových frází](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Rozpoznávání jazyka](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Analýza subjektivního hodnocení](https://go.microsoft.com/fwlink/?linkid=2018654)

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Pomocí příkazu Stáhněte image kontejneru.

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Stažení Docker pro kontejner extrakce klíčových frází

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>Vyžádané čtení Docker pro kontejner rozpoznávání jazyka

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-2x-container"></a>Stažení Docker pro kontejner mínění 2. x

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

### <a name="docker-pull-for-the-sentiment-3x-container"></a>Získání Docker pro kontejner mínění 3. x

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-sentiment-v3.0:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak používat kontejner

Jakmile je kontejner na hostitelském [počítači](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run)s požadovaným nastavením fakturace. K [](../text-analytics-resource-container-config.md#example-docker-run-commands) dispozici jsou `docker run` další příklady příkazu.
1. Dotazování koncového bodu předpovědi kontejneru pro [v2](#query-the-containers-v2-prediction-endpoint) nebo [V3](#query-the-containers-v3-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s`docker run`

Pomocí příkazu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) spusťte kterýkoli ze tří kontejnerů. Podrobnosti o tom, jak získat `{Endpoint_URI}` hodnoty a `{API_Key}` , najdete v článku [shromáždění požadovaných parametrů](#gathering-required-parameters) .

K dispozici jsou [Příklady](../text-analytics-resource-container-config.md#example-docker-run-commands) příkazů.`docker run`

### <a name="run-v2-container-example-of-docker-run-command"></a>Příklad spuštění příkazu Docker Run v kontejneru v2

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí kontejner klíčové fráze z image kontejneru.
* Přiděluje jedno PROCESORové jádro a 4 gigabajty (GB) paměti.
* Zpřístupňuje TCP port 5000 a přiděluje pseudo-TTY pro kontejner
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.

### <a name="run-v3-container-example-of-docker-run-command"></a>Příklad spuštění příkazu Docker Run v kontejneru V3

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-sentiment-v3.0 \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Tento příkaz:

* Spustí kontejner klíčové fráze z image kontejneru.
* Přiděluje 4 jádra procesoru a 4 gigabajty (GB) paměti.
* Zpřístupňuje TCP port 5000 a přiděluje pseudo-TTY pro kontejner
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-v2-prediction-endpoint"></a>Dotazování koncového bodu předpovědi v2 kontejneru

Kontejner poskytuje rozhraní API koncového bodu předpovědi založené na REST.

Pro rozhraní API kontejneru `https://localhost:5000`použijte hostitele.

## <a name="query-the-containers-v3-prediction-endpoint"></a>Dotazování koncového bodu prediktivního kontejneru V3

Kontejner poskytuje rozhraní API koncového bodu předpovědi založené na REST.

Pro rozhraní API kontejneru `https://localhost:5000`použijte hostitele.

### <a name="v3-api-request-post-body"></a>Text příspěvku žádosti rozhraní API V3

Následující JSON je příkladem těla příspěvku žádosti rozhraní API V3:

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Hello world. This is some input text that I love."
    },
    {
      "language": "en",
      "id": "2",
      "text": "It's incredibly sunny outside! I'm so happy."
    }
  ]
}
```

### <a name="v3-api-response-body"></a>V3 – tělo odpovědi rozhraní API

Následující JSON je příkladem těla příspěvku žádosti rozhraní API V3:

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.98570585250854492,
                "neutral": 0.0001625834556762,
                "negative": 0.0141316400840878
            },
            "sentences": [
                {
                    "sentiment": "neutral",
                    "sentenceScores": {
                        "positive": 0.0785155147314072,
                        "neutral": 0.89702343940734863,
                        "negative": 0.0244610067456961
                    },
                    "offset": 0,
                    "length": 12
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.98570585250854492,
                        "neutral": 0.0001625834556762,
                        "negative": 0.0141316400840878
                    },
                    "offset": 13,
                    "length": 36
                }
            ]
        },
        {
            "id": "2",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.89198976755142212,
                "neutral": 0.103382371366024,
                "negative": 0.0046278294175863
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.78401315212249756,
                        "neutral": 0.2067587077617645,
                        "negative": 0.0092281140387058
                    },
                    "offset": 0,
                    "length": 30
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.99996638298034668,
                        "neutral": 0.0000060341349126,
                        "negative": 0.0000275444017461
                    },
                    "offset": 31,
                    "length": 13
                }
            ]
        }
    ],
    "errors": []
}
```

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

* Rozhraní text Analytics poskytuje tři kontejnery Linuxu pro Docker, zapouzdření extrakce klíčových frází, rozpoznávání jazyka a analýzy subjektivního hodnocení.
* Image kontejneru se stáhnou z kontejneru registru Microsoft (MCR) v Azure.
* Spuštění imagí kontejnerů v Dockeru.
* Rozhraní REST API nebo sady SDK můžete použít k volání operací v kontejnerech rozhraní Text Analytics tak, že zadáte identifikátor URI kontejneru hostitele.
* Při vytváření instance kontejneru, je nutné zadat fakturační informace.

> [!IMPORTANT]
> Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Cognitive Services kontejnery Neodesílat data zákazníků (třeba image nebo text, který je analyzován) společnosti Microsoft.

## <a name="next-steps"></a>Další postup

* Kontrola [konfigurace kontejnery](../text-analytics-resource-container-config.md) nastavení konfigurace
* Přečtěte si [Nejčastější dotazy](../text-analytics-resource-faq.md) k řešení problémů souvisejících s funkcemi.
