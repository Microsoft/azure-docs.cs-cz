---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: d5a4b3a07854c2664de7ec60f3677b666798a9bd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145368"
---
### <a name="standard-and-neural-voices"></a>Standardní a neuronových sítí hlasů

Tato tabulka slouží k určování dostupnosti standardní a neuronových sítí hlasy oblasti nebo koncového bodu:

| Oblast | Koncový bod | Standard Voices | Neural Voices |
|--------|----------|-----------------|---------------|
| Austrálie – východ | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ano |
| Kanada – střed | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ano |
| USA – střed | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| Východní Asie | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| USA – východ | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ano |
| Východní USA 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| Francie – střed | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| Indie – střed | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ano |
| Japonsko – východ | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| Korea – střed | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| Středoseverní USA | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| Severní Evropa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| Středojižní USA | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ano |
| Jihovýchodní Asie | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ano |
| Velká Británie – jih | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ano |
| Západní Evropa | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ano |
| Západní USA | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| Západní USA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ano |

### <a name="custom-voices"></a>Vlastní hlasů

Pokud jste vytvořili vlastní hlasové písmo, použijte koncového bodu, který jste vytvořili. Můžete také použít koncových bodů uvedených níže, nahradí `{deploymentId}` s ID nasazení pro váš model hlasu.

| Oblast | Koncový bod |
|--------|----------|
| Austrálie – východ | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Kanada – střed | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA – střed | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Východní Asie | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA – východ | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Východní USA 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Francie – střed | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Indie – střed | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japonsko – východ | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Korea – střed | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Středoseverní USA | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Severní Evropa | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Středojižní USA | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Jihovýchodní Asie | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Velká Británie – jih | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Západní Evropa | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Západní USA | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Západní USA 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
