---
title: Rychlý Start – konfigurace pravidel a akcí v Azure IoT Central
description: V tomto rychlém startu se dozvíte, jak jako tvůrce nakonfigurovat pravidla a akce založené na telemetrie v aplikaci Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 90fc1385afb2ef921828465ba030674281e96ebf
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833843"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Rychlé zprovoznění: Konfigurace pravidel a akcí pro zařízení v Azure IoT Central

*Tento článek je pro operátory, tvůrce a správce.*

V tomto rychlém startu vytvoříte pravidlo, které pošle e-mail, když je vlhkost hlášená snímačem zařízení větší než 55%.

## <a name="prerequisites"></a>Požadavky

Než začnete, měli byste provést dvě předchozí rychlé starty a [vytvořit aplikaci Azure IoT Central](./quick-deploy-iot-central.md) a [Přidat simulované zařízení do aplikace IoT Central](./quick-create-simulated-device.md) a vytvořit tak šablonu zařízení **kontroleru senzorů** , se kterou chcete pracovat.

## <a name="create-a-telemetry-based-rule"></a>Vytvoření pravidla založeného na telemetrii

1. Pokud chcete do aplikace přidat nové pravidlo založené na telemetrie, v levém podokně vyberte **pravidla**.

1. Chcete-li vytvořit nové pravidlo, vyberte **+ Nový**.

1. Jako název pravidla zadejte **vlhkost okolního prostředí** .

1. V části **cílová zařízení** vyberte jako šablonu zařízení možnost **kontroler senzorů** . Tato možnost filtruje zařízení, na které se pravidlo vztahuje podle typu šablony zařízení. Kliknutím na **+ Filter** můžete přidat další kritéria filtru.

1. V části **podmínky** definujete, co aktivuje vaše pravidlo. Pomocí následujících informací Definujte podmínku na základě telemetrie teploty:

    | Pole        | Hodnota            |
    | ------------ | ---------------- |
    | Měření  | SensorHumid      |
    | Operátor     | je větší než  |
    | Hodnota        | 55               |

    Pokud chcete přidat další podmínky, vyberte **+ Podmínka**.

    :::image type="content" source="media/quick-configure-rules/condition.png" alt-text="Snímek obrazovky znázorňující podmínku pravidla":::

1. Pokud chcete přidat akci e-mailu, která se spustí při triggeru pravidla, vyberte **+ e-mail**.

1. Pomocí informací v následující tabulce definujte akci a potom vyberte **Hotovo**:

    | Nastavení   | Hodnota                                             |
    | --------- | ------------------------------------------------- |
    | Zobrazované jméno | Akce e-mailu operátoru                          |
    | Záměr        | Vaše e-mailová adresa                                |
    | Poznámky     | Vlhkost v životním prostředí překročila prahovou hodnotu. |

    > [!NOTE]
    > Pokud chcete dostávat e-mailové oznámení, e-mailová adresa musí být [ID uživatele v aplikaci](howto-administer.md) a tento uživatel se musel k aplikaci alespoň jednou přihlásit.

    :::image type="content" source="media/quick-configure-rules/action.png" alt-text="Snímek obrazovky zobrazující akci e-mailu, která se přidala do pravidla":::

1. Vyberte **Uložit**. Vaše pravidlo je uvedené na stránce **Pravidla**.

## <a name="test-the-rule"></a>Testování pravidla

Krátce po uložení se pravidlo aktivuje. Po splnění podmínek definovaných v pravidle pošle aplikace e-mail na adresu, kterou jste zadali v akci.

> [!NOTE]
> Po dokončení testování vypněte pravidlo, aby ve vaší doručené poště přestal přijímat výstrahy.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili:

* Vytvoření pravidla založeného na telemetrii
* Přidání akce

Další informace o monitorování zařízení připojených k vaší aplikaci získáte, když budete pokračovat v rychlém startu:

> [!div class="nextstepaction"]
> [Ke sledování svých zařízení použijte Azure IoT Central](quick-monitor-devices.md).
