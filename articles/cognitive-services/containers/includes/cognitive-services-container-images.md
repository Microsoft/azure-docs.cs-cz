---
title: Úložiště kontejnerů a obrázky
services: cognitive-services
author: aahill
manager: nitinme
description: Dvě tabulky reprezentující Registry kontejnerů, úložiště a názvy imagí pro všechny nabídky služby pro rozpoznávání.
ms.service: cognitive-services
ms.topic: include
ms.date: 09/03/2020
ms.author: aahi
ms.openlocfilehash: 2c2ebe9b419100163ae55c1be85dd1464904e841
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94978842"
---
### <a name="container-repositories-and-images"></a>Úložiště kontejnerů a obrázky

Následující tabulky obsahují seznam dostupných imagí kontejneru nabízených službou Azure Cognitive Services. Úplný seznam všech dostupných názvů imagí kontejneru a jejich dostupných značek najdete v tématu [Cognitive Services značek imagí kontejneru](../container-image-tags.md). 

#### <a name="generally-available"></a>Obecná dostupnost 

Microsoft Container Registry (MCR) zpřístupňuje všechny všeobecně dostupné kontejnery pro Cognitive Services. Kontejnery jsou k dispozici také přímo z [dokovacího centra](https://hub.docker.com/_/microsoft-azure-cognitive-services).

**LUIS**

| Kontejner | Název Container Registry/úložiště/image |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/language/luis` |

Další informace najdete v tématu [spuštění a instalace kontejnerů Luis](../../LUIS/luis-container-howto.md) .

**Analýza textu**

| Kontejner | Název Container Registry/úložiště/image |
|--|--|
| Analýza mínění V3 (angličtina) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en` |
| Analýza mínění V3 (španělština) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-es` |
| Analýza mínění V3 (francouzština) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-fr` |
| Analýza mínění V3 (italština) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-it` |
| Analýza mínění V3 (němčina) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-de` |
| Analýza mínění V3 (zjednodušená čínština) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zh` |
| Analýza mínění V3 (tradiční čínština) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zht` |
| Analýza mínění V3 (japonština) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-ja` |
| Analýza mínění V3 (portugalština) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-pt` |
| Analýza mínění V3 (holandština) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-nl` |

Další informace najdete v tématu [Jak spustit a nainstalovat kontejnery analýza textu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) .

**Detektor anomálií** 

| Kontejner | Název Container Registry/úložiště/image |
|--|--|
| Detektor anomálií | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` |

Další informace najdete v tématu [jak spouštět a instalovat kontejnery detektoru anomálií](../../anomaly-detector/anomaly-detector-container-howto.md) .

**Služba řeči**

> [!NOTE]
> Chcete-li používat kontejnery řeči, budete muset vyplnit [formulář žádosti online](https://aka.ms/csgate).

| Kontejner | Název Container Registry/úložiště/image |
|--|--|
| [Převod řeči na text](../../speech-service/speech-container-howto.md?tab=stt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` |
| [Custom Speech na text](../../speech-service/speech-container-howto.md?tab=cstt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` |
| [Převod textu na řeč](../../speech-service/speech-container-howto.md?tab=tts) | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` |

#### <a name="ungated-preview"></a>"Inhradloed" Preview 

Následující kontejnery Preview jsou veřejně dostupné. Microsoft Container Registry (MCR) zpřístupňuje všechny veřejně dostupné nebrané kontejnery pro Cognitive Services. Kontejnery jsou k dispozici také přímo z [dokovacího centra](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Služba | Kontejner | Název Container Registry/úložiště/image |
|--|--|--|
| [Analýza textu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extrakce klíčových frází | `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` |
| [Analýza textu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Rozpoznávání jazyka | `mcr.microsoft.com/azure-cognitive-services/textanalytics/language` |


#### <a name="gated-preview"></a>"Gated" verze Preview

Dříve byly do úložiště hostované kontejnery ověřovaného náhledu `containerpreview.azurecr.io` . Od září 22 2020 se tyto kontejnery (kromě Analýza textu pro stav) hostují na Microsoft Container Registry (MCR) a jejich stahování nevyžaduje použití příkazu Docker Login. Pokud chcete použít kontejner, budete potřebovat:

1. Dokončete [formulář žádosti](https://aka.ms/csgate) s vaším uživatelským ID a scénářem pro předplatné Azure. 
2. Po schválení Stáhněte kontejner z MCR. 
3. Pomocí klíče a koncového bodu z příslušného prostředku Azure ověřte kontejner za běhu. 

| Služba | Kontejner | Název Container Registry/úložiště/image |
|--|--|--|
| [Počítačové zpracování obrazu](../../Computer-vision/computer-vision-how-to-install-containers.md) | Číst v 2.0 | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| [Počítačové zpracování obrazu](../../Computer-vision/computer-vision-how-to-install-containers.md) | Čtení v 3.1 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |
| [Počítačové zpracování obrazu](../../computer-vision/spatial-analysis-container.md) | Prostorová analýza | `mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=ctts) | Vlastní převod textu na řeč | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=lid) | Rozpoznávání jazyka | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=ntts) | Neuronové převodu textu na řeč | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` |
| [Analýza textu pro zdravotnictví](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | Analýza textu pro zdravotnictví | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |