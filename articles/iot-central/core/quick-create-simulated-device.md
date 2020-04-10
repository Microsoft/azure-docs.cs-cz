---
title: Úvodní příručka – přidání simulovaného zařízení do Azure IoT Central
description: Tento rychlý start ukazuje, jak vytvořit šablonu zařízení a přidat simulované zařízení do aplikace IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 25e1742612c6fc8c326f2918a4d69c55a9888c97
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000438"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Úvodní příručka: Přidání simulovaného zařízení do aplikace IoT Central

*Tento článek je pro operátory, tvůrce a správce.*

Šablona zařízení definuje možnosti zařízení, které se připojuje k vaší aplikaci IoT Central. Mezi možnosti patří telemetrie, kterou zařízení odesílá, vlastnosti zařízení a příkazy, na které zařízení reaguje. Ze šablony zařízení může tvůrce nebo operátor přidat do aplikace skutečná i simulovaná zařízení. Simulovaná zařízení jsou užitečná pro testování chování aplikace IoT Central před připojením skutečných zařízení.

V tomto rychlém startu přidáte šablonu zařízení pro desku *MXChip IoT DevKit* (DevKit) a vytvoříte simulované zařízení. Chcete-li dokončit tento rychlý start, nepotřebujete skutečné zařízení, pracujete se simulací zařízení. Zařízení DevKit:

* Odešle telemetrii, například teplotu.
* Hlásí vlastnosti specifické pro zařízení, jako je úroveň jasu.
* Reaguje na příkazy, jako je zapnutí a vypnutí.
* Hlásí obecné vlastnosti zařízení, jako je verze firmwaru a sériové číslo.

## <a name="prerequisites"></a>Požadavky

Dokončete rychlý start [aplikace Create a Azure IoT Central](./quick-deploy-iot-central.md) a vytvořte aplikaci IoT Central pomocí vlastní šablony vlastní aplikace > vlastní **aplikace.**

## <a name="create-a-template"></a>Vytvoření šablony

Jako tvůrce můžete vytvářet a upravovat šablony zařízení v aplikaci IoT Central. Po publikování šablony zařízení můžete generovat simulované zařízení nebo připojit skutečná zařízení ze šablony zařízení. Simulovaná zařízení umožňují otestovat chování aplikace před připojením skutečného zařízení.

Pokud chcete do aplikace přidat novou šablonu zařízení, vlevém v levém podokně vyberte kartu **Šablony zařízení.**

![Stránka Šablony zařízení](./media/quick-create-simulated-device/device-definitions.png)

Šablona zařízení obsahuje model schopností zařízení, který definuje telemetrii, kterou zařízení odesílá, vlastnosti zařízení a příkazy, na které zařízení reaguje.

### <a name="add-a-device-capability-model"></a>Přidání modelu schopností zařízení

Existuje několik možností pro přidání modelu funkce zařízení do aplikace IoT Central. Model můžete vytvořit od začátku, importovat model ze souboru nebo vybrat zařízení z katalogu zařízení. IoT Central také podporuje přístup *založený na zařízení,* kde automaticky importuje model z úložiště, když se zařízení připojí poprvé. V tomto rychlém startu zvolíte zařízení z katalogu zařízení pro import modelu schopností zařízení.

Následující kroky ukazují, jak pomocí katalogu zařízení importovat model schopností pro zařízení **MXChip IoT DevKit.** Tato zařízení odesílají telemetrii, například teplotu, do vaší aplikace:

1. Pokud chcete přidat novou **+** šablonu zařízení, vyberte na stránce **Šablony zařízení.**

1. Na stránce **Vybrat typ šablony** přejděte dolů, dokud nenajdete dlaždici **MXChip IoT DevKit.**

1. Vyberte dlaždici **MXChip IoT DevKit** a pak vyberte **Další: Přizpůsobit**.

1. Na stránce **Revize** vyberte **Vytvořit**.

1. Po několika sekundách uvidíte novou šablonu zařízení:

    ![Šablona zařízení MXChip IoT DevKit](./media/quick-create-simulated-device/devkit-template.png)

    Model schopnosti MXChip IoT DevKit obsahuje rozhraní, jako je **mxchip_sensor**, **mxchip_settings**a informace **o zařízení**. Rozhraní definují možnosti zařízení MXChip IoT DevKit. Mezi možnosti patří telemetrie, kterou zařízení odesílá, vlastnosti, které zařízení hlásí, a příkazy, na které zařízení reaguje.

### <a name="add-cloud-properties"></a>Přidání vlastností cloudu

Šablona zařízení může obsahovat vlastnosti cloudu. Vlastnosti cloudu existují pouze v aplikaci IoT Central a nikdy se neodesílají nebo přijímají ze zařízení.

1. Vyberte **Vlastnosti cloudu** a **potom + Přidat vlastnost cloudu**. Pomocí informací v následující tabulce můžete do šablony zařízení přidat dvě vlastnosti cloudu:

    | Zobrazovaný název      | Sémantický typ | Schéma |
    | ----------------- | ------------- | ------ |
    | Last Service Date (Datum poslední údržby) | Žádný          | Datum   |
    | Jméno zákazníka     | Žádný          | Řetězec |

1. Chcete-li uložit změny, vyberte **uložit:**

    ![Vlastnosti cloudu](media/quick-create-simulated-device/cloud-properties.png)

## <a name="views"></a>Zobrazení

Jako tvůrce můžete přizpůsobit aplikaci tak, aby zobrazovala příslušné informace o zařízení operátorovi. Vlastní nastavení umožňují operátorovi spravovat zařízení připojená k aplikaci. Pro operátora můžete vytvořit dva typy zobrazení, které chcete použít k interakci se zařízeními:

* Formuláře pro zobrazení a úpravu vlastností zařízení a cloudu.
* Řídicí panely pro vizualizaci zařízení včetně telemetrie, kterou odesílají.

### <a name="default-views"></a>Výchozí zobrazení

Výchozí zobrazení jsou rychlý způsob, jak začít s vizualizací důležitých informací o zařízení. Pro šablonu zařízení můžete vygenerovat až tři výchozí zobrazení:

* Zobrazení **Příkazy** umožňuje operátorovi odesílat příkazy do vašeho zařízení.
* Zobrazení **Přehled** používá grafy a metriky k zobrazení telemetrie zařízení.
* Zobrazení **Informace** zobrazuje vlastnosti zařízení.

V yberte uzel **Zobrazení** v šabloně zařízení. Můžete vidět, že IoT Central vygeneroval **přehled** a **o** zobrazení pro vás při přidání šablony.

Přidání nového formuláře **Manage device,** který může operátor použít ke správě zařízení:

1. Vyberte uzel **Zobrazení** a pak vyberte **dlaždici Editing device a cloud data** a přidejte nové zobrazení.

1. Změňte název formuláře na **Spravovat zařízení**.

1. Vyberte vlastnosti **cloudu Název zákazníka** a **Datum poslední služby** a Vlastnost **Rychlost ventilátoru.** Pak vyberte **Přidat oddíl**:

    ![Vytvořit nový formulář](media/quick-create-simulated-device/new-form.png)

1. Vyberte **Uložit,** chcete-li nový formulář uložit.

## <a name="publish-device-template"></a>Publikovat šablonu zařízení

Než budete moci vytvořit simulované zařízení nebo připojit skutečné zařízení, musíte publikovat šablonu zařízení. I když IoT Central publikoval šablonu při prvním vytvoření, musíte publikovat aktualizovanou verzi.

Publikování šablony zařízení:

1. Přejděte na šablonu zařízení na stránce **Šablony zařízení.**

1. Vyberte **Publikovat**:

    ![Publikovaný model](media/quick-create-simulated-device/published-model.png)

1. V **šabloně Publikovat toto zařízení do dialogového** okna aplikace vyberte **Publikovat**. 

Po publikování šablony zařízení se zobrazí na stránce **Zařízení.** V publikované šabloně zařízení nelze upravit model schopností zařízení bez vytvoření nové verze. Můžete však provádět aktualizace vlastností cloudu, vlastního nastavení a zobrazení v publikované šabloně zařízení bez správy verzí. Po provedení jakýchkoli změn vyberte **Publikovat** a tyto změny přenesete na operátora.

## <a name="add-a-simulated-device"></a>Přidání simulovaného zařízení

Chcete-li do aplikace přidat simulované zařízení, použijte šablonu zařízení **MXChip IoT DevKit,** kterou jste vytvořili.

1. Chcete-li přidat nové zařízení jako **operátor,** zvolte Zařízení v levém podokně. Karta **Zařízení** zobrazuje **všechna zařízení** a šablonu zařízení **MXChip IoT DevKit.** Vyberte **MXChip IoT DevKit**.

1. Chcete-li přidat simulované zařízení DevKit, vyberte možnost **+**. Použijte navrhované **ID zařízení** nebo zadejte své vlastní **ID zařízení**s nižšími písmeny . Můžete také zadat název nového zařízení. Zkontrolujte, jestli je **simulované** přepínač **zapnuté,** a pak vyberte **Vytvořit**.

    ![Simulované zařízení](./media/quick-create-simulated-device/simulated-device.png)

Nyní můžete pracovat se zobrazeními, která byla vytvořena tvůrcem šablony zařízení pomocí simulovaných dat:

1. Vyberte simulované zařízení na stránce **Zařízení.**

1. Zobrazení **Přehled** zobrazuje vykreslení simulované telemetrie:

    ![Zobrazení přehledu](./media/quick-create-simulated-device/simulated-telemetry.png)

1. Zobrazení **O** zahrnutí zobrazuje hodnoty vlastností, včetně vlastností cloudu, které jste přidali do zobrazení.

1. Zobrazení **Příkazy** umožňuje spouštět příkazy, například **blikat** na zařízení.

1. Zobrazení **Spravovat zařízení** je formulář, který jste vytvořili pro operátora pro správu zařízení.

## <a name="use-a-simulated-device-to-improve-views"></a>Vylepšení zobrazení pomocí simulovaného zařízení

Po vytvoření nového simulovanézařízení, tvůrce můžete použít toto zařízení i nadále zlepšovat a stavět na zobrazení pro šablonu zařízení.

1. V levém podokně zvolte **Šablony zařízení** a vyberte šablonu **MXChip IoT DevKit.**

1. Vyberte libovolné zobrazení, které chcete upravit, nebo vytvořte nové zobrazení. Vyberte **Konfigurovat zařízení náhledu**a pak **Vybrat ze spuštěného zařízení**. Zde můžete zvolit, že nemáte žádné zařízení preview, skutečné zařízení nakonfigurované pro testování nebo existující zařízení, které jste přidali do IoT Central.

1. V seznamu zvolte simulované zařízení. Potom vyberte **Použít**. Nyní můžete vidět stejné simulované zařízení v šabloně zařízení zobrazení prostředí pro vytváření. Toto zobrazení je užitečné pro grafy a další vizualizace.

    ![Konfigurace zařízení náhledu](./media/quick-create-simulated-device/configure-preview.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit šablonu zařízení **MXChip IoT DevKit** a přidat simulované zařízení do aplikace.

Další informace o monitorovacích zařízeních připojených k aplikaci najdete na úvodním panelu:

> [!div class="nextstepaction"]
> [Konfigurace pravidel a akcí](./quick-configure-rules.md)
