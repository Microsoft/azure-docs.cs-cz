---
title: Rozpoznávání řeči syntézu Markup Language – hlasové služby
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
ms.openlocfilehash: f721c91c828b5c7c8497c07bb0cfe79646daf0f8
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868833"
---
# <a name="speech-synthesis-markup-language"></a>Značkovací jazyk syntézu řeči

Jazyk kódu syntézu řeči (SSML) je jazyk založený na formátu XML kód, který poskytuje způsob, jak řídit výslovnost a *prosody* z převodu textu na řeč. Prosody odkazuje na rytmus a výšku řeči, Hudba, pokud budete. Můžete fonetický přepis zadejte slova, poskytnout nápovědu pro interpretaci čísel, vložit pozastaví, výška ovládacího prvku, svazek a míry a další. Další informace najdete v tématu [řeči syntézu Markup Language (SSML) verze 1.0](http://www.w3.org/TR/2009/REC-speech-synthesis-20090303/).

Úplný seznam podporovaných jazyků, národní prostředí a hlasů (neuronových sítí nebo standardním), najdete v části [jazykovou podporu](language-support.md#text-to-speech).

Následující části obsahují ukázky pro běžné řeči syntézu úlohy.

>[!IMPORTANT]
> V současné době prosody označování je k dispozici pouze pro standardní hlasy.

## <a name="add-a-break"></a>Vložit zalomení
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>Mluvy frekvence změny
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>Výslovnost
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>Změnit svazek
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>Změnit výšku
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>Změnit výšku obrysu
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>Použití více hlasů
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Good morning!
</voice>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>Další postup

* [Podpora jazyků: hlasů, národní prostředí, jazyků](language-support.md)
