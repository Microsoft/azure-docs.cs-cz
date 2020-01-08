---
title: Zadat dotaz na koncový bod kontejneru řeči na text
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 2d96385f2d2d34d161739c55228220cf28871048
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "74132623"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Převod řeči na text nebo Custom Speech na text

Kontejner poskytuje rozhraní API koncových bodů dotazů založených na protokolu WebSocket, která jsou k dispozici prostřednictvím [sady Speech SDK](../index.md). Sada Speech SDK standardně používá online hlasové služby. Chcete-li použít kontejner, je nutné změnit inicializační metodu.

> [!TIP]
> Pokud používáte sadu Speech SDK s kontejnery, nemusíte zadávat [klíč předplatného prostředku Azure Speech nebo ověřovací token pro ověřování](../rest-speech-to-text.md#authentication).

Podívejte se na následující příklad:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Změnit z použití tohoto volání inicializace Azure-Cloud:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

do tohoto volání pomocí [hostitele](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)kontejneru:

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="pythontabpython"></a>[Python](#tab/python)

Změnit z použití tohoto volání inicializace Azure-Cloud:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

do tohoto volání pomocí [hostitele](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)kontejneru:

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
