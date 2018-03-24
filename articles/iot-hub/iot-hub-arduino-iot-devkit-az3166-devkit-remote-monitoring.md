---
title: 'IoT DevKit do cloudu: připojit IoT MXChip DevKit do služby Azure IoT Hub | Microsoft Docs'
description: V tomto kurzu zjistěte, jak odeslat stav senzorů na IoT DevKit AZ3166 Azure IoT Suite pro monitorování a vizualizace.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 92efd0970bcf516c4210f831a0c2f23b3ee7b5d8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-suite-for-remote-monitoring"></a>Připojení MXChip IoT DevKit Azure IoT Suite pro vzdálené monitorování

V tomto kurzu zjistěte, jak spouštět ukázkovou aplikaci na vašem DevKit k odesílání dat snímačů do sady Azure IoT Suite.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je vše v jednom Arduino kompatibilní fórum s bohatou periferní zařízení a senzory. Můžete vytvořit pomocí programu [Visual Studio Code rozšíření pro Arduino](https://aka.ms/arduino). A dodává se s rozšiřujících se [projekty katalogu](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) na požadované prototypu řešení Internetu věcí (IoT), která využít výhod služby Microsoft Azure.

## <a name="what-you-need"></a>Co potřebujete

Dokončit [Příručka Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) na:

* Mít vaše DevKit připojení Wi-Fi
* Příprava vývojového prostředí

Aktivní předplatné Azure. Pokud jeden nemáte, můžete zaregistrovat pomocí jedné z těchto dvou metod:

* Aktivovat [Bezplatný zkušební účet Microsoft Azure 30 dnů](https://azureinfo.microsoft.com/us-freetrial.html)
* Deklarace identity vaší [kreditu Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) jste předplatitelem MSDN nebo v sadě Visual Studio

## <a name="create-an-azure-iot-suite"></a>Vytvoření Azure IoT Suite

1. Přejděte na [Azure IoT Suite lokality](https://www.azureiotsuite.com/) a klikněte na tlačítko **vytvořte nové řešení**.
  ![Vyberte typ Azure IoT Suite](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)
  > [!WARNING]
  > Ve výchozím nastavení tato ukázka vytvoří Centrum IoT S2 po vytvoření jednoho IoT Suite. Pokud toto centrum IoT se nepoužívá s ohromnou počet zařízení, důrazně doporučujeme ho starší verzi z S2 S1 a odstranit IoT Suite, související IoT Hub se taky dají odstranit, pokud ji už nepotřebujete. 

2. Vyberte **vzdálené monitorování**.

3. Zadejte název řešení, vyberte předplatné a v oblasti a pak klikněte na tlačítko **vytvořte řešení**. Zřídí řešení může chvíli trvat.
  ![Vytvoření řešení](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Po dokončení poskytování klikněte na tlačítko **spusťte**. Některé Simulovaná zařízení jsou vytvořeny pro řešení během procesu zřizování. Klikněte na tlačítko **zařízení** je rezervovat. ![Řídicí panel](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  ![konzoly](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Klikněte na tlačítko **přidání zařízení**.

6. Klikněte na tlačítko **přidat nové** pro **vlastní zařízení**.
  ![Přidejte nové zařízení](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Klikněte na tlačítko **ručně definovat vlastní ID zařízení**, zadejte `AZ3166`a potom klikněte na **vytvořit**.
  ![Vytvoří se ID zařízení](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Poznamenejte si **název hostitele centra IoT**a klikněte na tlačítko **provádí**.

## <a name="open-the-remotemonitoring-sample"></a>Otevřete ukázku RemoteMonitoring

1. Odpojte DevKit z vašeho počítače, pokud je připojený.

2. Spustí kód VS.

3. Připojení DevKit k vašemu počítači. VS Code automaticky zjišťuje vaší DevKit a otevře na následujících stránkách:
  * Úvodní stránka DevKit.
  * Příklady Arduino: Praktických ukázky začít pracovat s DevKit.

4. Rozbalte levé straně **ARDUINO příklady** vyhledejte **příklady MXCHIP AZ3166 > AzureIoT**a vyberte **RemoteMonitoring**. Ho otevře nové okno VS Code s složce projektu v ní.
  > [!NOTE]
  > Pokud jste dojít zavřete podokno, můžete ho znovu otevřít. Použití `Ctrl+Shift+P` (systému macOS: `Cmd+Shift+P`) Chcete-li spustit příkaz palety, zadejte **Arduino**a potom najděte a vyberte **Arduino: Příklady**.

## <a name="provision-required-azure-services"></a>Zřídit požadované služby Azure

V okně řešení spuštění úkolu prostřednictvím `Ctrl+P` (systému macOS: `Cmd+P`) tak, že zadáte `task cloud-provision` zadaný textového pole:

V terminálu VS Code interaktivního příkazového řádku vás provede zřizování požadované služby Azure:

![Zřízení prostředků Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Vytvořit a odeslat kód zařízení

1. Použití `Ctrl+P` (systému macOS: `Cmd + P`) a typ **úlohy konfigurace připojení zařízení**.

2. Terminálu dotazem, zda chcete použít připojovací řetězec, který načte z `task cloud-provision` krok. Vlastní řetězec připojení zařízení může také vstupní kliknutím na tlačítko "Vytvořit novou..."

3. Terminálu zobrazí výzvu k zadání režim konfigurace. Uděláte to tak, podržte tlačítko A pak push a verzí na tlačítko Obnovit. Na obrazovce zobrazí DevKit ID a "Konfigurace".
  ![Vstupní připojovací řetězec](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Po `task config-device-connection` dokončení, klikněte na tlačítko `F1` se načíst příkazy VS Code a vyberte `Arduino: Upload`, pak spustí VS Code ověření a odesílání Arduino načrtnout: ![ověření a odeslání nákresu Arduino](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

DevKit restartuje a spuštění kódu.

## <a name="test-the-project"></a>Testování projektu

Při spuštění ukázkové aplikace, DevKit odešle data snímačů pomocí Wi-Fi do sady Azure IoT Suite. Pokud chcete zobrazit výsledek, postupujte takto:

1. Přejděte do sady Azure IoT Suite a klikněte na tlačítko **řídicí panel**.

2. V konzole řešení Azure IoT Suite zobrazí se stav vaší DevKit snímač.

![Data snímačů v Azure IoT Suite](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>ID zařízení změn

ID zařízení IoT hub můžete změnit pomocí následujících [Tato příručka](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/). A pokud chcete změnit pevně zakódované **AZ3166** do přizpůsobené ID zařízení v kódu. [Zde](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23) je řádek kódu, který můžete upravit.

## <a name="problems-and-feedback"></a>Problémy a zpětné vazby

Pokud narazíte na problémy, podívejte se na [nejčastější dotazy k](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo se obraťte na nás z následující kanály:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili postup DevKit zařízení připojit k vaší Azure IoT Suite a vizualizovat data snímačů, tady jsou navrhované další kroky:

* [Přehled služby Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/)
* [Připojte zařízení MXChip IoT DevKit do aplikace Microsoft IoT centrální](https://docs.microsoft.com/en-us/microsoft-iot-central/howto-connect-devkit)
