---
title: Přizpůsobení tlačítka pro moderní čtečku
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak přizpůsobit tlačítko, které spouští moderní čtečku.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 6461cc60a237cf834226b07db5e1ea6fbbea7a92
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946207"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Přizpůsobení tlačítka pro moderní čtečku

Tento článek ukazuje, jak přizpůsobit tlačítko, které spouští moderní čtečku, aby vyhovovala potřebám vaší aplikace.

## <a name="add-the-immersive-reader-button"></a>Přidání tlačítka pro moderní čtečku

Sada moderní čtečka SDK nabízí výchozí styly pro tlačítko, které spouští moderní čtečku. Pro povolení tohoto stylu použijte atribut třídy `immersive-reader-button`.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Přizpůsobení stylu tlačítka

Pomocí atributu `data-button-style` nastavte styl tlačítka. Povolené hodnoty jsou `icon`, `text`a `iconAndText`. Výchozí hodnota je `icon`.

### <a name="icon-button"></a>Tlačítko ikony

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Vykreslí se následující:

![Tlačítko ikony](./media/button-icon.png)

### <a name="text-button"></a>Tlačítko text

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Vykreslí se následující:

![Tlačítko ikony](./media/button-text.png)

### <a name="icon-and-text-button"></a>Tlačítko ikona a text

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Vykreslí se následující:

![Tlačítko ikony](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Přizpůsobení textu tlačítka

Nakonfigurujte jazyk a alternativní text pro tlačítko pomocí atributu `data-locale`. Výchozím jazykem je angličtina.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Přizpůsobení velikosti ikony

Velikost ikony moderního čtečky se dá nakonfigurovat pomocí atributu `data-icon-px-size`. Tím se nastaví velikost ikony v pixelech. Výchozí velikost je 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [referenční materiály k sadě pro moderní čtečku](./reference.md)