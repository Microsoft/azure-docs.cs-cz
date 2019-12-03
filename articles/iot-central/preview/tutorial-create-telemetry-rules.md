---
title: Kurz – vytváření a Správa pravidel v aplikaci Azure IoT Central
description: V tomto kurzu se dozvíte, jak pravidla Azure IoT Central umožňují monitorovat zařízení téměř v reálném čase a automaticky vyvolávat akce, jako je odeslání e-mailu, když se pravidlo aktivuje.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: e2ec01e372ebda79272b585ea6f1708029ea7b13
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702540"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Kurz: vytvoření pravidla a nastavení oznámení v aplikaci Azure IoT Central (funkce ve verzi Preview)

*Tento článek je pro operátory, tvůrce a správce.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Pomocí služby Azure IoT Central můžete vzdáleně monitorovat připojená zařízení. Pravidla Azure IoT Central umožňují monitorovat zařízení téměř v reálném čase a automaticky vyvolávat akce, jako je například odeslání e-mailu. V několika kliknutích můžete definovat podmínku pro monitorování telemetrie ze svých zařízení a nakonfigurovat odpovídající akci. Tento článek vysvětluje, jak vytvořit pravidla pro monitorování telemetrie odesílané zařízením.

Zařízení využívají telemetrii k posílání číselných dat ze zařízení. Pravidlo se aktivuje, když vybraná telemetrie zařízení přebírá určenou prahovou hodnotu.

V tomto kurzu vytvoříte pravidlo k odeslání e-mailu v případě, že teplota na zařízení snímače prostředí překračuje 80&deg; F.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření pravidla
> * Přidat akci e-mailu

## <a name="prerequisites"></a>Předpoklady

Než začnete, měli byste dokončit [Vytvoření aplikace IoT Central v Azure](./quick-deploy-iot-central.md) a [Přidat simulované zařízení do](./quick-create-pnp-device.md) rychlých startů pro aplikace IoT Central a vytvořit šablonu zařízení **snímače prostředí** , se kterou chcete pracovat.

## <a name="create-a-rule"></a>Vytvoření pravidla

Aby bylo možné vytvořit pravidlo telemetrie, musí mít šablona zařízení alespoň jednu určenou měření telemetrie. V tomto kurzu se používá zařízení snímače prostředí, které posílá telemetrii teploty a vlhkosti. Tuto šablonu zařízení jste přidali a vytvořili simulované zařízení v části [Přidání simulovaného zařízení do rychlého startu aplikace IoT Central](./quick-create-pnp-device.md) . Pravidlo monitoruje teplotu oznámenou zařízením a pošle e-mail, když překračuje 80 stupňů.

1. V levém podokně vyberte **pravidla**.

1. Pokud jste ještě nevytvořili žádná pravidla, zobrazí se následující obrazovka:

    ![Zatím žádná pravidla](media/tutorial-create-telemetry-rules/rules-landing-page1.png)

1. Pokud chcete přidat nové pravidlo, vyberte **+** .

1. Zadejte název _monitorování teploty_ pro identifikaci pravidla a stiskněte klávesu ENTER.

1. Vyberte šablonu zařízení **snímače životního prostředí** . Ve výchozím nastavení se pravidlo automaticky vztahuje na všechna zařízení přidružená k šabloně zařízení. Pokud chcete vyfiltrovat podmnožinu zařízení, vyberte **+ filtrovat** a použijte vlastnosti zařízení k identifikaci zařízení. Pokud chcete pravidlo zakázat, v záhlaví pravidla přepněte tlačítko **povoleno/zakázáno** :

    ![Filtry a povolení](media/tutorial-create-telemetry-rules/device-filters.png)

### <a name="configure-the-rule-conditions"></a>Konfigurace podmínek pravidla

Podmínky definují kritéria, která pravidla monitorují. V tomto kurzu nakonfigurujete pravidlo, které se aktivuje, když teplota překročí 80&deg; F.

1. V rozevíracím seznamu **telemetrie** vyberte možnost **teplota** .

1. V dalším kroku vyberte **je větší než** jako **operátor** a jako **hodnotu**zadejte _80_ .

    ![Podmínka](media/tutorial-create-telemetry-rules/condition-filled-out1.png)

1. Volitelně můžete nastavit **časovou agregaci**. Když vyberete časovou agregaci, musíte také v rozevíracím seznamu agregace vybrat typ agregace, například Average nebo Sum.

    * Bez agregace se pravidlo spustí pro každý datový bod telemetrie, který splňuje podmínku. Pokud je například pravidlo nakonfigurované tak, aby se aktivovalo, když je teplota vyšší než 80, pravidlo se spustí téměř okamžitě, když zařízení ohlásí teplotu > 80.
    * Při agregaci se pravidlo aktivuje, pokud agregovaná hodnota datových bodů telemetrie v časovém intervalu splňuje podmínku. Pokud je například pravidlo nakonfigurované tak, aby se aktivovalo, když je teplota nad 80, časová agregace je nastavená na 10 minut a typ agregace je průměr, pak se pravidlo aktivuje, když zařízení ohlásí průměrnou teplotu > 80 a počítá se za 10 minut. doba.

     ![Agregační podmínka](media/tutorial-create-telemetry-rules/aggregate-condition-filled-out1.png)

Výběrem **+ Podmínka**můžete přidat k pravidlu několik podmínek. Pokud je zadáno více podmínek, musí být splněny všechny podmínky, aby se pravidlo aktivovalo. Každá podmínka je propojena implicitní klauzulí `AND`. Pokud používáte časovou agregaci s více podmínkami, musí být agregované všechny hodnoty telemetrie.

### <a name="configure-actions"></a>Konfigurace akcí

Po definování podmínky nastavíte akce, které se mají provést, když se pravidlo aktivuje. Akce jsou vyvolány, pokud jsou všechny podmínky zadané v pravidle vyhodnoceny jako pravdivé. V současné době je e-mail jedinou dostupnou akcí.

1. V části **Akce** vyberte **+ e-mail** .

1. Zadejte _Upozornění na teplotu_ jako zobrazovaný název pro akci, vaši e-mailovou adresu v poli **do** a _měli byste se podívat na zařízení!_ jako Poznámka se zobrazí v těle e-mailu.

    > [!NOTE]
    > E-maily se odesílají jenom uživatelům přidaným do aplikace a přihlásili se aspoň jednou. Přečtěte si další informace o [správě uživatelů](howto-administer.md) v Azure IoT Central.

   ![Konfigurace akce](media/tutorial-create-telemetry-rules/configure-action1.png)

1. Akci uložíte kliknutím na **Hotovo**. Do pravidla můžete přidat několik akcí.

1. Pravidlo uložíte kliknutím na **Uložit**. Pravidlo se během několika minut ukončí a spustí se monitorování telemetrie, která se posílá do vaší aplikace. Pokud je splněna podmínka zadaná v pravidle, pravidlo aktivuje konfigurovanou akci e-mailu.

Po chvíli obdržíte e-mailovou zprávu, když se pravidlo aktivuje:

![Příklad e-mailu](media/tutorial-create-telemetry-rules/email.png)

## <a name="delete-a-rule"></a>Odstranění pravidla

Pokud pravidlo už nepotřebujete, odstraňte ho otevřením pravidla a volbou **Odstranit**.

## <a name="enable-or-disable-a-rule"></a>Povolení nebo zakázání pravidla

Vyberte pravidlo, které chcete povolit nebo zakázat. V pravidle přepněte tlačítko **Povolit** nebo **Zakázat** , aby bylo možné povolit nebo zakázat pravidlo pro všechna zařízení, která jsou v pravidle vymezená.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Povolení nebo zakázání pravidla pro zařízení

Vyberte pravidlo, které chcete povolit nebo zakázat. Přidejte filtr do části **scopes (obory** ), chcete-li zahrnout nebo vyloučit určité zařízení v šabloně zařízení.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

* Vytvoření pravidla založeného na telemetrii
* Přidání akce

Teď, když jste definovali pravidlo na základě prahové hodnoty, je navržený další krok, ve kterém se dozvíte, jak:

> [!div class="nextstepaction"]
> [Nakonfigurujte průběžný export dat](./howto-export-data.md).
