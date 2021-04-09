---
title: Připojení MXChip IoT DevKit k Azure IoT Hub vzdálené monitorování
description: V tomto kurzu se dozvíte, jak odeslat stav senzorů v IoT DevKit AZ3166 do akcelerátoru řešení vzdáleného monitorování Azure IoT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 6912124ce8d1741731d625dccfea445417b2488d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785012"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Připojení MXChip IoT DevKit k akcelerátoru řešení vzdáleného monitorování Azure IoT

V tomto kurzu se naučíte, jak spustit ukázkovou aplikaci v DevKit a poslat data ze senzorů do svého akcelerátoru řešení vzdáleného monitorování Azure IoT.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je vše v Arduino kompatibilní vývěsce s bohatými periferními zařízeními a snímači. Pro IT můžete vyvíjet pomocí [rozšíření Visual Studio Code Arduino](https://aka.ms/arduino). Obsahuje také katalog rostoucích [projektů](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) , který vám pomůže vyprototypit řešení Internet věcí (IoT), která využívají služby Microsoft Azure.

## <a name="what-you-need"></a>Co budete potřebovat

Dokončete [průvodce Začínáme](./iot-hub-arduino-iot-devkit-az3166-get-started.md) :

* Připojte DevKit k Wi-Fi
* Příprava vývojového prostředí

Musíte mít aktivní předplatné Azure. Pokud ho nemáte, můžete se zaregistrovat přes jednu z těchto dvou metod:

* Aktivujte si [bezplatný 30denní zkušební účet Microsoft Azure](https://azure.microsoft.com/free/) .

* Pokud jste předplatitelem MSDN nebo Visual studia, můžete svůj [kredit Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) uplatnit

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Vytvoření akcelerátoru řešení vzdáleného monitorování Azure IoT

1. Přejděte na [Web akcelerátory řešení Azure IoT](https://www.azureiotsolutions.com/) a klikněte na **vytvořit nové řešení**.

   ![Vybrat typ akcelerátoru řešení Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Ve výchozím nastavení Tato ukázka vytvoří IoT Hub S2 poté, co vytvoří jedno akcelerátor řešení vzdáleného monitorování IoT. Pokud se toto centrum IoT nepoužívá s větším počtem zařízení, důrazně doporučujeme, abyste ho nadowngradal z S2 na S1 a odstranili akcelerátor řešení vzdáleného monitorování IoT, aby se související IoT Hub mohly odstranit i v případě, že ji už nepotřebujete. 

2. Vyberte **vzdálené monitorování**.

3. Zadejte název řešení, vyberte předplatné a oblast a pak klikněte na **vytvořit řešení**. Může chvíli trvat, než se zřídí řešení.
  
   ![Vytvoření řešení](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Po dokončení zřizování klikněte na **Spustit**. Během procesu zřizování se vytvoří několik simulovaných zařízení pro řešení. Pokud je chcete zaregistrovat, klikněte na **zařízení** .

   ![Řídicí panel](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Konzola](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Klikněte na **Přidat zařízení**.

6. Klikněte na **Přidat nový** pro **vlastní zařízení**.
  
   ![Přidání nového zařízení](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Klikněte na možnost **Chci definovat vlastní ID zařízení**, zadejte `AZ3166` a potom klikněte na **vytvořit**.
  
   ![Vytvořit zařízení s ID](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Poznamenejte si **IoT Hub název hostitele** a klikněte na **Hotovo**.

## <a name="open-the-remotemonitoring-sample"></a>Otevřete ukázku RemoteMonitoring

1. Odpojte DevKit z počítače, pokud je připojený.

2. Spusťte VS Code.

3. Připojte DevKit k počítači. VS Code automaticky detekuje DevKit a otevře následující stránky:

   * Úvodní stránka DevKit
   * Příklady Arduino: praktické ukázky, které vám pomohou začít s DevKit.

4. Rozbalte části **Příklady Arduino** na levé straně, přejděte k **příkladům pro MXCHIP AZ3166 > AzureIoT** a vyberte **RemoteMonitoring**. Otevře se nové okno VS Code se složkou projektu v něm.

   > [!NOTE]
   > Pokud se rozhodnete podokno zavřít, můžete ho znovu otevřít. Použijte `Ctrl+Shift+P` (MacOS: `Cmd+Shift+P` ) k otevření palety příkazů, zadejte **Arduino** a pak vyhledejte a vyberte **Arduino: příklady**.

## <a name="provision-required-azure-services"></a>Zřízení požadovaných služeb Azure

V okně řešení spusťte úlohu prostřednictvím `Ctrl+P` (MacOS: `Cmd+P` ) zadáním `task cloud-provision` do zadaného textového pole.

V terminálu VS Code vás interaktivní příkazový řádek provede zřízením požadovaných služeb Azure.

![Zřizování prostředků Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Sestavení a nahrání kódu zařízení

1. Použijte `Ctrl+P` (MacOS: `Cmd + P` ) a zadejte **Konfigurace úlohy-zařízení-připojení**.

2. Terminál se zeptá, jestli chcete použít připojovací řetězec, který načte z `task cloud-provision` kroku. Můžete také zadat vlastní připojovací řetězec zařízení tak, že kliknete na vytvořit nový...

3. Terminál vás vyzve k zadání režimu konfigurace. Provedete to tak, že stisknete tlačítko a, nahrajete a uvolníte tlačítko obnovit. Na obrazovce se zobrazí ID DevKit a konfigurace.

   ![Vstupní připojovací řetězec](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Po `task config-device-connection` dokončení kliknutím `F1` načtěte vs Code příkazy a vyberte `Arduino: Upload` . VS Code spustí ověřování a nahrávání náčrtu Arduino.
  
   ![Ověření a nahrání Arduino náčrtu](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

DevKit se restartuje a začne používat kód.

## <a name="test-the-project"></a>Testování projektu

Po spuštění ukázkové aplikace DevKit odešle data ze senzorů přes WiFi do akcelerátoru řešení vzdáleného monitorování Azure IoT. Chcete-li zobrazit výsledek, postupujte podle následujících kroků:

1. Přejděte do akcelerátoru řešení vzdáleného monitorování Azure IoT a klikněte na **řídicí panel**.

2. V konzole řešení vzdáleného monitorování se zobrazí stav senzoru DevKit.

   ![Data snímačů v akcelerátoru řešení vzdáleného monitorování Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Změnit ID zařízení

Pokud chcete změnit pevně zakódované **AZ3166** na vlastní ID zařízení v kódu, upravte řádek kódu zobrazeného v [příkladu vzdáleného monitorování](/previous-versions/azure/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoring-v2).

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, přečtěte si [Nejčastější dotazy k sadě IoT Developer Kit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo se můžete obrátit na nás s použitím následujících kanálů:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s postupem připojení zařízení DevKit k akcelerátoru řešení vzdáleného monitorování Azure IoT a vizualizaci dat snímačů, tady jsou doporučené další kroky:

* [Přehled akcelerátorů řešení Azure IoT](/azure/iot-suite/)

* [Sada IoT Developer Kit](https://microsoft.github.io/azure-iot-developer-kit/)