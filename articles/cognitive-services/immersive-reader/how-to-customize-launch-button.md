---
title: Upravit tlačítko pro spuštění moderního čtecího zařízení
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak přizpůsobit tlačítko, které spouští moderní čtečku.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 03/08/2021
ms.author: metang
ms.openlocfilehash: d60e37a437cacda8afbe88a901089f9478a53c16
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608610"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Přizpůsobení tlačítka pro moderní čtečku

Tento článek ukazuje, jak přizpůsobit tlačítko, které spouští moderní čtečku, aby vyhovovala potřebám vaší aplikace.

## <a name="add-the-immersive-reader-button"></a>Přidání tlačítka pro moderní čtečku

Sada moderní čtečka SDK nabízí výchozí styly pro tlačítko, které spouští moderní čtečku. `immersive-reader-button`Pro povolení tohoto stylu použijte atribut class.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Přizpůsobení stylu tlačítka

Použijte `data-button-style` atribut pro nastavení stylu tlačítka. Povolené hodnoty jsou `icon` , `text` a `iconAndText` . Výchozí hodnota je `icon`.

### <a name="icon-button"></a>Tlačítko ikony

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Vykreslí se následující:

![Toto je tlačítko vykresleného textu](./media/button-icon.png)

### <a name="text-button"></a>Tlačítko text

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Vykreslí se následující:

![Toto je tlačítko vykreslené moderní čtečky.](./media/button-text.png)

### <a name="icon-and-text-button"></a>Tlačítko ikona a text

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Vykreslí se následující:

![Tlačítko ikony](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Přizpůsobení textu tlačítka

Nakonfigurujte jazyk a alternativní text pro tlačítko pomocí `data-locale` atributu. Výchozím jazykem je angličtina.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Přizpůsobení velikosti ikony

Velikost ikony moderního čtečky se dá nakonfigurovat pomocí `data-icon-px-size` atributu. Tím se nastaví velikost ikony v pixelech. Výchozí velikost je 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [referenční materiály k sadě pro moderní čtečku](./reference.md)