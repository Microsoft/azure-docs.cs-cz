---
title: Přizpůsobení uživatelského rozhraní Azure IoT Central | Microsoft Docs
description: Jak přizpůsobit motiv a odkazy na nápovědu pro aplikaci Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3aed1b47e2bce2eda60b953e5a4c8d6d7ee5c59f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976260"
---
# <a name="customize-the-azure-iot-central-ui-preview-features"></a>Přizpůsobení uživatelského rozhraní Azure IoT Central (funkce ve verzi Preview)

Tento článek popisuje, jak jako správce můžete přizpůsobit uživatelské rozhraní aplikace pomocí vlastních motivů a úpravou odkazů na odkazy, které odkazují na vaše vlastní prostředky pro usnadnění. 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Následující snímek obrazovky ukazuje stránku používající standardní motiv:

![Motiv úrovně Standard IoT Central](./media/howto-customize-ui/standard-ui.png)

Následující snímek obrazovky ukazuje stránku s použitím vlastního snímku obrazovky se zvýrazněnými přizpůsobenými prvky uživatelského rozhraní:

![Vlastní motiv IoT Central](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Vytvořit motiv

Chcete-li vytvořit vlastní motiv, přejděte na stránku **přizpůsobit aplikaci** v části **Správa** :

![IoT Central motivů](./media/howto-customize-ui/themes.png)

Na této stránce můžete přizpůsobit následující aspekty aplikace:

### <a name="application-logo"></a>Logo aplikace

Obrázek PNG, který není větší než 1 MB, s průhledným pozadím. Toto logo se zobrazí vlevo v záhlaví aplikace IoT Central.

Pokud obrázek loga obsahuje název vaší aplikace, můžete skrýt text s názvem aplikace. Další informace najdete v tématu [Správa aplikace](./howto-administer.md#change-application-name-and-url).

### <a name="browser-icon-favicon"></a>Ikona prohlížeče (favicon)

Obrázek PNG, který není větší než 32 × 32 pixelů s průhledným pozadím. Webový prohlížeč může použít tento obrázek v adresním řádku, historii, záložkách a na kartě prohlížeče.

### <a name="browser-colors"></a>Barvy v prohlížeči

Můžete změnit barvu záhlaví stránky a barvu použitou pro tlačítka zvýraznění a další světla. Použijte hexadecimální hodnotu o šesti znacích ve formátu `##ff6347`. Další informace o zápisu barev **hexadecimální hodnoty** naleznete v tématu [HTML Colors](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Kdykoli se můžete vrátit k výchozím možnostem na stránce **přizpůsobit svoji aplikaci** .

### <a name="changes-for-operators"></a>Změny operátorů

Pokud správce vytvoří vlastní motiv, pak operátoři a další uživatelé vaší aplikace již nemohou vybrat motiv v **Nastavení**.

## <a name="replace-help-links"></a>Nahradit odkazy na Help

Chcete-li poskytnout vlastní informace o nápovědě vašim operátorům a dalším uživatelům, můžete upravit odkazy v nabídce **pomocníka** aplikace.

Chcete-li upravit odkazy v nápovědě, přejděte na stránku **přizpůsobení stránky Help** v části **Správa** :

![Přizpůsobení odkazů na IoT Central v nápovědě](./media/howto-customize-ui/help-links.png)

Můžete také přidat nové položky do nabídky Help a odebrat výchozí položky:

![Přizpůsobená IoT Central – Help](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> V případě, že se můžete vrátit zpátky na výchozí odkazy v nápovědě na stránce přizpůsobit stránku s **nastavením** .

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak přizpůsobit uživatelské rozhraní ve vaší aplikaci IoT Central, je zde několik navrhovaných dalších kroků:

- [Správa aplikace](./howto-administer.md)
- [Přidání dlaždic do řídicího panelu](../core/howto-add-tiles-to-your-dashboard.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)