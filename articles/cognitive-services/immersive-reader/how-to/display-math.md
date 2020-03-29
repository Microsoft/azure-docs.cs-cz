---
title: Zobrazení matematiky v immerzivní čtečce
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže, jak zobrazit matematiku v immersive Reader.
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: e01cc9e8cedb6c38da0b56e04419c706d5d0566e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946119"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>Jak zobrazit matematiku v Immersive Reader

Immersive Reader může zobrazit matematiku, pokud je k dispozici ve formě matematického jazyka značek ([MathML](https://developer.mozilla.org/docs/Web/MathML)).
Typ MIME lze nastavit prostřednictvím [bloku](../reference.md#chunk)Immersive Reader . Další informace naleznete v [podporovaných typech MIME.](../reference.md#supported-mime-types)

## <a name="send-math-to-the-immersive-reader"></a>Poslat matematiku do immersive Reader
Chcete-li odeslat matematiku do immersive Reader, zadejte blok obsahující MathML a nastavte typ MIME na ```application/mathml+xml```;

Pokud byl například váš obsah následující:

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

Pak můžete zobrazit svůj obsah pomocí následujícího JavaScriptu.

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

Při spuštění immersive Reader, měli byste vidět:

![Matematika v immersive Reader](../media/how-tos/1-math.png)

## <a name="next-steps"></a>Další kroky

* Seznamte se s [sadou Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) a [referenční sadou Immersive Reader SDK](../reference.md)