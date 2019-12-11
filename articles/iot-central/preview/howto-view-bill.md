---
title: Správa vyúčtování a převod zkušební verze na průběžné používání Azure IoT Central aplikace | Microsoft Docs
description: Jako správce se dozvíte, jak spravovat vaše vyúčtování a převod ze zkušební verze na průběžné používání aplikace IoT Central Azure.
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0c1c1643fb8c8394ce3ce50e5926f05fd4d366f6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976413"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Správa vyúčtování v aplikaci IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Tento článek popisuje, jak jako správce můžete spravovat vyúčtování v aplikaci Azure IoT Central v části Správa a také jak můžete převést zkušební verzi na průběžné platby.

Pokud chcete získat přístup k části **Správa** a používat ji, musíte být v roli **správce** aplikace Azure IoT Central. Pokud vytvoříte aplikaci IoT Central pro Azure, automaticky se přiřadíte k roli **správce** této aplikace.

## <a name="view-your-bill"></a>Zobrazení vašeho vyúčtování

Chcete-li zobrazit vyúčtování, v části **Správa** klikněte na stránku **fakturace** . Na nové kartě se otevře stránka fakturace Azure, kde uvidíte fakturu pro každou z vašich aplikací IoT Central Azure.

## <a name="convert-your-trial-to-pay-as-you-go"></a>Převod zkušební verze na průběžné platby

- **Zkušební verze** aplikací je zdarma po dobu sedmi dnů, než vyprší jejich platnost. Před vypršením platnosti je možné je kdykoli převést na průběžné platby.
- Aplikace s průběžnými **platbami** se účtují podle zařízení a prvních pět bezplatných zařízení na jedno předplatné.

Další informace o cenách najdete na [stránce s cenami za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

V části fakturace můžete svoje zkušební aplikace převést na průběžné platby.

K dokončení tohoto samoobslužného procesu použijte následující postup:

1. V části **Správa** přejdete na stránku **fakturace** .

    ![Stav zkušební verze](media/howto-view-bill/freetrialbilling.png)

1. Vyberte **převést na**průběžné platby.

    ![Převést zkušební verzi](media/howto-view-bill/convert.png)

1. Vyberte odpovídající Azure Active Directory a pak předplatné Azure, které chcete použít pro vaši aplikaci s průběžnými platbami.

1. Po výběru možnosti **převést**je vaše aplikace teď aplikací s průběžnými platbami a vy začnete s fakturací.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spravovat vaše vyúčtování v aplikaci Azure IoT Central, je doporučeným dalším krokem informace o [přizpůsobení uživatelského rozhraní aplikace](howto-customize-ui.md) ve službě Azure IoT Central.