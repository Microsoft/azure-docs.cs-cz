---
title: Správa faktury a převod z bezplatného cenového plánu v aplikaci Azure IoT Central | Dokumenty společnosti Microsoft
description: Jako správce se dozvíte, jak spravovat fakturu a přejít od bezplatného cenového plánu ke standardnímu cenovému plánu v aplikaci Azure IoT Central.
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 888b9f31b8082ac124a773cc489293b335d7d255
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157491"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Správa faktury v aplikaci IoT Central

Tento článek popisuje, jak jako správce můžete spravovat účet v aplikaci Azure IoT Central v části správa. Dozvíte se, jak můžete přesunout aplikaci z bezplatného cenového plánu do standardního cenového plánu a také jak upgradovat nebo snížit úroveň cenového plánu.

Chcete-li získat přístup a používat část **Správa,** musíte být v roli *správce* nebo mít *vlastní roli uživatele,* která umožňuje zobrazit fakturaci pro aplikaci Azure IoT Central. Pokud vytvoříte aplikaci Azure IoT Central, automaticky se přiřadíte k roli **správce** pro tuto aplikaci.

## <a name="move-from-free-to-standard-pricing-plan"></a>Přechod z bezplatného na standardní cenový plán

- Aplikace, které používají bezplatný cenový plán, jsou zdarma po dobu sedmi dnů před vypršením jejich platnosti. Abyste se vyhnuli ztrátě dat, můžete je kdykoli před vypršením jejich platnosti přesunout do standardního cenového plánu.
- Aplikace, které používají standardní cenový plán, se účtují na jedno zařízení, přičemž první dvě zařízení jsou zdarma.

Další informace o cenách najdete na [stránce s cenami za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

V cenové části můžete aplikaci přesunout z bezplatného tarifu do standardního cenového plánu.

Chcete-li dokončit tento samoobslužný proces, postupujte takto:

1. Přejděte na stránku **Ceny** v části **Správa.**

    ![Zkušební stav](media/howto-view-bill/freetrialbilling.png)

1. Vyberte **Převést na placený plán**.

    ![Převést zkušební verzi](media/howto-view-bill/convert.png)

1. Vyberte příslušnou službu Azure Active Directory a pak předplatné Azure, které se má použít pro vaši aplikaci, která používá placený plán.

1. Po výběru **možnosti Převést**aplikace nyní používá placený plán a začne se vám účtovat.

> [!Note]
> Ve výchozím nastavení jste převedeni na *tarif standard 2.*

## <a name="how-to-change-your-application-pricing-plan"></a>Jak změnit cenový plán aplikace

Aplikace, které používají standardní cenový plán, se účtují na jedno zařízení, přičemž první dvě zařízení jsou zdarma.

V cenové části můžete kdykoli upgradovat nebo snížit úroveň cenový plán Azure IoT.

1. Přejděte na stránku **Ceny** v části **Správa.**

    ![Zkušební stav](media/howto-view-bill/pricing.png)

1. Vyberte **plán** a kliknutím na **Uložit** upgradujte nebo downgrade.

## <a name="view-your-bill"></a>Zobrazení vašeho vyúčtování

1. Vyberte příslušnou službu Azure Active Directory a pak předplatné Azure, které se má použít pro vaši aplikaci, která používá placený plán.

1. Po výběru **možnosti Převést**aplikace nyní používá placený plán a začne se vám účtovat.

> [!Note]
> Ve výchozím nastavení jste převedeni na *tarif standard 2.*

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o tom, jak spravovat účet v aplikaci Azure IoT Central, je dalším doporučeným krokem informace o [vlastním unovém uzlu aplikace](howto-customize-ui.md) v Azure IoT Central.