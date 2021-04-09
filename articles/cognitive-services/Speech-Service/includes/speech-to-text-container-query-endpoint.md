---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 167e33ff4a3af463e2537e2714e9e9bf5e125b61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98947897"
---
Kontejner poskytuje rozhraní API koncových bodů dotazů založených na protokolu WebSocket, která jsou k dispozici prostřednictvím [sady Speech SDK](../index.yml). Sada Speech SDK standardně používá online hlasové služby. Chcete-li použít kontejner, je nutné změnit inicializační metodu.

> [!TIP]
> Pokud používáte sadu Speech SDK s kontejnery, nemusíte zadávat [klíč předplatného prostředku Azure Speech nebo ověřovací token pro ověřování](../rest-speech-to-text.md#authentication).

Podívejte se na následující příklad:

# <a name="c"></a>[C#](#tab/csharp)

Změnit z použití tohoto volání inicializace Azure-Cloud:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Použití tohoto volání u [hostitele](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost)kontejneru:

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```

# <a name="python"></a>[Python](#tab/python)

Změnit z použití tohoto volání inicializace Azure-Cloud:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

Pro použití tohoto volání s [koncovým bodem](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)kontejneru:

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---
