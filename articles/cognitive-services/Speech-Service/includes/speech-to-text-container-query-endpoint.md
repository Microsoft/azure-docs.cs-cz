---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: e920ea3cd91f6275c4cb52dc070b0df60312d525
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92470853"
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

Použití tohoto volání u [hostitele](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet&preserve-view=true)kontejneru:

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

Pro použití tohoto volání s [koncovým bodem](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python&preserve-view=true)kontejneru:

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---
