---
title: Nahrajte Image do aplikace Azure IoT centrální | Microsoft Docs
description: Jako tvůrce zjistěte, jak připravit a odeslat do aplikace Azure IoT centrální bitové kopie.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: a43f2dd780604235ada1d8e3ae8a20563042fbaa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Příprava a odeslat do aplikace Azure IoT centrální bitové kopie

Tento článek popisuje, jak, jako tvůrce, můžete přizpůsobit aplikace Microsoft Azure IoT centrální tím, že nahrajete vlastních bitových kopií. Například můžete přizpůsobit řídicí panel zařízení se obrázek zařízení.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující:

1. Azure IoT centrální aplikaci. Další informace najdete v tématu [vytvořit aplikaci Azure IoT centrální](howto-create-application.md).
1. Nástroj pro škálování a změna velikosti souborů bitové kopie.

## <a name="choose-where-to-use-custom-images"></a>Vybrat místo pro použití vlastních bitových kopií

Můžete přidat vlastní Image do následujícího umístění v Azure IoT centrální aplikaci:

* **Správce aplikací** stránky

    ![Bitové kopie na stránku správce aplikace](media/howto-prepare-images/applicationmanager.png)

* Domovská stránka

    ![Bitové kopie na domovské stránce](media/howto-prepare-images/homepage.png)

* Šablona zařízení

    ![Bitové kopie v šabloně zařízení](media/howto-prepare-images/devicetemplate.png)

* Na dlaždici na řídicím panelu zařízení

    ![Bitové kopie na dlaždici zařízení](media/howto-prepare-images/devicetile.png)

* Na dlaždici na řídicím panelu sadu zařízení.

    ![Bitové kopie na dlaždici sada zařízení](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>Příprava bitové kopie

Ve všech umístěních čtyři můžete použít PNG, GIF nebo JPEG bitové kopie.

Následující tabulka shrnuje velikosti obrázků, které můžete použít:

| Umístění | Velikosti |
| -------- | ------ |
| **Správce aplikací** | 268 x 160 px |
| Šablona zařízení | 64 x 64 px |
| Domovská stránka a dlaždice řídicího panelu | Nejmenší velikostí dlaždice je 200 x 200 px, větší dlaždice může být čtverce nebo obdélníku násobky malé dlaždic. Například 200 x 400 px, 400 x 200 px nebo 400 × 400 px |

Pro nejlepší zobrazení v aplikaci byste měli vytvořit bitové kopie, které odpovídají dimenze uvedené v předchozí tabulce.

## <a name="upload-the-images"></a>Nahrajte Image

Následující části popisují, jak odesílat obrázky, které chcete použít v různých umístěních:

### <a name="application-manager"></a>Správce aplikací

Nahrát obrázek pro použití na **Správce aplikací**, přejděte na **nastavení aplikace** stránku **správy** části. Musí být správce tuto úlohu dokončit:

![Nahrajte image aplikace](media/howto-prepare-images/uploadapplicationmanager.png)

Kliknutím na obrázek odesílání a potom zvolte soubor k odeslání z místního počítače.

### <a name="home-page"></a>Domovská stránka

Pokud chcete uložit obrázek pro použití na domovské stránce, přejděte na **domovské stránky** vaší aplikace a režimu návrhu přepínač na. Musí být Tvůrce tuto úlohu dokončit:

![Nahrát obrázek domovské stránky](media/howto-prepare-images/uploadhomepage.png)

Kliknutím na obrázek odesílání a potom zvolte soubor k odeslání z místního počítače.

Po odešle obrázek, můžete jeho velikost při režimu návrhu je zapnutá.

### <a name="device-template"></a>Šablona zařízení

Chcete-li nahrát obrázek pro použití na šabloně pro zařízení, přejděte na **Explorer zařízení**, vyberte šablonu zařízení a klepněte na zařízení a přepnout režim návrhu. Musí být Tvůrce tuto úlohu dokončit:

![Nahrajte image šablony zařízení](media/howto-prepare-images/uploaddevicetemplate.png)

Kliknutím na obrázek odesílání a potom zvolte soubor k odeslání z místního počítače.

### <a name="device-dashboard"></a>Řídicí panel zařízení

Pokud chcete uložit obrázek pro použití na řídicím panelu zařízení, přejděte na **Explorer zařízení**, zvolte šablonu zařízení a zařízení. Zvolte **řídicí panel** na stránku a přepínače režimu návrhu. Musí být Tvůrce tuto úlohu dokončit:

![Nahrát řídicí panel obrázku zařízení](media/howto-prepare-images/uploaddevicedashboard.png)

Kliknutím na obrázek odesílání a potom zvolte soubor k odeslání z místního počítače.

Po odešle obrázek, můžete změnit velikost a změňte umístění při **režimu návrhu** je zapnutá.

### <a name="device-set-dashboard"></a>Zařízení nastavit řídicí panel

Chcete-li nahrát obrázek pro použití na řídicím panelu sady zařízení, přejděte na **sady zařízení** a vyberte sadu zařízení a zařízení. Zvolte **řídicí panel** stránky a přepínače **režimu návrhu** na:

![Nahrát obrázku zařízení sada řídicího panelu](media/howto-prepare-images/uploaddevicesetdashboard.png)

Kliknutím na obrázek odesílání a potom zvolte soubor k odeslání z místního počítače.

Po odešle obrázek, můžete změnit velikost a změnit umístění, zatímco je zapnutá režimu návrhu.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili jak připravit a odeslat do aplikace Azure IoT centrální bitové kopie, zde je navržené další krok:

> [!div class="nextstepaction"]
> [Spravovat zařízení v aplikaci Azure IoT centrální](howto-manage-devices.md)