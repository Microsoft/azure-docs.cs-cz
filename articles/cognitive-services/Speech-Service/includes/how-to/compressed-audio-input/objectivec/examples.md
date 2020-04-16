---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421944"
---
Chcete-li streamovat v komprimovaném formátu `SPXPullAudioInputStream` `SPXPushAudioInputStream`zvuku do služby Řeč, vytvořte nebo .

Následující výstřižek ukazuje, `SPXAudioConfiguration` jak vytvořit a `SPXPushAudioInputStream`z instance , určení MP3 jako kompresní formát datového proudu.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

Další úryvek ukazuje, jak lze komprimovaná zvuková data `SPXPushAudioInputStream`číst ze souboru a napumpovat do souboru .

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
