---
title: Vytváření a Správa pravidel událostí v aplikaci Azure IoT Central | Microsoft Docs
description: Pravidla událostí pro Azure IoT Central umožňují monitorovat zařízení téměř v reálném čase a automaticky vyvolávat akce, jako je odeslání e-mailu, když se pravidlo aktivuje.
author: dominicbetts
ms.author: dobett
ms.date: 07/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1a7fc2b38e8354fb29255bb7f9d6b2c03ed53725
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879952"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Vytvoření pravidla události a nastavení oznámení v aplikaci Azure IoT Central (funkce ve verzi Preview)

*Tento článek je pro operátory, tvůrce a správce.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Pomocí služby Azure IoT Central můžete vzdáleně monitorovat připojená zařízení. Pravidla Azure IoT Central umožňují monitorovat zařízení téměř v reálném čase a automaticky vyvolávat akce, jako je například odeslání e-mailu. V několika kliknutích můžete definovat podmínku, pro kterou chcete monitorovat data zařízení, a nakonfigurovat odpovídající akci. Tento článek vysvětluje, jak vytvořit pravidla pro monitorování událostí odesílaných zařízením.

Zařízení můžou k posílání důležitých nebo informativních událostí zařízení používat měření událostí. Pravidlo události se aktivuje, když zařízení nahlásí vybranou událost zařízení.

## <a name="create-an-event-rule"></a>Vytvoření pravidla událostí

Aby bylo možné vytvořit pravidlo události, musí mít šablona zařízení alespoň jednu definici měření události. V tomto příkladu se používá mrazírenské zařízení Automate, které hlásí událost chyby motoru ventilátoru. Pravidlo monitoruje událost hlášenou zařízením a při každém hlášení události pošle e-mail.

1. Přejděte na stránku **pravidla** .

1. Pokud jste ještě nevytvořili žádná pravidla, zobrazí se následující obrazovka:

   ![Zatím žádná pravidla](media/howto-create-event-rules-pnp/rules-landing-page1.png)

1. Vyberte **+ nové pravidlo** a zobrazte typy pravidel, která můžete vytvořit.

1. Zvolením možnosti **událost** vytvořte pravidlo monitorování událostí.

   ![Typy pravidel](media/howto-create-event-rules-pnp/rule-types1.png)

1. Zadejte název, který vám pomůže identifikovat pravidlo a stiskněte klávesu ENTER.

1. V části scopes (obory) vyberte definici zařízení , pro kterou chcete toto pravidlo nastavit. Na této obrazovce můžete také vyfiltrovat zařízení, na která se pravidlo vztahuje, pomocí **+ Filter**. Pravidlo se automaticky použije pro všechna zařízení v šabloně zařízení. Pokud chcete pravidlo zakázat, vyberte v záhlaví tlačítko **Zakázat** .

### <a name="configure-the-rule-conditions"></a>Konfigurace podmínek pravidla

Podmínka definuje kritéria, která jsou sledována pravidlem.

1. Vyberte, zda chcete **nastavit agregaci** jako zapnuto nebo vypnuto.

   - Bez agregace pravidlo spustí aktivační událost pro každý datový bod události, který splňuje podmínku. Pokud například nakonfigurujete podmínku pravidla, která se aktivuje při výskytu **chybové** události ventilátoru, pak se pravidlo spustí téměř okamžitě, jakmile zařízení ohlásí událost.
   - Pokud je **počet** použit jako agregační funkce, musíte zadat **hodnotu** a **časový interval** , ve kterém je nutné vyhodnotit podmínku. V tomto případě je počet událostí agregovaný a pravidlo se aktivuje pouze v případě, že počet agregovaných událostí odpovídá hodnotě.

1. Z rozevíracího seznamu **měření** vyberte událost, kterou chcete monitorovat. V tomto příkladu byla vybrána událost **chyb motoru ventilátoru** .

1. Volitelně můžete také nastavit **počet** jako agregaci a zadat hodnotu, na které bude pravidlo aktivováno.

     Například pokud chcete upozornit, když v 5 minutách dojde k více než třem událostem zařízení, vyberte událost a nastavte agregační funkci jako **Count**, operátor jako **větší než**a **hodnotu** 3. Nastavte **časový interval** na **5 minut**. Pravidlo se aktivuje, když zařízení během 5 minut odešle víc než tři události. Frekvence vyhodnocení pravidla je stejná jako **časový interval**, což znamená, že v tomto příkladu se pravidlo vyhodnocuje jednou za 5 minut.

 ![Podmínka](media/howto-create-event-rules-pnp/aggregate-condition-filled-out1.png)

> [!NOTE]
> Pod podmínkou lze přidat více než jednu měřeníudálosti. Pokud je zadáno více podmínek, musí být splněny všechny podmínky, aby se pravidlo aktivovalo. Každá podmínka se implicitně spojí s klauzulí a. Při použití agregace musí být každé měření agregované.

### <a name="configure-actions"></a>Konfigurace akcí

V této části se dozvíte, jak nastavit akce, které se mají provést při vyvolání pravidla. Akce se vyvolají, pokud se všechny podmínky zadané v pravidle vyhodnotí jako pravdivé.

1. V části **Akce** klikněte na **akci +** . Tady se zobrazí seznam dostupných akcí.  

   ![Přidat akci](media/howto-create-event-rules-pnp/add-action1.png)

1. Vyberte akci **e-mail** , zadejte zobrazovaný název pro akci, platnou e-mailovou adresu v poli **do** a zadejte poznámku, která se zobrazí v těle e-mailu, když se pravidlo aktivuje.

   > [!NOTE]
   > E-maily se odesílají jenom uživatelům přidaným do aplikace a přihlásili se aspoň jednou. Přečtěte si další informace o [správě uživatelů](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) v Azure IoT Central.

   ![Konfigurace akce](media/howto-create-event-rules-pnp/configure-action1.png)

1. Akci uložíte kliknutím na Hotovo.

1. Pravidlo uložíte kliknutím na **Uložit**. Toto pravidlo se během několika minut ukončí a spustí monitorovací události, které se odesílají do vaší aplikace. Pokud je splněna podmínka zadaná v pravidle, pravidlo aktivuje konfigurovanou akci e-mailu.

## <a name="parameterize-the-rule"></a>Parametrizovat pravidlo

Pravidla můžou z **vlastností zařízení** odvodit určité šířky jako parametry. Použití parametrů je užitečné v situacích, kdy se prahové hodnoty událostí liší v různých zařízeních. Když vytvoříte pravidlo, vyberte vlastnost zařízení, která určuje prahovou hodnotu, jako je například **maximální ideální prahová**hodnota, namísto zadání absolutní hodnoty, například 3 události. Když se pravidlo spustí, bude odpovídat událostem zařízení s hodnotou nastavenou ve vlastnosti zařízení.

Použití parametrů je efektivní způsob, jak snížit počet pravidel, která se mají spravovat.

Akce lze také nakonfigurovat pomocí **vlastnosti zařízení** jako parametru. Pokud je e-mailová adresa uložená jako vlastnost zařízení, dá se použít při definování adresy **na** .

## <a name="delete-a-rule"></a>Odstranění pravidla

Pokud pravidlo už nepotřebujete, odstraňte ho otevřením pravidla a volbou **Odstranit**. Odstraněním pravidla se odebere ze šablony zařízení a všech přidružených zařízení.

## <a name="enable-or-disable-a-rule"></a>Povolení nebo zakázání pravidla

Vyberte pravidlo, které chcete povolit nebo zakázat. V pravidle přepněte tlačítko **Povolit** nebo **Zakázat** , aby bylo možné povolit nebo zakázat pravidlo pro všechna zařízení, která jsou v oboru pro dané pravidlo.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Povolení nebo zakázání pravidla pro zařízení

Vyberte pravidlo, které chcete povolit nebo zakázat. Přidejte filtr do části scopes ( **obory** ), chcete-li zahrnout nebo vyloučit určité zařízení v šabloně zařízení.

## <a name="next-steps"></a>Další postup

Teď, když jste se seznámili s vytvářením pravidel v aplikaci Azure IoT Central, je navržený další krok, kde se dozvíte, [jak spravovat vaše zařízení](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .
