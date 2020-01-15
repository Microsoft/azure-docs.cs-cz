---
title: Spuštění moderního čtečky s obsahem HTML
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže, jak spustit moderní čtečku s obsahem HTML.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: bc7ab46113e1b819fc71a9f6e8a18400f8acfbef
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946240"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>Spuštění moderního čtecího zařízení s obsahem HTML

Tento článek ukazuje, jak spustit moderní čtečku s obsahem HTML.

## <a name="prepare-the-html-content"></a>Příprava obsahu HTML

Obsah, který chcete vykreslit, umístěte do moderního čtecího zařízení uvnitř kontejneru elementu. Ujistěte se, že prvek kontejneru má jedinečný `id`. Moderní čtečka poskytuje podporu základních prvků HTML. Další informace najdete v [referenčních](./reference.md#html-support) informacích.

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

Použijte `id` prvku kontejneru k získání obsahu HTML v kódu JavaScriptu.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Spuštění moderního čtecího zařízení s vaším obsahem HTML

Při volání `ImmersiveReader.launchAsync`nastavte vlastnost `mimeType` bloku na `text/html`, aby bylo možné vykreslování HTML.

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

* Prozkoumejte [referenční materiály k sadě pro moderní čtečku](./reference.md)