---
title: Připojení IoT DevKit k řešení vzdáleného monitorování – Azure | Dokumenty společnosti Microsoft
description: V tomto návodu se dozvíte, jak odesílat telemetrii ze senzorů na zařízení IoT DevKit AZ3166 akcelerátoru řešení vzdáleného monitorování pro monitorování a vizualizaci.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 6e9f9c89cf2e5e40d37a1532e688490aae294181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888868"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>Připojení zařízení IoT DevKit k akcelerátoru řešení vzdáleného monitorování

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Tento návod vám ukáže, jak spustit ukázkovou aplikaci na zařízení IoT DevKit. Ukázkový kód odesílá telemetrii ze senzorů na zařízení DevKit do akcelerátoru řešení.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je all-in-one Arduino kompatibilní deska s bohatými periferiemi a senzory. Můžete vyvíjet pro něj pomocí [Azure IoT Device Workbench](https://aka.ms/iot-workbench) nebo [Azure IoT Tools](https://aka.ms/azure-iot-tools) rozšíření pack v Kódu Visual Studia. [Katalog projektů](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) obsahuje ukázkové aplikace, které vám pomohou prototypovat řešení IoT.

## <a name="before-you-begin"></a>Než začnete

Postup v tomto kurzu můžete provést nejprve následujícím úkolem:

* Připravte si DevKit podle kroků v [části Connect IoT DevKit AZ3166 k Azure IoT Hub v cloudu](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="open-sample-project"></a>Otevřít ukázkový projekt

Otevření ukázky vzdáleného monitorování v kódu VS:

1. Ujistěte se, že váš IoT DevKit není k vašemu počítači. Nejprve spusťte kód VS a potom připojte devkit k počítači.

1. Kliknutím `F1` otevřete paletu příkazů, zadejte a vyberte **Azure IoT Device Workbench: Open Examples...**. Pak vyberte **IoT DevKit** jako nástěnku.

1. Najít **vzdálené monitorování** a klepnout na tlačítko Otevřít **ukázku**. Otevře se nové okno VS Kód zobrazující složku projektu:

   ![Pracovní plocha IoT, vyberte příklad vzdáleného monitorování](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Konfigurace zařízení

Konfigurace připojovacího řetězce zařízení služby IoT Hub na zařízení DevKit:

1. Přepněte IoT DevKit do **konfiguračního režimu**:

    * Podržte tlačítko **A**.
    * Stiskněte a uvolněte tlačítko **Reset.**

1. Na obrazovce se zobrazí ID `Configuration`DevKit a .

    ![Režim konfigurace IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Stisknutím **klávesy F1** otevřete paletu příkazů, zadejte a vyberte **pracovní plocha zařízení Azure IoT: Konfigurace nastavení zařízení > připojovacího řetězce konfiguračního zařízení**.

1. Vložte připojovací řetězec, který jste zkopírovali dříve, a stisknutím **klávesy Enter** zařízení nakonfigurujte.

## <a name="build-the-code"></a>Sestavení kódu

Vytvoření a nahrání kódu zařízení:

1. Stisknutím `F1` otevřete paletu příkazů, zadejte a vyberte **Pracovní plocha zařízení Azure IoT: Nahrát kód zařízení**:

1. VS Code zkompiluje a nahraje kód do vašeho zařízení DevKit:

    ![Pracovní plocha IoT: Zařízení – nahráno >](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. Zařízení DevKit se restartuje a spustí kód, který jste nahráli.

## <a name="test-the-sample"></a>Otestujte vzorek

Chcete-li ověřit, zda ukázková aplikace, kterou jste nahráli do zařízení DevKit, funguje, proveďte následující kroky:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Zobrazení telemetrie odeslané do řešení vzdáleného monitorování

Když se spustí ukázková aplikace, zařízení DevKit odešle telemetrii ze svých senzorů data přes Wi-Fi do akcelerátoru řešení. Chcete-li zobrazit telemetrii:

1. Přejděte na řídicí panel řešení a klepněte na **položku Průzkumník zařízení**.

1. Klikněte na název zařízení DevKit. na pravé kartě můžete zobrazit telemetrii z DevKitu v reálném čase:

    ![Data senzorů ve službě Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>Ovládání zařízení DevKit

Akcelerátor řešení vzdáleného monitorování umožňuje ovládat zařízení na dálku. Ukázkový kód implementuje tři metody, které můžete vidět v části **Metoda,** když vyberete zařízení na stránce **Průzkumník zařízení:**

![Metody IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Chcete-li změnit barvu jedné z LED diod DevKit, použijte metodu **LedColor:**

1. Vyberte název zařízení ze seznamu zařízení a klikněte na **úlohy**:

    ![Vytvoření úlohy](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Nakonfigurujte úlohy pomocí následujících hodnot a klepněte na **použít**:

   * Vybrat úlohu: **Spustit metodu**
   * Název metody: **LedColor**
   * Název pracovní ho zaměstnání: **ChangeLedColor**

     ![Nastavení úlohy](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Po několika sekundách se změní barva LED diody RGB (pod tlačítkem A) na vašem DevKitu:

    ![IoT DevKit červený dioda](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se budete chtít přesunout ke kurzům, ponechte akcelerátor řešení pro vzdálené monitorování nasazený.

Pokud už akcelerátor řešení nepotřebujete, odstraňte ho ze stránky Zřízená řešení tak, že ho vyberete a kliknete na Odstranit řešení:

![Odstranění řešení](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na nějaké problémy, podívejte se na [časté dotazy k IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo nás oslovte pomocí následujících kanálů:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak připojit zařízení DevKit k akcelerátoru řešení vzdáleného monitorování, zde jsou některé navrhované další kroky:

* [Přehled akcelerátorů řešení Azure IoT](https://docs.microsoft.com/azure/iot-accelerators/)
* [Přizpůsobení uživatelského rozhraní](iot-accelerators-remote-monitoring-customize.md)
* [Připojení IoT DevKit k vaší aplikaci Azure IoT Central](../iot-central/core/howto-connect-devkit.md)