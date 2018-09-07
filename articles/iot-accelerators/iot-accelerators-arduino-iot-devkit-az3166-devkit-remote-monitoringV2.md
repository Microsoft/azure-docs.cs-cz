---
title: IoT DevKit do cloudu – IoT DevKit AZ3166 se připojit k akcelerátoru řešení vzdáleného sledování IoT | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak odeslat stav senzory IoT DevKit AZ3166 se do akcelerátoru řešení vzdáleného sledování IoT pro monitorování a vizualizace.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: isacabe
ms.openlocfilehash: d3175290c1a7fca5e35f4438392f29324868f1a3
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054912"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Připojení MXChip IoT DevKit AZ3166 se k akcelerátoru řešení vzdáleného monitorování IoT


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se dozvíte, jak spustit ukázkovou aplikaci na váš DevKit odeslat data ze senzorů akcelerátor řešení.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je vše v jednom Arduino kompatibilní deska bohaté periferní zařízení a senzorů. Můžete vyvíjet pro něj pomocí [rozšíření Visual Studio Code pro Arduino](https://aka.ms/arduino). A stále se rozšiřující obsahuje [katalogu projektů](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) a provede vás prototypu Internet of Things (IoT) řešení, které budou využívat služeb Microsoft Azure.

## <a name="what-you-need"></a>Co potřebujete

Projděte si [– Příručka Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) a **dokončit pouze v následujících částech**:

* Připravte svůj hardware
* Konfigurace Wi-Fi
* Začněte používat DevKit
* Příprava vývojového prostředí


## <a name="open-the-remotemonitoring-sample-in-vs-code"></a>Otevřete ukázku RemoteMonitoring ve VS Code

1. Odpojte MXChip DevKit z vašeho počítače, pokud je připojený.

2. Spusťte VS Code.

3. Připojení MXChip DevKit k vašemu počítači.

4. Použití `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) otevřete paletu příkazů, zadejte **Arduino**a poté vyhledejte a vyberte **Arduino: Příklady**.

 5. Rozbalte na levé straně **ARDUINO příklady** vyhledejte **příklady MXCHIP AZ3166 > AzureIoT**a vyberte **RemoteMonitoringv2**. Otevře se nové okno VS Code s složky projektu do něj.

  > [!NOTE]
  > Pokud se vám nezobrazí **příklady MXCHIP**, použijte `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) otevřete paletu příkazů a typ **Arduino panel Správce**. Vyberte ho a pak vyhledejte **AZ3166** v rámci panelu správce. Pak by měl být opakováním kroku 5 výše a podívejte se na příklady.

  ![Otevřít projekt vzdáleného monitorování](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-arduino-examples.png)

  > [!NOTE]
  > Pokud máte náhodou zavřete podokno, můžete ho znovu otevřít. Použití `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) otevřete paletu příkazů, zadejte **Arduino**a poté vyhledejte a vyberte **Arduino: Příklady**.

## <a name="build-and-upload-the-device-code-to-your-mxchip"></a>Vytvoření a nahrávání zařízení kódu do vaší MXChip

1. Použití `Ctrl+P` (macOS: `Cmd + P`) a typ **úloh konfiguračním připojení zařízení**.

  ![Zvolte předplatné Azure a centrem IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion.png)

2. Terminálu zobrazí dotaz, zda chcete použít připojovací řetězec zařízení IoT, byste chtěli použít. Vyberte *vytvořit nový*a nyní jej vložte.

  ![Vložte připojovací řetězec](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. Terminálu někdy výzvu k zadání režim konfigurace. Chcete-li tak učinit, podržte **tlačítko A**, potom nasdílejte a verzi **tlačítko por resetování** a uvolněte tlačítko A. Na obrazovce se zobrazí DevKit ID a "Configuration".

  ![Obrazovka DevKit zařízení](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > Připojovací řetězec má být uložen do schránky, pokud jste postupovali podle poslední části tohoto kurzu. Pokud ne, by měl přejít na web Azure Portal a vyhledejte služby IoT Hub vaší skupiny prostředků vzdáleného monitorování. Tam uvidíte připojená zařízení služby IoT Hub a zkopírujte připojovací řetězec zařízení.

  ![Vyhledejte připojovací řetězec](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-connection-string-of-a-device.png)

  Teď jste úspěšně připojení a ověřit zařízení MXChip IoT Hub. Pokud chcete zobrazit nové fyzické zařízení v nástroji VS Code část "Zařízení Azure IoT Hub", je nutné stáhnout [rozšíření Azure IoT Toolkit.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) 

  Teď můžete vidět nové fyzické zařízení v části "Zařízení Azure IoT Hub" VS Code:

  ![Všimněte si, že nové zařízení centra IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-new-iot-hub-device.png)

4. Nakonec odešlete RemoteMonitoringV2.ino kódu na vaše MxChip zahajte odesílání dat na akcelerátor řešení IoT. Použití `Ctrl + Shift + P` (macOS: `Cmd + Shift + P`) a typ **nahrát Arduino**. VS Code pak bude zahájeno odesílání kódu na vaše MXChip a upozorní vás po dokončení. 

## <a name="test-the-project"></a>Testování projektu

Když spustíte ukázkovou aplikaci, MXChip DevKit odešle data ze senzorů přes Wi-Fi vaše akcelerátory řešení IoT. K zobrazení výsledku, postupujte podle těchto kroků:

1. Přejděte na akcelerátor řešení IoT a klikněte na tlačítko **řídicí panel**.

2. V konzole akcelerátorů řešení IoT se zobrazí stav MXChip DevKit senzoru. 

![Data ze senzorů v akcelerátory řešení IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-dashboard.png)

Pokud kliknete na název snímače (AZ3166) na kartě se otevře na pravé straně řídicího panelu, ve kterém uvidíte graf MXChip senzorů v reálném čase.


## <a name="send-a-c2d-message"></a>Odešlete zprávu C2D
V2 vzdáleného monitorování umožňuje vyvolat vzdálené metody v zařízení.
Příklad kódu čipu MX publikuje tři metody, které se zobrazí v části způsob při výběru senzoru.

![Čip TPM MX metody](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-methods.png)

Můžete změnit barvu jednoho z indikátorů LED čip TPM MX pomocí metody "LedColor". To je, zaškrtněte políčko zařízení a klikněte na tlačítko plánu. 

![Čip TPM MX metody](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-schedule.png)

Zvolte metodu nazvanou ChangeColor v rozevíracím seznamu, kde všechny metody se zobrazí, zápis názvu a použít.

![Čip TPM MX rozevíracího seznamu](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-change-color.png)

Za několik sekund, by měl váš fyzický čip TPM MX Změna barvy RGB LED (dole a tlačítko)

![Čip TPM MX Indikátor](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-led.png)

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, podívejte se na [nejčastějších dotazech týkajících se](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo kontaktujte nás z následujících kanálů:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak připojit k vaše akcelerátory řešení IoT DevKit zařízení a vizualizaci dat snímačů, tady jsou další navrhované kroky:

* [Přehled akcelerátorů řešení IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Připojení MXChip IoT DevKit zařízení do aplikace Microsoft IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
* [Sady pro vývojáře IoT](https://microsoft.github.io/azure-iot-developer-kit/)
