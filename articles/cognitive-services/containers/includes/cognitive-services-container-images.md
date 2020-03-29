---
title: Úložiště kontejnerů a image
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Dvě tabulky představující registry kontejnerů, úložiště a názvy obrázků pro všechny nabídky služby Cognitive Service.
ms.service: cognitive-services
ms.topic: include
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 55a3bb5f894d3ab753cfec64687abc9c7cae53cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082490"
---
### <a name="container-repositories-and-images"></a>Úložiště kontejnerů a image

Níže uvedené tabulky jsou seznam dostupných ibionů kontejnerů nabízených službami Azure Cognitive Services. Úplný seznam všech dostupných názvů bitových obrázků kontejneru a jejich dostupných značek naleznete v [tématu Značky image kontejneru cognitive services](../container-image-tags.md). V současné době neexistují žádné kontejnery služeb Cognitive Services, které jsou obecně dostupné (GA). V současné době, dokud nebudou učiněna další oznámení - kontejnery jsou k dispozici buď jako *Public Ungated* nebo *Public Gated Preview*.

 - *Veřejné ungated*: kontejnery jsou k dispozici veřejně bez gating mechanismus.
 - *Public Gated Preview*: kontejnery jsou k dispozici veřejně, ale nejprve vyžadují formální požadavek na přístup k registru kontejneru.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Veřejné "Ungated" (kontejner `mcr.microsoft.com`registru: )

Microsoft Container Registry (MCR) syndikuje všechny veřejně dostupné "nevysuzované" kontejnery pro služby Cognitive Services. Kontejnery jsou také k dispozici přímo z [centra Dockeru](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Služba | Kontejner | Registr kontejnerů / úložiště / název obrázku |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Analýza textu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extrakce klíčových frází | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Analýza textu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Rozpoznávání jazyka | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Analýza textu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Analýza mínění | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Veřejné "Gated" Preview (kontejner registru: `containerpreview.azurecr.io`)

Registr Náhled kontejneru hostuje všechny veřejně dostupné "gated" kontejnery pro kognitivní služby. Tyto kontejnery vyžadují formální požadavek na přístup k nim prostřednictvím jejich registru kontejneru.

| Služba | Kontejner | Registr kontejnerů / úložiště / název obrázku |
|--|--|--|
| [Detektor anomálií](../../anomaly-detector/anomaly-detector-container-howto.md) | Detektor anomálií | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Počítačové vidění](../../Computer-vision/computer-vision-how-to-install-containers.md) | Čtení | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Tvář](../../face/face-how-to-install-containers.md) | Tvář | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Nástroj pro rozpoznávání formulářů](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Rozpoznávání formulářů | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=stt) | Převod řeči na text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=cstt) | Vlastní převod řeči na text | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=tts) | Převod textu na řeč | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md?tab=ctts) | Vlastní převod textu na řeč | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
