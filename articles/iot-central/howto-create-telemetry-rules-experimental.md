---
title: Vytvoření a Správa pravidel telemetrie v aplikaci Azure IoT Central | Dokumentace Microsoftu
description: Pravidla telemetrických dat ve službě Azure IoT Central umožňují monitorujte svoje zařízení téměř v reálném čase a automatickému vyvolávání akce, jako je odeslání e-mailu, když se pravidlo aktivuje.
author: ankitgupta
ms.author: ankitgup
ms.date: 02/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1d6f43b23bddf2d1ff7a2a41a11b4a2c8623d372
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768619"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Vytvořit pravidlo telemetrická data a nastavení oznámení v aplikaci Azure IoT Central

*Tento článek je pro operátory, tvůrce a správce.*

Azure IoT Central můžete použít pro vzdálené monitorování připojených zařízení. Pravidla ve službě Azure IoT Central umožňují monitorujte svoje zařízení téměř v reálném čase a automaticky vyvolat akce, jako například odeslat e-mailu nebo aktivovat Microsoft Flow. Pomocí několika kliknutí můžete definovat podmínky pro kterou chcete monitorovat data zařízení a nakonfigurujte odpovídající akci. Tento článek vysvětluje, jak vytvořit pravidla, která monitorování telemetrická data odesílaná ze zařízení.

Zařízení můžete použít měření telemetrických dat k odeslání číselná data ze zařízení. Telemetrie pravidlo aktivuje, když telemetrická data pro vybrané zařízení překročí zadanou prahovou hodnotu.

## <a name="create-a-telemetry-rule"></a>Vytvořit pravidlo telemetrie

K vytvoření pravidla telemetrie, šablona zařízení musí mít definován alespoň jeden telemetrická data měření. Tento příklad používá Automat chladicí zařízení, která odesílá telemetrické údaje teploty a vlhkosti. Toto pravidlo monitoruje teplota hlášená zařízení a odešle e-mailu, když se překročí 80 stupňů.

1. Pomocí Device Explorer přejděte k šabloně zařízení, pro který chcete přidat pravidlo pro.

1. V rámci vybrané šablony klikněte na existující zařízení. 

    >[!TIP]
    >Pokud šablonu nebude mít všechna zařízení, nejdříve přidejte nové zařízení.

1. Pokud jste dosud nevytvořili žádná pravidla, zobrazí se na následující obrazovce:

    ![Zatím žádná pravidla](media/howto-create-telemetry-rules-experimental/Rules_Landing_Page.png)

1. Na **pravidla** klikněte na tlačítko **+ nové pravidlo** zobrazíte typy pravidel, můžete vytvořit.

1. Klikněte na tlačítko **Telemetrie** k vytvoření pravidla monitorování telemetrie zařízení.

    ![Typy pravidel](media/howto-create-telemetry-rules-experimental/Rule_Types.png)

1. Zadejte název, který pomáhá identifikovat pravidla v této šabloně zařízení.

1. Umožňuje okamžitě pravidla pro všechna zařízení, které jsou vytvořené pro tuto šablonu, přepnout **Povolit pravidlo pro všechna zařízení pro tuto šablonu**.

   ![Podrobnosti pravidla](media/howto-create-telemetry-rules-experimental/Rule_Detail.png)

    Toto pravidlo automaticky platí pro všechna zařízení v šabloně zařízení.

### <a name="configure-the-rule-conditions"></a>Konfigurace podmínky pravidla

Podmínka definuje kritéria, která je sledována tímto pravidlem.

1. Klikněte na tlačítko **+** vedle **podmínky** přidat novou podmínku.

1. Vyberte telemetrie, kterou chcete monitorovat z **měření** rozevíracího seznamu.

   ![Podmínka](media/howto-create-telemetry-rules-experimental/Aggregate_Condition_Filled_Out.png)

1. Dále zvolte **agregace**, **operátor**a poskytují **prahová hodnota** hodnotu.
    - Agregace je volitelný. Bez agregace, aktivační události pravidlo pro každý datový bod telemetrických dat, který splňuje podmínky. Například, pokud je konfigurováno pravidlo aktivuje, když teplota je víc než 80, pak toto pravidlo aktivuje téměř okamžitě, když zařízení ohlásí teploty > 80.
    - Pokud agregační funkce, jako je průměr, minimum, maximum, počet je vybrán, potom musí uživatel **agregační interval** přes která podmínka je potřeba zhodnotit. Například pokud nastavíte období jako "5 minut" a vaše pravidlo hledá průměrná teplota nad 80, pravidlo aktivuje, když je průměrná teplota nad 80 pro alespoň 5 minut. Četnost vyhodnocování pravidel je stejná jako **agregační interval**, to znamená, že v tomto příkladu, pravidlo se vyhodnotí jednou každých 5 minut.

    >[!NOTE]
    >Více než jedno měření telemetrická data mohou být přidány do **podmínku**. Pokud jsou zadány více podmínek, musí být splněny všechny podmínky pro pravidlo pro aktivaci. Každá podmínka získá připojí pomocí klauzule "A" implicitně. Při použití agregace, musí být agregovaný každou měření.

### <a name="configure-actions"></a>Konfigurace akcí

V této části se dozvíte, jak vytvořit akce má provést, když se aktivuje pravidlo. Akce získat vyvoláno, pokud všechny podmínky zadaná v pravidle vyhodnocen na hodnotu true.

1. Zvolte **+** vedle **akce**. Zde můžete zobrazit seznam dostupných akcí.  

    ![Přidání akce](media/howto-create-telemetry-rules-experimental/Add_Action.png)

1. Zvolte **e-mailu** akce, zadejte platnou e-mailovou adresu **k** pole a zadejte poznámku vložit do těla e-mailu, když se pravidlo aktivuje.

    > [!NOTE]
    > E-mailů se odesílají pouze pro uživatele, které byly přidány do aplikace a nejméně jednou přihlásili. Další informace o [Správa uživatelů](howto-administer-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) v Azure IoT Central.

   ![Konfigurace akce](media/howto-create-telemetry-rules-experimental/Configure_Action.png)

1. Chcete-li uložit pravidlo, zvolte **Uložit**. Pravidlo uvedete během několika minut a zahájí monitorování telemetrických dat odesílaných do vaší aplikace. Když je splněna podmínka uvedená v pravidle, pravidlo aktivuje nakonfigurovaná e-mailovou akci.

Pravidla, jako je Microsoft Flow a webhooky, můžete přidat další akce. Můžete přidat až 5 akcí na jedno pravidlo.

- [Microsoft Flow akce](howto-add-microsoft-flow-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) aktivovala pracovního postupu v Microsoft Flow, když se aktivuje pravidlo 
- [Akce Webhooku](howto-create-webhooks-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) upozornit jiné služby, když se aktivuje pravidlo

## <a name="parameterize-the-rule"></a>Parametrizovat pravidlo

Pravidla lze odvodit z určité hodnoty **vlastnosti zařízení** jako parametry. Pomocí parametrů je užitečné ve scénářích, kde se liší telemetrie prahové hodnoty pro různá zařízení. Když vytvoříte pravidlo, zvolte Vlastnosti zařízení, která určuje mezní hodnotu, jako například **maximální prahová hodnota ideální**, namísto zadávání absolutní hodnotu, jako je například 80 stupňů. Když se pravidlo spustí, odpovídá telemetrii zařízení s hodnotou nastavenou ve vlastnosti zařízení.

Pomocí parametrů je účinný způsob, jak snížit počet pravidel pro správu na zařízení šablony.

Akce lze konfigurovat také pomocí **vlastnosti zařízení** jako parametr. Pokud se e-mailová adresa je uložená jako vlastnost a potom je možné použít při definování **k** adresu.

## <a name="delete-a-rule"></a>Odstranění pravidla

Pokud pravidlo již nepotřebujete, odstraňte otevřením pravidlo a výběr **odstranit**. Odstraňuje se pravidlo odebere z šablony zařízení a všechna přidružená zařízení.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Povolit nebo zakázat pravidlo šablony zařízení

Přejděte do zařízení a vyberte pravidlo, které chcete povolit nebo zakázat. Přepnout **Povolit pravidlo pro všechna zařízení z této šablony** tlačítko v pravidle, která povolí nebo zakáže pravidlo pro všechna zařízení, která jsou přidružená k šabloně zařízení.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Povolit nebo zakázat pravidlo pro zařízení

Přejděte do zařízení a vyberte pravidlo, které chcete povolit nebo zakázat. Přepnout **Povolit pravidlo pro toto zařízení** tlačítko k povolení nebo zakázání pravidla pro dané zařízení.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak vytvořit pravidla v aplikaci Azure IoT Central, tady jsou některé další krok:

- [Přidání akce Microsoft Flow v pravidlech](howto-add-microsoft-flow-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
- [Přidání akce Webhooku v pravidlech](howto-create-webhooks-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
- [Jak spravovat vaše zařízení](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
