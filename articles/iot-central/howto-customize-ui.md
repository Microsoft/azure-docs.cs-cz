---
title: Přizpůsobení uživatelského rozhraní Azure IoT Central | Dokumentace Microsoftu
description: Přizpůsobení motivu a pomáhají odkazy aplikace Azure IoT central
author: dominicbetts
ms.author: dobett
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0256396cd228898f3852772b113e6064a0656746
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237654"
---
# <a name="customize-the-azure-iot-central-ui"></a>Přizpůsobení uživatelského rozhraní Azure IoT Central 

*Tento článek se týká správcům.*

IoT Central umožňuje přizpůsobit uživatelské rozhraní vaší aplikace tak, že použití vlastní motivy a úprava odkazy nápovědy tak, aby odkazoval na prostředky nápovědy vlastní. Na následujícím snímku obrazovky se zobrazenou stránkou pomocí standardní motivu:

![Standardní motiv IoT Central](./media/howto-customize-ui/standard-ui.png)

Na následujícím snímku obrazovky se zobrazenou stránkou pomocí vlastní snímek obrazovky se zvýrazněnými vlastní elementy uživatelského rozhraní:

![Vlastní motiv centrální IoT](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Vytvořit motiv

Vytvořte vlastní motiv, přejděte na **upravit aplikaci** stránku **správu** části:

![Motivy IoT Central](./media/howto-customize-ui/themes.png)

Na této stránce si můžete přizpůsobit následující aspekty vaší aplikace:

### <a name="application-logo"></a>Logo aplikace

Obrázek ve formátu PNG, nesmí být větší než 1 MB, s průhledným pozadím. Toto logo se zobrazí na levé straně v záhlaví okna aplikace IoT Central.

Pokud vaše logo image obsahuje název vaší aplikace, můžete skrýt text názvu aplikace. Další informace najdete v tématu [spravovat nastavení aplikace](./howto-administer.md#manage-application-settings).

### <a name="browser-icon-favicon"></a>Ikona prohlížeče (favicon)

Obrázek ve formátu PNG, nesmí být větší než 32 x 32 pixelů s průhledným pozadím. Webový prohlížeč, můžete použít tuto bitovou kopii do adresního řádku, historie, záložek a kartu prohlížeče.

### <a name="browser-colors"></a>Barvy prohlížeče

Můžete změnit barvu záhlaví stránky a barvu použitou pro accenting tlačítka a další vylepšení. Použít barvu hexadecimálně hodnotu šest znaků ve formátu `##ff6347`. Další informace o **HEXADECIMÁLNÍ hodnotu** barva zápisu, naleznete v tématu [HTML barvy](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Můžete se vždycky vrátit zpátky na výchozí možnosti na **upravit aplikaci** stránky.

### <a name="changes-for-operators"></a>Změny pro operátory

Pokud správce vytvoří vlastní motiv a potom operátory a ostatní uživatelé vaší aplikace můžete už zvolte jen motiv v **nastavení**.

## <a name="replace-help-links"></a>Nahradit odkazy nápovědy

K poskytnutí informací o vlastní operátory a dalším uživatelům, můžete upravit odkazy na aplikace **pomáhají** nabídky.

Upravit odkazy nápovědy, přejděte na **přizpůsobit nápovědy** stránku **správu** části:

![Upravit odkazy nápovědy IoT Central](./media/howto-customize-ui/help-links.png)

Můžete také přidat nové položky do nabídky Nápověda, odeberte výchozí položky:

![Vlastní nápověda IoT Central](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> Můžete se vždycky vrátit zpátky na výchozí odkazy nápovědy na **přizpůsobit nápovědy** stránky.

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak přizpůsobit uživatelské rozhraní aplikace IoT Central, tady jsou některé další navrhované kroky:

> [!div class="nextstepaction"]
> [Spravovat aplikaci](./howto-administer.md)
> [nakonfigurovat řídicí panel aplikací](./howto-configure-homepage.md)