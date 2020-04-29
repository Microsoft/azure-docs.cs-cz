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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673982"
---
## <a name="create-a-device-template"></a>Vytvoření šablony zařízení

Vytvořte složku s názvem `environmental-sensor` na místním počítači.

Stáhněte si soubor JSON [modelu schopností environmentálního senzoru](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) a uložte ho `environmental-sensor` do složky.

Pomocí textového editoru nahraďte tyto dvě instance `{YOUR_COMPANY_NAME_HERE}` názvem vaší společnosti v `EnvironmentalSensorInline.capabilitymodel.json` souboru, který jste stáhli.

V aplikaci Azure IoT Central vytvořte šablonu zařízení s názvem *senzor okolního prostředí* importem souboru modelu `EnvironmentalSensorInline.capabilitymodel.json` schopností zařízení:

![Šablona zařízení s importovaným modelem schopností zařízení](./media/iot-central-add-environmental-sensor/device-template.png)

Model schopností zařízení zahrnuje dvě rozhraní: standardní **informace o zařízení** a vlastní rozhraní **snímače životního prostředí** . Rozhraní **snímače prostředí** definuje následující možnosti:

| Typ | Zobrazovaný název | Popis |
| ---- | ------------ | ----------- |
| Vlastnost | Stav zařízení     | Stav zařízení. K dispozici jsou dva stavy online/offline. |
| Vlastnost (zapisovatelná) | Jméno zákazníka    | Jméno zákazníka, který aktuálně provozuje zařízení. |
| Vlastnost (zapisovatelná) | Úroveň jasu | Úroveň jasu světla na zařízení. Může být zadáno jako 1 (vysoká), 2 (střední), 3 (nízká). |
| Telemetrie | Teplota | Aktuální teplota zjištěná zařízením. |
| Telemetrie | Vlhkost    | Aktuální vlhkost zjištěná zařízením |
| Příkaz | blikají          | Zahájit blikání indikátoru LED na zařízení pro daný časový interval. |
| Příkaz | turnon         | Zapněte na zařízení indikátor LED. |
| Příkaz | turnoff        | Vypněte na zařízení indikátor LED. |
| Příkaz | rundiagnostics | Tento asynchronní příkaz spustí na zařízení diagnostiku spuštění. |

Pokud chcete přizpůsobit, jak se ve vaší aplikaci IoT Central zobrazí vlastnost **stav zařízení** , vyberte **přizpůsobit** v šabloně zařízení. Rozbalte položku **stav zařízení** , zadejte _online_ jako **skutečný název** a _offline_ jako **název false**. Pak změny uložte:

![Přizpůsobení šablony zařízení](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>Vytváření zobrazení

Zobrazení vám umožní pracovat se zařízeními připojenými k vaší IoT Central aplikaci. Můžete mít například zobrazení, která zobrazují telemetrii, zobrazení, která zobrazují vlastnosti, a zobrazení, která umožňují upravit zapisovatelné a cloudové vlastnosti. Zobrazení jsou součástí šablony zařízení.

Chcete-li přidat některá výchozí zobrazení do šablony zařízení **snímače životního prostředí** , přejděte do šablony zařízení, vyberte možnost **zobrazení**a vyberte dlaždici **Generovat výchozí zobrazení** . Ujistěte **se, že je** **zapnutý** **Přehled** , a pak vyberte **Generovat výchozí zobrazení řídicích panelů**. Teď máte ve vaší šabloně definovaná dvě výchozí zobrazení.

Rozhraní **snímače prostředí** zahrnuje dvě zapisovatelné vlastnosti – **jméno zákazníka** a **úroveň jasu**. Chcete-li vytvořit zobrazení, můžete použít k úpravě těchto vlastností:

1. Vyberte **zobrazení** a pak vyberte dlaždici pro **úpravu zařízení a dat v cloudu** .

1. Jako název formuláře zadejte _vlastnosti_ .

1. Vyberte vlastnosti **úrovně jasu** a **název zákazníka** . Pak vyberte **přidat oddíl**.

1. Uložte provedené změny.

![Přidání zobrazení pro povolení úprav vlastností](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>Publikování šablony

Než budete moct přidat zařízení, které používá šablonu zařízení **snímače prostředí** , musíte ho publikovat.

V šabloně zařízení vyberte **publikovat**. Na panelu **aplikace v části publikovat tuto šablonu zařízení** vyberte **publikovat**.

Chcete-li ověřit, zda je šablona připravena k použití, přejděte na stránku **zařízení** v aplikaci IoT Central. V části **zařízení** se zobrazuje seznam publikovaných zařízení v aplikaci:

![Publikované šablony na stránce zařízení](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central přidejte reálné zařízení do šablony zařízení, kterou jste vytvořili v předchozí části:

1. Na stránce **zařízení** vyberte šablonu zařízení **snímače životního prostředí** .

    > [!TIP]
    > Nezapomeňte vybrat šablonu, kterou chcete použít, než vyberete **+ Nový**. v opačném případě vytvoříte nepřidružené zařízení.

1. Vyberte **+ Nový**.

1. Ujistěte se, **že je** **simulovaná** . Pak vyberte **vytvořit**.

Klikněte na název zařízení a pak vyberte **připojit**. Poznamenejte si informace o připojení zařízení na stránce **připojení zařízení** – **Rozsah ID**, **ID zařízení**a **primární klíč**. Tyto hodnoty budete potřebovat při vytváření kódu zařízení:

![Informace o připojení zařízení](./media/iot-central-add-environmental-sensor/device-connection.png)
