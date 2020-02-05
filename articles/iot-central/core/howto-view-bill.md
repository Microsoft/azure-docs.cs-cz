---
title: Spravujte svůj účet a převeďte ho z bezplatného cenového plánu v Azure IoT Central aplikaci | Microsoft Docs
description: Jako správce se dozvíte, jak spravovat fakturu a přejít od bezplatného cenového plánu ke standardnímu cenovému plánu ve vaší aplikaci Azure IoT Central.
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: e45109842a081b437c20de81321bf70f909efa2a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982335"
---
# <a name="view-your-bill-in-iot-central-application"></a>Zobrazit vaše vyúčtování v IoT Central aplikaci

Tento článek popisuje, jak jako správce můžete spravovat vyúčtování v aplikaci Azure IoT Central v části Správa. Naučíte se, jak můžete přesunout svoji aplikaci od bezplatného cenového plánu do standardního cenového plánu a také jak upgradovat nebo downgradovat Cenový tarif.

Pokud chcete získat přístup k části pro **správu** a používat ji, musíte být v roli *správce* nebo mít *vlastní roli uživatele* , která vám umožní zobrazit fakturaci pro aplikaci IoT Central Azure. Pokud vytvoříte aplikaci IoT Central pro Azure, automaticky se přiřadíte k roli **správce** této aplikace.

## <a name="move-from-free-to-standard-pricing-plan"></a>Přejít od bezplatného cenového plánu na standardní

- Aplikace, které používají bezplatný cenový plán, jsou po dobu sedmi dnů zdarma. Aby nedošlo ke ztrátě dat, můžete je kdykoli přesunout do standardního cenového plánu.

V části s cenami můžete svou aplikaci přesunout od bezplatného ke standardnímu cenovému plánu.

K dokončení tohoto samoobslužného procesu použijte následující postup:

1. V části **Správa** přejdete na stránku s **cenami** .

    ![Stav zkušební verze](media/howto-administer/freetrialbilling.png)

1. Vyberte **převést na placený plán**.

    ![Převést zkušební verzi](media/howto-administer/convert.png)

1. Vyberte odpovídající Azure Active Directory a pak předplatné Azure, které chcete použít pro vaši aplikaci, která používá placený plán.

1. Po výběru **převést**vaše aplikace teď používá placený plán a vy začnete s fakturací.

> [!Note]
> Ve výchozím nastavení budete převedeni na cenový tarif *Standard 2* .

## <a name="how-to-change-your-application-pricing-plan"></a>Postup změny cenového plánu aplikace

- Aplikace, které používají standardní cenový plán, se účtují podle zařízení, přičemž první dvě zařízení jsou zdarma na jednu aplikaci.

V části s cenami můžete kdykoli upgradovat nebo downgradovat cenové tarify Azure IoT.

1. V části **Správa** přejdete na stránku s **cenami** .

    ![Stav zkušební verze](media/howto-administer/pricing.png)

1. Vyberte **plán** a kliknutím na **Uložit** proveďte upgrade nebo downgrade.

Další informace o cenách najdete na [stránce s cenami za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="view-your-bill"></a>Zobrazení vašeho vyúčtování

Pokud chcete zobrazit fakturu, na stránce s **cenami** v části **Správa** použijte. Na nové kartě se otevře stránka s cenami Azure, kde uvidíte fakturu za každou z vašich aplikací IoT Central Azure.


## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak zobrazit vyúčtování v aplikaci Azure IoT Central, je doporučeným dalším krokem informace o [přizpůsobení uživatelského rozhraní aplikace](howto-customize-ui.md) ve službě Azure IoT Central.