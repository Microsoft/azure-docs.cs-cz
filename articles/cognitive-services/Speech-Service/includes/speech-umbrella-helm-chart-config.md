---
title: Nainstalujte kontejnery řeči
titleSuffix: Azure Cognitive Services
description: Podrobnosti o možnostech konfigurace grafu helm zastřešující řeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711810"
---
### <a name="speech-umbrella-chart"></a>Rozpoznávání řeči (zastřešující graf)

Hodnoty v grafu nejvyšší úrovně "zastřešující" přepisují hodnoty odpovídající dílčí grafu. Proto všechny místní, kterou tady by měly být přidány přizpůsobených hodnot.

|Parametr|Popis|Výchozí|
| -- | -- | -- | -- |
| `speechToText.enabled` | Zda **speech to text** aktivace služby. | `true` |
| `speechToText.verification.enabled` | Zda `helm test` funkce pro **speech to text** aktivace služby. | `true` |
| `speechToText.verification.image.registry` | Úložiště imagí dockeru, který `helm test` používá k testování **speech to text** služby. Příkaz Helm vytvoří samostatné pod uvnitř clusteru pro testování a získává *testu použijte* image z registru. | `docker.io` |
| `speechToText.verification.image.repository` | Úložiště imagí dockeru, který `helm test` používá k testování **speech to text** služby. Helm testů pod používá toto úložiště na vyžádání *testu použijte* bitové kopie. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Značce image dockeru, který se používá s `helm test` pro **speech to text** služby. Helm testů pod používá tuto značku přetahování *testu použijte* bitové kopie. | `latest` |
| `speechToText.verification.image.pullByHash` | Zda *testu použijte* image dockeru načítána algoritmem hash. Pokud `true`, `speechToText.verification.image.hash` by se měl přidat, s hodnotou hash platná bitová kopie. | `false` |
| `speechToText.verification.image.arguments` | Argumenty pro spuštění *testu použijte* image dockeru. Helm testů pod předá tyto argumenty kontejneru při spuštění `helm test`. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Zda **převod textu na řeč** aktivace služby. | `true` |
| `textToSpeech.verification.enabled` | Zda `helm test` funkce pro **speech to text** aktivace služby. | `true` |
| `textToSpeech.verification.image.registry` | Úložiště imagí dockeru, který `helm test` používá k testování **speech to text** služby. Příkaz Helm vytvoří samostatné pod uvnitř clusteru pro testování a získává *testu použijte* image z registru. | `docker.io` |
| `textToSpeech.verification.image.repository` | Úložiště imagí dockeru, který `helm test` používá k testování **speech to text** služby. Helm testů pod používá toto úložiště na vyžádání *testu použijte* bitové kopie. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Značce image dockeru, který se používá s `helm test` pro **speech to text** služby. Helm testů pod používá tuto značku přetahování *testu použijte* bitové kopie. | `latest` |
| `textToSpeech.verification.image.pullByHash` | Zda *testu použijte* image dockeru načítána algoritmem hash. Pokud `true`, `textToSpeech.verification.image.hash` by se měl přidat, s hodnotou hash platná bitová kopie. | `false` |
| `textToSpeech.verification.image.arguments` | Spustit s argumenty *testu použijte* image dockeru. Pod helm testu při spuštění předá tyto argumenty do kontejneru `helm test`. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |