---
title: Kurz – vytváření a Správa pravidel v aplikaci Azure IoT Central
description: V tomto kurzu se dozvíte, jak pravidla Azure IoT Central umožňují monitorovat zařízení téměř v reálném čase a automaticky vyvolávat akce, jako je odeslání e-mailu, když se pravidlo aktivuje.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: b0b5aafd85fe6d992afa9d879f73ef0ec43e00d3
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834370"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Kurz: Vytvoření pravidla a nastavení oznámení v aplikaci Azure IoT Central

*Tento článek je pro operátory, tvůrce a správce.*

Pomocí služby Azure IoT Central můžete vzdáleně monitorovat připojená zařízení. Pravidla Azure IoT Central umožňují monitorovat zařízení téměř v reálném čase a automaticky vyvolávat akce, jako je například odeslání e-mailu. Tento článek vysvětluje, jak vytvořit pravidla pro monitorování telemetrie, kterou vaše zařízení odesílá.

Zařízení využívají telemetrii k posílání číselných dat ze zařízení. Pravidlo se aktivuje, když vybraná telemetrie přebírá určenou prahovou hodnotu.

V tomto kurzu vytvoříte pravidlo k odeslání e-mailu, když teplota v zařízení se simulovaným senzorem překročí 70 &deg; F.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvoření pravidla
> * Přidat akci e-mailu

## <a name="prerequisites"></a>Požadavky

Než začnete, dokončete průvodce [vytvořením aplikace Azure IoT Central](./quick-deploy-iot-central.md) a [přidejte simulované zařízení do](./quick-create-simulated-device.md) rychlých startů aplikace IoT Central k vytvoření šablony zařízení **kontroleru senzorů** , se kterou chcete pracovat.

## <a name="create-a-rule"></a>Vytvoření pravidla

Aby bylo možné vytvořit pravidlo telemetrie, musí šablona zařízení obsahovat alespoň jednu hodnotu telemetrie. V tomto kurzu se používá simulované zařízení **snímače senzorů** , které posílá telemetrii teploty a vlhkosti. Tuto šablonu zařízení jste přidali a vytvořili simulované zařízení v části [Přidání simulovaného zařízení do rychlého startu aplikace IoT Central](./quick-create-simulated-device.md) . Pravidlo monitoruje teplotu oznámenou zařízením a pošle e-mail, když překračuje 70 stupňů.

> [!NOTE]
> Pro každou aplikaci je stanovený limit 50 pravidel.

1. V levém podokně vyberte **pravidla**.

1. Pokud jste ještě nevytvořili žádná pravidla, zobrazí se následující obrazovka:

    :::image type="content" source="media/tutorial-create-telemetry-rules/rules-landing-page.png" alt-text="Snímek obrazovky, který zobrazuje prázdný seznam pravidel":::

1. Kliknutím na **+ Nový** přidejte nové pravidlo.

1. Zadejte název _monitorování teploty_ pro identifikaci pravidla a stiskněte klávesu ENTER.

1. Vyberte šablonu zařízení **kontroleru senzorů** . Ve výchozím nastavení se pravidlo automaticky vztahuje na všechna zařízení přidružená k šabloně zařízení. Pokud chcete vyfiltrovat podmnožinu zařízení, vyberte **+ filtrovat** a použijte vlastnosti zařízení k identifikaci zařízení. Chcete-li pravidlo zakázat, přepněte tlačítko **povoleno/zakázáno** :

    :::image type="content" source="media/tutorial-create-telemetry-rules/device-filters.png" alt-text="Snímek obrazovky, který zobrazuje výběr šablony zařízení v definici pravidla":::

### <a name="configure-the-rule-conditions"></a>Konfigurace podmínek pravidla

Podmínky definují kritéria, která pravidla monitorují. V tomto kurzu nakonfigurujete pravidlo, které se aktivuje, když teplota překročí 70 &deg; F.

1. V rozevíracím seznamu **telemetrie** vyberte možnost **teplota** .

1. V dalším kroku vyberte **je větší než** jako **operátor** a jako **hodnotu** zadejte _70_ .

    :::image type="content" source="media/tutorial-create-telemetry-rules/condition-filled-out.png" alt-text="Snímek obrazovky zobrazující stav teploty pro pravidlo":::

1. Volitelně můžete nastavit **časovou agregaci**. Když vyberete časovou agregaci, musíte také v rozevíracím seznamu agregace vybrat typ agregace, například Average nebo Sum.

    * Bez agregace se pravidlo spustí pro každý datový bod telemetrie, který splňuje podmínku. Pokud například nakonfigurujete pravidlo tak, aby se aktivovalo, když je teplota vyšší než 70, pravidlo se spustí téměř okamžitě, když teplota zařízení překročí tuto hodnotu.
    * Při agregaci se pravidlo aktivuje, pokud agregovaná hodnota datových bodů telemetrie v časovém intervalu splňuje podmínku. Pokud například nakonfigurujete pravidlo tak, aby se aktivovalo, když je teplota vyšší než 70 a průměrná časová agregace 10 minut, pak se pravidlo aktivuje, když zařízení ohlásí průměrnou teplotu větší než 70, počítáno v intervalu 10 minut.

    :::image type="content" source="media/tutorial-create-telemetry-rules/aggregate-condition-filled-out.png" alt-text="Snímek obrazovky zobrazující vyplněnou agregovanou podmínku":::

Výběrem **+ Podmínka** můžete přidat k pravidlu několik podmínek. Pokud je zadáno více podmínek, musí být splněny všechny podmínky, aby se pravidlo aktivovalo. Každá podmínka je propojena implicitní `AND` klauzulí. Pokud používáte časovou agregaci s více podmínkami, musí být agregované všechny hodnoty telemetrie.

### <a name="configure-actions"></a>Konfigurace akcí

Po definování podmínky nastavíte akce, které se mají provést, když se pravidlo aktivuje. Akce jsou vyvolány, pokud jsou všechny podmínky zadané v pravidle vyhodnoceny jako pravdivé.

1. V části **Akce** vyberte **+ e-mail** .

1. Zadejte _Upozornění na teplotu_ jako zobrazovaný název pro akci, vaši e-mailovou adresu v poli **do** a _měli byste se podívat na zařízení!_ jako Poznámka se zobrazí v těle e-mailu.

    > [!NOTE]
    > E-maily se odesílají jenom uživatelům přidaným do aplikace a přihlásili se aspoň jednou. Přečtěte si další informace o [správě uživatelů](howto-administer.md) v Azure IoT Central.

    :::image type="content" source="media/tutorial-create-telemetry-rules/configure-action.png" alt-text="Snímek obrazovky, který zobrazuje akci e-mailu pro pravidlo":::

1. Akci uložíte kliknutím na **Hotovo**. Do pravidla můžete přidat několik akcí.

1. Pravidlo uložíte kliknutím na **Uložit**. Pravidlo se během několika minut ukončí a spustí se monitorování telemetrie, která se posílá do vaší aplikace. Pokud je splněna podmínka zadaná v pravidle, pravidlo aktivuje konfigurovanou akci e-mailu.

Po chvíli obdržíte e-mailovou zprávu, když se pravidlo aktivuje:

:::image type="content" source="media/tutorial-create-telemetry-rules/email.png" alt-text="Snímek obrazovky, který zobrazuje e-mail s oznámením":::

## <a name="delete-a-rule"></a>Odstranění pravidla

Pokud pravidlo už nepotřebujete, odstraňte ho otevřením pravidla a volbou **Odstranit**.

## <a name="enable-or-disable-a-rule"></a>Povolení nebo zakázání pravidla

Vyberte pravidlo, které chcete povolit nebo zakázat. Pokud chcete povolit nebo zakázat pravidlo pro všechna zařízení, která jsou v pravidle vymezená, přepněte v pravidle tlačítko **povoleno/zakázáno** .

## <a name="enable-or-disable-a-rule-for-specific-devices"></a>Povolení nebo zakázání pravidla pro určitá zařízení

Vyberte pravidlo, které chcete upravit. V části **cílová zařízení** použijte jeden nebo víc filtrů, abyste mohli zúžit rozsah pravidla na zařízení, která chcete monitorovat.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

* Vytvoření pravidla založeného na telemetrii
* Přidání akce

Teď, když jste definovali pravidlo na základě prahové hodnoty, je navržený další krok, ve kterém se dozvíte, jak:

> [!div class="nextstepaction"]
> [Vytvořit Webhooky na pravidlech](./howto-create-webhooks.md).
