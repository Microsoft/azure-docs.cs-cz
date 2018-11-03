---
title: Nahrávání obrázků do aplikace Azure IoT Central | Dokumentace Microsoftu
description: Jako tvůrce informace o přípravě a nahrávání imagí do aplikace Azure IoT Central.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 18c44a3d91a4964d054c8e142394da7d69772ed0
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50960697"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Přípravě a nahrávání imagí do aplikace Azure IoT Central

Tento článek popisuje, jak, jako tvůrce, můžete přizpůsobit aplikaci Microsoft Azure IoT Central tak, že nahrajete vlastní Image. Například můžete přizpůsobit řídicí panel zařízení se obrázek zařízení.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující:

1. Aplikace Azure IoT Central. Další informace najdete v tématu [vytvořit aplikaci rychlý Start](quick-deploy-iot-central.md).
1. Nástroj pro změnu měřítka a změna velikosti soubory s obrázky.

## <a name="choose-where-to-use-custom-images"></a>Rozhodnete, jak použít vlastní Image

Můžete přidat vlastní Image do následujícího umístění, v aplikaci Azure IoT Central:

* **Správce aplikací** stránky

    ![Obrázek na stránce správce aplikace](media/howto-prepare-images/applicationmanager.png)

* Na domovské stránce

    ![Obrázek na domovské stránce](media/howto-prepare-images/homepage.png)

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
| **Správce aplikací** | 268 x 160 px |
| Šablona zařízení | 64 × 64 pixelů |
| Domovská stránka a dlaždice řídicího panelu | Nejmenší velikost dlaždice je 200 x 200 pixelů, větší dlaždice může být čtvercová nebo obdélníkový násobky malých dlaždic. Například 200 x 400 px, 400 x 200 pixelů nebo 400 x 400 px |

Nejlepšího zobrazení v aplikaci byste měli vytvořit Image, které odpovídají dimenze je znázorněno v předchozí tabulce.

## <a name="upload-the-images"></a>Nahrání Image

Následující části popisují, jak nahrát Image na použití v různých umístěních:

### <a name="application-manager"></a>Správce aplikací

Nahrát obrázek pro použití na **Správce aplikací**, přejděte na **nastavení aplikace** stránku **správu** oddílu. Musíte být správcem a dokončete tuto úlohu:

![Nahrání image aplikací](media/howto-prepare-images/uploadapplicationmanager.png)

Kliknutím na nahrát obrázek a pak zvolte soubor k nahrání z místního počítače.

### <a name="home-page"></a>Domovská stránka

Pokud chcete nahrát obrázek pro použití na domovské stránce, přejděte na **domovské stránky** vaší aplikace a přepínač režimu návrhu na. Musí být Tvůrce k dokončení této úlohy:

![Nahrajte obrázek domovské stránky](media/howto-prepare-images/uploadhomepage.png)

Kliknutím na nahrát obrázek a pak zvolte soubor k nahrání z místního počítače.

Po nahrání obrázku velikosti při přepnutí režim návrhu.

### <a name="device-template"></a>Šablona zařízení

Pokud chcete nahrát obrázek, který použijete v šabloně zařízení, přejděte na **Device Explorer**, zvolte šablonu zařízení a pak zařízení a přepnout režim návrhu. Musí být Tvůrce k dokončení této úlohy:

![Nahrání image šablony zařízení](media/howto-prepare-images/uploaddevicetemplate.png)

Kliknutím na nahrát obrázek a pak zvolte soubor k nahrání z místního počítače.

### <a name="device-dashboard"></a>Řídicí panel zařízení

K nahrání obrázku na řídicí panel zařízení používat, přejděte na **Device Explorer**, zvolte šablonu zařízení a zařízení. Klikněte na tlačítko **řídicí panel** na stránku a přepínač režimu návrhu. Musí být Tvůrce k dokončení této úlohy:

![Nahrajte obrázek řídicího panelu zařízení](media/howto-prepare-images/uploaddevicedashboard.png)

Kliknutím na nahrát obrázek a pak zvolte soubor k nahrání z místního počítače.

Po nahrání image, můžete změnit velikost a umístění při **režimu návrhu** je zapnuté.

### <a name="device-set-dashboard"></a>Zařízení nastavit řídicí panel

Pokud chcete nahrát obrázek pro použití na zařízení nastavit řídicí panel, přejděte na **sady zařízení** a zvolte sadu zařízení a zařízení. Klikněte na tlačítko **řídicí panel** stránky a přepínač **režimu návrhu** na:

![Nahrajte obrázek řídicího panelu nastavení zařízení](media/howto-prepare-images/uploaddevicesetdashboard.png)

Kliknutím na nahrát obrázek a pak zvolte soubor k nahrání z místního počítače.

Po nahrání image můžete změnit velikost a umístění, zatímco byl zapnut režim návrhu.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak připravit a nahrávání imagí do aplikace Azure IoT Central, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Správa zařízení v aplikaci Azure IoT Central](howto-manage-devices.md)