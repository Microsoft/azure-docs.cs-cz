---
title: Konfigurace pravidel a akcí v Azure IoT Central | Microsoft Docs
description: Tento kurz ukazuje, jak vy jako tvůrce nakonfigurujete v aplikaci Azure IoT Central pravidla a akce založené na telemetrii.
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 5358563de9f35d2cef74a32cb1794de418066810
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72955792"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Kurz: Konfigurace pravidel a akcí pro vaše zařízení v Azure IoT Central

*Tento článek je pro operátory, tvůrce a správce.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

V tomto kurzu vytvoříte pravidlo, které pošle e-mail, když teplota zařízení připojené klimatizace překročí 90&deg; F.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření pravidla založeného na telemetrii
> * Přidání akce

## <a name="prerequisites"></a>Předpoklady

Než začnete, musíte dokončit kurz [definování nového typu zařízení ve vaší aplikaci](tutorial-define-device-type.md) a vytvořit šablonu zařízení **Connected Air Conditioner** (Připojená klimatizace), se kterou budete pracovat.

## <a name="create-a-telemetry-based-rule"></a>Vytvoření pravidla založeného na telemetrii

1. Chcete-li do aplikace přidat nové pravidlo založené na telemetrie, vyberte v levém podokně **šablony zařízení**:

    ![Stránka šablony zařízení](media/tutorial-configure-rules/templatespage1.png)

    Zobrazí se šablona zařízení **připojeného vzduchu (1.0.0)** , kterou jste vytvořili v předchozím kurzu.

2. Pokud chcete přizpůsobit šablonu zařízení, vyberte **připojenou** šablonu klimatizace, kterou jste vytvořili v předchozím kurzu.

3. Pokud chcete přidat pravidlo založené na telemetrie v zobrazení **pravidla** , vyberte **pravidla**, vyberte **+ nové pravidlo**a potom vyberte **telemetrie**:

    ![Zobrazení pravidel](media/tutorial-configure-rules/newrule.png)

5. Při definování pravidla použijte informace v následující tabulce:

    | Nastavení                                      | Hodnota                             |
    | -------------------------------------------- | ------------------------------    |
    | Name (Název)                                         | Upozornění klimatizace na teplotu |
    | Enable rule for all devices of this template (Povolit pravidlo pro všechna zařízení této šablony) | Zapnuto                                |
    | Podmínka                                    | Teplota je větší než 90    |
    | Agregace                                  | Žádné                              |

    ![Podmínka pravidla teploty](media/tutorial-configure-rules/temperaturerule.png)

    Potom vyberte **Uložit**.

## <a name="add-an-action"></a>Přidání akce

Když definujete pravidlo, také definujete akci, kterou chcete spustit, když jsou podmínky pravidla splněny. V tomto kurzu vytvoříte pravidlo s akcí, která pošle e-mailové oznámení.

1. Pokud chcete přidat **akci**, nejdřív **uložte** pravidlo a potom se posuňte dolů na panelu **konfigurovat pravidlo telemetrie** . Zvolte **+** vedle **položky akce**a pak zvolte **e-mail**:

    ![Akce pravidla teploty](media/tutorial-configure-rules/addaction.png)

2. Při definování akce použijte informace v následující tabulce:

    | Nastavení   | Hodnota                          |
    | --------- | ------------------------------ |
    | až        | Vaše e-mailová adresa             |
    | Poznámky     | Teplota klimatizace překročila prahovou hodnotu. |

    > [!NOTE]
    > Pokud chcete dostávat e-mailové oznámení, e-mailová adresa musí být [ID uživatele v aplikaci](howto-administer.md) a tento uživatel se musel k aplikaci alespoň jednou přihlásit.

    ![Teplotní akce](media/tutorial-configure-rules/temperatureaction.png)

3. Vyberte **Save** (Uložit). Vaše pravidlo je uvedené na stránce **pravidla** .

## <a name="test-the-rule"></a>Testování pravidla

Krátce po uložení se pravidlo aktivuje. Pokud jsou splněny podmínky definované v pravidle, vaše aplikace pošle zprávu na e-mailovou adresu, kterou jste zadali v akci.

> [!NOTE]
> Po dokončení testování vypněte pravidlo, aby ve vaší doručené poště přestal přijímat výstrahy.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Vytvoření pravidla založeného na telemetrii
> * Přidání akce

Teď, když jste definovali pravidlo na základě prahové hodnoty, je navržený další krok [přizpůsobení zobrazení operátoru](tutorial-customize-operator.md).

Další informace o různých typech pravidel v Azure IoT Central a postup parametrizace definice pravidla najdete v tématech:
* [Vytvoření pravidla telemetrie a nastavení oznámení](howto-create-telemetry-rules.md)
* [Vytvoření pravidla událostí a nastavení oznámení](howto-create-event-rules.md)

<!-- Next tutorials in the sequence -->