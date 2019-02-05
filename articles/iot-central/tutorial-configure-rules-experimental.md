---
title: Konfigurace pravidel a akcí v Azure IoT Central | Microsoft Docs
description: Tento kurz ukazuje, jak vy jako tvůrce nakonfigurujete v aplikaci Azure IoT Central pravidla a akce založené na telemetrii.
author: ankitscribbles
ms.author: ankitgup
ms.date: 01/28/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 0789f113a68d98ff0d70fd3e9b3c8528122c2fc2
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55729162"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-new-ui-design"></a>Kurz: Konfigurace pravidla a akce pro vaše zařízení v Azure IoT Central (návrh nové uživatelské rozhraní)

*Tento článek je pro operátory, tvůrce a správce.*

V tomto kurzu vytvoříte pravidlo, které pošle e-mail, když teplota zařízení připojené klimatizace překročí 90&deg; F.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření pravidla založeného na telemetrii
> * Přidání akce

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte dokončit kurz [definování nového typu zařízení ve vaší aplikaci](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) a vytvořit šablonu zařízení **Connected Air Conditioner** (Připojená klimatizace), se kterou budete pracovat.

## <a name="create-a-telemetry-based-rule"></a>Vytvoření pravidla založeného na telemetrii

1. Chcete-li přidat nové pravidlo na základě telemetrie pro vaši aplikaci, v levé navigační nabídce zvolte **šablon**:

    ![Stránka šablony zařízení](media/tutorial-configure-rules-experimental/templatespage1.png)

    Zobrazí **připojené Vzduchovod (1.0.0)** zařízení šablonu, kterou jste vytvořili v předchozím kurzu.

2. Přizpůsobení šablony zařízení, klikněte na tlačítko **připojené Vzduchovod** šablonu, kterou jste vytvořili v předchozím kurzu.

3. Přidání telemetrické pravidla v **pravidla** zobrazení, zvolte **pravidla**, klikněte na tlačítko **+ nové pravidlo**a klikněte na tlačítko **Telemetrie**:

    ![Zobrazení pravidel](media/tutorial-configure-rules-experimental/newrule.png)

5. Při definování pravidla použijte informace v následující tabulce:

    | Nastavení                                      | Hodnota                             |
    | -------------------------------------------- | ------------------------------    |
    | Název                                         | Upozornění klimatizace na teplotu |
    | Enable rule for all devices of this template (Povolit pravidlo pro všechna zařízení této šablony) | Zapnuto                                |
    | Podmínka                                    | Teplota je větší než 90    |
    | Agregace                                  | Žádný                              |

    ![Podmínka pravidla teploty](media/tutorial-configure-rules-experimental/temperaturerule.png)

    Potom klikněte na **Uložit**.

## <a name="add-an-action"></a>Přidání akce

Když definujete pravidlo, také definujete akci, kterou chcete spustit, když jsou podmínky pravidla splněny. V tomto kurzu vytvoříte pravidlo s akci, která se odešle e-mailové oznámení.

1. Chcete-li přidat **akce**, první **Uložit** pravidlo a poté přejděte dolů na **konfigurovat pravidlo Telemetrie** panelu. Zvolte **+** vedle **akce**a klikněte na tlačítko **e-mailu**:

    ![Akce pravidla teploty](media/tutorial-configure-rules-experimental/addaction.png)

2. Při definování akce použijte informace v následující tabulce:

    | Nastavení   | Hodnota                          |
    | --------- | ------------------------------ |
    | Akce        | Vaše e-mailová adresa             |
    | Poznámky     | Teplota klimatizace překročila prahovou hodnotu. |

    > [!NOTE]
    > Pokud chcete dostávat e-mailové oznámení, e-mailová adresa musí být [ID uživatele v aplikaci](howto-administer.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) a tento uživatel se musel k aplikaci alespoň jednou přihlásit.

    ![Akce teploty](media/tutorial-configure-rules-experimental/temperatureaction.png)

3. Klikněte na **Uložit**. Pravidlo je uvedeno na **pravidla** stránky.

## <a name="test-the-rule"></a>Testování pravidla

Krátce po uložení se pravidlo aktivuje. Pokud jsou splněny podmínky definované v pravidle, vaše aplikace pošle zprávu na e-mailovou adresu, kterou jste zadali v akci.

![Akce e-mailu](media/tutorial-configure-rules-experimental/email.png)

> [!NOTE]
> Po dokončení testování vypněte pravidlo zastavit příjem oznámení ve vaší doručené pošty.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Vytvoření pravidla založeného na telemetrii
> * Přidání akce

Teď, když jste definovali pravidlo založené na prahových hodnotách navrhované dalším krokem je [přizpůsobení zobrazení operátoru](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

Další informace o různých typech pravidel v Azure IoT Central a postup parametrizace definice pravidla najdete v tématech:
* [Vytvoření pravidla telemetrie a nastavení oznámení](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Vytvoření pravidla událostí a nastavení oznámení](howto-create-event-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

<!-- Next tutorials in the sequence -->
