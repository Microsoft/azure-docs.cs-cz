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
ms.openlocfilehash: d6deecf558105701be591c1f08923eca2c1e3bbd
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879939"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Vytvoření pravidla telemetrie a nastavení oznámení v aplikaci Azure IoT Central (funkce ve verzi Preview)

*Tento článek je pro operátory, tvůrce a správce.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Pomocí služby Azure IoT Central můžete vzdáleně monitorovat připojená zařízení. Pravidla Azure IoT Central umožňují monitorovat zařízení téměř v reálném čase a automaticky vyvolávat akce, jako je například odeslání e-mailu nebo triggeru Microsoft Flow. V několika kliknutích můžete definovat podmínku, pro kterou chcete monitorovat data zařízení, a nakonfigurovat odpovídající akci. Tento článek vysvětluje, jak vytvořit pravidla pro monitorování telemetrie odesílané zařízením.

Zařízení můžou používat měření telemetrie k posílání číselných dat ze zařízení. Pravidlo telemetrie se aktivuje, když vybraná telemetrie zařízení přebírá určenou prahovou hodnotu.

## <a name="create-a-telemetry-rule"></a>Vytvoření pravidla telemetrie

Aby bylo možné vytvořit pravidlo telemetrie, musí mít definice zařízení alespoň jednu určenou měření telemetrie. V tomto příkladu se používá mrazírenské zařízení Automate na počítač, které posílá telemetrii teploty a vlhkosti. Pravidlo monitoruje teplotu oznámenou zařízením a pošle e-mail, když překračuje 80 stupňů.

1. Přejděte na stránku **pravidla** .

1. Pokud jste ještě nevytvořili žádná pravidla, zobrazí se následující obrazovka:

    ![Zatím žádná pravidla](media/howto-create-telemetry-rules-pnp/rules-landing-page1.png)

1. Vyberte **+ nové pravidlo** a zobrazte typy pravidel, která můžete vytvořit.

1. Vyberte **telemetrie** a vytvořte pravidlo pro monitorování telemetrie zařízení.

    ![Typy pravidel](media/howto-create-telemetry-rules-pnp/rule-types1.png)


1. Zadejte název, který vám pomůže identifikovat pravidlo a stisknout Enter.

1. V části scopes (obory) vyberte definici zařízení, na kterou chcete toto pravidlo nastavit. Na této obrazovce můžete také vyfiltrovat zařízení, na která se pravidlo vztahuje, pomocí **+ Filter**. Pravidlo se automaticky použije pro všechna zařízení v šabloně zařízení. Pokud chcete pravidlo zakázat, vyberte v záhlaví tlačítko **Zakázat** .

### <a name="configure-the-rule-conditions"></a>Konfigurace podmínek pravidla

Podmínka definuje kritéria, která jsou sledována pravidlem.

1. Vyberte, zda chcete **nastavit agregaci** jako zapnuto nebo vypnuto.

      - Agregace je volitelná. Bez agregace se pravidlo spustí pro každý datový bod telemetrie, který splňuje podmínku. Pokud je například pravidlo nakonfigurované tak, aby se aktivovalo, když je teplota vyšší než 80, pravidlo se spustí téměř okamžitě, když zařízení ohlásí teplotu > 80.
      - Pokud je zvolena agregační funkce, jako je například Average, min, Max, Count, musí uživatel zadat **časový interval** , ve kterém je nutné vyhodnotit podmínku. Pokud jste například nastavili periodu na 5 minut a pravidlo vyhledá průměrnou teplotu nad 80, pravidlo se aktivuje, když je průměrná teplota nad 80 po dobu alespoň 5 minut. Frekvence vyhodnocení pravidla je stejná jako **časový interval**, což znamená, že v tomto příkladu se pravidlo vyhodnocuje jednou za 5 minut.

1. Z rozevíracího seznamu **měření** vyberte telemetrii, kterou chcete monitorovat.

1. Dále vyberte **operátor** a zadejte **hodnotu**.

     ![Podmínka](media/howto-create-telemetry-rules-pnp/aggregate-condition-filled-out1.png)


>[!NOTE]
>Výběrem **+ Podmínka**lze přidat více než jedno měření telemetrie. Pokud je zadáno více podmínek, musí být splněny všechny podmínky, aby se pravidlo aktivovalo. Každá podmínka se implicitně spojí s klauzulí a. Při použití agregace musí být každé měření agregované.

### <a name="configure-actions"></a>Konfigurace akcí

V této části se dozvíte, jak nastavit akce, které se mají provést při vyvolání pravidla. Akce se vyvolají, pokud se všechny podmínky zadané v pravidle vyhodnotí jako pravdivé.

1. V části **Akce** klikněte na **akci +** . Tady se zobrazí seznam dostupných akcí.  

    ![Přidat akci](media/howto-create-telemetry-rules-pnp/add-action1.png)


1. Vyberte akci **e-mail** , zadejte zobrazovaný název pro akci, platnou e-mailovou adresu v poli **do** a zadejte poznámku, která se zobrazí v těle e-mailu, když se pravidlo aktivuje.

    > [!NOTE]
    > E-maily se odesílají jenom uživatelům přidaným do aplikace a přihlásili se aspoň jednou. Přečtěte si další informace o [správě uživatelů](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) v Azure IoT Central.

   ![Konfigurace akce](media/howto-create-telemetry-rules-pnp/configure-action1.png)


1. Akci uložíte kliknutím na Hotovo.

1. Pravidlo uložíte kliknutím na **Uložit**. Pravidlo se během několika minut ukončí a spustí se monitorování telemetrie, která se posílá do vaší aplikace. Pokud je splněna podmínka zadaná v pravidle, pravidlo aktivuje konfigurovanou akci e-mailu.

## <a name="parameterize-the-rule"></a>Parametrizovat pravidlo

Pravidla můžou z **vlastností zařízení** odvodit určité šířky jako parametry. Použití parametrů je užitečné v situacích, kdy se mezní hodnoty telemetrie liší pro různá zařízení. Při vytváření pravidla vyberte vlastnost zařízení, která určuje mezní hodnotu, jako je například **maximální ideální prahová**hodnota, namísto zadání absolutní hodnoty, například 80 stupňů. Když se pravidlo spustí, bude odpovídat telemetrie zařízení s hodnotou nastavenou ve vlastnosti zařízení.

Použití parametrů je efektivní způsob, jak snížit počet pravidel, která se mají spravovat.

Akce lze také nakonfigurovat pomocí **vlastnosti zařízení** jako parametru. Pokud je e-mailová adresa uložená jako vlastnost, dá se použít při definování adresy **na** .

## <a name="delete-a-rule"></a>Odstranění pravidla

Pokud pravidlo už nepotřebujete, odstraňte ho otevřením pravidla a volbou **Odstranit**. Odstraněním pravidla se odebere ze šablony zařízení a všech přidružených zařízení.

## <a name="enable-or-disable-a-rule"></a>Povolení nebo zakázání pravidla

Vyberte pravidlo, které chcete povolit nebo zakázat. V pravidle přepněte tlačítko **Povolit** nebo **Zakázat** , aby bylo možné povolit nebo zakázat pravidlo pro všechna zařízení, která jsou v pravidle vymezená.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Povolení nebo zakázání pravidla pro zařízení

Vyberte pravidlo, které chcete povolit nebo zakázat. Přidejte filtr do části scopes ( **obory** ), chcete-li zahrnout nebo vyloučit určité zařízení v šabloně zařízení.

## <a name="next-steps"></a>Další postup

Teď, když jste se seznámili s vytvářením pravidel v aplikaci Azure IoT Central, je navržený další krok, kde se dozvíte, [jak spravovat vaše zařízení](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .
