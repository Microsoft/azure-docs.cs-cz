---
title: Instalace kontejnerů řeči
titleSuffix: Azure Cognitive Services
description: Podrobnější informace o možnostech konfigurace grafu funkce Speech deštník Helm
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 0257f3af44cc85d0a3656472db224ae5a7e19161
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80874333"
---
### <a name="speech-umbrella-chart"></a>Řeč (deštníkový graf)

Hodnoty v grafu "deštník" na nejvyšší úrovni přepíší odpovídající hodnoty dílčího grafu. Proto by se tady měly přidat všechny místní přizpůsobené hodnoty.

|Parametr|Popis|Výchozí|
| -- | -- | -- | -- |
| `speechToText.enabled` | Zda je povolena služba **Převod řeči na text** . | `true` |
| `speechToText.verification.enabled` | Určuje, jestli `helm test` je povolená možnost pro službu **Převod řeči na text** . | `true` |
| `speechToText.verification.image.registry` | Úložiště imagí Docker, které nástroj `helm test` používá k testování služby **Speech to text** . Helm vytvoří samostatného pod clusterem za účelem testování a vyžádá si z tohoto registru image s *použitím testu* . | `docker.io` |
| `speechToText.verification.image.repository` | Úložiště imagí Docker, které nástroj `helm test` používá k testování služby **Speech to text** . Helm test pod používá toto úložiště k získání image *použití testu* . | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Značka obrázku Docker, která se používá `helm test` pro službu pro **Převod řeči na text** . Helm test pod používá tuto značku k vyžádání image *použití testu* . | `latest` |
| `speechToText.verification.image.pullByHash` | Zda je bitová kopie *vyzkoušena* pomocí algoritmu hash. IF `true` `speechToText.verification.image.hash` by měl být přidán s platnou hodnotou hash obrázku. | `false` |
| `speechToText.verification.image.arguments` | Argumenty použité ke spuštění bitové kopie Docker *použijte test* . Helm test pod předává tyto argumenty kontejneru při spuštění `helm test` . | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Určuje, zda je povolena služba **Převod textu na mluvené slovo** . | `true` |
| `textToSpeech.verification.enabled` | Určuje, jestli `helm test` je povolená možnost pro službu **Převod řeči na text** . | `true` |
| `textToSpeech.verification.image.registry` | Úložiště imagí Docker, které nástroj `helm test` používá k testování služby **Speech to text** . Helm vytvoří samostatného pod clusterem za účelem testování a vyžádá si z tohoto registru image s *použitím testu* . | `docker.io` |
| `textToSpeech.verification.image.repository` | Úložiště imagí Docker, které nástroj `helm test` používá k testování služby **Speech to text** . Helm test pod používá toto úložiště k získání image *použití testu* . | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Značka obrázku Docker, která se používá `helm test` pro službu pro **Převod řeči na text** . Helm test pod používá tuto značku k vyžádání image *použití testu* . | `latest` |
| `textToSpeech.verification.image.pullByHash` | Zda je bitová kopie *vyzkoušena* pomocí algoritmu hash. IF `true` `textToSpeech.verification.image.hash` by měl být přidán s platnou hodnotou hash obrázku. | `false` |
| `textToSpeech.verification.image.arguments` | Argumenty, které mají být provedeny s obrázkem Docker *použijte test* . Helm test pod předá tyto argumenty do kontejneru při spuštění `helm test` . | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |