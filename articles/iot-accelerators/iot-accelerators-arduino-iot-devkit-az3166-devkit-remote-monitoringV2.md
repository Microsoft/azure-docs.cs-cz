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
ms.openlocfilehash: 92ce85a3cc94702468a13348d3a41667498c68f5
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43187387"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Připojení MXChip IoT DevKit AZ3166 se k akcelerátoru řešení vzdáleného monitorování IoT


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se dozvíte, jak spustit ukázkovou aplikaci na váš DevKit odeslat data ze senzorů akcelerátor řešení.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je vše v jednom Arduino kompatibilní deska bohaté periferní zařízení a senzorů. Můžete vyvíjet pro něj pomocí [rozšíření Visual Studio Code pro Arduino](https://aka.ms/arduino). A stále se rozšiřující obsahuje [katalogu projektů](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) a provede vás prototypu Internet of Things (IoT) řešení, které budou využívat služeb Microsoft Azure.

## <a name="what-you-need"></a>Co potřebujete

Dokončit [– Příručka Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) na:

* Mají vaše DevKit připojení k Wi-Fi
* Příprava vývojového prostředí


## <a name="open-the-remotemonitoring-sample"></a>Otevřete ukázku RemoteMonitoring

1. Odpojte DevKit z vašeho počítače, pokud je připojený.

2. Spusťte VS Code.

3. Připojení DevKit k vašemu počítači. VS Code automaticky detekuje vaše DevKit a otevře na následujících stránkách:
  * Úvodní stránka DevKit.
  * Příklady Arduino: Praktické ukázky, které vám umožní začít DevKit.

4. Rozbalte na levé straně **ARDUINO příklady** vyhledejte **příklady MXCHIP AZ3166 > AzureIoT**a vyberte **RemoteMonitoringv2**. Otevře se nové okno VS Code s složky projektu do něj.

  ![Otevřít projekt vzdáleného monitorování](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-arduino-examples.png)


  > [!NOTE]
  > Pokud máte náhodou zavřete podokno, můžete ho znovu otevřít. Použití `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) otevřete paletu příkazů, zadejte **Arduino**a poté vyhledejte a vyberte **Arduino: Příklady**.

## <a name="add-a-new-physical-device"></a>Přidání nového fyzického zařízení

Na portálu přejděte na **zařízení** a existuje, klikněte v **+ nové zařízení** tlačítko. 

![Přidání nového zařízení](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device.png)

*Nový formulář zařízení* by mělo být vyplněno.
1. Klikněte na tlačítko **fyzické** v *typ zařízení* oddílu.
2. Definovat vlastní ID zařízení (třeba *MXChip* nebo *AZ3166*).
3. Zvolte **automaticky vygenerovat klíče** v *ověřovací klíč* oddílu.
4. Klikněte na tlačítko *použít* tlačítko.

![Přidání nového formuláře zařízení](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-new-device-form.png)

Počkejte, dokud na portálu se dokončí zřizování nového zařízení.

![Zřízení nového zařízení ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device-provisioning.png)


Konfigurace nové zařízení, pak se zobrazí.
Kopírovat **připojovací řetězec** vygenerována.

![Připojovací řetězec zařízení](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-new-device-connstring.png)


Tento připojovací řetězec se používá v další části.





## <a name="build-and-upload-the-device-code"></a>Vytvoření a nahrání kódu zařízení

Přejděte zpět do Visual Studio Code: 

1. Použití `Ctrl+P` (macOS: `Cmd + P`) a typ **úloh konfiguračním připojení zařízení**.

  ![Zvolte předplatné Azure a centrem IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion.png)

2. Terminálu zobrazí dotaz, zda chcete použít připojovací řetězec zařízení IoT, byste chtěli použít. Vyberte *vytvořit nový*a nyní jej vložte.

  ![Vložte připojovací řetězec](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. Terminálu někdy výzvu k zadání režim konfigurace. Uděláte to tak, podržte tlačítko A pak push a uvolněte tlačítko Obnovení nastavení a pak uvolněte tlačítko A. Na obrazovce se zobrazí DevKit ID a "Configuration".

  ![Obrazovka DevKit zařízení](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > Připojovací řetězec má být uložen do schránky, pokud jste postupovali podle poslední části tohoto kurzu. Pokud ne, by měl přejít na web Azure Portal a vyhledejte služby IoT Hub vaší skupiny prostředků vzdáleného monitorování. Tam uvidíte připojená zařízení služby IoT Hub a zkopírujte připojovací řetězec zařízení.

  ![Vyhledejte připojovací řetězec](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-connection-string-of-a-device.png)


Teď můžete vidět nové fyzické zařízení v části "Zařízení Azure IoT Hub" VS Code:

![Všimněte si, že nové zařízení centra IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-new-iot-hub-device.png)

## <a name="test-the-project"></a>Testování projektu

Když spustíte ukázkovou aplikaci, DevKit odešle data ze senzorů přes Wi-Fi vaše akcelerátory řešení IoT. K zobrazení výsledku, postupujte podle těchto kroků:

1. Přejděte na akcelerátor řešení IoT a klikněte na tlačítko **řídicí panel**.

2. V konzole akcelerátorů řešení IoT se zobrazí stav DevKit senzoru. 

![Data ze senzorů v akcelerátory řešení IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-dashboard.png)

Pokud kliknete na název snímače (AZ3166) na kartě se otevře na pravé straně řídicího panelu, ve kterém uvidíte graf čip TPM MX senzorů v reálném čase.


## <a name="send-a-c2d-message"></a>Odešlete zprávu C2D
V2 vzdáleného monitorování umožňuje vyvolat vzdálené metody v zařízení.
Příklad kódu čipu MX publikuje tři metody, které se zobrazí v části způsob při výběru senzoru.

![Čip TPM MX metody](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-methods.png)

Můžete změnit barvu jednoho LED čip TPM MX pomocí metody "LedColor". To je, zaškrtněte políčko zařízení a klikněte na tlačítko plánu. 

![Čip TPM MX metody](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-schedule.png)

Zvolte metodu nazvanou ChangeColor v rozevíracím seznamu, kde všechny metody se zobrazí, zápis názvu a použít.

![Čip TPM MX rozevíracího seznamu](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-change-color.png)

Za několik sekund, by měl váš fyzický čip TPM MX změnit barva RGB vedla (dole a tlačítko)

![Čip TPM vedly MX](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-led.png)

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, podívejte se na [nejčastějších dotazech týkajících se](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo kontaktujte nás z následujících kanálů:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak připojit k vaše akcelerátory řešení IoT DevKit zařízení a vizualizaci dat snímačů, tady jsou další navrhované kroky:

* [Přehled akcelerátorů řešení IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Připojení MXChip IoT DevKit zařízení do aplikace Microsoft IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
* [Sady pro vývojáře IoT](https://microsoft.github.io/azure-iot-developer-kit/)
