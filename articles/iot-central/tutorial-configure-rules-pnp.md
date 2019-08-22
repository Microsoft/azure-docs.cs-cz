---
title: Konfigurace pravidel a akcí v Azure IoT Central | Microsoft Docs
description: Tento kurz ukazuje, jak vy jako tvůrce nakonfigurujete v aplikaci Azure IoT Central pravidla a akce založené na telemetrii.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: c2aa6edfe7ce9b670ea1015e2da72f3ecc48c9ec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878149"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>Kurz: Konfigurace pravidel a akcí pro zařízení ve službě Azure IoT Central (funkce ve verzi Preview)

*Tento článek je pro operátory, tvůrce a správce.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

V tomto kurzu vytvoříte pravidlo, které pošle e-mail, když teplota na zařízení snímače prostředí překračuje 90&deg; F.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření pravidla založeného na telemetrii
> * Přidat akci

## <a name="prerequisites"></a>Požadavky

Než začnete, měli byste v kurzu [použití definice nového typu zařízení](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) vytvořit šablonu zařízení **snímače prostředí** , se kterou chcete pracovat.

## <a name="create-a-telemetry-based-rule"></a>Vytvoření pravidla založeného na telemetrii

1. Chcete-li do aplikace přidat nové pravidlo založené na telemetrie, vyberte v levé navigační nabídce možnost **pravidla**.

1. Chcete-li vytvořit nové pravidlo, vyberte **+ Nový**. Pak zvolte **telemetrie**.

1. Jako název pravidla zadejte **teplotu okolního prostředí** .

1. V části **obor** vyberte jako šablonu zařízení **senzor prostředí** . Obor, pro který se toto pravidlo vztahuje. Pomocí **+ Filter**můžete přidat další kritéria filtru.

1. V části **Podmínka** definujete, co aktivuje vaše pravidlo. Pomocí následujících informací Definujte podmínku na základě telemetrie teploty:

    | Pole                                        | Value                             |
    | -------------------------------------------- | ------------------------------    |
    | Měření                                  | Teplota                       |
    | Operator                                     | je větší než                   |
    | Value                                        | 90                                |

    Pokud chcete přidat další podmínky, vyberte **+ Podmínka**.

    ![Vytvořit podmínku pravidla](./media/tutorial-configure-rules-pnp/condition.png)

1. Pokud chcete přidat akci, která se spustí při triggeru pravidla, vyberte **+ Akce**a zvolte **e-mail**.

1. K definování akce použijte informace v následující tabulce:

    | Nastavení   | Value                                             |
    | --------- | ------------------------------------------------- |
    | Do        | Vaše e-mailová adresa                                |
    | Poznámky     | Teplota okolního prostředí přesáhla prahovou hodnotu. |

    > [!NOTE]
    > Pokud chcete dostávat e-mailové oznámení, e-mailová adresa musí být [ID uživatele v aplikaci](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) a tento uživatel se musel k aplikaci alespoň jednou přihlásit.

    ![Vytvořit akci pravidla](./media/tutorial-configure-rules-pnp/action.png)

1. Vyberte **Uložit**. Vaše pravidlo je uvedené na stránce **pravidla** .

## <a name="test-the-rule"></a>Testování pravidla

Krátce po uložení se pravidlo aktivuje. Pokud jsou splněny podmínky definované v pravidle, vaše aplikace pošle zprávu na e-mailovou adresu, kterou jste zadali v akci.

> [!NOTE]
> Po dokončení testování vypněte pravidlo, aby ve vaší doručené poště přestal přijímat výstrahy.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

* Vytvoření pravidla založeného na telemetrii
* Přidat akci

Teď, když jste definovali pravidlo na základě prahové hodnoty, je navržený další krok:

> [!div class="nextstepaction"]
> [Vytvoření pravidla událostí a nastavení oznámení](howto-create-event-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
