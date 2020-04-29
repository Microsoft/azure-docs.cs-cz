---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421944"
---
Chcete-li streamovat v komprimovaném zvukovém formátu ke službě rozpoznávání `SPXPullAudioInputStream` řeči `SPXPushAudioInputStream`, vytvořte nebo.

Následující fragment kódu ukazuje, jak vytvořit `SPXAudioConfiguration` z instance a `SPXPushAudioInputStream`zadáním formátu MP3 jako kompresního formátu datového proudu.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

Další fragment kódu ukazuje, jak lze komprimovat zvuková data ze souboru a vytvořit z něj `SPXPushAudioInputStream`čerpadla.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
