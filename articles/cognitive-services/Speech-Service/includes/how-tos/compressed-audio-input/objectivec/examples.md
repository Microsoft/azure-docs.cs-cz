---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943729"
---
Pokud chcete streamovat v komprimovaném zvukovém formátu ke službě rozpoznávání řeči, vytvořte `SPXPullAudioInputStream` nebo `SPXPushAudioInputStream`.

Následující fragment kódu ukazuje, jak vytvořit `SPXAudioConfiguration` z instance `SPXPushAudioInputStream`a určením formátu MP3 jako kompresního formátu datového proudu.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

Další fragment kódu ukazuje, jak je možné číst komprimovaná zvuková data ze souboru a nacházet z něj do `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
