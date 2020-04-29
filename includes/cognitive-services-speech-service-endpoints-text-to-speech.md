---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: a1de313245e826511a7e9c864165ac44bcb48530
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80234235"
---
### <a name="standard-and-neural-voices"></a>Hlasy Standard a neuronové

Pomocí této tabulky můžete určit dostupnost standardních a neuronové hlasů podle oblasti/koncového bodu:

| Oblast | Koncový bod | Standardní hlasy | Hlasy neuronové |
|--------|----------|-----------------|---------------|
| Austrálie – východ | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ano |
| Brazílie – jih | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| Střední Kanada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ano |
| USA – střed | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| Východní Asie | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| USA – východ | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ano |
| USA – východ 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| Francie – střed | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| Indie – střed | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ano |
| Japonsko – východ | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| Japonsko – západ | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| Jižní Korea – střed | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| USA – středosever | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| Severní Evropa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| USA – středojih | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ano |
| Jihovýchodní Asie | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ano |
| Spojené království – jih | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ano |
| Západní Evropa | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ano |
| USA – západ | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ne |
| USA – západ 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Ano | Ano |

### <a name="custom-voices"></a>Vlastní hlasy

Pokud jste vytvořili vlastní písmo hlasu, použijte koncový bod, který jste vytvořili. Můžete použít také níže uvedené koncové body a nahradit tak `{deploymentId}` ID nasazení pro váš hlasový model.

| Oblast | Koncový bod |
|--------|----------|
| Austrálie – východ | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Brazílie – jih | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Střední Kanada | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA – střed | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Východní Asie | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA – východ | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA – východ 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Francie – střed | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Indie – střed | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japonsko – východ | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japonsko – západ | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Jižní Korea – střed | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA – středosever | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Severní Evropa | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA – středojih | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Jihovýchodní Asie | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Spojené království – jih | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Západní Evropa | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA – západ | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA – západ 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
