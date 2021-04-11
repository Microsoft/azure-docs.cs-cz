---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 832cc1d4f9ec3cec4ada6e964e3ab2f6f023dd41
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554632"
---
### <a name="neural-and-standard-voices"></a>Neuronové a standardní hlasy

Pomocí této tabulky můžete určit **dostupnost neuronové a standardní hlasy** podle oblasti/koncového bodu:

| Oblast | Koncový bod |
|--------|----------|
| Austrálie – východ | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` |
| Brazílie – jih | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| Střední Kanada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA – střed | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Východní Asie | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| East US | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA – východ 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` |
| Francie – střed | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| Indie – střed | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Japonsko – východ | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` |
| Japonsko – západ | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` |
| Jižní Korea – střed | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA – středosever | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Severní Evropa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| Středojižní USA | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Southeast Asia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Spojené království – jih | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA – středozápad | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| West Europe | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA – západ | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Západní USA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` |

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

### <a name="long-audio-api"></a>Rozhraní API pro dlouhé zvukové zařízení

Rozhraní API pro dlouhé zvukové rozhraní je k dispozici ve více oblastech s jedinečnými koncovými body.

| Oblast | Koncový bod |
|--------|----------|
| East US | `https://eastus.customvoice.api.speech.microsoft.com` |
| Indie – střed | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Southeast Asia | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Spojené království – jih | `https://uksouth.customvoice.api.speech.microsoft.com` |
| West Europe | `https://westeurope.customvoice.api.speech.microsoft.com` |
