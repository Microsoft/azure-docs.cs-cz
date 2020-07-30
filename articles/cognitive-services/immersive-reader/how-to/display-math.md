---
title: Zobrazit matematiku v moderní čtečce
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak v moderní čtečce zobrazit matematiku.
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: pasta
ms.custom: devx-track-javascript
ms.openlocfilehash: 6bde75fe0fb856633d7849173faf21db76edc950
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406907"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>Jak zobrazit matematiku v moderní čtečce

Moderní čtečka může zobrazit matematiku, pokud je k dispozici ve formě formátu[MathML](https://developer.mozilla.org/docs/Web/MathML)(matematických Markup Language).
Typ MIME se dá nastavit přes [blok](../reference.md#chunk)ponořeného čtecího zařízení. Další informace najdete v tématu [podporované typy MIME](../reference.md#supported-mime-types) .

## <a name="send-math-to-the-immersive-reader"></a>Odeslání matematiky do moderního čtecího zařízení
Aby bylo možné odesílat matematickému čtečce, poskytněte blok obsahující kódování MathML a nastavte typ MIME na ```application/mathml+xml``` .

Například pokud váš obsah byl následující:

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

Pak můžete zobrazit obsah pomocí následujícího JavaScriptu.

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

Při spuštění moderního čtecího zařízení byste měli vidět:

![Matematika v moderní čtečce](../media/how-tos/1-math.png)

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [sadu moderních čtenářů](https://github.com/microsoft/immersive-reader-sdk) a [referenční materiály k sadě pro moderní čtečku](../reference.md)