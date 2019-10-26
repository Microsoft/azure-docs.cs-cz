---
title: Nahrávání imagí do aplikace Azure IoT Central | Microsoft Docs
description: Jako tvůrce se naučíte, jak připravit a nahrávat obrázky do aplikace Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: d8a863ee971fa154740f3726034fc4604b02fd34
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949921"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Příprava a nahrání imagí do aplikace Azure IoT Central

Tento článek popisuje, jak jako tvůrce můžete přizpůsobit aplikaci Azure IoT Central tím, že nahrajete vlastní image. Můžete například přizpůsobit řídicí panel zařízení pomocí obrázku zařízení.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující:

1. Aplikace Azure IoT Central. Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
1. Nástroj pro škálování a změnu velikosti souborů obrázků.

## <a name="choose-where-to-use-custom-images"></a>Zvolit, kde se mají používat vlastní image

Vlastní image můžete přidat do následujících umístění v aplikaci Azure IoT Central:

* Stránka **Moje aplikace**

    ![Obrázek na stránce Správce aplikací](media/howto-prepare-images/applicationmanager.png)

* Řídicí panel aplikace

    ![Obrázek na řídicím panelu aplikace](media/howto-prepare-images/homepage.png)

* Šablona zařízení

    ![Obrázek v šabloně zařízení](media/howto-prepare-images/devicetemplate.png)

* Dlaždice na řídicím panelu zařízení

    ![Obrázek na dlaždici zařízení](media/howto-prepare-images/devicetile.png)

* Dlaždice na řídicím panelu pro sadu zařízení

    ![Dlaždice obrázek v sadě zařízení](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>Příprava imagí

Ve všech čtyřech umístěních můžete použít obrázky PNG, GIF nebo JPEG.

Následující tabulka shrnuje velikosti imagí, které můžete použít:

| Umístění | Velikosti |
| -------- | ------ |
| Správce aplikací | 268x160 px |
| Šablona zařízení | 64 × 64 px |
| Dlaždice řídicího panelu | Dlaždice nejmenší velikosti je 200x200 px, větší dlaždice můžou být buď čtvercové, nebo obdélníkové násobky malých dlaždic. Například 200x400 px, 400x200 px nebo 400 × 400 px |

Pro nejlepší zobrazení v aplikaci byste měli vytvořit obrázky, které odpovídají dimenzím uvedeným v předchozí tabulce.

## <a name="upload-the-images"></a>Nahrání imagí

Následující části popisují, jak nahrát obrázky v různých umístěních:

### <a name="application-manager"></a>Správce aplikací

Pokud chcete nahrát obrázek, který se má použít na stránce **Moje aplikace** , přejděte na stránku **nastavení aplikace** v části **Správa** . K dokončení této úlohy musíte být správcem:

![Nahrát obrázek aplikace](media/howto-prepare-images/uploadapplicationmanager.png)

Výběrem dlaždice **Obrázek aplikace** Nahrajte obrázek (268x160 px) z místního počítače.

### <a name="application-dashboard"></a>Řídicí panel aplikací

Pokud chcete nahrát obrázek na řídicím panelu aplikace, přejděte na stránku na **řídicím panelu** aplikace a vyberte **Upravit**. K dokončení této úlohy musíte být tvůrce:

![Odeslat obrázek řídicího panelu](media/howto-prepare-images/uploadhomepage.png)

V části **Konfigurovat image**vyberte dlaždici **Obrázek** a nahrajte image z místního počítače. Dlaždice nejmenší velikosti je 200x200 px, větší dlaždice můžou být buď čtvercové, nebo obdélníkové násobky malých dlaždic. Například 200x400 px, 400x200 px nebo 400 × 400 px.

**Uložte** nahraný obrázek. Velikost můžete změnit v režimu úprav. Po dokončení vyberte **Hotovo** .

### <a name="device-template"></a>Šablona zařízení

Chcete-li nahrát obrázek do šablony zařízení, přejděte na **šablony zařízení** a vyberte šablonu zařízení. K dokončení této úlohy musíte být tvůrce:

![Odeslat obrázek šablony zařízení](media/howto-prepare-images/uploaddevicetemplate.png)

Výběrem dlaždice obrázek Nahrajte obrázek (64 × 64 px) z místního počítače.

### <a name="device-dashboard"></a>Řídicí panel zařízení

Pokud chcete nahrát obrázek na řídicí panel zařízení, přejděte na **šablony zařízení** a vyberte šablonu zařízení. Pak zvolte kartu **řídicí panel** . K dokončení této úlohy musíte být tvůrce:

![Odeslat obrázek řídicího panelu zařízení](media/howto-prepare-images/uploaddevicedashboard.png)

V části **Konfigurovat obrázek**vyberte dlaždici **Obrázek** a pak zvolte soubor, který se má nahrát z místního počítače. Dlaždice nejmenší velikosti je 200x200 px, větší dlaždice můžou být buď čtvercové, nebo obdélníkové násobky malých dlaždic. Například 200x400 px, 400x200 px nebo 400 × 400 px.

**Uložte** nahraný obrázek. V režimu úprav můžete změnit velikost a umístění. Po dokončení vyberte **Hotovo** .

### <a name="device-set-dashboard"></a>Řídicí panel pro nastavení zařízení

Pokud chcete nahrát image na řídicím panelu pro nastavení zařízení, přejděte na **sady zařízení** a zvolte sadu zařízení a pak na zařízení. Pak zvolte stránku **řídicí panel** a vyberte **Upravit**:

![Odeslat obrázek řídicího panelu pro nastavení zařízení](media/howto-prepare-images/uploaddevicesetdashboard.png)

V části **Konfigurovat image**vyberte dlaždici **Obrázek** a nahrajte image z místního počítače. Dlaždice nejmenší velikosti je 200x200 px, větší dlaždice můžou být buď čtvercové, nebo obdélníkové násobky malých dlaždic. Například 200x400 px, 400x200 px nebo 400 × 400 px.

**Uložte** nahraný obrázek. V režimu úprav můžete změnit velikost a umístění. Po dokončení vyberte **Hotovo** .

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak připravit a nahrávat image do aplikace IoT Central v Azure, najdete tady Doporučené další kroky:

* [Přizpůsobení uživatelského rozhraní Azure IoT Central](./howto-customize-ui.md)
* [Přidání dlaždic do řídicího panelu](./howto-add-tiles-to-your-dashboard.md)
* [Správa zařízení v aplikaci Azure IoT Central](howto-manage-devices.md)
