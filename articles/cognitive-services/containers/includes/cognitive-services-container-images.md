---
title: Úložiště kontejnerů a obrázky
services: cognitive-services
author: aahill
manager: nitinme
description: Dvě tabulky reprezentující Registry kontejnerů, úložiště a názvy imagí pro všechny nabídky služby pro rozpoznávání.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: ff039d6d5879e036aecc63b46359d84673f84a0e
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89424632"
---
### <a name="container-repositories-and-images"></a>Úložiště kontejnerů a obrázky

Následující tabulky obsahují seznam dostupných imagí kontejneru nabízených službou Azure Cognitive Services. Úplný seznam všech dostupných názvů imagí kontejneru a jejich dostupných značek najdete v tématu [Cognitive Services značek imagí kontejneru](../container-image-tags.md). 

#### <a name="generally-available"></a>Obecná dostupnost 

Microsoft Container Registry (MCR) zpřístupňuje všechny všeobecně dostupné kontejnery pro Cognitive Services. Kontejnery jsou k dispozici také přímo z [dokovacího centra](https://hub.docker.com/_/microsoft-azure-cognitive-services).

#### <a name="luis"></a>[LUIS](#tab/luis)

| Kontejner LUIS | Název Container Registry/úložiště/image |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |

Další informace najdete v tématu [spuštění a instalace kontejnerů Luis](../../LUIS/luis-container-howto.md) .

#### <a name="text-analytics"></a>[Analýza textu](#tab/text-analytics)

| Analýza textu kontejner | Název Container Registry/úložiště/image |
|--|--|
| Analýza mínění V3 (angličtina) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en` |
| Analýza mínění V3 (španělština) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-es` |
| Analýza mínění V3 (francouzština) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-fr` |
| Analýza mínění V3 (italština) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-it` |
| Analýza mínění V3 (němčina) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-de` |
| Analýza mínění V3 (zjednodušená čínština) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zh` |
| Analýza mínění V3 (tradiční čínština) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zht` |
| Analýza mínění V3 (japonština) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-ja` |
| Analýza mínění V3 (portugalština) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-pt` |
| Analýza mínění V3 (holandština) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-nl` |

Další informace najdete v tématu [Jak spustit a nainstalovat kontejnery analýza textu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) .

---

#### <a name="public-ungated-preview-container-registry-mcrmicrosoftcom"></a>Public "inhradloed" Preview (registr kontejneru: `mcr.microsoft.com` )

Následující kontejnery Preview jsou veřejně dostupné. Microsoft Container Registry (MCR) zpřístupňuje všechny veřejně dostupné nebrané kontejnery pro Cognitive Services. Kontejnery jsou k dispozici také přímo z [dokovacího centra](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Služba | Kontejner | Název Container Registry/úložiště/image |
|--|--|--|
| [Analýza textu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extrakce klíčových frází | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Analýza textu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Rozpoznávání jazyka | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Detektor anomálií](../../anomaly-detector/anomaly-detector-container-howto.md) | Detektor anomálií | `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Public "gated" Preview (registr kontejneru: `containerpreview.azurecr.io` )

Následující chráněné kontejnery Preview jsou hostovány v registru Preview kontejneru a vyžadují aplikaci pro přístup. Další informace najdete v následujících článcích o kontejneru.

| Služba | Kontejner | Název Container Registry/úložiště/image |
|--|--|--|
| [Počítačové zpracování obrazu](../../Computer-vision/computer-vision-how-to-install-containers.md) | Číst | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Rozpoznávání tváře](../../face/face-how-to-install-containers.md) | Rozpoznávání tváře | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Nástroj pro rozpoznávání formulářů](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Rozpoznávání formulářů | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=stt) | Převod řeči na text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech na text | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=tts) | Převod textu na řeč | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=ctts) | Vlastní převod textu na řeč | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=ntts) | Neuronové převodu textu na řeč | `containerpreview.azurecr.io/microsoft/cognitive-services-neural-text-to-speech` |
| [Analýza textu pro stav](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | Analýza textu pro zdravotnictví | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |
