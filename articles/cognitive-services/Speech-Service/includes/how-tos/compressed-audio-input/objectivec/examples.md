---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943729"
---
Chcete-li streamovat v komprimovaném formátu `SPXPullAudioInputStream` `SPXPushAudioInputStream`zvuku do služby Řeč, vytvořte nebo .

Následující výstřižek ukazuje, `SPXAudioConfiguration` jak vytvořit a `SPXPushAudioInputStream`z instance , určení MP3 jako kompresní formát datového proudu.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

Další úryvek ukazuje, jak lze komprimovaná zvuková data `SPXPushAudioInputStream`číst ze souboru a napumpovat do souboru .

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
