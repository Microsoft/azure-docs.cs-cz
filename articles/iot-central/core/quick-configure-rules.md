---
title: Úvodní příručka – konfigurace pravidel a akcí v Azure IoT Central
description: Tento rychlý start ukazuje, jako tvůrce, jak nakonfigurovat pravidla a akce založené na telemetrii ve vaší aplikaci Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 4bbf571d6b73a6f43c1c3b1ce261da6963a74183
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77169480"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Úvodní příručka: Konfigurace pravidel a akcí pro vaše zařízení v Azure IoT Central

*Tento článek je pro operátory, tvůrce a správce.*

V tomto rychlém startu vytvoříte pravidlo, které odešle e-mail, když&deg; teplota nahlášená senzorem zařízení překročí 90 F.

## <a name="prerequisites"></a>Požadavky

Než začnete, měli byste dokončit dva předchozí rychlé starty [Vytvořte aplikaci Azure IoT Central](./quick-deploy-iot-central.md) a [přidejte simulované zařízení do aplikace IoT Central](./quick-create-pnp-device.md) a vytvořte šablonu zařízení **MXChip IoT DevKit,** se kterou chcete pracovat.

## <a name="create-a-telemetry-based-rule"></a>Vytvoření pravidla založeného na telemetrii

1. Pokud chcete do aplikace přidat nové pravidlo založené na telemetrii, v levém podokně vyberte **Pravidla**.

1. Chcete-li vytvořit nové **+** pravidlo, vyberte možnost .

1. Jako název pravidla zadejte **Teplotu prostředí.**

1. V části **Cílová zařízení** vyberte jako šablonu zařízení **MXChip IoT DevKit.** Tato možnost filtruje zařízení, na která se pravidlo vztahuje podle typu šablony zařízení. Další kritéria filtru můžete přidat výběrem možnosti **+ Filtr**.

1. V části **Podmínky** definujete, co spustí vaše pravidlo. Následující informace slouží k definování podmínky na základě teplotní telemetrie:

    | Pole        | Hodnota            |
    | ------------ | ---------------- |
    | Měření  | Teplota      |
    | Operátor     | je větší než  |
    | Hodnota        | 90               |

    Chcete-li přidat další podmínky, vyberte **možnost + Podmínka**.

    ![Vytvořit podmínku pravidla](./media/quick-configure-rules/condition.png)

1. Chcete-li přidat akci e-mailu, která se spustí, když se pravidlo aktivuje, vyberte **možnost + E-mail**.

1. Pomocí informací v následující tabulce definujte akci a pak vyberte **Hotovo**:

    | Nastavení   | Hodnota                                             |
    | --------- | ------------------------------------------------- |
    | Zobrazované jméno | E-mailová akce operátora                          |
    | Akce        | Vaše e-mailová adresa                                |
    | Poznámky     | Teplota prostředí překročila prahovou hodnotu. |

    > [!NOTE]
    > Pokud chcete dostávat e-mailové oznámení, e-mailová adresa musí být [ID uživatele v aplikaci](howto-administer.md) a tento uživatel se musel k aplikaci alespoň jednou přihlásit.

    ![Vytvořit akci pravidla](./media/quick-configure-rules/action.png)

1. Vyberte **Uložit**. Pravidlo je uvedeno na stránce **Pravidla.**

## <a name="test-the-rule"></a>Testování pravidla

Krátce po uložení se pravidlo aktivuje. Pokud jsou splněny podmínky definované v pravidle, vaše aplikace pošle zprávu na e-mailovou adresu, kterou jste zadali v akci.

> [!NOTE]
> Po dokončení testování vypněte pravidlo a zastavte příjem upozornění ve složce Doručená pošta.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili:

* Vytvoření pravidla založeného na telemetrii
* Přidání akce

Další informace o monitorovacích zařízeních připojených k aplikaci najdete na úvodním panelu:

> [!div class="nextstepaction"]
> [Azure IoT Central můžete monitorovat svá zařízení](quick-monitor-devices.md).
