---
title: Kurz – vytváření a správa pravidel v aplikaci Azure IoT Central
description: Tento kurz ukazuje, jak pravidla Azure IoT Central umožňují sledovat vaše zařízení téměř v reálném čase a automaticky vyvolat akce, jako je například odeslání e-mailu, když se pravidlo aktivuje.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f61a41fa89c7006341db928472f6b20d272bc550
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77167433"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Kurz: Vytvoření pravidla a nastavení oznámení v aplikaci Azure IoT Central

*Tento článek je pro operátory, tvůrce a správce.*

Azure IoT Central můžete použít ke vzdálenému monitorování připojených zařízení. Pravidla Azure IoT Central umožňují monitorovat vaše zařízení téměř v reálném čase a automaticky vyvolat akce, jako je například odeslání e-mailu. Pouhými několika kliknutími můžete definovat podmínku pro sledování telemetrie ze zařízení a konfiguraci odpovídající akce. Tento článek vysvětluje, jak vytvořit pravidla pro sledování telemetrie odeslané zařízením.

Zařízení používají telemetrii k odesílání číselných dat ze zařízení. Pravidlo se aktivuje, když vybraná telemetrická metrie zařízení překročí zadanou prahovou hodnotu.

V tomto kurzu vytvoříte pravidlo pro odeslání e-mailu, pokud teplota v&deg; simulovaném zařízení senzoru prostředí překročí 70 F.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvoření pravidla
> * Přidání akce e-mailu

## <a name="prerequisites"></a>Požadavky

Než začnete, měli byste dokončit [vytvoření aplikace Azure IoT Central](./quick-deploy-iot-central.md) a přidat simulované zařízení do rychlého spuštění aplikace [IoT Central](./quick-create-pnp-device.md) k vytvoření šablony zařízení **MXChip IoT DevKit** pro práci.

## <a name="create-a-rule"></a>Vytvoření pravidla

Chcete-li vytvořit pravidlo telemetrie, musí být definováno alespoň jedno měření telemetrie. Tento kurz používá zařízení senzoru prostředí, které odesílá telemetrii teploty a vlhkosti. Tuto šablonu zařízení jste přidali a vytvořili simulované zařízení v rychlém startu [přidat simulované zařízení do aplikace IoT Central.](./quick-create-pnp-device.md) Pravidlo monitoruje teplotu hlášenou zařízením a odešle e-mail, když jde nad 70 stupňů.

1. V levém podokně vyberte **Pravidla**.

1. Pokud jste ještě nevytvořili žádná pravidla, zobrazí se následující obrazovka:

    ![Dosud žádná pravidla](media/tutorial-create-telemetry-rules/rules-landing-page1.png)

1. Výběrem **+** přidáte nové pravidlo.

1. Zadejte název _Monitor teploty,_ který pravidlo identifikuje, a stiskněte Enter.

1. Vyberte šablonu zařízení **MXChip IoT DevKit.** Ve výchozím nastavení se pravidlo automaticky vztahuje na všechna zařízení přidružená k šabloně zařízení. Chcete-li filtrovat podmnožinu zařízení, vyberte **možnost + Filtr** a použijte vlastnosti zařízení k identifikaci zařízení. Chcete-li pravidlo zakázat, přepněte v záhlaví pravidla tlačítko **Povoleno/Zakázáno:**

    ![Filtry a povolení](media/tutorial-create-telemetry-rules/device-filters.png)

### <a name="configure-the-rule-conditions"></a>Konfigurace podmínek pravidla

Podmínky definují kritéria, která pravidlo monitoruje. V tomto kurzu nakonfigurujete pravidlo pro&deg; oheň, když teplota překročí 70 F.

1. V rozevíracím souboru **Telemetrie** vyberte **Možnost Teplota.**

1. Dále zvolte **Je větší než** jako **Operátor** a zadejte _70_ jako **Value**.

    ![Podmínka](media/tutorial-create-telemetry-rules/condition-filled-out1.png)

1. Volitelně můžete nastavit **agregaci času**. Když vyberete časovou agregaci, musíte také vybrat typ agregace, například průměr nebo součet z rozevíracího přehledu agregace.

    * Bez agregace pravidlo aktivuje pro každý datový bod telemetrie, který splňuje podmínku. Pokud je například pravidlo nakonfigurováno tak, aby se aktivovalo, když je teplota vyšší než 70, pravidlo se aktivuje téměř okamžitě, když zařízení hlásí teplotu > 70.
    * S agregací pravidlo aktivuje, pokud agregační hodnota datových bodů telemetrie v časovém okně splňuje podmínku. Pokud je například pravidlo nakonfigurováno tak, aby se aktivovalo, když je teplota vyšší než 70, je agregace času nastavena na 10 minut a typ agregace je průměrný, pak se pravidlo aktivuje, když zařízení hlásí průměrnou teplotu > 70, vypočtenou v intervalu 10 minut.

     ![Agregovaná podmínka](media/tutorial-create-telemetry-rules/aggregate-condition-filled-out1.png)

Výběrem možnosti + Podmínka můžete k pravidlu přidat více **podmínek**. Pokud je zadáno více podmínek, musí být splněny všechny podmínky, aby se pravidlo aktivovat. Každá podmínka je spojena implicitní `AND` klauzulí. Pokud používáte agregaci času s více podmínkami, musí být agregovány všechny hodnoty telemetrie.

### <a name="configure-actions"></a>Konfigurace akcí

Po definování podmínky nastavíte akce, které mají být podniknue při požáru pravidla. Akce jsou vyvolány, pokud jsou všechny podmínky zadané v pravidle vyhodnoceny jako true.

1. V části **Akce** vyberte **+ E-mail.**

1. Zadejte _upozornění na teplotu_ jako zobrazovaný název akce, e-mailovou adresu do pole **Do** a měli byste _zařízení zkontrolovat!_ jako poznámku, která se zobrazí v textu e-mailu.

    > [!NOTE]
    > E-maily jsou odesílány pouze uživatelům, kteří byli přidáni do aplikace a přihlásili se alespoň jednou. Přečtěte si další informace o [správě uživatelů](howto-administer.md) v Azure IoT Central.

   ![Konfigurovat akci](media/tutorial-create-telemetry-rules/configure-action1.png)

1. Chcete-li akci uložit, zvolte **Hotovo**. K pravidlu můžete přidat více akcí.

1. Chcete-li pravidlo uložit, zvolte **Uložit**. Pravidlo přejde do provozu během několika minut a spustí monitorování telemetrie odesílané do vaší aplikace. Pokud je splněna podmínka zadaná v pravidle, pravidlo aktivuje nakonfigurovanou akci e-mailu.

Po chvíli obdržíte e-mailovou zprávu při požáru pravidla:

![Příklad e-mailu](media/tutorial-create-telemetry-rules/email.png)

## <a name="delete-a-rule"></a>Odstranění pravidla

Pokud již pravidlo nepotřebujete, odstraňte ho tak, že pravidlo otevřete a **zvolíte Odstranit**.

## <a name="enable-or-disable-a-rule"></a>Povolení nebo zakázání pravidla

Zvolte pravidlo, které chcete povolit nebo zakázat. Chcete-li pravidlo povolit nebo zakázat, přepněte tlačítko **Povolit** nebo **Zakázat** v pravidle a povolte nebo zakažte pravidlo pro všechna zařízení, která jsou v pravidle vymezena.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Povolení nebo zakázání pravidla pro zařízení

Zvolte pravidlo, které chcete povolit nebo zakázat. Přidejte filtr v části **Obory,** který zahrne nebo vyloučí určité zařízení do šablony zařízení.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

* Vytvoření pravidla založeného na telemetrii
* Přidání akce

Teď, když jste definovali pravidlo založené na prahové hodnotě, je navrhovaným dalším krokem naučit se:

> [!div class="nextstepaction"]
> [Konfigurace průběžného exportu dat](./howto-export-data.md).
