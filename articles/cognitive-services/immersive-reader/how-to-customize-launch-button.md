---
title: Přizpůsobení tlačítka Immersive Reader
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže, jak přizpůsobit tlačítko, které spustí Immersive Reader.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 6461cc60a237cf834226b07db5e1ea6fbbea7a92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946207"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Jak přizpůsobit tlačítko Immersive Reader

Tento článek ukazuje, jak přizpůsobit tlačítko, které spustí immersive Reader, aby vyhovovaly potřebám vaší aplikace.

## <a name="add-the-immersive-reader-button"></a>Přidání tlačítka Immersive Reader

Sada Immersive Reader SDK poskytuje výchozí styl pro tlačítko, které spouští immersive reader. Pomocí `immersive-reader-button` atributu třídy povolte tento styl.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Přizpůsobení stylu tlačítka

Pomocí `data-button-style` atributu nastavte styl tlačítka. Povolené hodnoty `icon`jsou `text`, `iconAndText`a . Výchozí hodnota je `icon`.

### <a name="icon-button"></a>Tlačítko ikony

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Tím se vykreslí následující:

![Tlačítko ikony](./media/button-icon.png)

### <a name="text-button"></a>Tlačítko Text

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Tím se vykreslí následující:

![Tlačítko ikony](./media/button-text.png)

### <a name="icon-and-text-button"></a>Ikona a textové tlačítko

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Tím se vykreslí následující:

![Tlačítko ikony](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Přizpůsobení textu tlačítka

Nakonfigurujte jazyk a alternativní `data-locale` text tlačítka pomocí atributu. Výchozím jazykem je angličtina.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Přizpůsobení velikosti ikony

Velikost ikony Immersive Reader lze nakonfigurovat `data-icon-px-size` pomocí atributu. Tím nastavíte velikost ikony v obrazových bodech. Výchozí velikost je 20 px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Další kroky

* Seznamte se s [referenční sadou Immersive Reader SDK](./reference.md)