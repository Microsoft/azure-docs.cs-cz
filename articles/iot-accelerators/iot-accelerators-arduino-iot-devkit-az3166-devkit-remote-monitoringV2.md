---
title: IoT DevKit do cloudu – IoT DevKit AZ3166 se připojit k akcelerátoru řešení vzdáleného sledování IoT | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak odeslat stav senzory IoT DevKit AZ3166 se do akcelerátoru řešení vzdáleného sledování IoT pro monitorování a vizualizace.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: isacabe
ms.openlocfilehash: 32742b2a680370f443051e2d86f90d94e8632850
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720578"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Připojení MXChip IoT DevKit AZ3166 se k akcelerátoru řešení vzdáleného monitorování IoT

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Se dozvíte, jak spustit ukázkovou aplikaci na vaše IoT DevKit odeslat data ze senzorů akcelerátor řešení.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je vše v jednom Arduino kompatibilní deska bohaté periferní zařízení a senzorů. Můžete vyvíjet pro něj pomocí [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) ve Visual Studio Code. A stále se rozšiřující obsahuje [katalogu projektů](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) a provede vás prototypu Internet of Things (IoT) řešení, které budou využívat služeb Microsoft Azure.

## <a name="what-you-need"></a>Co potřebujete

Projděte si [– Příručka Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) a **dokončit pouze v následujících částech**:

* Připravte svůj hardware
* Konfigurace Wi-Fi
* Začněte používat DevKit
* Příprava vývojového prostředí

## <a name="open-the-remote-monitoring-sample-in-vs-code"></a>Otevřete ukázkový vzdálené monitorování v nástroji VS Code

1. Ujistěte se, že je vaše IoT DevKit **Nepřipojeno** k vašemu počítači. Nejprve spusťte VS Code a DevKit připojte se k počítači.

2. Klikněte na tlačítko `F1` otevřete paletu příkazů, typ a vyberte **IoT Workbench: Příklady**. Potom vyberte **IoT DevKit** jako panel.

3. Najít **vzdálené monitorování** a klikněte na tlačítko **otevřít ukázkové**. Otevře se nové okno VS Code s složky projektu do něj.
  ![Aplikace IoT Workbench, vyberte příklad vzdáleného monitorování](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-iot-hub-device-connection-string"></a>Nakonfigurovat připojovací řetězec služby IoT Hub zařízení

1. Přepnout IoT DevKit do **režim konfigurace**. Postupujte následovně:
   * Podržte tlačítko **A**.
   * Se službami push a verzi **resetování** tlačítko.

2. Na obrazovce se zobrazí DevKit ID a "Configuration".
   
  ![IoT DevKit režim konfigurace](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

3. Klikněte na tlačítko `F1` otevřete paletu příkazů, typ a vyberte **IoT Workbench: zařízení > Konfigurace nastavení zařízení**.

4. Vložte připojovací řetězec, který jste právě zkopírovali kliknutím `Enter` k jeho konfiguraci.

## <a name="build-and-upload-the-device-code"></a>Vytvoření a nahrání kódu zařízení

1. Klikněte na tlačítko `F1` otevřete paletu příkazů, typ a vyberte **IoT Workbench: zařízení > nahrát zařízení**.
  ![IoT Workbench: Zařízení - > nahrát](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-upload.png)

1. VS Code pak spustí sestavení a publikování kódu na vaše DevKit.
  ![IoT Workbench: Zařízení - > nahrát](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

DevKit restartuje a spustí kód.

## <a name="test-the-project"></a>Testování projektu

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Zobrazit telemetrická data odesílaná do řešení vzdáleného monitorování

Spuštění ukázkové aplikace, DevKit odešle data ze senzorů přes Wi-Fi do řešení vzdáleného monitorování. K zobrazení výsledku, postupujte podle těchto kroků:

1. Přejděte na řídicí panel řešení a klikněte na tlačítko **zařízení**.

2. Klikněte na název zařízení, na kartě pravé straně se zobrazí DevKit stav ze senzorů v reálném čase.
  ![Data ze senzorů v sadě Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="send-a-c2d-message"></a>Odešlete zprávu C2D

Řešení vzdáleného monitorování umožňuje vyvolat vzdálené metody v zařízení. Kód sxample publikuje tři metody, které se zobrazí v **metoda** části, pokud je vybrána senzoru.

![IoT DevKit metody](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Dejte nám zkuste změnit barvu jednoho LED DevKit pomocí metody "LedColor".

1. Vyberte název zařízení ze seznamu zařízení a klepněte na **úlohy**.

  ![Vytvoření úlohy](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

2. Konfigurace úlohy, jak je uvedeno níže a klikněte na tlačítko **použít**.
  * Vyberte úlohu: **Run – metoda**
  * Název metody: **LedColor**
  * Název úlohy: **ChangeLedColor**
  
  ![Nastavení úlohy](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

Za několik sekund by měla vaše DevKit Změna barvy RGB LED (pod tlačítko A).

![IoT DevKit red vedla](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se budete chtít přesunout ke kurzům, ponechte akcelerátor řešení vzdáleného monitorování nasazený.

Pokud už nepotřebujete akcelerátor řešení, odstraníte ji ze stránky řešení zajištěno, že ji vyberete a potom kliknutím na Odstranit řešení:

![Odstranění řešení](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, podívejte se na [nejčastějších dotazech týkajících se IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo kontaktujte nás prostřednictvím následujících kanálů:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak připojit k akcelerátor řešení vzdáleného monitorování Azure IoT DevKit zařízení a vizualizaci dat snímačů, tady jsou další navrhované kroky:

* [Přehled akcelerátorů řešení Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Přizpůsobení uživatelského rozhraní](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)
* [IoT DevKit připojit k aplikaci Azure IoT Central](../iot-central/howto-connect-devkit.md)