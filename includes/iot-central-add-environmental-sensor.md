---
title: zahrnout soubor
description: zahrnout soubor
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9618dc1cef8d04cc5906579af0ef372694aeaaa9
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673982"
---
## <a name="create-a-device-template"></a>Vytvoření šablony zařízení

Vytvořte složku `environmental-sensor` volanou v místním počítači.

Stáhněte si [model schopnosti životního prostředí JSON](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) soubor a uložte jej do `environmental-sensor` složky.

Pomocí textového `{YOUR_COMPANY_NAME_HERE}` editoru nahraďte dvě instance názvu `EnvironmentalSensorInline.capabilitymodel.json` společnosti v souboru, který jste stáhli.

V aplikaci Azure IoT Central vytvořte šablonu zařízení `EnvironmentalSensorInline.capabilitymodel.json` nazvanou Senzor *prostředí* importem souboru modelu schopností zařízení:

![Šablona zařízení s importovaným modelem schopností zařízení](./media/iot-central-add-environmental-sensor/device-template.png)

Model schopností zařízení obsahuje dvě rozhraní: standardní informační rozhraní **zařízení** a vlastní rozhraní **senzoru prostředí.** Rozhraní **senzoru prostředí** definuje následující možnosti:

| Typ | Zobrazovaný název | Popis |
| ---- | ------------ | ----------- |
| Vlastnost | Stav zařízení     | Stav zařízení. K dispozici jsou dva stavy online/offline. |
| Vlastnost (zapisovatelná) | Jméno zákazníka    | Jméno zákazníka, který zařízení aktuálně provozuje. |
| Vlastnost (zapisovatelná) | Úroveň jasu | Úroveň jasu světla na zařízení. Může být specifikován jako 1 (vysoká), 2 (střední), 3 (nízká). |
| Telemetrie | Teplota | Aktuální teplota zjištěná zařízením. |
| Telemetrie | Vlhkost    | Aktuální vlhkost zjištěná zařízením. |
| Příkaz | Blikat          | Začněte blikat LED diodu na zařízení pro daný časový interval. |
| Příkaz | odbočka         | Zapněte led diodu na zařízení. |
| Příkaz | Odbočku        | Vypněte led diodu na zařízení. |
| Příkaz | rundiagnostics | Tento asynchronní příkaz spustí diagnostiku spuštěnou v zařízení. |

Pokud chcete přizpůsobit, jak se vlastnost **Stav zařízení** zobrazí ve vaší aplikaci IoT Central, vyberte **Přizpůsobit** v šabloně zařízení. Rozbalte položku **Stav zařízení,** zadejte _online_ jako **true název** a _offline_ jako **false name**. Pak uložte změny:

![Přizpůsobení šablony zařízení](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>Vytváření zobrazení

Zobrazení umožňují interakci se zařízeními připojenými k aplikaci IoT Central. Můžete mít například zobrazení, která zobrazují telemetrii, zobrazení, která zobrazují vlastnosti, a zobrazení, která umožňují upravovat zapisovatelné a cloudové vlastnosti. Zobrazení jsou součástí šablony zařízení.

Chcete-li do šablony **zařízení environmentálního senzoru** přidat některá výchozí zobrazení, přejděte do šablony zařízení, vyberte **Zobrazení**a vyberte dlaždici Generovat **výchozí zobrazení.** Ujistěte se, že **jsou přehled** a **informace** **zapnuty**, a pak vyberte **Generovat výchozí zobrazení řídicího panelu**. Nyní máte v šabloně definována dvě výchozí zobrazení.

Rozhraní **environmentálního senzoru** obsahuje dvě zapisovatelné vlastnosti - **jméno zákazníka** a **úroveň jasu**. Chcete-li vytvořit zobrazení, můžete použít k úpravám těchto vlastností:

1. Vyberte **Zobrazení** a pak vyberte **dlaždici Editing device a cloud data.**

1. Jako název formuláře zadejte _Vlastnosti._

1. Vyberte vlastnosti **Úroveň jasu** a **Jméno zákazníka.** Pak vyberte **Přidat oddíl**.

1. Uložte provedené změny.

![Přidání zobrazení pro povolení úprav vlastností](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>Publikování šablony

Před přidáním zařízení, které používá šablonu **zařízení environmentálního senzoru,** je nutné ji publikovat.

V šabloně zařízení vyberte **Publikovat**. V šabloně **Publikovat toto zařízení do panelu aplikace** vyberte **Publikovat**.

Chcete-li zkontrolovat, zda je šablona připravená k použití, přejděte na stránku **Zařízení** v aplikaci IoT Central. V části **Zařízení** je uveden seznam publikovaných zařízení v aplikaci:

![Publikované šablony na stránce zařízení](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central přidejte skutečné zařízení do šablony zařízení, kterou jste vytvořili v předchozí části:

1. Na stránce **Zařízení** vyberte šablonu **zařízení environmentálního senzoru.**

    > [!TIP]
    > Nezapomeňte vybrat šablonu, kterou chcete použít, než vyberete **+ Nový**, jinak vytvoříte nepřidružené zařízení.

1. Vyberte **+ Nový**.

1. Ujistěte se, že **simulované** je **vypnuto**. Pak vyberte **Vytvořit**.

Klikněte na název zařízení a pak vyberte **Připojit**. Poznamenejte si informace o připojení zařízení na stránce **Připojení k zařízení** – obor **ID**, **ID zařízení**a primární **klíč**. Tyto hodnoty potřebujete při vytváření kódu zařízení:

![Informace o připojení zařízení](./media/iot-central-add-environmental-sensor/device-connection.png)
