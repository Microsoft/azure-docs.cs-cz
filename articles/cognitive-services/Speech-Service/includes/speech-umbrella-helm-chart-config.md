---
title: Instalace kontejnerů řeči
titleSuffix: Azure Cognitive Services
description: Podrobnosti o možnostech konfigurace grafu kormidelník řeči.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 0257f3af44cc85d0a3656472db224ae5a7e19161
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874333"
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