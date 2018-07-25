---
title: Vytvoření a Správa pravidel telemetrie v aplikaci Azure IoT Central | Dokumentace Microsoftu
description: Pravidla telemetrických dat ve službě Azure IoT Central umožňují monitorujte svoje zařízení téměř v reálném čase a automatickému vyvolávání akce, jako je odeslání e-mailu, když se pravidlo aktivuje.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 42516e4dd6a85e0d07d4a8e70e958b2ec6e84aad
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225196"
---
# <a name="create-a-telemetry-rule-and-set-up-an-action-in-your-azure-iot-central-application"></a>Vytvořit pravidlo telemetrie a nastavit akci v aplikaci Azure IoT Central

Můžete Microsoft Azure IoT Central a vzdáleně monitorovat připojených zařízení. Pravidla ve službě Azure IoT Central umožňují monitorujte svoje zařízení téměř v reálném čase a automatickému vyvolávání akce, jako je odesílání e-mailu nebo při splnění podmínky pravidla, která aktivuje pracovní postup ve službě Microsoft Flow. Pomocí několika kliknutí můžete definovat podmínky, které mají sledujte svoje zařízení a nakonfigurujte akci, která se má vyvolat. Tento článek vysvětluje pravidlo telemetrická data podrobněji.

Azure IoT Central používá [telemetrická data měření](howto-set-up-template.md) k zaznamenání dat o zařízení. Každý typ měření má klíčové atributy, které definují měření. Můžete vytvořit pravidla pro každý typ měření zařízení monitorování a generovat výstrahy, když se pravidlo aktivuje. Telemetrie pravidlo aktivuje, když telemetrická data pro vybrané zařízení překročí zadanou prahovou hodnotu.

## <a name="create-a-telemetry-rule"></a>Vytvořit pravidlo telemetrie

Tato část ukazuje, jak vytvořit pravidlo telemetrická data. Tento příklad používá vzduchovod připojených zařízení, která odesílá telemetrické údaje teploty a vlhkosti. Toto pravidlo monitoruje teplota hlášená zařízení a odešle e-mailu, když se překročí 80 stupňů.

1. Přejděte na stránku podrobností zařízení pro zařízení, které chcete přidat pravidlo.

1. Pokud jste dosud nevytvořili žádná pravidla, zobrazí se následující obrazovka:

    ![Zatím žádná pravidla](media/howto-create-telemetry-rules/image1.png)

1. Na **pravidla** kartě **+ nové pravidlo** zobrazíte typy pravidel, můžete vytvořit.

    ![Typy pravidel](media/howto-create-telemetry-rules/image2.png)

1. Zvolte **Telemetrie** dlaždici otevřete formulář pro vytvoření pravidla.

    ![Pravidlo telemetrie](media/howto-create-telemetry-rules/image3.png)

1. Zvolte název, který pomáhá identifikovat pravidla v této šabloně zařízení.

1. Pravidla pro všechna zařízení, které jsou vytvořené z této šablony okamžitě povolit, přepněte **Povolit pravidlo**.

### <a name="configure-the-rule-condition"></a>Konfigurace podmínky pravidla

Tato část ukazuje, jak přidat podmínku monitorování teplotní telemetrie.

1. Zvolte **+** vedle **podmínku**.

1. Zvolte **teploty** typ telemetrie z rozevíracího seznamu. Potom vyberte operátor a zadejte prahovou hodnotu. Můžete přidat více podmínek telemetrická data. Pokud jsou zadány více podmínek, musí být splněny všechny podmínky pro pravidlo pro aktivaci.

    ![Přidání telemetrické podmínky](media/howto-create-telemetry-rules/image4.png)

    > [!NOTE]
    > Vyberte alespoň jeden měření telemetrická data, když definujete podmínku pravidla, která telemetrická data.

1. Klikněte na tlačítko **Uložit** uložte pravidlo. Pravidlo uvedete během několika minut a zahájí monitorování telemetrických dat odesílaných do vaší aplikace.

### <a name="add-an-action"></a>Přidání akce

Tento příklad ukazuje, jak přidat akci pro pravidlo. To ukazuje, jak přidat akci e-mailu, ale můžete také přidat další akce:
-  [Microsoft Flow akce](howto-add-microsoft-flow.md) aktivovala pracovního postupu v Microsoft Flow, když se aktivuje pravidlo
- [Akce Webhooku](howto-create-webhooks.md) upozornit jiné služby, když se aktivuje pravidlo

> [!NOTE]
> V tuto chvíli může být přidružená k jediné pravidlo pouze 1 akce.

1. Zvolte **+** vedle **akce**. Zde můžete zobrazit seznam dostupných akcí.

    ![Přidání akce](media/howto-create-telemetry-rules/image5.png)

1. Zvolte **e-mailu** akce, zadejte platnou e-mailovou adresu **k** pole a zadejte poznámku vložit do těla e-mailu, když se pravidlo aktivuje.

    > [!NOTE]
    > E-mailů se odesílají pouze pro uživatele, které byly přidány do aplikace a nejméně jednou přihlásili. Další informace o [Správa uživatelů](howto-administer.md) v Azure IoT Central.

   ![Konfigurace akce](media/howto-create-telemetry-rules/image6.png)

1. Klikněte na **Uložit**. Pravidlo uvedete během několika minut a zahájí monitorování telemetrických dat odesílaných do vaší aplikace. Když se podmínka uvedená v pravidle shoduje, pravidlo aktivuje nakonfigurovaná e-mailové akce.

## <a name="parameterize-the-rule"></a>Parametrizovat pravidlo

Pravidla lze odvodit z určité hodnoty **vlastnosti zařízení** jako parametry. Pomocí parametrů je užitečné ve scénářích, kde se liší telemetrie prahové hodnoty pro různá zařízení. Když vytvoříte pravidlo, zvolte Vlastnosti zařízení, která určuje mezní hodnotu, jako například **maximální prahová hodnota ideální**, namísto zadávání absolutní hodnotu, jako je například 80 stupňů. Když se pravidlo spustí, odpovídá telemetrii zařízení s hodnotou poskytla ve vlastnosti zařízení.

Pomocí parametrů je účinný způsob, jak snížit počet pravidel pro správu na zařízení šablony.

Akce lze konfigurovat také pomocí **vlastnosti zařízení** jako parametr. Pokud e-mailovou adresu se ukládá jako vlastnosti zařízení, pak je možné použít při definování **k** adresu.

## <a name="delete-a-rule"></a>Odstranit pravidlo

Pokud pravidlo již nepotřebujete, odstraňte otevřením pravidlo a výběr **odstranit**. Odstraňuje se pravidlo odebere z šablony zařízení a všechna přidružená zařízení.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Povolit nebo zakázat pravidlo šablony zařízení

Přejděte do zařízení a vyberte pravidlo, které chcete povolit nebo zakázat. Při přepnutí **Povolit pravidlo pro všechna zařízení z této šablony** tlačítko v pravidle povolí nebo zakáže pravidlo pro všechna zařízení přidružená k šabloně zařízení.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Povolit nebo zakázat pravidlo pro zařízení

Přejděte do zařízení a vyberte pravidlo, které chcete povolit nebo zakázat. Přepnout **Povolit pravidlo pro toto zařízení** tlačítko k povolení nebo zakázání pravidla pro dané zařízení.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak upravit pravidla v aplikaci Azure IoT Central, tady jsou další navrhované kroky:

> [!div class="nextstepaction"]
> [Přidání akce Microsoft Flow k pravidlu](howto-add-microsoft-flow.md)
> [způsobu správy zařízení](howto-manage-devices.md)
