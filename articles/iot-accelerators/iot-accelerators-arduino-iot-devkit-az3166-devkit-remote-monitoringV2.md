---
title: 'IoT DevKit do cloudu: připojit IoT DevKit AZ3166 k akcelerátoru řešení vzdáleného sledování IoT | Microsoft Docs'
description: V tomto kurzu zjistěte, jak odeslat stav senzorů na IoT DevKit AZ3166 k akcelerátoru vzdáleného sledování IoT řešení pro monitorování a vizualizace.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: isacabe
ms.openlocfilehash: 8aa4d660fbf785a4caf99bcdeddc86aa9929c50d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627735"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Připojit MXChip IoT DevKit AZ3166 k akcelerátoru řešení IoT vzdálené monitorování


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu zjistěte, jak spouštět ukázkovou aplikaci na vašem DevKit k odesílání dat snímačů do vašeho řešení akcelerátoru.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je vše v jednom Arduino kompatibilní fórum s bohatou periferní zařízení a senzory. Můžete vytvořit pomocí programu [Visual Studio Code rozšíření pro Arduino](https://aka.ms/arduino). A dodává se s rozšiřujících se [projekty katalogu](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) na požadované prototypu řešení Internetu věcí (IoT), která využít výhod služby Microsoft Azure.

## <a name="what-you-need"></a>Co potřebujete

Dokončit [Příručka Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) na:

* Mít vaše DevKit připojení Wi-Fi
* Příprava vývojového prostředí


## <a name="open-the-remotemonitoring-sample"></a>Otevřete ukázku RemoteMonitoring

1. Odpojte DevKit z vašeho počítače, pokud je připojený.

2. Spustí kód VS.

3. Připojení DevKit k vašemu počítači. VS Code automaticky zjišťuje vaší DevKit a otevře na následujících stránkách:
  * Úvodní stránka DevKit.
  * Příklady Arduino: Praktických ukázky začít pracovat s DevKit.

4. Rozbalte levé straně **ARDUINO příklady** vyhledejte **příklady MXCHIP AZ3166 > AzureIoT**a vyberte **RemoteMonitoringv2**. Ho otevře nové okno VS Code s složce projektu v ní.

  ![Otevřít projekt vzdálené monitorování](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-arduino-examples.png)


  > [!NOTE]
  > Pokud jste dojít zavřete podokno, můžete ho znovu otevřít. Použití `Ctrl+Shift+P` (systému macOS: `Cmd+Shift+P`) Chcete-li spustit příkaz palety, zadejte **Arduino**a potom najděte a vyberte **Arduino: Příklady**.

## <a name="add-a-new-physical-device"></a>Přidejte nové fyzického zařízení

Na portálu, přejděte na **zařízení** části a klikněte v **+ nové zařízení** tlačítko. 

![Přidání nového zařízení](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device.png)

*Nového formuláře zařízení* by měly být vyplněny.
1. Klikněte na tlačítko **fyzické** v *typ zařízení* části.
2. Definovat vlastní ID zařízení (například *MXChip* nebo *AZ3166*).
3. Zvolte **automaticky generovat klíče** v *ověřovací klíč* části.
4. Klikněte na tlačítko *použít* tlačítko.

![Přidání nového zařízení formuláře](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-new-device-form.png)

Počkejte, dokud nebude dokončeno portálu zřizování nové zařízení.

![Zřizování nové zařízení ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device-provisioning.png)


Konfigurace nového zařízení, pak budou zobrazeny.
Kopírování **připojovací řetězec** vygenerovat.

![Řetězec připojení zařízení](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-new-device-connstring.png)


Tento připojovací řetězec se používá v další části.





## <a name="build-and-upload-the-device-code"></a>Vytvořit a odeslat kód zařízení

Přejděte zpět na Visual Studio Code: 

1. Použití `Ctrl+P` (systému macOS: `Cmd + P`) a typ **úlohy konfigurace připojení zařízení**.

  ![Zvolte předplatné Azure a službou IoT Hub](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion.png)

2. Terminálu dotazem, zda chcete použít připojovací řetězec zařízení IoT byste chtěli použít. Vyberte *vytvořit nový*a teď ho vložte.

  ![Vložte připojovací řetězec](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. Terminálu někdy výzvu k zadání režimu konfigurace. Uděláte to tak, podržte tlačítko A pak push verze na tlačítko Obnovit a uvolněním tlačítka A. Na obrazovce zobrazí DevKit ID a "Konfigurace".

  ![Obrazovka DevKit zařízení](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > Připojovací řetězec má být uložen do schránky, pokud jste postupovali podle v poslední části tohoto kurzu. Pokud ne, doporučujeme přejít na portál Azure a vyhledejte službu IoT Hub vaší skupiny prostředků vzdálené monitorování. Zde vidíte IoT Hub připojená zařízení a zkopírujte připojovací řetězec zařízení.

  ![Podívejte se na připojovací řetězec](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-connection-string-of-a-device.png)


Nyní uvidíte novou fyzického zařízení v části VS Code "Zařízení Azure IoT Hub":

![Všimněte si nového zařízení IoT Hub](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-new-iot-hub-device.png)

## <a name="test-the-project"></a>Testování projektu

Při spuštění ukázkové aplikace, DevKit odešle data snímačů pomocí Wi-Fi akcelerátory řešení IoT. Pokud chcete zobrazit výsledek, postupujte takto:

1. Přejděte do vaší akcelerátoru řešení IoT a klikněte na tlačítko **řídicí panel**.

2. V konzole akcelerátoru řešení IoT zobrazí se stav vaší DevKit senzor. 

![Data snímačů v akcelerátorů řešení IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-dashboard.png)

Pokud kliknete na název senzoru (AZ3166) na kartě se otevře na pravé straně řídicího panelu, kde uvidíte graf senzorů čip TPM MX v reálném čase.


## <a name="send-a-c2d-message"></a>Odeslat zprávu C2D
Vzdálené monitorování v2 umožňuje vyvolání vzdálené metody na zařízení.
Čip TPM MX ukázkový kód publikuje tři metody, které vidíte v části způsob, pokud je vybrána senzoru.

![Čip TPM MX metody](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-methods.png)

Můžete změnit barvu LED čip TPM MX metodou "LedColor". Pro provedení této akce, zaškrtněte políčko zařízení a klikněte na tlačítko plán. 

![Čip TPM MX metody](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-schedule.png)

Zvolte metodu volat ChangeColor v rozevírací nabídce, kde všechny metody zobrazují, zápis název a použít.

![Čip TPM MX rozevíracího seznamu](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-change-color.png)

V několik sekund, musí vaše fyzické čip TPM MX změnit barva RGB vedla (dole a tlačítko)

![Čip TPM vedených MX](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-led.png)


## <a name="change-device-id"></a>ID zařízení změn

ID zařízení IoT hub můžete změnit pomocí následujících [Tato příručka](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).


## <a name="problems-and-feedback"></a>Problémy a zpětné vazby

Pokud narazíte na problémy, podívejte se na [nejčastější dotazy k](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo se obraťte na nás z následující kanály:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili postup DevKit zařízení připojit k vaší akcelerátorů řešení IoT a vizualizovat data snímačů, tady jsou navrhované další kroky:

* [Přehled akcelerátorů řešení IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Připojte zařízení MXChip IoT DevKit do aplikace Microsoft IoT centrální](https://docs.microsoft.com/en-us/microsoft-iot-central/howto-connect-devkit)
