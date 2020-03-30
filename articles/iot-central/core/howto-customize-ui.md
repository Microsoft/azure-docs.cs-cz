---
title: Přizpůsobení centrálního uhlavního operačního systému Azure | Dokumenty společnosti Microsoft
description: Jak přizpůsobit motiv a odkazy nápovědy pro centrální aplikaci Azure IoT
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 8f76f143b6c6a26b88b78e20d8d5d8ae1ae48553
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157975"
---
# <a name="customize-the-azure-iot-central-ui"></a>Přizpůsobení centrálního hlavního operačního centra Azure IoT

Tento článek popisuje, jak jako správce můžete přizpůsobit unové rozhraní aplikace použitím vlastních motivů a úpravou odkazů nápovědy tak, aby ukazovaly na vlastní prostředky nápovědy. 



Následující snímek obrazovky zobrazuje stránku pomocí standardního motivu:

![Motiv Standardní IoT – střed](./media/howto-customize-ui/standard-ui.png)

Následující snímek obrazovky zobrazuje stránku pomocí vlastního snímku obrazovky se zvýrazněnými přizpůsobenými prvky uživatelského rozhraní:

![Motiv Vlastní IoT –Střed](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Vytvořit motiv

Chcete-li vytvořit vlastní motiv, přejděte na stránku **Přizpůsobit aplikaci** v části **Správa:**

![Témata IoT Central](./media/howto-customize-ui/themes.png)

Na této stránce můžete přizpůsobit následující aspekty aplikace:

### <a name="application-logo"></a>Logo aplikace

Obrázek PNG, ne větší než 1 MB, s průhledným pozadím. Toto logo se zobrazuje vlevo na záhlaví aplikace IoT Central.

Pokud obrázek loga obsahuje název aplikace, můžete text názvu aplikace skrýt. Další informace naleznete v [tématu Správa aplikace](howto-administer.md#change-application-name-and-url).

### <a name="browser-icon-favicon"></a>Ikona prohlížeče (favicon)

Obrázek PNG, ne větší než 32 x 32 pixelů, s průhledným pozadím. Webový prohlížeč může tento obrázek použít na panelu Adresa, historii, záložkách a na kartě prohlížeče.

### <a name="browser-colors"></a>Barvy prohlížeče

Můžete změnit barvu záhlaví stránky a barvu použitou pro tlačítka zvýraznění a další světla. Ve formátu `##ff6347`použijte šestiznakovou šestnáctkovou hodnotu . Další informace o barevném zápisu **hodnoty HEX** naleznete v tématu [HTML Colors](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Na stránce **Přizpůsobit aplikaci** se můžete vždy vrátit k výchozím možnostem.

### <a name="changes-for-operators"></a>Změny pro operátory

Pokud správce vytvoří vlastní motiv, operátoři a ostatní uživatelé aplikace již nemohou vybrat motiv v **nastavení**.

## <a name="replace-help-links"></a>Nahradit odkazy nápovědy

Chcete-li svým operátorům a ostatním uživatelům poskytnout vlastní informace nápovědy, můžete upravit odkazy v nabídce **Nápověda** aplikace.

Chcete-li upravit odkazy nápovědy, přejděte na stránku **Přizpůsobit nápovědu** v části **Správa:**

![Přizpůsobení odkazů nápovědy služby IoT Central](./media/howto-customize-ui/help-links.png)

Do nabídky nápovědy můžete také přidat nové položky a odebrat výchozí položky:

![Přizpůsobená nápověda ioT central](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> Na stránce **Přizpůsobit nápovědu** se můžete vždy vrátit k výchozím odkazům nápovědy.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili přizpůsobit ui v aplikaci IoT Central, tady jsou některé navrhované další kroky:

- [Správa vaší aplikace](./howto-administer.md)
- [Přidání dlaždic na řídicí panel](howto-add-tiles-to-your-dashboard.md)