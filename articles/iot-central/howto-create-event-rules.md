---
title: Vytvoření a Správa pravidel událostí v aplikaci Azure IoT Central | Dokumentace Microsoftu
description: Pravidla události ve službě Azure IoT Central umožňují monitorujte svoje zařízení téměř v reálném čase a automatickému vyvolávání akce, jako je odeslání e-mailu, když se pravidlo aktivuje.
services: iot-central
author: ankitgupta
ms.author: ankitgup
ms.date: 04/29/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: ede7748b1471136cf792c2b30b7c90e12b0b274a
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006844"
---
# <a name="create-an-event-rule-and-set-up-an-action-in-your-azure-iot-central-application"></a>Vytvořit pravidlo události a akce v aplikaci Azure IoT Central

Můžete Microsoft Azure IoT Central a vzdáleně monitorovat připojených zařízení. Pravidla ve službě Azure IoT Central umožňují monitorujte svoje zařízení téměř v reálném čase a automatickému vyvolávání akce, jako je odesílání e-mailu nebo při splnění podmínky pravidla, která aktivuje pracovní postup ve službě Microsoft Flow. Pomocí několika kliknutí můžete definovat podmínku, která má monitorovat data zařízení a nakonfigurujte akci, která se má vyvolat. Tento článek vysvětluje pravidlo podrobně monitorování událostí.

Azure IoT Central používá [událost měření](howto-set-up-template.md) k zaznamenání dat o zařízení. Každý typ měření má klíčové atributy, které definují měření. Můžete vytvořit pravidla pro každý typ měření zařízení monitorování a generovat výstrahy, když se pravidlo aktivuje. Pravidlo události aktivuje, když událost vybrané zařízení je ohlášena zařízení.

## <a name="create-an-event-rule"></a>Vytvoření pravidla událostí

V této části se dozvíte, jak vytvořit pravidlo události. Tento příklad používá Automat chladicí zařízení této sestavy ventilátor motor chybové události. Toto pravidlo sleduje události oznámí zařízení a odešle e-mail vždy, když se použije v hlášení události.

1. Přejděte na stránku podrobností zařízení pro zařízení, které chcete přidat pravidlo.

1. Pokud jste dosud nevytvořili žádná pravidla, zobrazí se následující obrazovka:

    ![Zatím žádná pravidla](media/howto-create-event-rules/image1.png)

1. Na **pravidla** kartě **+ nové pravidlo** zobrazíte typy pravidel, můžete vytvořit.

    ![Typy pravidel](media/howto-create-event-rules/image2.png)

1. Klikněte na **události** otevřete formulář pro vytvoření pravidla.

    ![Pravidla události](media/howto-create-event-rules/image3.png)

1. Zvolte název, který pomáhá identifikovat pravidla v této šabloně zařízení.

1. Pravidla pro všechna zařízení, které jsou vytvořené z této šablony okamžitě povolit, přepněte **Povolit pravidlo**.

### <a name="configure-the-rule-condition"></a>Konfigurace podmínky pravidla

Tato část ukazuje, jak přidat podmínku monitorování měření ventilátor motor chybové události.

1. Zvolte **+** vedle **podmínku**.

1. Vyberte událost měření z rozevíracího seznamu, který chcete monitorovat. V tomto příkladu **Motor chyba ventilátor** událost byla vybrána.

1. Volitelně můžete zadat také hodnotu v případě, že chcete monitorovat určitou hodnotu události hlášených zařízení. Například zařízení hlásí stejnou událost s různé chybové kódy, jako hodnotu v podmínce pravidla, zadejte kód chyby: zajistí, že pravidlo aktivuje pouze v případě, že zařízení pošle tuto konkrétní hodnotu jako datovou část události. Ponechání tomto prázdné znamená, že pravidlo se aktivuje vždy, když zařízení pošle událostí bez ohledu na hodnotu událostí.

    ![Přidat podmínku události](media/howto-create-event-rules/image4.png)

    > [!NOTE]
    > Při definování podmínku pravidla události, je třeba vybrat alespoň jednu událost měření.

1. Klikněte na tlačítko **Uložit** uložte pravidlo. Pravidlo uvedete během několika minut a zahájí monitorování událostí odesílaných do vaší aplikace.

### <a name="add-an-action"></a>Přidání akce

Tato část ukazuje, jak přidat akci pro pravidlo. To ukazuje, jak přidat akci e-mailu, ale můžete také [přidejte akci Microsoft Flow](howto-add-microsoft-flow.md) pravidla aktivovala pracovního postupu v Microsoft Flow, když se aktivuje pravidlo.

> [!NOTE]
> V tuto chvíli může být přidružená k jediné pravidlo pouze 1 akce.

1. Zvolte **+** vedle **akce**. Zde můžete zobrazit seznam dostupných akcí.

    ![Přidání akce](media/howto-create-event-rules/image5.png)

1. Zvolte **e-mailu** akce, zadejte platnou e-mailovou adresu **k** pole a zadejte poznámku vložit do těla e-mailu, když se pravidlo aktivuje.

    > [!NOTE]
    > E-mailů se odesílají pouze pro uživatele, které byly přidány do aplikace a nejméně jednou přihlásili. Další informace o [Správa uživatelů](howto-administer.md) v Azure IoT Central.

   ![Konfigurace akce](media/howto-create-event-rules/image6.png)

1. Klikněte na **Uložit**. Pravidlo uvedete během několika minut a zahájí monitorování událostí odesílaných do vaší aplikace. Když se podmínka uvedená v pravidle shoduje, pravidlo aktivuje nakonfigurovaná e-mailové akce.

## <a name="parameterize-the-rule"></a>Parametrizovat pravidlo

Akce lze konfigurovat také pomocí **vlastnosti zařízení** jako parametr. Pokud e-mailovou adresu se ukládá jako vlastnosti zařízení, pak je možné použít při definování **k** adresu.

## <a name="delete-a-rule"></a>Odstranit pravidlo

Pokud pravidlo již nepotřebujete, odstraňte otevřením pravidlo a výběr **odstranit**. Odstraňuje se pravidlo odebere z šablony zařízení a všechna přidružená zařízení.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Povolit nebo zakázat pravidlo šablony zařízení

Přejděte do zařízení a vyberte pravidlo, které chcete povolit nebo zakázat. Při přepnutí **Povolit pravidlo pro všechna zařízení z této šablony** tlačítko v pravidle povolí nebo zakáže pravidlo pro všechna zařízení přidružená k šabloně zařízení.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Povolit nebo zakázat pravidlo pro zařízení

Přejděte do zařízení a vyberte pravidlo, které chcete povolit nebo zakázat. Přepnout **Povolit pravidlo pro toto zařízení** tlačítko k povolení nebo zakázání pravidla pro dané zařízení.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak vytvořit pravidla v aplikaci Azure IoT Central, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Přidání akce Microsoft Flow k pravidlu](howto-add-microsoft-flow.md)
> [jak ke správě svých zařízení](howto-manage-devices.md).
