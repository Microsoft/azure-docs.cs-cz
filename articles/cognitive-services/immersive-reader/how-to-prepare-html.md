---
title: Příprava obsahu HTML pro moderní čtečku
titleSuffix: Azure Cognitive Services
description: Naučte se spouštět moderní čtečku pomocí HTML, JavaScriptu, Pythonu, Androidu nebo iOS. Moderní čtečka používá osvědčené techniky pro zlepšení porozumění čtení pro jazykové učení, nově vznikající čtenáře a studenty s rozdíly v učení.
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 40aab9510eb90c405f92be49a3b4c0a3f756c872
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620014"
---
# <a name="how-to-prepare-html-content-for-immersive-reader"></a>Příprava obsahu HTML pro moderní čtečku

V tomto článku se dozvíte, jak strukturovat kód HTML a načíst obsah, aby ho bylo možné použít pro moderní čtečku.

## <a name="prepare-the-html-content"></a>Příprava obsahu HTML

Obsah, který chcete vykreslit, umístěte do moderního čtecího zařízení uvnitř kontejneru elementu. Ujistěte se, že prvek kontejneru má jedinečný `id` . Moderní čtečka poskytuje podporu základních prvků HTML. Další informace najdete v [referenčních](reference.md#html-support) informacích.

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

Použijte `id` prvek kontejneru k získání obsahu HTML v kódu JavaScriptu.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Spuštění moderního čtecího zařízení s vaším obsahem HTML

Při volání `ImmersiveReader.launchAsync` nastavte vlastnost bloku na hodnotu `mimeType` `text/html` Povolit vykreslování HTML.

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

* Prozkoumejte [referenční materiály k sadě pro moderní čtečku](reference.md)