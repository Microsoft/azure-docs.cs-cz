---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: e5f38da6a18e8a4c59bea77f4ddd5a68db1e1902
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610734"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Převod řeči na text nebo Custom Speech na text

Kontejner poskytuje rozhraní API koncových bodů dotazů založených na protokolu WebSocket, která jsou k dispozici prostřednictvím [sady Speech SDK](../index.yml). Sada Speech SDK standardně používá online hlasové služby. Chcete-li použít kontejner, je nutné změnit inicializační metodu.

> [!TIP]
> Pokud používáte sadu Speech SDK s kontejnery, nemusíte zadávat [klíč předplatného prostředku Azure Speech nebo ověřovací token pro ověřování](../rest-speech-to-text.md#authentication).

Podívejte se na následující příklad:

# <a name="c"></a>[R #](#tab/csharp)

Změnit z použití tohoto volání inicializace Azure-Cloud:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

do tohoto volání pomocí [hostitele](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)kontejneru:

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

---
