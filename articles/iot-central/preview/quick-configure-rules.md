---
title: Rychlý Start – konfigurace pravidel a akcí v Azure IoT Central
description: V tomto rychlém startu se dozvíte, jak jako tvůrce nakonfigurovat pravidla a akce založené na telemetrie v aplikaci Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: f81171d0f545bbf7f903d54bee761817e9451d9d
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706721"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>Rychlý Start: Konfigurace pravidel a akcí pro zařízení ve službě Azure IoT Central (funkce ve verzi Preview)

*Tento článek je pro operátory, tvůrce a správce.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

V tomto rychlém startu vytvoříte pravidlo, které pošle e-mail, když teplota na zařízení snímače prostředí překračuje 90&deg; F.

## <a name="prerequisites"></a>Předpoklady

Než začnete, měli byste provést dvě předchozí rychlé starty a [vytvořit aplikaci Azure IoT Central](./quick-deploy-iot-central.md) a [Přidat simulované zařízení do aplikace IoT Central](./quick-create-pnp-device.md) a vytvořit tak šablonu zařízení **snímače prostředí** , se kterou chcete pracovat.

## <a name="create-a-telemetry-based-rule"></a>Vytvoření pravidla založeného na telemetrii

1. Pokud chcete do aplikace přidat nové pravidlo založené na telemetrie, v levém podokně vyberte **pravidla**.

1. Chcete-li vytvořit nové pravidlo, vyberte **+ Nový**.

1. Jako název pravidla zadejte **teplotu okolního prostředí** .

1. V části **cílová zařízení** vyberte jako šablonu zařízení **senzor prostředí** . Tato možnost filtruje zařízení, na které se pravidlo vztahuje podle typu šablony zařízení. Můžete přidat další kritéria filtru, a to volbou **+ Filter**.

1. V části **podmínky** definujete, co aktivuje vaše pravidlo. Pomocí následujících informací Definujte podmínku na základě telemetrie teploty:

    | Pole                                        | Hodnota                             |
    | -------------------------------------------- | ------------------------------    |
    | Měření                                  | Teplota                       |
    | Operátor                                     | je větší než                   |
    | Hodnota                                        | 90                                |

    Pokud chcete přidat další podmínky, vyberte **+ Podmínka**.

    ![Vytvořit podmínku pravidla](./media/quick-configure-rules/condition.png)

1. Pokud chcete přidat akci e-mailu, která se spustí při triggeru pravidla, vyberte **+ e-mail**.

1. K definování akce použijte informace v následující tabulce:

    | Nastavení   | Hodnota                                             |
    | --------- | ------------------------------------------------- |
    | Zobrazované jméno | Akce e-mailu operátoru                          |
    | až        | Vaše e-mailová adresa                                |
    | Poznámky     | Teplota okolního prostředí přesáhla prahovou hodnotu. |

    > [!NOTE]
    > Pokud chcete dostávat e-mailové oznámení, e-mailová adresa musí být [ID uživatele v aplikaci](howto-administer.md) a tento uživatel se musel k aplikaci alespoň jednou přihlásit.

    ![Vytvořit akci pravidla](./media/quick-configure-rules/action.png)

1. Vyberte **Save** (Uložit). Vaše pravidlo je uvedené na stránce **pravidla** .

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
