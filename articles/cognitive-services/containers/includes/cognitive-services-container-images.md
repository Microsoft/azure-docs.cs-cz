---
title: Úložiště kontejnerů a obrázky
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Dvě tabulky reprezentující Registry kontejnerů, úložiště a názvy imagí pro všechny nabídky služby pro rozpoznávání.
ms.service: cognitive-services
ms.topic: include
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 2058dd6e52ddb417e24368b27384df9a222c378e
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74142189"
---
### <a name="container-repositories-and-images"></a>Úložiště kontejnerů a obrázky

Následující tabulky obsahují seznam dostupných imagí kontejneru nabízených službou Azure Cognitive Services. Úplný seznam všech dostupných názvů imagí kontejneru a jejich dostupných značek najdete v tématu [Cognitive Services značek imagí kontejneru](../container-image-tags.md).

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Public "inhradloed" (Registry kontejneru: `mcr.microsoft.com`)

Microsoft Container Registry (MCR) zpřístupňuje všechny veřejně dostupné "nebránované" kontejnery pro Cognitive Services. Jsou k dispozici také přímo z [dokovacího centra](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Služba | Kontejner | Název Container Registry/úložiště/image |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Analýza textu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extrakce klíčových frází | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Analýza textu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Rozpoznávání jazyka | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Analýza textu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Analýza mínění | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Public "gated" Preview (registr kontejneru: `containerpreview.azurecr.io`)

Registry Preview kontejneru hostují všechny veřejně dostupné kontejnery "gated" pro Cognitive Services. Tyto kontejnery vyžadují formální žádost o přístup, aby je bylo možné spotřebovat.

| Služba | Kontejner | Název Container Registry/úložiště/image |
|--|--|--|
| [Detektor anomálií](../../anomaly-detector/anomaly-detector-container-howto.md) | Detektor anomálií | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Počítačové zpracování obrazu](../../Computer-vision/computer-vision-how-to-install-containers.md) | Čtení | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Rozpoznávání tváře](../../face/face-how-to-install-containers.md) | Tvář | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Nástroj pro rozpoznávání formulářů](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Rozpoznávání formulářů | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=stt) | Převod řeči na text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech na text | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=tts) | Převod textu na řeč | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=ctts) | Vlastní převod textu na řeč | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
