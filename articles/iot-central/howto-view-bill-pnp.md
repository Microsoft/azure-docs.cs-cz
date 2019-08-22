---
title: Zobrazení faktury a převedení zkušební verze na průběžné používání Azure IoT Central aplikace | Microsoft Docs
description: Jako správce se dozvíte, jak zobrazit vyúčtování a převést ze zkušební verze na průběžné používání aplikace IoT Central Azure.
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: d27288718b7f224c78c9cf1f15d05198f628fd15
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879640"
---
# <a name="view-your-bill-in-an-iot-central-application"></a>Zobrazení faktury v IoT Central aplikaci

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Tento článek popisuje, jak jako správce můžete zobrazit vyúčtování v aplikaci Azure IoT Central v části Správa a také jak můžete převést zkušební verzi na průběžné platby.

Pokud chcete získat přístup k části **Správa** a používat ji, musíte být v roli **správce** aplikace Azure IoT Central. Pokud vytvoříte aplikaci IoT Central pro Azure, automaticky se přiřadíte k roli **správce** této aplikace.

## <a name="view-your-bill"></a>Zobrazení vašeho vyúčtování

Chcete-li zobrazit vyúčtování, v části **Správa** klikněte na stránku **fakturace** . Na nové kartě se otevře stránka fakturace Azure, kde uvidíte fakturu pro každou z vašich aplikací IoT Central Azure.

## <a name="convert-your-trial-to-pay-as-you-go"></a>Převod zkušební verze na průběžné platby

- **Zkušební verze** aplikací je zdarma po dobu sedmi dnů, než vyprší jejich platnost. Před vypršením platnosti je možné je kdykoli převést na průběžné platby.
- Aplikace s průběžnými platbami se účtují podle zařízení a prvních pět bezplatných zařízení na jedno předplatné.

Další informace o cenách najdete na [stránce s cenami za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

V části fakturace můžete svoje zkušební aplikace převést na průběžné platby.

K dokončení tohoto samoobslužného procesu použijte následující postup:

1. V části **Správa** přejdete na stránku **fakturace** .

    ![Stav zkušební verze](media/howto-view-bill-pnp/freetrialbilling.png)

1. Vyberte **převést na**průběžné platby.

    ![Převést zkušební verzi](media/howto-view-bill-pnp/convert.png)

1. Vyberte odpovídající Azure Active Directory a pak předplatné Azure, které chcete použít pro vaši aplikaci s průběžnými platbami.

1. Po výběru možnosti **převést**je vaše aplikace teď aplikací s průběžnými platbami a vy začnete s fakturací.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak zobrazit vyúčtování v aplikaci Azure IoT Central, je doporučeným dalším krokem informace o [přizpůsobení uživatelského rozhraní aplikace](howto-customize-ui-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) ve službě Azure IoT Central.