---
title: Úložiště kontejnerů a obrázky
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Dvě tabulky reprezentující Registry kontejnerů, úložiště a názvy imagí pro všechny nabídky služby pro rozpoznávání.
ms.service: cognitive-services
ms.topic: include
ms.date: 09/06/2019
ms.author: dapine
ms.openlocfilehash: 181f4acd86dfacb15592ded6f2df3287e3dc13bf
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130145"
---
### <a name="container-repositories-and-images"></a>Úložiště kontejnerů a obrázky

Níže uvedené tabulky jsou vyčerpávajícím seznamem dostupných imagí kontejneru nabízených službou Azure Cognitive Services.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Public "inhradloed" (registr kontejneru `mcr.microsoft.com`:)

Microsoft Container Registry hostuje všechny veřejně dostupné "nebránované" kontejnery pro Cognitive Services.

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
| [Počítačové zpracování obrazu](../../Computer-vision/computer-vision-how-to-install-containers.md) | Rozpoznávání textu | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text` |
| [Počítačové zpracování obrazu](../../Computer-vision/computer-vision-how-to-install-containers.md) | Čtení | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Rozpoznávání tváře](../../face/face-how-to-install-containers.md) | Tvář | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Nástroj pro rozpoznávání formulářů](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Rozpoznávání formulářů | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md) | Převod řeči na text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md) | Převod textu na řeč | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
