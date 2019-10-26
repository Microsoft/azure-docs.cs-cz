---
title: Vytváření a Správa pravidel telemetrie v aplikaci Azure IoT Central | Microsoft Docs
description: Pravidla telemetrie Azure IoT Central umožňují monitorovat zařízení téměř v reálném čase a automaticky vyvolávat akce, jako je odeslání e-mailu, když se pravidlo aktivuje.
author: ankitgupta
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d586f899a46a1404af6e798eda4d783a5a7ead85
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953782"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Vytvoření pravidla telemetrie a nastavení oznámení v aplikaci Azure IoT Central

*Tento článek je pro operátory, tvůrce a správce.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Pomocí služby Azure IoT Central můžete vzdáleně monitorovat připojená zařízení. Pravidla Azure IoT Central umožňují monitorovat zařízení téměř v reálném čase a automaticky vyvolávat akce, jako je například odeslání e-mailu nebo triggeru Microsoft Flow. V několika kliknutích můžete definovat podmínku, pro kterou chcete monitorovat data zařízení, a nakonfigurovat odpovídající akci. Tento článek vysvětluje, jak vytvořit pravidla pro monitorování telemetrie odesílané zařízením.

Zařízení můžou používat měření telemetrie k posílání číselných dat ze zařízení. Pravidlo telemetrie se aktivuje, když vybraná telemetrie zařízení přebírá určenou prahovou hodnotu.

## <a name="create-a-telemetry-rule"></a>Vytvoření pravidla telemetrie

Aby bylo možné vytvořit pravidlo telemetrie, musí mít šablona zařízení alespoň jednu určenou měření telemetrie. V tomto příkladu se používá mrazírenské zařízení Automate na počítač, které posílá telemetrii teploty a vlhkosti. Pravidlo monitoruje teplotu oznámenou zařízením a pošle e-mail, když překračuje 80 stupňů.

1. Pomocí stránky **šablony zařízení** přejděte na šablonu zařízení, pro kterou přidáváte pravidlo.

1. Pokud jste ještě nevytvořili žádná pravidla, zobrazí se následující obrazovka:

    ![Zatím žádná pravidla](media/howto-create-telemetry-rules/rules_landing_page1.png)

1. Na kartě **pravidla** vyberte **+ nové pravidlo** a zobrazte typy pravidel, která můžete vytvořit.

1. Vyberte **telemetrie** a vytvořte pravidlo pro monitorování telemetrie zařízení.

    ![Typy pravidel](media/howto-create-telemetry-rules/rule_types1.png)

1. Zadejte název, který vám pomůže identifikovat pravidlo v této šabloně zařízení.

1. Chcete-li ihned Povolit pravidlo pro všechna zařízení vytvořená pro tuto šablonu, přepněte **pravidlo Povolit pro všechna zařízení pro tuto šablonu**.

   ![Podrobnosti pravidla](media/howto-create-telemetry-rules/rule_detail1.png)

    Pravidlo se automaticky použije pro všechna zařízení v šabloně zařízení.

### <a name="configure-the-rule-conditions"></a>Konfigurace podmínek pravidla

Podmínka definuje kritéria, která jsou sledována pravidlem.

1. Pokud chcete přidat novou podmínku, vyberte **+** vedle **podmínky** .

1. Z rozevíracího seznamu **měření** vyberte telemetrii, kterou chcete monitorovat.

1. V dalším kroku vyberte **agregace**, **operátor**a zadejte **prahovou** hodnotu.
   - Agregace je volitelná. Bez agregace se pravidlo spustí pro každý datový bod telemetrie, který splňuje podmínku. Pokud je například pravidlo nakonfigurované tak, aby se aktivovalo, když je teplota vyšší než 80, pravidlo se spustí téměř okamžitě, když zařízení ohlásí teplotu > 80.
   - Pokud je vybrána agregační funkce jako Average, min, Max, Count, musí uživatel poskytnout **agregované časové okno** , ve kterém je nutné vyhodnotit podmínku. Pokud jste například nastavili periodu na 5 minut a pravidlo vyhledá průměrnou teplotu nad 80, pravidlo se aktivuje, když je průměrná teplota nad 80 po dobu alespoň 5 minut. Frekvence vyhodnocení pravidla je stejná jako **agregované časové okno**, což znamená, že se v tomto příkladu pravidlo vyhodnocuje jednou za 5 minut.

     ![Podmínka](media/howto-create-telemetry-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >Pod **podmínkou**lze přidat více než jedno měření telemetrie. Pokud je zadáno více podmínek, musí být splněny všechny podmínky, aby se pravidlo aktivovalo. Každý podmínka se spojí s klauzulí a implicitně. Při použití agregace musí být každé měření agregované.

### <a name="configure-actions"></a>Konfigurace akcí

V této části se dozvíte, jak nastavit akce, které se mají provést při vyvolání pravidla. Akce se vyvolají, pokud se všechny podmínky zadané v pravidle vyhodnotí jako pravdivé.

1. Vyberte **+** vedle **Možnosti akce**. Tady se zobrazí seznam dostupných akcí.  

    ![Přidat akci](media/howto-create-telemetry-rules/add_action1.png)

1. Vyberte akci **e-mail** , do pole **do** zadejte platnou e-mailovou adresu a v případě triggeru pravidla uveďte poznámku, která se zobrazí v těle e-mailu.

    > [!NOTE]
    > E-maily se odesílají jenom uživatelům přidaným do aplikace a přihlásili se aspoň jednou. Přečtěte si další informace o [správě uživatelů](howto-administer.md) v Azure IoT Central.

   ![Konfigurace akce](media/howto-create-telemetry-rules/configure_action1.png)

1. Pravidlo uložíte kliknutím na **Uložit**. Pravidlo se během několika minut ukončí a spustí se monitorování telemetrie, která se posílá do vaší aplikace. Pokud je splněna podmínka zadaná v pravidle, pravidlo aktivuje konfigurovanou akci e-mailu.

Do pravidla můžete přidat další akce, jako jsou Microsoft Flow a Webhooky. Můžete přidat až 5 akcí na pravidlo.

- [Microsoft Flow akce](howto-add-microsoft-flow.md) , která aktivuje pracovní postup v Microsoft Flow při aktivaci pravidla 
- [Akce Webhooku](howto-create-webhooks.md) , která upozorní na další služby, když se aktivuje pravidlo

## <a name="parameterize-the-rule"></a>Parametrizovat pravidlo

Pravidla můžou z **vlastností zařízení** odvodit určité šířky jako parametry. Použití parametrů je užitečné v situacích, kdy se mezní hodnoty telemetrie liší pro různá zařízení. Když vytvoříte pravidlo, vyberte vlastnost zařízení, která určuje mezní hodnotu, jako je například **maximální ideální prahová**hodnota, namísto zadání absolutní hodnoty, například 80 stupňů. Když se pravidlo spustí, bude odpovídat telemetrie zařízení s hodnotou nastavenou ve vlastnosti zařízení.

Použití parametrů je efektivní způsob, jak snížit počet pravidel pro správu podle šablony zařízení.

Akce lze také nakonfigurovat pomocí **vlastnosti zařízení** jako parametru. Pokud je e-mailová adresa uložená jako vlastnost, dá se použít při definování adresy **na** .

## <a name="delete-a-rule"></a>Odstranění pravidla

Pokud pravidlo už nepotřebujete, odstraňte ho otevřením pravidla a volbou **Odstranit**. Odstraněním pravidla se odebere ze šablony zařízení a všech přidružených zařízení.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Povolení nebo zakázání pravidla pro šablonu zařízení

Přejděte na zařízení a vyberte pravidlo, které chcete povolit nebo zakázat. V pravidle zaškrtněte tlačítko **Povolit pravidlo pro všechna zařízení této šablony** , čímž povolíte nebo zakážete pravidlo pro všechna zařízení, která jsou přidružená k šabloně zařízení.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Povolení nebo zakázání pravidla pro zařízení

Přejděte na zařízení a vyberte pravidlo, které chcete povolit nebo zakázat. Přepínač **Povolit pravidlo pro toto zařízení** zaškrtněte, pokud chcete povolit nebo zakázat pravidlo pro toto zařízení.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak vytvořit pravidla v aplikaci Azure IoT Central, tady je několik dalších kroků:

- [Přidat akci Microsoft Flow v pravidlech](howto-add-microsoft-flow.md)
- [Přidat akci Webhooku do pravidel](howto-create-webhooks.md)
- [Seskupit více akcí ke spuštění z jednoho nebo více pravidel](howto-use-action-groups.md)
- [Správa zařízení](howto-manage-devices.md)
