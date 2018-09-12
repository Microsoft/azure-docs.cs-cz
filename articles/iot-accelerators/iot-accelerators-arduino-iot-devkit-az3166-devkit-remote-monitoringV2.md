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
ms.openlocfilehash: 35ef6ef02e5ae8a4b9231121615f44e0dc00ad15
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378733"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Připojení MXChip IoT DevKit AZ3166 se k akcelerátoru řešení vzdáleného monitorování IoT

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se dozvíte, jak spustit ukázkovou aplikaci na váš DevKit odeslat data ze senzorů akcelerátor řešení.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je vše v jednom Arduino kompatibilní deska bohaté periferní zařízení a senzorů. Můžete vyvíjet pro něj pomocí [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) ve Visual Studio Code. A stále se rozšiřující obsahuje [katalogu projektů](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) a provede vás prototypu Internet of Things (IoT) řešení, které budou využívat služeb Microsoft Azure.

## <a name="what-you-need"></a>Co potřebujete

Projděte si [– Příručka Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) a **dokončit pouze v následujících částech**:

* Připravte svůj hardware
* Konfigurace Wi-Fi
* Začněte používat DevKit
* Příprava vývojového prostředí


## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Vytvoření akcelerátoru řešení vzdáleného monitorování Azure IoT

AZ3166 se zařízení, které vytvoříte v tomto kurzu odesílá data do instance akcelerátoru řešení vzdáleného monitorování. Pokud jste ještě nezřídili instance v účtu Azure, postupujte [rychlý Start](https://docs.microsoft.com/azure/iot-accelerators/quickstart-remote-monitoring-deploy) Průvodce Uděláte to tak.

Po dokončení nasazení pro řešení vzdáleného monitorování, otevřete řídicí panel řešení.

![Řídicí panel řešení](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard-info.png)

## <a name="add-a-device-to-the-remote-monitoring-solution"></a>Přidat zařízení do řešení vzdáleného monitorování

1. Na řídicím panelu přejděte na **zařízení** a klikněte **nové zařízení**.
   ![Přidání nového zařízení](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-add-device.png)

2. Konfigurace zařízení s použitím následující informace a klikněte na tlačítko **použít**.
  * Typ zařízení: **fyzické**
  * ID zařízení: **AZ3166**
  * Typ ověřování: **symetrický klíč**
  * Ověřovací klíč: **automaticky vygenerovat klíče**
  
  ![Přidání nového formuláře zařízení](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-add-new-device-form.png)

3. Po vytvoření nového zařízení je zkopírovat **primární klíč připojovacího řetězce**. Toto je zařízení, na kterém je vytvořený ve službě Azure IoT Hub pod.
  
  ![Připojovací řetězec zařízení](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-new-device-connstring.png)

## <a name="open-the-remote-monitoring-sample-in-vs-code"></a>Otevřete ukázkový vzdálené monitorování v nástroji VS Code

1. Ujistěte se, že je vaše IoT DevKit **Nepřipojeno** k vašemu počítači. Nejprve spusťte VS Code a DevKit připojte se k počítači.

1. Klikněte na tlačítko `F1` otevřete paletu příkazů, typ a vyberte **IoT Workbench: Příklady**. Potom vyberte **IoT DevKit** jako panel.

1. Najít **vzdálené monitorování** a klikněte na tlačítko **otevřít ukázkové**. Otevře se nové okno VS Code s složky projektu do něj.
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

1. Klikněte na název zařízení (AZ3166) na kartě se otevře na pravé straně řídicího panelu, kde můžete zobrazit stav senzor na DevKit v reálném čase.
  ![Data ze senzorů v sadě Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="send-a-c2d-message"></a>Odešlete zprávu C2D

Řešení vzdáleného monitorování umožňuje vyvolat vzdálené metody v zařízení. Kód sxample publikuje tři metody, které se zobrazí v **metoda** části, pokud je vybrána senzoru.

![IoT DevKit metody](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Dejte nám zkuste změnit barvu jednoho LED DevKit pomocí metody "LedColor".

1. Vyberte **AZ3166** ze seznamu zařízení a klikněte na kartu **úlohy**.

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