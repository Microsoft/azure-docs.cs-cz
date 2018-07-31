---
title: IoT DevKit do cloudu – připojení k Azure IoT Hub IoT MXChip DevKit | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak odeslat stav senzory IoT DevKit AZ3166 se do akcelerátoru řešení vzdáleného monitorování Azure IoT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 806ae38f614c44ce25b8fcc159b74f1bda3f00f3
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343113"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Připojení MXChip IoT DevKit k akcelerátoru řešení vzdáleného monitorování Azure IoT

V tomto kurzu se dozvíte, jak spustit ukázkovou aplikaci na váš DevKit odeslat data ze senzorů akcelerátor řešení vzdáleného monitorování Azure IoT.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je vše v jednom Arduino kompatibilní deska bohaté periferní zařízení a senzorů. Můžete vyvíjet pro něj pomocí [rozšíření Visual Studio Code pro Arduino](https://aka.ms/arduino). A stále se rozšiřující obsahuje [katalogu projektů](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) a provede vás prototypu Internet of Things (IoT) řešení, které budou využívat služeb Microsoft Azure.

## <a name="what-you-need"></a>Co potřebujete

Dokončit [– Příručka Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) na:

* Mají vaše DevKit připojení k Wi-Fi
* Příprava vývojového prostředí

Aktivní předplatné Azure. Pokud nemáte, můžete zaregistrovat pomocí jedné z těchto dvou metod:

* Aktivovat [bezplatné 30denní zkušební verzi účtu Microsoft Azure](https://azure.microsoft.com/free/)

* Deklarace identity vaší [kredit Azure ve výši](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) Pokud máte předplatné MSDN nebo Visual Studio

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Vytvoření akcelerátoru řešení vzdáleného monitorování Azure IoT

1. Přejděte na [lokality akcelerátory řešení Azure IoT](https://www.azureiotsolutions.com/) a klikněte na tlačítko **vytvořit nové řešení**.

   ![Vyberte typ akcelerátorů řešení Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Tato ukázka vytvoří ve výchozím nastavení, služby IoT Hub S2 po vytvoření jednoho akcelerátoru řešení vzdáleného monitorování IoT. Pokud toto centrum IoT se nepoužívá s masivní počet zařízení, důrazně doporučujeme downgradovat z S2 na S1 a odstranit akcelerátor řešení vzdálené monitorování IoT, aby související služby IoT Hub můžete také odstranit, pokud ho už nepotřebují. 

2. Vyberte **vzdálené monitorování**.

3. Zadejte název řešení, vyberte předplatné a oblast a potom klikněte na **vytvořit řešení**. Zřídí řešení může chvíli trvat.
  
   ![Vytvořit řešení](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Po zřízení dokončí, klikněte na tlačítko **spuštění**. Některé Simulovaná zařízení jsou vytvořené pro dané řešení během procesu zřizování. Klikněte na tlačítko **zařízení** je.

   ![Řídicí panel](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Konzola](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Klikněte na tlačítko **přidat zařízení**.

6. Klikněte na tlačítko **přidat nový** pro **vlastní zařízení**.
  
   ![Přidání nového zařízení](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Klikněte na tlačítko **definovat vlastní ID zařízení**, zadejte `AZ3166`a potom klikněte na tlačítko **vytvořit**.
  
   ![Vytvoření s ID zařízení](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Poznamenejte si **název hostitele služby IoT Hub**a klikněte na tlačítko **provádí**.

## <a name="open-the-remotemonitoring-sample"></a>Otevřete ukázku RemoteMonitoring

1. Odpojte DevKit z vašeho počítače, pokud je připojený.

2. Spusťte VS Code.

3. Připojení DevKit k vašemu počítači. VS Code automaticky detekuje vaše DevKit a otevře na následujících stránkách:

  * Úvodní stránka DevKit.
  * Příklady Arduino: Praktické ukázky, které vám umožní začít DevKit.

4. Rozbalte na levé straně **ARDUINO příklady** vyhledejte **příklady MXCHIP AZ3166 > AzureIoT**a vyberte **RemoteMonitoring**. Otevře se nové okno VS Code s složky projektu do něj.

   > [!NOTE]
   > Pokud máte náhodou zavřete podokno, můžete ho znovu otevřít. Použití `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) otevřete paletu příkazů, zadejte **Arduino**a poté vyhledejte a vyberte **Arduino: Příklady**.

## <a name="provision-required-azure-services"></a>Zřízení požadovaných služeb Azure

V okně řešení spustit úlohu `Ctrl+P` (macOS: `Cmd+P`) tak, že zadáte `task cloud-provision` do zadaného textového pole.

V terminálu VS Code interaktivního příkazového řádku vás provede zřizování požadovaných služeb Azure.

![Zřízení prostředků Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Vytvoření a nahrání kódu zařízení

1. Použití `Ctrl+P` (macOS: `Cmd + P`) a typ **úloh konfiguračním připojení zařízení**.

2. Terminálu zeptá, jestli chcete použít připojovací řetězec, který načte z `task cloud-provision` kroku. Může také zadejte vlastní připojovací řetězec zařízení kliknutím na tlačítko "Vytvořit novou..."

3. Terminálu zobrazí výzvu k zadání režim konfigurace. Uděláte to tak, podržte tlačítko A pak push a uvolněte tlačítko Obnovení nastavení. Na obrazovce se zobrazí DevKit ID a "Configuration".

   ![Vstupní připojovací řetězec](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Po `task config-device-connection` dokončí, klikněte na tlačítko `F1` načtení příkazy VS Code a vyberte `Arduino: Upload`. VS Code spustí ověřování a odesílání Arduino sketch.
  
   ![Ověřování a odesílání Arduino sketch](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

DevKit restartuje a spustí kód.

## <a name="test-the-project"></a>Testování projektu

Když spustíte ukázkovou aplikaci, DevKit odešle data ze senzorů přes Wi-Fi akcelerátor řešení vzdáleného monitorování Azure IoT. K zobrazení výsledku, postupujte podle těchto kroků:

1. Přejděte na akcelerátor řešení vzdáleného monitorování Azure IoT a klikněte na tlačítko **řídicí panel**.

2. V konzole řešení vzdáleného monitorování se zobrazí stav DevKit senzoru.

   ![Data ze senzorů v akcelerátoru řešení vzdáleného monitorování Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Změna ID zařízení

ID zařízení ve službě IoT Hub můžete změnit podle následujícího [vlastní ID zařízení Průvodce](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/). Pokud chcete změnit pevně zakódované **AZ3166** přizpůsobené zařízení ID v kódu, upravte řádek oi kód zobrazený [vzdálené monitorování příklad](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23).

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, podívejte se na [sady pro vývojáře IoT nejčastějších dotazech týkajících se](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo kontaktujte nás prostřednictvím následujících kanálů:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak připojit k akcelerátor řešení vzdáleného monitorování Azure IoT DevKit zařízení a vizualizaci dat snímačů, tady jsou další navrhované kroky:

* [Přehled akcelerátorů řešení Azure IoT](https://docs.microsoft.com/azure/iot-suite/)

* [Připojení MXChip IoT DevKit zařízení do aplikace Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)