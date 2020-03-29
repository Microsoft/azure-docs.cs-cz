---
title: Instalace kontejnerů řeči
titleSuffix: Azure Cognitive Services
description: Podrobnosti o možnostech konfigurace grafu kormidelník řeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73522615"
---
### <a name="speech-umbrella-chart"></a>Řeč (zastřešující graf)

Hodnoty v "zastřešujícím" grafu nejvyšší úrovně přepíší odpovídající hodnoty dílčího grafu. Proto všechny místní přizpůsobené hodnoty by měly být přidány zde.

|Parametr|Popis|Výchozí|
| -- | -- | -- | -- |
| `speechToText.enabled` | Určuje, zda je povolena služba **převodu řeči na text.** | `true` |
| `speechToText.verification.enabled` | Určuje, `helm test` zda je povolena možnost služby **převodu řeči na text.** | `true` |
| `speechToText.verification.image.registry` | Úložiště bitových údajů `helm test` dockeru, které se používá k testování služby **převodu řeči na text.** Helm vytvoří samostatný pod uvnitř clusteru pro testování a vytáhne *bitovou* kopii použití testu z tohoto registru. | `docker.io` |
| `speechToText.verification.image.repository` | Úložiště bitových údajů `helm test` dockeru, které se používá k testování služby **převodu řeči na text.** Testovací pod helmu používá toto úložiště k vytahovat bitovou kopii *pro použití testu.* | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Značka image dockeru `helm test` používaná pro **službu převodu řeči na text.** Testovací modul helmu používá tuto značku k *vytažení bitové* kopie pro použití testu. | `latest` |
| `speechToText.verification.image.pullByHash` | Zda *je bitová* kopie dockeru pro testování vytahována pomocí hash. Pokud `true` `speechToText.verification.image.hash` , by měla být přidána, s platnou hodnotou hash obrázku. | `false` |
| `speechToText.verification.image.arguments` | Argumenty použité k provedení image dockeru *pro testování.* Modul testu helmu předá tyto `helm test`argumenty kontejneru při spuštění . | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Určuje, zda je povolena služba **převodu textu na řeč.** | `true` |
| `textToSpeech.verification.enabled` | Určuje, `helm test` zda je povolena možnost služby **převodu řeči na text.** | `true` |
| `textToSpeech.verification.image.registry` | Úložiště bitových údajů `helm test` dockeru, které se používá k testování služby **převodu řeči na text.** Helm vytvoří samostatný pod uvnitř clusteru pro testování a vytáhne *bitovou* kopii použití testu z tohoto registru. | `docker.io` |
| `textToSpeech.verification.image.repository` | Úložiště bitových údajů `helm test` dockeru, které se používá k testování služby **převodu řeči na text.** Testovací pod helmu používá toto úložiště k vytahovat bitovou kopii *pro použití testu.* | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Značka image dockeru `helm test` používaná pro **službu převodu řeči na text.** Testovací modul helmu používá tuto značku k *vytažení bitové* kopie pro použití testu. | `latest` |
| `textToSpeech.verification.image.pullByHash` | Zda *je bitová* kopie dockeru pro testování vytahována pomocí hash. Pokud `true` `textToSpeech.verification.image.hash` , by měla být přidána, s platnou hodnotou hash obrázku. | `false` |
| `textToSpeech.verification.image.arguments` | Argumenty, které mají být provedeny s bitovou kopii *testovacího použití* dockeru. Modul testu kormidla předá tyto `helm test`argumenty kontejneru při spuštění . | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |