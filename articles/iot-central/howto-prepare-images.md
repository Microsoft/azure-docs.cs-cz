---
title: Nahrávání obrázků do aplikace Azure IoT Central | Dokumentace Microsoftu
description: Jako tvůrce informace o přípravě a nahrávání imagí do aplikace Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: c8444fd57fe939dfb212d88c22a071c13dac691e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767324"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Přípravě a nahrávání imagí do aplikace Azure IoT Central

Tento článek popisuje, jak, jako tvůrce, můžete přizpůsobit aplikaci Azure IoT Central tak, že nahrajete vlastní Image. Například můžete přizpůsobit řídicí panel zařízení se obrázek zařízení.

## <a name="before-you-begin"></a>Před zahájením

K dokončení kroků v tomto článku budete potřebovat následující:

1. Aplikace Azure IoT Central. Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
1. Nástroj pro změnu měřítka a změna velikosti soubory s obrázky.

## <a name="choose-where-to-use-custom-images"></a>Rozhodnete, jak použít vlastní Image

Můžete přidat vlastní Image do následujícího umístění, v aplikaci Azure IoT Central:

* **Moje aplikace** stránky

    ![Obrázek na stránce správce aplikace](media/howto-prepare-images/applicationmanager.png)

* Řídicí panel aplikací

    ![Obrázek na řídicím panelu aplikací](media/howto-prepare-images/homepage.png)

* Šablona zařízení

    ![Image šablony zařízení](media/howto-prepare-images/devicetemplate.png)

* Dlaždice na řídicím panelu zařízení

    ![Na dlaždici dodržování Image](media/howto-prepare-images/devicetile.png)

* Dlaždice na řídicím panelu nastavení zařízení

    ![Obrázek na dlaždici nastavení zařízení](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>Příprava imagí

Ve všech čtyřech umístěních můžete použít obrázky PNG, ve formátu GIF nebo JPEG.

Následující tabulka shrnuje velikosti obrázků, které můžete použít:

| Umístění | Velikosti |
| -------- | ------ |
| Správce aplikací | 268x160 px |
| Šablona zařízení | 64x64 px |
| Dlaždice řídicího panelu | Nejmenší velikost dlaždice je 200 x 200 pixelů, větší dlaždice může být čtvercová nebo obdélníkový násobky malých dlaždic. Například 200 x 400 px, 400 x 200 pixelů nebo 400 x 400 px |

Nejlepšího zobrazení v aplikaci byste měli vytvořit Image, které odpovídají dimenze je znázorněno v předchozí tabulce.

## <a name="upload-the-images"></a>Nahrání Image

Následující části popisují, jak nahrávat obrázky v různých umístěních:

### <a name="application-manager"></a>Správce aplikací

Nahrát obrázek pro použití na **Moje aplikace** stránce, přejděte na **nastavení aplikace** stránku **správu** části. Musíte být správcem a dokončete tuto úlohu:

![Nahrání image aplikací](media/howto-prepare-images/uploadapplicationmanager.png)

Výběrem dané dlaždice aplikace Image nahrajete svou image připravená (268 x 160 px) z místního počítače.

### <a name="application-dashboard"></a>Řídicí panel aplikací

K nahrání obrázku na řídicí panel aplikací, přejděte **řídicí panel** stránky vaší aplikace a vyberte **upravit**. Musí být Tvůrce k dokončení této úlohy:

![Nahrajte obrázek řídicího panelu](media/howto-prepare-images/uploadhomepage.png)

V části Konfigurace Image vyberte dlaždici s obrázkem nahrát připravenou image z místního počítače. Nejmenší velikost dlaždice je 200 x 200 pixelů, větší dlaždice může být čtvercová nebo obdélníkový násobky malých dlaždic. Například 200 x 400 px, 400 x 200 pixelů nebo 400 x 400 px.

**Uložit** nahraný obrázek. Změnit velikost je v režimu úprav. Vyberte **provádí** po dokončení.

### <a name="device-template"></a>Šablona zařízení

K nahrání obrázku do šablony zařízení, přejděte na **šablon** a výběr šablony funkce zařízení. Musí být Tvůrce k dokončení této úlohy:

![Nahrání image šablony zařízení](media/howto-prepare-images/uploaddevicetemplate.png)

Vyberte dlaždici s obrázkem do nahrajete svou image připravená (64 × 64 pixelů) z místního počítače.

### <a name="device-dashboard"></a>Řídicí panel zařízení

K nahrání obrázku na řídicí panel zařízení, přejděte na **šablon** a výběr šablony funkce zařízení. Klikněte na tlačítko **řídicí panel** kartu. Musí být Tvůrce k dokončení této úlohy:

![Nahrajte obrázek řídicího panelu zařízení](media/howto-prepare-images/uploaddevicedashboard.png)

V části Konfigurace Image vyberte dlaždici s obrázkem a pak zvolte soubor k odeslání z místního počítače. Nejmenší velikost dlaždice je 200 x 200 pixelů, větší dlaždice může být čtvercová nebo obdélníkový násobky malých dlaždic. Například 200 x 400 px, 400 x 200 pixelů nebo 400 x 400 px.

**Uložit** nahraný obrázek. Můžete změnit velikost a umístění v režimu úprav. Vyberte **provádí** po dokončení.

### <a name="device-set-dashboard"></a>Zařízení nastavit řídicí panel

K nahrání obrázku na řídicí panel sady zařízení, přejděte na **sady zařízení** a zvolte sadu zařízení a zařízení. Klikněte na tlačítko **řídicí panel** stránku a vybrat **upravit**:

![Nahrajte obrázek řídicího panelu nastavení zařízení](media/howto-prepare-images/uploaddevicesetdashboard.png)

V části Konfigurace Image vyberte dlaždici s obrázkem nahrát připravenou image z místního počítače. Nejmenší velikost dlaždice je 200 x 200 pixelů, větší dlaždice může být čtvercová nebo obdélníkový násobky malých dlaždic. Například 200 x 400 px, 400 x 200 pixelů nebo 400 x 400 px.

**Uložit** nahraný obrázek. Můžete změnit velikost a umístění v režimu úprav. Vyberte **provádí** po dokončení.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak připravit a nahrávání imagí do aplikace Azure IoT Central, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Správa zařízení v aplikaci Azure IoT Central](howto-manage-devices.md)