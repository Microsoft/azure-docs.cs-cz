---
title: Jazyk využívající značky syntézu řeči (SSML) – hlasové služby
titleSuffix: Azure Cognitive Services
description: K řízení výslovnost a prosody v převodu textu na řeč pomocí Markup Language syntézu řeči.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 57fc7e699d88dbe777750e3acdb7f96794b66fc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61460282"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Jazyk SSML (Speech Synthesis Markup Language)

Jazyk kódu syntézu řeči (SSML) je jazyk založený na formátu XML kód, který poskytuje způsob, jak řídit výslovnost a *prosody* z převodu textu na řeč. Prosody odkazuje na rytmus a výšku řeči, Hudba, pokud budete. Můžete fonetický přepis zadejte slova, poskytnout nápovědu pro interpretaci čísel, vložit pozastaví, výška ovládacího prvku, svazek a míry a další. Další informace najdete v tématu [řeči syntézu Markup Language (SSML) verze 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/).

Úplný seznam podporovaných jazyků, národní prostředí a hlasů (neuronových sítí nebo standardním), najdete v části [jazykovou podporu](language-support.md#text-to-speech).

Následující části obsahují ukázky pro běžné řeči syntézu úlohy.

## <a name="add-a-break"></a>Vložit zalomení
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>Mluvy frekvence změny

Rychlost čtení lze použít pro standardní hlasy v aplikaci word nebo úrovni vět. Zatímco mluvený míra může používat jedině pro Neurální hlasy na úrovni vět.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>Výslovnost
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>Změnit svazek

Svazek změny mohou být použity na standardní hlasy v aplikaci word nebo úrovni vět. Zatímco změny hlasitosti dá používat jedině pro Neurální hlasy na úrovni vět.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>Změnit výšku

Rozteč změny mohou být použity na standardní hlasy v aplikaci word nebo úrovni vět. Vzhledem k tomu od změny dá používat jedině pro Neurální hlasy na úrovni vět.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>Změnit výšku obrysu

> [!IMPORTANT]
> Rozteč tvarování změny nejsou podporovány s hlasy neuronových sítí.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>Použití více hlasů
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Good morning!
</voice>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>Další postup

* [Podpora jazyků: hlasů, národní prostředí, jazyků](language-support.md)
