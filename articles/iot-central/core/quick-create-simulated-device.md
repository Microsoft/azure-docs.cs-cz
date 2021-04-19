---
title: Rychlý Start – přidání simulovaného zařízení do Azure IoT Central
description: V tomto rychlém startu se dozvíte, jak vytvořit šablonu zařízení a přidat do aplikace IoT Central simulované zařízení.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 0396c028c8c0e1a18baf037fed4efc21aad944f1
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715095"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Rychlý Start: Přidání simulovaného zařízení do aplikace IoT Central

Šablona zařízení definuje možnosti zařízení, které se připojuje k vaší IoT Central aplikaci. Mezi možnosti patří telemetrie, kterou zařízení odesílá, vlastnosti zařízení a příkazy, na které zařízení reaguje. Pomocí šablony zařízení můžete do aplikace přidat skutečná i simulovaná zařízení. Simulovaná zařízení jsou užitečná pro testování chování aplikace IoT Central před propojením reálných zařízení.

V tomto rychlém startu přidáte šablonu zařízení pro sadu ESP32-Azure pro vývojovou desku IoT Kit a vytvoříte simulované zařízení. K dokončení tohoto rychlého startu nepotřebujete skutečné zařízení, budete pracovat s simulací zařízení. Zařízení ESP32:

* Odesílá telemetrii jako teplotu.
* Oznamuje vlastnosti specifické pro zařízení, například maximální teplotu od restartování zařízení.
* Reaguje na příkazy, jako je třeba restartování.
* Sestavy obecných vlastností zařízení, jako je například verze firmwaru a sériové číslo.

## <a name="prerequisites"></a>Požadavky

Dokončete průvodce [vytvořením aplikace Azure IoT Central](./quick-deploy-iot-central.md) pro vytvoření IoT Central aplikace pomocí vlastní šablony **aplikace > vlastní** .

## <a name="create-a-device-template"></a>Vytvořit šablonu zařízení

Chcete-li přidat novou šablonu zařízení do aplikace, vyberte kartu **šablony zařízení** v levém podokně.

:::image type="content" source="media/quick-create-simulated-device/device-definitions.png" alt-text="Snímek obrazovky znázorňující prázdný seznam šablon zařízení":::

Šablona zařízení zahrnuje model zařízení, který definuje:

* Telemetrii, kterou zařízení odesílá
* Vlastnosti zařízení.
* Příkazy, na které zařízení reaguje

### <a name="add-a-device-template"></a>Přidání šablony zařízení

K dispozici je několik možností, jak přidat model zařízení do aplikace IoT Central. Můžete vytvořit nový model od začátku, importovat model ze souboru nebo vybrat zařízení z katalogu zařízení. IoT Central také podporuje přístup k *prvnímu zařízení* , kde aplikace automaticky importuje model z úložiště při prvním připojení reálného zařízení.

V tomto rychlém startu zvolíte zařízení z katalogu zařízení, ve kterém chcete vytvořit šablonu zařízení.

Následující kroky ukazují, jak pomocí katalogu zařízení importovat model pro zařízení **ESP32** . Tato zařízení odesílají telemetrii, například teplotu, do vaší aplikace:

1. Chcete-li přidat novou šablonu zařízení, vyberte **+ Nový** na stránce **šablony zařízení** .

1. Na stránce **Vybrat typ** se posuňte dolů, dokud nenajdete dlaždici **ESP32-Azure IoT Kit** v části **použití předem nakonfigurované šablony zařízení** .

1. Vyberte dlaždici **ESP32-Azure IoT Kit** a potom vyberte **Další: zkontrolovat**.

1. Na stránce **Kontrola** vyberte **vytvořit**.

1. Po několika sekundách uvidíte novou šablonu zařízení:

    :::image type="content" source="media/quick-create-simulated-device/devkit-template.png" alt-text="Snímek obrazovky se šablonou zařízení pro zařízení ESP32":::

    Název šablony je **kontroler senzoru**. Model obsahuje komponenty, jako je například **kontroleru senzorů**, **SensorTemp** a **rozhraní informací o zařízení**. Komponenty definují možnosti zařízení ESP32. Mezi možnosti patří telemetrie, vlastnosti a příkazy.

### <a name="add-cloud-properties"></a>Přidání vlastností cloudu

Šablona zařízení může obsahovat vlastnosti cloudu. Vlastnosti cloudu existují jenom v aplikaci IoT Central a nikdy se neodesílají do zařízení nebo se z něj nepřijímají. Přidání dvou vlastností cloudu:

1. Vyberte **vlastnosti cloudu** a pak **+ přidat cloudovou vlastnost**. K přidání dvou vlastností cloudu do šablony zařízení použijte informace v následující tabulce:

    | Zobrazovaný název      | Sémantický typ | Schéma |
    | ----------------- | ------------- | ------ |
    | Last Service Date (Datum poslední údržby) | Žádné          | Date (Datum)   |
    | Název zákazníka     | Žádné          | Řetězec |

1. Kliknutím na **Uložit** uložte změny:

    :::image type="content" source="media/quick-create-simulated-device/cloud-properties.png" alt-text="Snímek obrazovky zobrazující dvě vlastnosti cloudu":::

## <a name="views"></a>Zobrazení

Aplikaci můžete přizpůsobit tak, aby zobrazovala relevantní informace o zařízení. Přizpůsobení umožňují jiným spravovat zařízení připojená k aplikaci. Můžete vytvořit dva typy zobrazení pro interakci se zařízeními:

* Formuláře pro zobrazení a úpravy vlastností zařízení a cloudu.
* Řídicí panely, které znázorňují zařízení, včetně telemetrie, kterou odesílají.

### <a name="default-views"></a>Výchozí zobrazení

Výchozí zobrazení představují rychlý způsob, jak začít s vizualizací důležitých informací o zařízení. Pro šablonu zařízení můžete mít vygenerované až tři výchozí zobrazení:

* Zobrazení **příkazy** umožňuje odeslat příkazy do zařízení.
* Zobrazení **přehledu** používá grafy a metriky k zobrazení telemetrie zařízení.
* V zobrazení **informace** se zobrazí vlastnosti zařízení.

V šabloně zařízení vyberte uzel **zobrazení** . V případě, že jste šablonu přidali, vidíte, že IoT Central vygenerovala **Přehled**, **informace o** zobrazení a zobrazení **nezpracovaných dat** .

Přidání nového formuláře pro správu zařízení:

1. Vyberte uzel **zobrazení** a vyberte dlaždici **data zařízení a cloudu** a přidejte nové zobrazení.

1. Změňte název formuláře, aby bylo možné **spravovat zařízení**.

1. Vyberte **název zákazníka** a poslední vlastnosti cloudového **data služby** a cílovou vlastnost **teplota** . Pak vyberte **přidat oddíl**:

    :::image type="content" source="media/quick-create-simulated-device/new-form.png" alt-text="Snímek obrazovky zobrazující nový formulář přidaný do šablony zařízení":::

1. Kliknutím na **Uložit** uložte nový formulář.

## <a name="publish-device-template"></a>Publikovat šablonu zařízení

Než budete moct vytvořit simulované zařízení nebo připojit reálné zařízení, budete muset publikovat šablonu zařízení. I když IoT Central šablonu publikovali při jejím prvním vytvoření, je nutné publikovat aktualizovanou verzi.

Publikování šablony zařízení:

1. Na stránce **šablony zařízení** přejděte na šablonu zařízení **kontroleru senzorů** .

1. Na panelu příkazů v horní části stránky vyberte **publikovat** .

1. V dialogovém okně, které se zobrazí, vyberte **publikovat**.

Po publikování se šablona zařízení zobrazí na stránce **zařízení** . V publikované šabloně zařízení nemůžete upravovat model zařízení bez vytváření nové verze. Můžete ale upravit vlastnosti cloudu, přizpůsobení a zobrazení v publikované šabloně zařízení bez správy verzí. Po provedení jakýchkoli změn vyberte **publikovat**  a nahrajte tyto změny pro skutečná a simulovaná zařízení, která chcete použít.

## <a name="add-a-simulated-device"></a>Přidání simulovaného zařízení

K přidání simulovaného zařízení do aplikace použijete šablonu zařízení **ESP32** , kterou jste vytvořili.

1. Pokud chcete přidat nové zařízení, v levém podokně vyberte **zařízení** . Karta **zařízení** zobrazuje **všechna zařízení** a šablonu zařízení **KONTROLERU senzorů** pro zařízení ESP32. Vyberte **kontroler senzorů**.

1. Pokud chcete přidat simulované zařízení DevKit, vyberte **+ Nový**. Použijte navržené **ID zařízení** nebo zadejte vlastní. ID zařízení může obsahovat písmena, číslice a `-` znak. Můžete také zadat název nového zařízení. Ujistěte se, že je nastavení **Simulovat toto zařízení** nastaveno na **Ano** , a pak vyberte **vytvořit**.

    :::image type="content" source="media/quick-create-simulated-device/simulated-device.png" alt-text="Snímek obrazovky zobrazující zařízení simulovaného kontroleru senzorů":::

Nyní můžete pracovat se zobrazeními, která byla vytvořena dříve pomocí simulovaných dat:

1. Výběr simulovaného zařízení na stránce **zařízení**

    * Zobrazení **přehledu** ukazuje vykreslení simulované telemetrie:

        :::image type="content" source="media/quick-create-simulated-device/simulated-telemetry.png" alt-text="Snímek obrazovky zobrazující stránku s přehledem pro simulované zařízení":::

    * V zobrazení **informace** se zobrazují hodnoty vlastností.

    * Zobrazení **příkazy** umožňuje spouštět příkazy, jako je třeba **restartování** na zařízení.

    * Zobrazení **Správa zařízení** je formulář, který jste vytvořili pro správu zařízení.

    * Zobrazení **nezpracovaná data** vám umožní zobrazit nezpracované telemetrie a hodnoty vlastností odesílané zařízením. Toto zobrazení je užitečné pro ladění zařízení.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak vytvořit šablonu zařízení **kontroleru senzorů** pro zařízení ESP32 a přidat do aplikace simulované zařízení.

Další informace o monitorování zařízení připojených k vaší aplikaci získáte, když budete pokračovat v rychlém startu:

> [!div class="nextstepaction"]
> [Konfigurace pravidel a akcí](./quick-configure-rules.md)
