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
ms.openlocfilehash: 56ced4f5e2fd0fbf829f72cff2413998398a7a09
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066012"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Kurz: Konfigurace pravidla a akce pro vaše zařízení v Azure IoT Central

*Tento článek je pro operátory, tvůrce a správce.*

V tomto kurzu vytvoříte pravidlo, které pošle e-mail, když teplota zařízení připojené klimatizace překročí 90&deg; F.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření pravidla založeného na telemetrii
> * Přidání akce

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte dokončit kurz [definování nového typu zařízení ve vaší aplikaci](tutorial-define-device-type.md) a vytvořit šablonu zařízení **Connected Air Conditioner** (Připojená klimatizace), se kterou budete pracovat.

## <a name="create-a-telemetry-based-rule"></a>Vytvoření pravidla založeného na telemetrii

1. Chcete-li přidat nové pravidlo na základě telemetrie pro vaši aplikaci, v levé navigační nabídce **šablon**:

    ![Stránka šablony zařízení](media/tutorial-configure-rules/templatespage1.png)

    Zobrazí **připojené Vzduchovod (1.0.0)** zařízení šablonu, kterou jste vytvořili v předchozím kurzu.

2. Chcete-li upravit šablonu vašeho zařízení, vyberte **připojené Vzduchovod** šablonu, kterou jste vytvořili v předchozím kurzu.

3. Přidání telemetrické pravidla v **pravidla** zobrazit, vyberte možnost **pravidla**vyberte **+ nové pravidlo**a pak vyberte **Telemetrie**:

    ![Zobrazení pravidel](media/tutorial-configure-rules/newrule.png)

5. Při definování pravidla použijte informace v následující tabulce:

    | Nastavení                                      | Hodnota                             |
    | -------------------------------------------- | ------------------------------    |
    | Name                                         | Upozornění klimatizace na teplotu |
    | Enable rule for all devices of this template (Povolit pravidlo pro všechna zařízení této šablony) | Zapnuto                                |
    | Podmínka                                    | Teplota je větší než 90    |
    | Agregace                                  | Žádný                              |

    ![Podmínka pravidla teploty](media/tutorial-configure-rules/temperaturerule.png)

    Potom vyberte **Uložit**.

## <a name="add-an-action"></a>Přidání akce

Když definujete pravidlo, také definujete akci, kterou chcete spustit, když jsou podmínky pravidla splněny. V tomto kurzu vytvoříte pravidlo s akci, která se odešle e-mailové oznámení.

1. Chcete-li přidat **akce**, první **Uložit** pravidlo a poté přejděte dolů na **konfigurovat pravidlo Telemetrie** panelu. Zvolte **+** vedle **akce**a klikněte na tlačítko **e-mailu**:

    ![Akce pravidla teploty](media/tutorial-configure-rules/addaction.png)

2. Při definování akce použijte informace v následující tabulce:

    | Nastavení   | Hodnota                          |
    | --------- | ------------------------------ |
    | Do        | Vaše e-mailová adresa             |
    | Poznámky     | Teplota klimatizace překročila prahovou hodnotu. |

    > [!NOTE]
    > Pokud chcete dostávat e-mailové oznámení, e-mailová adresa musí být [ID uživatele v aplikaci](howto-administer.md) a tento uživatel se musel k aplikaci alespoň jednou přihlásit.

    ![Akce teploty](media/tutorial-configure-rules/temperatureaction.png)

3. Vyberte **Uložit**. Pravidlo je uvedeno na **pravidla** stránky.

## <a name="test-the-rule"></a>Testování pravidla

Krátce po uložení se pravidlo aktivuje. Pokud jsou splněny podmínky definované v pravidle, vaše aplikace pošle zprávu na e-mailovou adresu, kterou jste zadali v akci.

> [!NOTE]
> Po dokončení testování vypněte pravidlo zastavit příjem oznámení ve vaší doručené pošty.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Vytvoření pravidla založeného na telemetrii
> * Přidání akce

Teď, když jste definovali pravidlo založené na prahových hodnotách navrhované dalším krokem je [přizpůsobení zobrazení operátoru](tutorial-customize-operator.md).

Další informace o různých typech pravidel v Azure IoT Central a postup parametrizace definice pravidla najdete v tématech:
* [Vytvoření pravidla telemetrie a nastavení oznámení](howto-create-telemetry-rules.md)
* [Vytvoření pravidla událostí a nastavení oznámení](howto-create-event-rules.md)

<!-- Next tutorials in the sequence -->