---
title: Rychlý Start – konfigurace pravidel a akcí v Azure IoT Central
description: V tomto rychlém startu se dozvíte, jak jako tvůrce nakonfigurovat pravidla a akce založené na telemetrie v aplikaci Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: a63e77fb3c50d4387c27912336d65f9f84d2d5c9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027801"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Rychlý Start: Konfigurace pravidel a akcí pro vaše zařízení v Azure IoT Central

*Tento článek je pro operátory, tvůrce a správce.*

V tomto rychlém startu vytvoříte pravidlo, které pošle e-mail, když teplota hlášená snímačem zařízení překračuje 90&deg; F.

## <a name="prerequisites"></a>Požadavky

Než začnete, měli byste provést dvě předchozí rychlé starty a [vytvořit aplikaci Azure IoT Central](./quick-deploy-iot-central.md) a [Přidat simulované zařízení do aplikace IoT Central](./quick-create-pnp-device.md) a vytvořit tak šablonu zařízení **MXChip IoT DevKit** , se kterou chcete pracovat.

## <a name="create-a-telemetry-based-rule"></a>Vytvoření pravidla založeného na telemetrii

1. Pokud chcete do aplikace přidat nové pravidlo založené na telemetrie, v levém podokně vyberte **pravidla**.

1. Chcete-li vytvořit nové pravidlo, vyberte možnost **+** .

1. Jako název pravidla zadejte **teplotu okolního prostředí** .

1. V části **cílová zařízení** vyberte jako šablonu zařízení **MXChip IoT DevKit** . Tato možnost filtruje zařízení, na které se pravidlo vztahuje podle typu šablony zařízení. Můžete přidat další kritéria filtru a vybrat **+ filtrovat**.

1. V části **podmínky** definujete, co aktivuje vaše pravidlo. Pomocí následujících informací Definujte podmínku na základě telemetrie teploty:

    | Pole        | Hodnota            |
    | ------------ | ---------------- |
    | Měření  | Teplota      |
    | Operátor     | je větší než  |
    | Hodnota        | 90               |

    Pokud chcete přidat další podmínky, vyberte **+ Podmínka**.

    ![Vytvořit podmínku pravidla](./media/quick-configure-rules/condition.png)

1. Pokud chcete přidat akci e-mailu, která se spustí při triggeru pravidla, vyberte **+ e-mail**.

1. Pomocí informací v následující tabulce definujte akci a potom vyberte **Hotovo**:

    | Nastavení   | Hodnota                                             |
    | --------- | ------------------------------------------------- |
    | Zobrazované jméno | Akce e-mailu operátoru                          |
    | až        | Vaše e-mailová adresa                                |
    | Poznámky     | Teplota okolního prostředí přesáhla prahovou hodnotu. |

    > [!NOTE]
    > Pokud chcete dostávat e-mailové oznámení, e-mailová adresa musí být [ID uživatele v aplikaci](howto-administer.md) a tento uživatel se musel k aplikaci alespoň jednou přihlásit.

    ![Vytvořit akci pravidla](./media/quick-configure-rules/action.png)

1. Vyberte **Uložit**. Vaše pravidlo je uvedené na stránce **pravidla** .

## <a name="test-the-rule"></a>Testování pravidla

Krátce po uložení se pravidlo aktivuje. Pokud jsou splněny podmínky definované v pravidle, vaše aplikace pošle zprávu na e-mailovou adresu, kterou jste zadali v akci.

> [!NOTE]
> Po dokončení testování vypněte pravidlo, aby ve vaší doručené poště přestal přijímat výstrahy.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili:

* Vytvoření pravidla založeného na telemetrii
* Přidání akce

Další informace o monitorování zařízení připojených k vaší aplikaci získáte, když budete pokračovat v rychlém startu:

> [!div class="nextstepaction"]
> [Ke sledování svých zařízení použijte Azure IoT Central](quick-monitor-devices.md).
