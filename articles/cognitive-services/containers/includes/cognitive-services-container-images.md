---
title: Úložiště kontejnerů a obrázky
services: cognitive-services
author: aahill
manager: nitinme
description: Dvě tabulky reprezentující Registry kontejnerů, úložiště a názvy imagí pro všechny nabídky služby pro rozpoznávání.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a854a090af908da691e9b26f5b0714c6560fc0ba
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876814"
---
### <a name="container-repositories-and-images"></a>Úložiště kontejnerů a obrázky

Následující tabulky obsahují seznam dostupných imagí kontejneru nabízených službou Azure Cognitive Services. Úplný seznam všech dostupných názvů imagí kontejneru a jejich dostupných značek najdete v tématu [Cognitive Services značek imagí kontejneru](../container-image-tags.md). V současné době nejsou k dispozici žádné Cognitive Services kontejnery, které jsou všeobecně dostupné (GA). V době, kdy je to možné, dokud nebudou vytvořeny další oznámení – kontejnery jsou k dispozici buď jako *veřejné* , nebo jako *veřejně ověřované verze Preview*.

 - *Veřejný nebraný*: kontejnery jsou veřejně dostupné bez mechanismu uzavírání.
 - *Public gated Preview*: kontejnery jsou veřejně dostupné, ale nejdřív vyžadují formální požadavek na přístup k registru kontejneru.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Public "inhradloed" (registr kontejneru `mcr.microsoft.com`:)

Microsoft Container Registry (MCR) zpřístupňuje všechny veřejně dostupné "nebránované" kontejnery pro Cognitive Services. Kontejnery jsou k dispozici také přímo z [dokovacího centra](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Služba | Kontejner | Název Container Registry/úložiště/image |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cogni'ive-services/luis` |
| [Analýza textu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extrakce klíčových frází | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Analýza textu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Rozpoznávání jazyka | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Analýza textu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Analýza mínění | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Public "gated" Preview (registr kontejneru: `containerpreview.azurecr.io`)

Registry Preview kontejneru hostují všechny veřejně dostupné kontejnery "gated" pro Cognitive Services. Tyto kontejnery vyžadují formální žádost o přístup k nim prostřednictvím jejich registru kontejnerů.

| Služba | Kontejner | Název Container Registry/úložiště/image |
|--|--|--|
| [Detektor anomálií](../../anomaly-detector/anomaly-detector-container-howto.md) | Detektor anomálií | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | Čtení | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Tvář](../../face/face-how-to-install-containers.md) | Tvář | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Nástroj pro rozpoznávání formulářů](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Rozpoznávání formulářů | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=stt) | Převod řeči na text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech na text | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=tts) | Převod textu na řeč | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=ctts) | Vlastní převod textu na řeč | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
