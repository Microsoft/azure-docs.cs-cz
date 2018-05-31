---
title: Konfigurace pravidel a akcí v Azure IoT Central | Microsoft Docs
description: Tento kurz ukazuje, jak vy jako tvůrce nakonfigurujete v aplikaci Azure IoT Central pravidla a akce založené na telemetrii.
services: iot-central
author: ankitgupta
ms.author: ankitgup
ms.date: 04/16/2018
ms.topic: tutorial
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 5ac19e0e25ea3e25ede4d87776c01f8bcaea4655
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202233"
---
# <a name="2---configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>2 – Konfigurace pravidel a akcí pro vaše zařízení v Azure IoT Central

Tento kurz ukazuje, jak vy jako tvůrce nakonfigurujete v aplikaci Microsoft Azure IoT Central pravidla a akce založené na telemetrii.

V tomto kurzu vytvoříte pravidlo, které pošle e-mail, když teplota zařízení připojené klimatizace překročí 90&deg; F.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření pravidla založeného na telemetrii
> * Přidání akce

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte dokončit kurz [definování nového typu zařízení ve vaší aplikaci](tutorial-define-device-type.md) a vytvořit šablonu zařízení **Connected Air Conditioner** (Připojená klimatizace), se kterou budete pracovat.

## <a name="create-a-telemetry-based-rule"></a>Vytvoření pravidla založeného na telemetrii

1. Chcete-li do vaší aplikaci přidat nové pravidlo založené na telemetrii, v levé navigační nabídce zvolte **Device Explorer**:

    ![Stránka Device Explorer](media/tutorial-configure-rules/explorerpage.png)

    Zobrazí se šablona zařízení **Connected Air Conditioner (1.0.0)** a zařízení **Connected Air Conditioner-1**, které jste vytvořili v předchozím kurzu.

1. Pokud chcete zahájit přizpůsobení zařízení připojené klimatizace, vyberte zařízení, které jste vytvořili v předchozím kurzu:

    ![Stránka připojené klimatizace](media/tutorial-configure-rules/builderdevicelist.png)

1. Když chcete začít přidávat pravidla v zobrazení **Rules** (Pravidla), vyberte **Rules**:

    ![Zobrazení pravidel](media/tutorial-configure-rules/builderrulesview.png)

1. V tomto kurzu přidáte pravidlo založené na prahové hodnotě telemetrie. Chcete-li začít vytvářet pravidlo založené na prahové hodnotě, zvolte **New Rule** (Nové pravidlo) a pak **Telemetry** (Telemetrie).

1. Při definování pravidla použijte informace v následující tabulce:

    | Nastavení     | Hodnota                          |
    | ----------- | ------------------------------ |
    | Název        | Teplota klimatizace    |
    | Povolit pravidlo | Zapnuto                             |
    | Podmínka   | Teplota je větší než 90 |

    ![Podmínka pravidla teploty](media/tutorial-configure-rules/buildertemperaturerule.png)

## <a name="add-an-action"></a>Přidání akce

Když definujete pravidlo, také definujete akci, kterou chcete spustit, když jsou podmínky pravidla splněny. V tomto kurzu přidáte akci odesílání e-mailu jako oznámení, že se pravidlo aktivovalo.

1. Chcete-li přidat **akci**, přejděte dolů na panel **Configure Telemetry Rule** (Konfigurovat pravidlo telemetrie), vyberte **+** vedle **Actions** (Akce) a pak zvolte **Email** (E-mail):

    ![Akce pravidla teploty](media/tutorial-configure-rules/builderaddaction.png)

1. Při definování akce použijte informace v následující tabulce:

    | Nastavení   | Hodnota                          |
    | --------- | ------------------------------ |
    | Akce        | Vaše e-mailová adresa             |
    | Poznámky     | Teplotní v klimatizaci překročila prahovou hodnotu. |

    > [!NOTE]
    > Pokud chcete dostávat e-mailové oznámení, e-mailová adresa musí být [ID uživatele v aplikaci](howto-administer.md) a tento uživatel se musel k aplikaci alespoň jednou přihlásit.

    ![Akce teploty Application Builder (Tvůrce aplikací)](media/tutorial-configure-rules/buildertemperatureaction.png)

1. Zvolte **Uložit**. Pravidlo je uvedené na stránce **Rules** (Pravidla):

    ![Pravidla Application Builder (Tvůrce aplikací)](media/tutorial-configure-rules/builderrules.png)

## <a name="test-the-rule"></a>Testování pravidla

Krátce po uložení se pravidlo aktivuje. Pokud jsou splněny podmínky definované v pravidle, vaše aplikace pošle zprávu na e-mailovou adresu, kterou jste zadali v akci.

![Akce e-mailu](media/tutorial-configure-rules/email.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Vytvoření pravidla založeného na telemetrii
> * Přidání akce

Teď, když jste definovali pravidlo založené na prahové hodnotě, dalším doporučeným krokem je [Přizpůsobení zobrazení operátora](tutorial-customize-operator.md).

Další informace o různých typech pravidel v Azure IoT Central a postup parametrizace definice pravidla najdete v tématech:
* [Vytvoření pravidla telemetrie a nastavení oznámení](howto-create-telemetry-rules.md)
* [Vytvoření pravidla událostí a nastavení oznámení](howto-create-event-rules.md)

<!-- Next tutorials in the sequence -->