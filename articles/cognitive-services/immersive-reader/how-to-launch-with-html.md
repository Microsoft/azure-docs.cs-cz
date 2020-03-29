---
title: Spuštění Asistivní čtečky s obsahem HTML
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže, jak spustit Immersive Reader s obsahem HTML.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: bc7ab46113e1b819fc71a9f6e8a18400f8acfbef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946240"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>Jak spustit Immersive Reader s obsahem HTML

Tento článek ukazuje, jak spustit immersive Reader s obsahem HTML.

## <a name="prepare-the-html-content"></a>Příprava obsahu HTML

Umístěte obsah, který chcete vykreslit v immersive reader uvnitř prvku kontejneru. Ujistěte se, že prvek `id`kontejneru má jedinečný . Immersive Reader poskytuje podporu pro základní prvky HTML, naleznete v [odkazu](./reference.md#html-support) pro další informace.

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>Získání obsahu HTML v JavaScriptu

Pomocí `id` elementu kontejneru získáte obsah HTML v kódu JavaScriptu.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Spuštění immersive Reader u svého obsahu HTML

Při `ImmersiveReader.launchAsync`volání nastavte `mimeType` vlastnost bloku `text/html` tak, aby umožňovala vykreslování HTML.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Další kroky

* Seznamte se s [referenční sadou Immersive Reader SDK](./reference.md)