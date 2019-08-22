---
title: Vytváření a Správa pravidel událostí v aplikaci Azure IoT Central | Microsoft Docs
description: Pravidla událostí pro Azure IoT Central umožňují monitorovat zařízení téměř v reálném čase a automaticky vyvolávat akce, jako je odeslání e-mailu, když se pravidlo aktivuje.
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c931cfbcff750d96828641669c4aaa15e7932970
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877411"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Vytvoření pravidla události a nastavení oznámení v aplikaci Azure IoT Central

*Tento článek je pro operátory, tvůrce a správce.*

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Pomocí služby Azure IoT Central můžete vzdáleně monitorovat připojená zařízení. Pravidla Azure IoT Central umožňují monitorovat zařízení téměř v reálném čase a automaticky vyvolávat akce, jako je například odeslání e-mailu nebo triggeru Microsoft Flow. V několika kliknutích můžete definovat podmínku, pro kterou chcete monitorovat data zařízení, a nakonfigurovat odpovídající akci. Tento článek vysvětluje, jak vytvořit pravidla pro monitorování událostí odesílaných zařízením.

Zařízení můžou k posílání důležitých nebo informativních událostí zařízení používat měření událostí. Pravidlo události se aktivuje, když zařízení nahlásí vybranou událost zařízení.

## <a name="create-an-event-rule"></a>Vytvoření pravidla událostí

Aby bylo možné vytvořit pravidlo události, musí mít šablona zařízení alespoň jednu definici měření události. V tomto příkladu se používá mrazírenské zařízení Automate, které hlásí událost chyby motoru ventilátoru. Pravidlo monitoruje událost hlášenou zařízením a při každém hlášení události pošle e-mail.

1. Pomocí stránky **šablony zařízení** přejděte na šablonu zařízení, pro kterou přidáváte pravidlo.

1. Pokud jste ještě nevytvořili žádná pravidla, zobrazí se následující obrazovka:

    ![Zatím žádná pravidla](media/howto-create-event-rules/rules_landing_page1.png)

1. Na kartě **pravidla** vyberte **+ nové pravidlo** a zobrazte typy pravidel, která můžete vytvořit.

1. Kliknutím na dlaždici **událost** vytvořte pravidlo monitorování událostí.

    ![Typy pravidel](media/howto-create-event-rules/rule_types1.png)

1. Zadejte název, který vám pomůže identifikovat pravidlo v této šabloně zařízení.

1. Chcete-li ihned Povolit pravidlo pro všechna zařízení vytvořená z této šablony, přepněte **možnost Povolit pravidlo pro všechna zařízení v této**šabloně.

    ![Podrobnosti pravidla](media/howto-create-event-rules/rule_detail1.png)

    Pravidlo se automaticky použije pro všechna zařízení v šabloně zařízení.

### <a name="configure-the-rule-conditions"></a>Konfigurace podmínek pravidla

Podmínka definuje kritéria, která jsou sledována pravidlem.

1. Kliknutím na **+** další **podmínky** přidejte novou podmínku.

1. Z rozevíracího seznamu měření vyberte událost, kterou chcete monitorovat. V tomto příkladu byla vybrána událost **chyb motoru ventilátoru** .

   ![Podmínka](media/howto-create-event-rules/condition_filled_out1.png)

1. Volitelně můžete také nastavit **počet** jako agregaci a poskytnout odpovídající prahovou hodnotu.

   - Bez agregace pravidlo spustí aktivační událost pro každý datový bod události, který splňuje podmínku. Pokud například nakonfigurujete podmínku pravidla, která se aktivuje při výskytu **chybové** události ventilátoru, pak se pravidlo spustí téměř okamžitě, jakmile zařízení ohlásí událost.
   - Pokud je počet použit jako agregační funkce, musíte zadat **prahovou hodnotu** a **agregovaný časový interval** , ve kterém je nutné vyhodnotit podmínku. V tomto případě je počet událostí agregovaný a pravidlo se aktivuje pouze v případě, že počet agregovaných událostí odpovídá prahové hodnotě.

     Například pokud chcete upozornit, když v 5 minutách dojde k více než třem událostem zařízení, vyberte událost a nastavte agregační funkci jako "Count", operátor jako "větší než" a "prahová hodnota" jako 3. Nastavte "časové období agregace" jako "5 minut". Pravidlo se aktivuje, když zařízení během 5 minut odešle víc než tři události. Frekvence vyhodnocení pravidla je stejná jako **agregované časové okno**, což znamená, že se v tomto příkladu pravidlo vyhodnocuje jednou za 5 minut.

     ![Přidat podmínku události](media/howto-create-event-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >Pod podmínkou lze přidat více než jednu měřeníudálosti. Pokud je zadáno více podmínek, musí být splněny všechny podmínky, aby se pravidlo aktivovalo. Každá podmínka se implicitně spojí s klauzulí a. Při použití agregace musí být každé měření agregované.

### <a name="configure-actions"></a>Konfigurace akcí

V této části se dozvíte, jak nastavit akce, které se mají provést při vyvolání pravidla. Akce se vyvolají, pokud se všechny podmínky zadané v pravidle vyhodnotí jako pravdivé.

1. Klikněte na **+** tlačítko Další **Akce**. Tady se zobrazí seznam dostupných akcí.

    ![Přidat akci](media/howto-create-event-rules/add_action1.png)

1. Vyberte akci **e-mail** , do pole **do** zadejte platnou e-mailovou adresu a v případě triggeru pravidla uveďte poznámku, která se zobrazí v těle e-mailu.

    > [!NOTE]
    > E-maily se odesílají jenom uživatelům přidaným do aplikace a přihlásili se aspoň jednou. Přečtěte si další informace o [správě uživatelů](howto-administer.md) v Azure IoT Central.

   ![Konfigurace akce](media/howto-create-event-rules/configure_action1.png)

1. Pravidlo uložíte kliknutím na **Uložit**. Toto pravidlo se během několika minut ukončí a začne monitorovat události odesílané do vaší aplikace. Když podmínka zadaná v pravidle souhlasí, pravidlo aktivuje konfigurovanou akci e-mailu.

Do pravidla můžete přidat další akce, jako jsou Microsoft Flow a Webhooky. Můžete přidat až 5 akcí na pravidlo.

- [Microsoft Flow akce](howto-add-microsoft-flow.md) , která aktivuje pracovní postup v Microsoft Flow při aktivaci pravidla 
- [Akce](howto-create-webhooks.md) Webhooku, která upozorní na další služby, když se aktivuje pravidlo

## <a name="parameterize-the-rule"></a>Parametrizovat pravidlo

Akce lze také nakonfigurovat pomocí **vlastnosti zařízení** jako parametru. Pokud je e-mailová adresa uložená jako vlastnost zařízení, dá se použít při definování adresy **na** .

## <a name="delete-a-rule"></a>Odstranění pravidla

Pokud pravidlo už nepotřebujete, odstraňte ho otevřením pravidla a volbou **Odstranit**. Odstraněním pravidla se odebere ze šablony zařízení a všech přidružených zařízení.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Povolení nebo zakázání pravidla pro šablonu zařízení

Přejděte na zařízení a vyberte pravidlo, které chcete povolit nebo zakázat. Chcete-li povolit nebo zakázat pravidlo pro všechna zařízení, která jsou přidružena k šabloně zařízení, přepněte tlačítko **Povolit pravidlo pro všechna zařízení v této šabloně** .

## <a name="enable-or-disable-a-rule-for-a-device"></a>Povolení nebo zakázání pravidla pro zařízení

Přejděte na zařízení a vyberte pravidlo, které chcete povolit nebo zakázat. Přepínač **Povolit pravidlo pro toto zařízení** zaškrtněte, pokud chcete povolit nebo zakázat pravidlo pro toto zařízení.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak vytvořit pravidla v aplikaci Azure IoT Central, tady je několik dalších kroků:

- [Přidat akci Microsoft Flow v pravidlech](howto-add-microsoft-flow.md)
- [Přidat akci Webhooku do pravidel](howto-create-webhooks.md)
- [Seskupit více akcí ke spuštění z jednoho nebo více pravidel](howto-use-action-groups.md)
- [Správa zařízení](howto-manage-devices.md)
