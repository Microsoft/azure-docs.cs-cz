---
title: Instalace kontejnerů řeči
titleSuffix: Azure Cognitive Services
description: Podrobnější informace o možnostech konfigurace grafu funkce Speech deštník Helm
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "67717203"
---
### <a name="speech-umbrella-chart"></a>Řeč (deštníkový graf)

Hodnoty v grafu "deštník" na nejvyšší úrovni přepíší odpovídající hodnoty dílčího grafu. Proto by se tady měly přidat všechny místní přizpůsobené hodnoty.

|Parametr|Popis|Výchozí|
| -- | -- | -- | -- |
| `speechToText.enabled` | Zda je povolena služba **Převod řeči na text** . | `true` |
| `speechToText.verification.enabled` | Určuje, jestli je povolená  možnostproslužbuPřevodřečina`helm test` text. | `true` |
| `speechToText.verification.image.registry` | Úložiště imagí Docker, které `helm test` nástroj používá k testování služby **Speech to text** . Helm vytvoří samostatného pod clusterem za účelem testování a vyžádá si z tohoto registru image s *použitím testu* . | `docker.io` |
| `speechToText.verification.image.repository` | Úložiště imagí Docker, které `helm test` nástroj používá k testování služby **Speech to text** . Helm test pod používá toto úložiště k získání image *použití testu* . | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Značka obrázku Docker, která se `helm test` používá pro službu pro **Převod řeči na text** . Helm test pod používá tuto značku k vyžádání image *použití testu* . | `latest` |
| `speechToText.verification.image.pullByHash` | Zda je  bitová kopie vyzkoušena pomocí algoritmu hash. IF `true`bymělbýt přidánsplatnouhodnotouhashobrázku.`speechToText.verification.image.hash` | `false` |
| `speechToText.verification.image.arguments` | Argumenty použité ke spuštění bitové kopie Docker *použijte test* . Helm test pod předává tyto argumenty kontejneru při spuštění `helm test`. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Určuje, zda je povolena služba **Převod textu na mluvené slovo** . | `true` |
| `textToSpeech.verification.enabled` | Určuje, jestli je povolená  možnostproslužbuPřevodřečina`helm test` text. | `true` |
| `textToSpeech.verification.image.registry` | Úložiště imagí Docker, které `helm test` nástroj používá k testování služby **Speech to text** . Helm vytvoří samostatného pod clusterem za účelem testování a vyžádá si z tohoto registru image s *použitím testu* . | `docker.io` |
| `textToSpeech.verification.image.repository` | Úložiště imagí Docker, které `helm test` nástroj používá k testování služby **Speech to text** . Helm test pod používá toto úložiště k získání image *použití testu* . | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Značka obrázku Docker, která se `helm test` používá pro službu pro **Převod řeči na text** . Helm test pod používá tuto značku k vyžádání image *použití testu* . | `latest` |
| `textToSpeech.verification.image.pullByHash` | Zda je  bitová kopie vyzkoušena pomocí algoritmu hash. IF `true`bymělbýt přidánsplatnouhodnotouhashobrázku.`textToSpeech.verification.image.hash` | `false` |
| `textToSpeech.verification.image.arguments` | Argumenty, které mají být provedeny s obrázkem Docker *použijte test* . Helm test pod předá tyto argumenty do kontejneru při spuštění `helm test`. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |