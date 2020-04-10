---
title: Kurz – vytváření a správa pravidel v aplikaci Azure IoT Central
description: Tento kurz ukazuje, jak pravidla Azure IoT Central umožňují sledovat vaše zařízení téměř v reálném čase a automaticky vyvolat akce, jako je například odeslání e-mailu, když se pravidlo aktivuje.
author: dominicbetts
ms.author: dobett
ms.date: 04/06/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 555da74da65f3b1897a276cf819a263334cfa053
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80999056"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Kurz: Vytvoření pravidla a nastavení oznámení v aplikaci Azure IoT Central

*Tento článek je pro operátory, tvůrce a správce.*

Azure IoT Central můžete použít ke vzdálenému monitorování připojených zařízení. Pravidla Azure IoT Central umožňují monitorovat vaše zařízení téměř v reálném čase a automaticky vyvolat akce, jako je například odeslání e-mailu. Tento článek vysvětluje, jak vytvořit pravidla pro sledování telemetrie, která vaše zařízení odesílají.

Zařízení používají telemetrii k odesílání číselných dat ze zařízení. Pravidlo se aktivuje, když vybraná telemetrická metrie zařízení překročí zadanou prahovou hodnotu.

V tomto kurzu vytvoříte pravidlo pro odeslání e-mailu, pokud teplota v&deg; simulovaném zařízení senzoru prostředí překročí 70 F.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvoření pravidla
> * Přidání akce e-mailu

## <a name="prerequisites"></a>Požadavky

Než začnete, [dokončete vytvoření aplikace Azure IoT Central](./quick-deploy-iot-central.md) a [přidejte simulované zařízení do](./quick-create-simulated-device.md) rychlých startů aplikace IoT Central a vytvořte šablonu zařízení **MXChip IoT DevKit,** se kterou budete pracovat.

## <a name="create-a-rule"></a>Vytvoření pravidla

Chcete-li vytvořit pravidlo telemetrie, musí šablona zařízení obsahovat alespoň jednu hodnotu telemetrie. Tento kurz používá simulované zařízení **MXChip IoT DevKit,** které odesílá telemetrii teploty a vlhkosti. Tuto šablonu zařízení jste přidali a vytvořili simulované zařízení v rychlém startu [přidat simulované zařízení do aplikace IoT Central.](./quick-create-simulated-device.md) Pravidlo monitoruje teplotu hlášenou zařízením a odešle e-mail, když jde nad 70 stupňů.

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

    * Bez agregace pravidlo aktivuje pro každý datový bod telemetrie, který splňuje podmínku. Pokud například nakonfigurujete pravidlo tak, aby se aktivovalo, když je teplota vyšší než 70, pravidlo se aktivuje téměř okamžitě, když teplota zařízení překročí tuto hodnotu.
    * S agregací pravidlo aktivuje, pokud agregační hodnota datových bodů telemetrie v časovém okně splňuje podmínku. Pokud například nakonfigurujete pravidlo tak, aby se aktivovalo, když je teplota vyšší než 70 a s průměrnou agregací času 10 minut, pravidlo se aktivuje, když zařízení hlásí průměrnou teplotu vyšší než 70, vypočítanou v intervalu 10 minut.

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

Zvolte pravidlo, které chcete povolit nebo zakázat. Chcete-li pravidlo povolit nebo zakázat pro všechna zařízení, která mají v pravidle obor, přepněte v pravidle tlačítko **Povoleno/Zakázáno.**

## <a name="enable-or-disable-a-rule-for-specific-devices"></a>Povolení nebo zakázání pravidla pro určitá zařízení

Zvolte pravidlo, které chcete přizpůsobit. Pomocí jednoho nebo více filtrů v části **Cílová zařízení** zúžíte rozsah pravidla na zařízení, která chcete monitorovat.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

* Vytvoření pravidla založeného na telemetrii
* Přidání akce

Teď, když jste definovali pravidlo založené na prahové hodnotě, je navrhovaným dalším krokem naučit se:

> [!div class="nextstepaction"]
> [Konfigurace průběžného exportu dat](./howto-export-data.md).
