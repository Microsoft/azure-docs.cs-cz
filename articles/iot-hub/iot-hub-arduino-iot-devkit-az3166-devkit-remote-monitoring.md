---
title: Připojení mxchip iot devkitu k vzdálenému monitorování služby Azure IoT Hub
description: V tomto kurzu se dozvíte, jak odesílat stav senzorů na IoT DevKit AZ3166 do akcelerátoru řešení vzdáleného monitorování Azure IoT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 9eab035d494892671a2451866311ca06599ec030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953721"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Připojení mxchip iot devkitu k urychlovači řešení vzdáleného monitorování Azure IoT

V tomto kurzu se dozvíte, jak spustit ukázkovou aplikaci na devKitu k odeslání dat senzorů do akcelerátoru řešení vzdáleného monitorování Azure IoT.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je all-in-one Arduino kompatibilní deska s bohatými periferiemi a senzory. Můžete vytvořit pro něj pomocí [rozšíření Visual Studio Code pro Arduino](https://aka.ms/arduino). A přichází s rostoucím [katalogem projektů,](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) který vás provede prototypovými řešeními Internetu věcí (IoT), která využívají služeb Microsoft Azure.

## <a name="what-you-need"></a>Co potřebujete

Dokončete [příručku Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) s tímto účelem:

* Připojení devKitu k Wi-Fi
* Příprava vývojového prostředí

Aktivní předplatné Azure. Pokud ji nemáte, můžete se zaregistrovat jednou z těchto dvou metod:

* Aktivace [bezplatného 30denního zkušebního účtu Microsoft Azure](https://azure.microsoft.com/free/)

* Nárokujte si [kredit Azure,](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) pokud jste předplatitelem MSDN nebo Visual Studia

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Vytvoření akcelerátoru řešení vzdáleného monitorování Azure IoT

1. Přejděte na [web akcelerátorů řešení Azure IoT](https://www.azureiotsolutions.com/) a klikněte na **Vytvořit nové řešení**.

   ![Vyberte typ akcelerátoru řešení Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Ve výchozím nastavení tato ukázka vytvoří S2 IoT Hub poté, co vytvoří jeden akcelerátor řešení vzdáleného monitorování IoT. Pokud se tento ioT hub nepoužívá s obrovským počtem zařízení, důrazně doporučujeme přejít z S2 na S1 a odstranit akcelerátor řešení vzdáleného monitorování IoT, aby bylo možné odstranit i související službu IoT Hub, když už ho nepotřebujete. 

2. Vyberte **možnost Vzdálené monitorování**.

3. Zadejte název řešení, vyberte předplatné a oblast a klepněte na **tlačítko Vytvořit řešení**. Řešení může chvíli trvat, než se zřídí.
  
   ![Vytvoření řešení](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Po dokončení zřizování klikněte na **tlačítko Spustit**. Některá simulovaná zařízení jsou vytvořena pro řešení během procesu zřizování. Klikněte na **zařízení,** abyste je zkontrolovali.

   ![Řídicí panel](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Konzola](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Klepněte **na tlačítko PŘIDAT ZAŘÍZENÍ**.

6. Klikněte na **Přidat nové** pro **vlastní zařízení**.
  
   ![Přidání nového zařízení](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Klikněte na **Ponechat definovat vlastní ID zařízení**, zadejte `AZ3166`a potom klikněte na **Vytvořit**.
  
   ![Vytvoření zařízení s ID](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Poznamenejte si **název hostitele centra IoT**Hub a klepněte na **tlačítko Hotovo**.

## <a name="open-the-remotemonitoring-sample"></a>Otevření ukázky Vzdáleného monitorování

1. Pokud je devKit připojen, odpojte jej od počítače.

2. Spusťte VS Code.

3. Připojte devKit k počítači. VS Code automaticky detekuje váš DevKit a otevře následující stránky:

   * Úvodní stránka DevKitu.
   * Arduino Příklady: Hands-on vzorky začít s DevKit.

4. Rozbalte sekci **PŘÍKLADY ARDUINO** na levé straně, přejděte na **příklady pro MXCHIP AZ3166 > AzureIoT**a vyberte **RemoteMonitoring**. Otevře nové okno VS Code se složkou projektu.

   > [!NOTE]
   > Pokud podokno nezavřete, můžete jej znovu otevřít. Pomocí `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) otevřete paletu příkazů, zadejte **Arduino**a pak najděte a vyberte **Arduino: Příklady**.

## <a name="provision-required-azure-services"></a>Poskytování požadovaných služeb Azure

V okně řešení spusťte `Ctrl+P` úlohu přes `Cmd+P`(macOS: ) zadáním `task cloud-provision` do poskytnutého textového pole.

V terminálu VS Code vás interaktivní příkazový řádek provede zřízením požadovaných služeb Azure.

![Zřízení prostředků Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Vytvoření a nahrání kódu zařízení

1. Použijte `Ctrl+P` (macOS: `Cmd + P`) a **zadejte konfigurační konfiguraci zařízení**.

2. Terminál se zeptá, zda chcete použít připojovací `task cloud-provision` řetězec, který načte z kroku. Můžete také zadat vlastní připojovací řetězec zařízení kliknutím na tlačítko Vytvořit nový...'

3. Terminál vás vyzve k přepání konfiguračního režimu. Chcete-li tak učinit, podržte tlačítko A a stiskněte a uvolněte tlačítko reset. Na obrazovce se zobrazí ID DevKit u a "Konfigurace".

   ![Vstupní připojovací řetězec](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Po `task config-device-connection` dokončení klepněte na tlačítko `F1` načíst `Arduino: Upload`příkazy VS Kód a vyberte . VS Code začne ověřovat a nahrávat skicu Arduino.
  
   ![Ověření a nahrání náčrtu Arduino](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

DevKit restartuje a spustí kód.

## <a name="test-the-project"></a>Testování projektu

Když se spustí ukázková aplikace, DevKit odešle data senzorů přes Wi-Fi do akcelerátoru řešení vzdáleného monitorování Azure IoT. Chcete-li zobrazit výsledek, postupujte takto:

1. Přejděte na akcelerátor řešení vzdáleného monitorování Azure IoT a klikněte na **ŘÍDICÍ PANEL**.

2. Na konzoli řešení vzdáleného monitorování uvidíte stav senzoru DevKit.

   ![Data senzorů v urychlovači řešení vzdáleného monitorování Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Změna ID zařízení

Pokud chcete změnit pevně zakódovaný **AZ3166** na vlastní ID zařízení v kódu, upravte řádek kódu zobrazený v [příkladu vzdáleného monitorování](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23).

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, podívejte se na často kladené dotazy k [ioT vývojářské kitu](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo nás oslovte pomocí následujících kanálů:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak připojit zařízení DevKit k urychlovači řešení Vzdálenémonitorování Azure IoT a vizualizovat data senzoru, tady jsou navrhované další kroky:

* [Přehled akcelerátorů řešení Azure IoT](https://docs.microsoft.com/azure/iot-suite/)

* [Připojení zařízení MXChip IoT DevKit k aplikaci Azure IoT Central](/azure/iot-central/core/howto-connect-devkit)

* [Sada pro vývojáře IoT](https://microsoft.github.io/azure-iot-developer-kit/) 
