---
title: Připojení IoT DevKit akcelerátor řešení vzdálené monitorování – Azure | Dokumentace Microsoftu
description: V této příručce s postupy se dozvíte, jak odesílat telemetrická data ze senzorů v zařízení IoT DevKit AZ3166 na akcelerátor řešení vzdálené monitorování pro monitorování a vizualizace.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 7f67868f6220ab2940aa8ac4d4bf24f82191cc22
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620247"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>Připojit zařízení IoT DevKit k akcelerátoru řešení vzdáleného monitorování

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Tato příručka ukazuje, jak spustit ukázkovou aplikaci na vašem zařízení IoT DevKit. Vzorový kód odesílá telemetrii ze senzorů v zařízení DevKit akcelerátor řešení.

[IoT DevKit](https://aka.ms/iot-devkit) je vše v jednom Arduino kompatibilní deska bohaté periferní zařízení a senzorů. Můžete vyvíjet pro něj pomocí [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) ve Visual Studio Code. [Katalogu projektů](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) obsahuje ukázkové aplikace můžete řešení IoT prototypu.

## <a name="prerequisites"></a>Požadavky

Postupujte podle [příručku Začínáme s IoT DevKet získávání](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) a dokončování následujících částí:

* Připravte svůj hardware
* Konfigurace Wi-Fi
* Začněte používat DevKit
* Příprava vývojového prostředí

## <a name="open-the-sample"></a>Otevřete ukázku

Otevřete ukázkový vzdálené monitorování v nástroji VS Code:

1. Ujistěte se, že vaše IoT DevKit se k počítači. Nejprve spusťte VS Code a DevKit připojte se k počítači.

1. Klikněte na tlačítko `F1` otevřete paletu příkazů, typ a vyberte **IoT Workbench: Příklady**. Potom vyberte **IoT DevKit** jako panel.

1. Najít **vzdálené monitorování** a klikněte na tlačítko **otevřít ukázkové**. Otevře se nové okno VS Code zobrazující složku projektu:

  ![Aplikace IoT Workbench, vyberte příklad vzdáleného monitorování](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Konfigurace zařízení

Pro konfiguraci připojovací řetězec zařízení centra IoT DevKit zařízení:

1. Přepnout IoT DevKit do **režim konfigurace**:

    * Podržte tlačítko **A**.
    * Se službami push a verzi **resetování** tlačítko.

1. Na obrazovce se zobrazí DevKit ID a `Configuration`.

    ![IoT DevKit režim konfigurace](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Stisknutím klávesy **F1** otevřete paletu příkazů, typ a vyberte **IoT Workbench: zařízení > Konfigurace nastavení zařízení**.

1. Vložte připojovací řetězec, který jste dříve zkopírovali a stiskněte klávesu **Enter** ke konfiguraci zařízení.

## <a name="build-the-code"></a>Sestavení kódu

K vytvoření a nahrání kódu zařízení:

1. Stisknutím klávesy **F1**"** otevřete paletu příkazů, typ a vyberte **IoT Workbench: zařízení > nahrát zařízení**:

    ![IoT Workbench: Zařízení - > nahrát](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-upload.png)

1. VS Code kompiluje a odesílá kód do vašeho zařízení DevKit:

    ![IoT Workbench: Zařízení - > nahrát](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. DevKit zařízení restartuje a spouští kód, který jste nahráli.

## <a name="test-the-sample"></a>Testování ukázky

Chcete-li ověřit, že ukázkovou aplikaci, kterou jste nahráli do zařízení DevKit funguje, proveďte následující kroky:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Zobrazit telemetrická data odesílaná do řešení vzdáleného monitorování

Spuštění ukázkové aplikace zařízení DevKit odesílá telemetrii z jeho data snímačů přes Wi-Fi akcelerátor řešení. Chcete-li zobrazit telemetrická data:

1. Přejděte na řídicí panel řešení a klikněte na tlačítko **zařízení**.

1. Klikněte na název zařízení DevKit zařízení. na kartě napravo uvidíte telemetrii z DevKit v reálném čase:

    ![Data ze senzorů v sadě Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>Ovládací prvek DevKit zařízení

Akcelerátor řešení vzdálené monitorování umožňuje vzdálené řízení zařízení. Vzorový kód implementuje tři metody, které se zobrazí v **metoda** části při výběru zařízení na **zařízení** stránky:

![IoT DevKit metody](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Chcete-li změnit barvu jednoho DevKit LED, použijte **LedColor** metody:

1. Vyberte název zařízení ze seznamu zařízení a klepněte na **úlohy**:

    ![Vytvoření úlohy](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Konfigurace úloh pomocí následující hodnoty a klikněte na tlačítko **použít**:

    * Vyberte úlohu: **Run – metoda**
    * Název metody: **LedColor**
    * Název úlohy: **ChangeLedColor**

    ![Nastavení úlohy](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Za pár sekund, barva RGB LED (pod tlačítko A) na DevKit změny:

    ![IoT DevKit red vedla](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se budete chtít přesunout ke kurzům, ponechte akcelerátor řešení vzdáleného monitorování nasazený.

Pokud už nepotřebujete akcelerátor řešení, odstraníte ji ze stránky řešení zajištěno, že ji vyberete a potom kliknutím na Odstranit řešení:

![Odstranění řešení](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na jakékoli problémy, podívejte se na [nejčastějších dotazech týkajících se IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo kontaktujte nás prostřednictvím následujících kanálů:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak připojit zařízení DevKit akcelerátor řešení vzdálené monitorování, zde jsou některé další navrhované kroky:

* [Přehled akcelerátorů řešení Azure IoT](https://docs.microsoft.com/azure/iot-accelerators/)
* [Přizpůsobení uživatelského rozhraní](iot-accelerators-remote-monitoring-customize.md)
* [IoT DevKit připojit k aplikaci Azure IoT Central](../iot-central/howto-connect-devkit.md)