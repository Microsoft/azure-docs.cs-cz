---
title: Vytvoření a Správa pravidel událostí v aplikaci Azure IoT Central | Dokumentace Microsoftu
description: Pravidla události ve službě Azure IoT Central umožňují monitorujte svoje zařízení téměř v reálném čase a automatickému vyvolávání akce, jako je odeslání e-mailu, když se pravidlo aktivuje.
author: ankitgupta
ms.author: ankitgup
ms.date: 08/14/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 40c7b2865795f8c6a5cfbabe4d59aea1715d4a57
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "42054673"
---
# <a name="create-an-eevent-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Vytvoření eEvent pravidla a nastavení oznámení v aplikaci Azure IoT Central

Azure IoT Central můžete použít pro vzdálené monitorování připojených zařízení. Pravidla ve službě Azure IoT Central umožňují monitorujte svoje zařízení téměř v reálném čase a automaticky vyvolat akce, jako například odeslat e-mailu nebo aktivovat Microsoft Flow. Pomocí několika kliknutí můžete definovat podmínky pro kterou chcete monitorovat data zařízení a nakonfigurujte odpovídající akci. Tento článek vysvětluje, jak vytvořit pravidla, která sledovat události odeslané ze zařízení.

Zařízení můžete použít měření událostí k odesílání událostí důležité nebo informační zařízení. Pravidlo události aktivuje, když událost vybrané zařízení je ohlášena zařízení.

## <a name="create-an-event-rule"></a>Vytvoření pravidla událostí

Pokud chcete vytvořit pravidlo události, šablona zařízení musí mít alespoň jednu událost měření definovaných. Tento příklad používá Automat chladicí zařízení, která generuje sestavy ventilátor motor chybová událost. Toto pravidlo sleduje události oznámí zařízení a odešle e-mail vždy, když se použije v hlášení události.

1. Pomocí Device Explorer přejděte k šabloně zařízení, pro který chcete přidat pravidlo pro.

1. V rámci vybrané šablony klikněte na existující zařízení. 

    >[!TIP] 
    >Pokud šablonu nebude mít všechna zařízení, nejdříve přidejte nové zařízení.

1. Pokud jste dosud nevytvořili žádná pravidla, zobrazí se následující obrazovka:

    ![Zatím žádná pravidla](media\howto-create-event-rules\Rules_Landing_Page.png)


1. Na **pravidla** klikněte na tlačítko **+ nové pravidlo** zobrazíte typy pravidel, můžete vytvořit.


1. Klikněte na **události** dlaždice a vytvořte pravidlo.

    ![Typy pravidel](media\howto-create-event-rules\Rule_Types.png)

    
1. Zadejte název, který pomáhá identifikovat pravidla v této šabloně zařízení.

1. Pravidla pro všechna zařízení, které jsou vytvořené z této šablony okamžitě povolit, přepněte **Povolit pravidlo pro všechna zařízení pro tuto šablonu**.

    ![Podrobnosti pravidla](media\howto-create-event-rules\Rule_Detail.png)

    Toto pravidlo automaticky platí pro všechna zařízení v šabloně zařízení.

### <a name="configure-the-rule-conditions"></a>Konfigurace podmínky pravidla

Podmínka definuje kritéria, která je sledována tímto pravidlem.

1. Zvolte **+** vedle **podmínky** přidat novou podmínku.

1. Vyberte událost, která chcete monitorovat z rozevíracího seznamu měření. V tomto příkladu **Motor chyba ventilátor** událost byla vybrána.

   ![Podmínka](media\howto-create-event-rules\Condition_Filled_Out.png) 


1. Volitelně můžete také nastavit **počet** jako **agregace** a zadejte odpovídající prahovou hodnotu.

    - Bez agregace, aktivační události pravidlo pro každý datový bod událostí, který splňuje podmínky. Například pokud nakonfigurujete podmínku pravidla, která má aktivovat, když dojde k události "Ventilátor Motor chyba" pak toto pravidlo aktivuje téměř okamžitě, když zařízení ohlásí tuto událost.
    - Pokud počet slouží jako agregační funkci a pak je nutné zadat **prahová hodnota** a **agregační interval** přes která podmínka je potřeba zhodnotit. V takovém případě je agregován počet událostí a pravidlo spustí jenom v případě, že počet agregovaných událostí odpovídá prahovou hodnotu.
 
    Například pokud chcete upozornění, když existuje více než tři události zařízení během 5 minut, pak vyberte události a nastavte agregační funkci "počet", operátor jako "větší než" a "prahová hodnota" jako 3. Nastavení "Agregace časové období" na "5 minut". Pravidlo aktivuje, když více než tři události jsou odeslané ze zařízení do 5 minut. Četnost vyhodnocování pravidel je stejná jako **agregační interval**, to znamená, že v tomto příkladu, pravidlo se vyhodnotí jednou každých 5 minut. 

    ![Přidat podmínku události](media\howto-create-event-rules\Aggregate_Condition_Filled_Out.png)

    >[!NOTE] 
    >Více než jedno měření události mohou být přidány do **podmínku**. Pokud jsou zadány více podmínek, musí být splněny všechny podmínky pro pravidlo pro aktivaci. Každá podmínka získá připojí pomocí klauzule "A" implicitně. Při použití agregace, musí být agregovaný každou měření.

### <a name="configure-actions"></a>Konfigurace akcí

V této části se dozvíte, jak vytvořit akce má provést, když se aktivuje pravidlo. Akce získat vyvoláno, pokud všechny podmínky zadaná v pravidle vyhodnocen na hodnotu true.

1. Zvolte **+** vedle **akce**. Zde můžete zobrazit seznam dostupných akcí. 

    ![Přidání akce](media\howto-create-event-rules\Add_Action.png)

1. Zvolte **e-mailu** akce, zadejte platnou e-mailovou adresu **k** pole a zadejte poznámku vložit do těla e-mailu, když se pravidlo aktivuje.

    > [!NOTE]
    > E-mailů se odesílají pouze pro uživatele, které byly přidány do aplikace a nejméně jednou přihlásili. Další informace o [Správa uživatelů](howto-administer.md) v Azure IoT Central.

   ![Konfigurace akce](media\howto-create-event-rules\Configure_Action.png)

1. Chcete-li uložit pravidlo, zvolte **Uložit**. Pravidlo uvedete během několika minut a zahájí monitorování událostí odesílaných do vaší aplikace. Když se podmínka uvedená v pravidle shoduje, pravidlo aktivuje nakonfigurovaná e-mailové akce.

Pravidla, jako je Microsoft Flow a webhooky, můžete přidat další akce. Můžete přidat až 5 akcí na jedno pravidlo.

- [Microsoft Flow akce](howto-add-microsoft-flow.md) aktivovala pracovního postupu v Microsoft Flow, když se aktivuje pravidlo 
- [Akce Webhooku](howto-create-webhooks.md) upozornit jiné služby, když se aktivuje pravidlo

## <a name="parameterize-the-rule"></a>Parametrizovat pravidlo

Akce lze konfigurovat také pomocí **vlastnosti zařízení** jako parametr. Pokud e-mailovou adresu se ukládá jako vlastnosti zařízení, pak je možné použít při definování **k** adresu.

## <a name="delete-a-rule"></a>Odstranit pravidlo

Pokud pravidlo již nepotřebujete, odstraňte otevřením pravidlo a výběr **odstranit**. Odstraňuje se pravidlo odebere z šablony zařízení a všechna přidružená zařízení.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Povolit nebo zakázat pravidlo šablony zařízení

Přejděte do zařízení a vyberte pravidlo, které chcete povolit nebo zakázat. Přepnout **Povolit pravidlo pro všechna zařízení z této šablony** tlačítko k povolení nebo zakázání pravidla pro všechna zařízení, která jsou přidružená k šabloně zařízení.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Povolit nebo zakázat pravidlo pro zařízení

Přejděte do zařízení a vyberte pravidlo, které chcete povolit nebo zakázat. Přepnout **Povolit pravidlo pro toto zařízení** tlačítko k povolení nebo zakázání pravidla pro dané zařízení.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak vytvořit pravidla v aplikaci Azure IoT Central, tady jsou některé další krok:

- [Přidání akce Microsoft Flow v pravidlech](howto-add-microsoft-flow.md)
- [Přidání akce Webhooku v pravidlech](howto-create-webhooks.md)
- [Jak spravovat vaše zařízení](howto-manage-devices.md)
