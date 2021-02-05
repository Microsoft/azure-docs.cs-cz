---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: df6f7311613057c445ae714b8b11240d0d5be14b
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569425"
---
### <a name="standard-and-neural-voices"></a>Hlasy Standard a neuronové

Pomocí této tabulky můžete určit dostupnost standardních a neuronové hlasů podle oblasti/koncového bodu:

| Oblast | Koncový bod | Standardní hlasy | Hlasy neuronové |
|--------|----------|-----------------|---------------|
| Austrálie – východ | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Yes |
| Brazílie – jih | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | No |
| Střední Kanada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Yes |
| USA – střed | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | No |
| Východní Asie | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | No |
| East US | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Yes |
| USA – východ 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | No |
| Francie – střed | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | No |
| Indie – střed | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Yes |
| Japonsko – východ | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | No |
| Japonsko – západ | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | No |
| Jižní Korea – střed | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | No |
| USA – středosever | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | No |
| Severní Evropa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | No |
| Středojižní USA | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Yes |
| Southeast Asia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Yes |
| Spojené království – jih | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Yes |
| West Europe | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Yes |
| USA – západ | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | No |
| Západní USA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Yes |

> [!TIP]
> [Hlasy ve verzi Preview](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) jsou dostupné jenom v těchto třech oblastech: Východní USA, západní Evropa a jihovýchodní Asie.

### <a name="custom-voices"></a>Vlastní hlasy

Pokud jste vytvořili vlastní písmo hlasu, použijte koncový bod, který jste vytvořili. Můžete použít také níže uvedené koncové body a nahradit tak `{deploymentId}` ID nasazení pro váš hlasový model.

| Oblast | Koncový bod |
|--------|----------|
| Austrálie – východ | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Brazílie – jih | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Střední Kanada | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA – střed | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Východní Asie | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| East US | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA – východ 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Francie – střed | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Indie – střed | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japonsko – východ | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japonsko – západ | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Jižní Korea – střed | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA – středosever | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Severní Evropa | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Středojižní USA | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Southeast Asia | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Spojené království – jih | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| West Europe | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA – západ | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Západní USA 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |

### <a name="custom-neural-voice"></a>Vlastní neuronové hlas

Následující tabulka obsahuje podrobnosti o regionální podpoře pro vlastní funkce hlasu neuronové.

| Funkce | Podporované oblasti |
|---|---|
| Hostování hlasového modelu | Východní USA, Západní USA 2, Střed USA – jih, jihovýchodní Asie, Velká Británie – jih, Západní Evropa, Austrálie – východ |
| Znaky v reálném čase | Východní USA, Západní USA 2, Střed USA – jih, jihovýchodní Asie, Velká Británie – jih, Západní Evropa, Austrálie – východ |
| Dlouhé zvukové znaky | Východní USA, Západní Evropa, Velká Británie – jih, jihovýchodní Asie, Indie – střed |
| Vlastní školení neuronové | Východní USA Velká Británie – jih |
