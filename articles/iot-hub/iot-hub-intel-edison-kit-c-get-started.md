---
title: Intel Edison do cloudu (C) - Intel Edison připojit ke službě Azure IoT Hub | Dokumentace Microsoftu
description: Zjistěte, jak nastavit a Intel Edison připojit ke službě Azure IoT Hub pro Intel Edison k odesílání dat do cloudové platformy Azure v tomto kurzu.
author: rangv
manager: ''
keywords: Azure iot intel edison, intel edison iot hub, intel edison odesílat data do cloudu, intel edison s cloudem
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 2e0f8b46f2020db0bb657a8e099158c349af6a89
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187661"
---
# <a name="connect-intel-edison-to-azure-iot-hub-c"></a>Připojení k Azure IoT Hub (C) Intel Edison

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

V tomto kurzu začnete pomocí seznámení se základy práce s Intel Edison. Pak se naučíte bez problémů propojit si zařízení do cloudu s využitím [Azure IoT Hub](about-iot-hub.md).

Sadu ještě nemáte? Spustit [zde](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>Co můžete dělat

* Nastavení Intel Edison a a Groove moduly.
* Vytvoření služby IoT hub.
* Registrace zařízení pro Edison ve službě IoT hub.
* Spusťte ukázkovou aplikaci na Edison k odesílání dat ze senzorů do služby IoT hub.

Intel Edison připojení do služby IoT hub, kterou vytvoříte. Potom spustíte ukázkovou aplikaci na Edison ke shromažďování dat teploty a vlhkosti ze senzoru teploty Groove. A konečně odeslat data ze senzorů do služby IoT hub.

## <a name="what-you-learn"></a>Co se naučíte

* Postup vytvoření služby Azure IoT hub a získat nový připojovací řetězec zařízení.
* Jak se připojit Edison s teplotní snímač Groove.
* Jak shromažďovat data ze senzorů pomocí Průvodce ukázkovou aplikaci na Edison.
* Jak odeslat data ze senzorů do služby IoT hub.

## <a name="what-you-need"></a>Co potřebujete

![Co potřebujete](media/iot-hub-intel-edison-kit-c-get-started/0_kit.png)

* Na panelu Intel Edison
* Panel rozšíření Arduino
* Aktivní předplatné Azure. Pokud nemáte účet Azure [vytvořit Bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) za několik minut.
* Na Macu nebo počítači se systémem Windows nebo Linux.
* Připojení k Internetu.
* B Micro kabelem USB typ A
* Zdroj napájení aktuální direct (DC). Váš zdroj napájení byste ohodnotili následujícím způsobem:
  - ŘADIČ DOMÉNY 7 15V
  - Alespoň 1500mA
  - System center/vnitřní kód pin musí být pozitivní pole napájení

Následující položky jsou volitelné:

* Grove základní Shield V2
* Groove - teplotní snímač
* Kabel Groove
* Oddělovačem pruhy nebo šrouby zahrnout v balení, včetně dvou šrouby připevnit modulu na panelu rozšíření a čtyři postupné hlavou a plastů nosníků.

> [!NOTE] 
Tyto položky jsou volitelné, protože podpora vzorek kódu Simulovaná data ze senzorů.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-intel-edison"></a>Instalace Intel Edison

### <a name="assemble-your-board"></a>Poskládejte si panelu

Tato část obsahuje kroky k připojení modul Intel® Edison k panelu rozšíření.

1. Modul Intel® Edison v rámci bílé osnovy umístíte na panelu rozšíření, zarovnání otvory v modulu s šrouby na panelu rozšíření.

2. Klepnout na modulu pod slova `What will you make?` dokud máte pocit, že stává hračkou.

   ![Poskládejte si panel 2](media/iot-hub-intel-edison-kit-c-get-started/1_assemble_board2.jpg)

3. Dva hex nuts (součástí balíčku) použijte k zabezpečení modulu do panelu rozšíření.

   ![Poskládejte si panel 3](media/iot-hub-intel-edison-kit-c-get-started/2_assemble_board3.jpg)

4. Vložte šroubu jedním ze čtyř rohu děr na panelu rozšíření. Otočením a zkracují jeden bílé plastů nosníků na šroubek.

   ![Poskládejte si panel 4](media/iot-hub-intel-edison-kit-c-get-started/3_assemble_board4.jpg)

5. Opakujte pro další tři rohu nosníků.

   ![Poskládejte si panel 5](media/iot-hub-intel-edison-kit-c-get-started/4_assemble_board5.jpg)

Nyní je sestavena panelu.

   ![sestavený nástěnky](media/iot-hub-intel-edison-kit-c-get-started/5_assembled_board.jpg)

### <a name="connect-the-grove-base-shield-and-the-temperature-sensor"></a>Připojit Shield Grove základní a teplotní snímač

1. Umístěte Shield Grove základní na panelu. Zajistěte, aby že všechny PIN kódy jsou úzce zapojí se do panelu.
   
   ![Grove základní Shield](media/iot-hub-intel-edison-kit-c-get-started/6_grove_base_sheild.jpg)

2. Kabel Groove připojit senzor teploty Groove na Shield Grove základní **A0** portu.

   ![Připojte se k teplotní snímač](media/iot-hub-intel-edison-kit-c-get-started/7_temperature_sensor.jpg)
   
   ![Připojení Edison a snímače](media/iot-hub-intel-edison-kit-c-get-started/16_edion_sensor.png)

Vaše senzor je teď připravený.

### <a name="power-up-edison"></a>Poskytněte Edison

1. Zařaďte napájení.

   ![Připojte zdroj napájení](media/iot-hub-intel-edison-kit-c-get-started/8_plug_power.jpg)

2. Zelená LED (označené jako zdroj dat DS1 na panelu rozšíření Arduino *) by bylo možné a zůstat po.

3. Počkejte minutu pro panel na spuštění.

   > [!NOTE]
   > Pokud nemáte ke zdroji napájení řadiče domény, můžete stále power panelu přes USB port. Zobrazit `Connect Edison to your computer` podrobné informace. Provozování panelu tímto způsobem může způsobit nepředvídatelné chování z panelu, zejména v případě, že pomocí sítě Wi-Fi nebo řízení motory.

### <a name="connect-edison-to-your-computer"></a>Připojte se k počítači Edison

1. Přepnout dolů mikrospínače na dvou micro portů USB, tak, aby Edison v režimu zařízení. Rozdíly mezi režimem zařízení a hostitele, použijte odkaz [tady](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode).

   ![Přepnout dolů mikrospínače](media/iot-hub-intel-edison-kit-c-get-started/9_toggle_down_microswitch.jpg)

2. Začátek micro portu USB připojte micro kabelu USB.

   ![Začátek micro portu USB](media/iot-hub-intel-edison-kit-c-get-started/10_top_usbport.jpg)

3. Druhém konci USB kabelu připojte k počítači.

   ![Počítač USB](media/iot-hub-intel-edison-kit-c-get-started/11_computer_usb.jpg)

4. Budete vědět, že panelu plně inicializován, když se počítač připojí novou jednotku (podobně jako vkládání SD karty do počítače).

## <a name="download-and-run-the-configuration-tool"></a>Stáhněte si a spusťte konfigurační nástroj
Získejte nejnovější nástroje Konfigurace z [tento odkaz](https://software.intel.com/en-us/iot/hardware/edison/downloads) uvedené v části `Installers` záhlaví. Spusťte nástroj a postupujte podle jeho na obrazovce pokyny, kde je potřeba kliknutím na tlačítko Další

### <a name="flash-firmware"></a>Flash firmwaru
1. Na `Set up options` klikněte na `Flash Firmware`.
2. Vyberte obrázek, který se na panelu flash pomocí jedné z následujících akcí:
   - Chcete-li stáhnout a flash panelu pomocí nejnovější image firmwaru, která je k dispozici od Intelu, vyberte `Download the latest image version xxxx`.
   - Chcete-li flash panelu s obrázkem, který jste už uložili ve vašem počítači, vyberte `Select the local image`. Vyhledejte a vyberte bitovou kopii, kterou chcete flash na váš panel.
3. Nástroj instalační program se pokusí o flash panelu. Blikající celý proces může trvat až 10 minut.

### <a name="set-password"></a>Nastavit heslo
1. Na `Set up options` klikněte na `Enable Security`.
2. Můžete nastavit vlastní název pro Intel® Edison panelu. Tato položka je nepovinná.
3. Zadejte heslo pro panelu a potom klikněte na `Set password`.
4. Označte heslo, které se použije v pozdější.

### <a name="connect-wi-fi"></a>Připojení Wi-Fi
1. Na `Set up options` klikněte na `Connect Wi-Fi`. Počkejte, až jednu minutu jako počítač hledá dostupné sítě Wi-Fi.
2. Z `Detected Networks` rozevíracího seznamu vyberte vaší sítě.
3. Z `Security` rozevíracího seznamu vyberte typ zabezpečení vaší sítě.
4. Zadejte přihlašovací jméno a heslo informace a pak klikněte na tlačítko `Configure Wi-Fi`.
5. Označte opište IP adresu, která se použije v pozdější.

> [!NOTE]
> Ujistěte se, že Edison je připojený ke stejné síti jako počítače. Váš počítač připojí k vaší Edison s použitím IP adresy.

   ![Připojte se k teplotní snímač](media/iot-hub-intel-edison-kit-c-get-started/12_configuration_tool.png)

Blahopřejeme! Úspěšně jste nakonfigurovali Edison.

## <a name="run-a-sample-application-on-intel-edison"></a>Spustit ukázkovou aplikaci na Intel Edison

### <a name="prepare-the-azure-iot-device-sdk"></a>Příprava zařízení Azure IoT SDK

1. Použijte jednu z následujících klientů SSH z hostitelského počítače pro připojení k Intel Edison. IP adresa je z konfigurační nástroj a heslo je ta, kterou jste nastavili v tomto nástroji.
    - [PuTTY](http://www.putty.org/) pro Windows.
    - Integrovaného klienta SSH v systému Ubuntu nebo macOS (Spustit `ssh root@"the IP address"`).

2. Naklonujte ukázkovou aplikaci klienta do vašeho zařízení. 
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-edison-client-app.git
   ```

3. Pak přejděte do složky úložiště ke spuštění následujícího příkazu sestavte sadu SDK Azure IoT

   ```bash
   cd iot-hub-c-intel-edison-client-app
   sed -i -e 's/\r$//' buildSDK.sh
   chmod 755 buildSDK.sh
   ./buildSDK.sh
   ```

### <a name="configure-the-sample-application"></a>Nakonfigurovat ukázkovou aplikaci

1. Otevřete konfigurační soubor spuštěním následujících příkazů:

   ```bash
   nano config.h
   ```

   ![Konfigurační soubor](media/iot-hub-intel-edison-kit-c-get-started/13_configure_file.png)

   Existují dvě makra v tomto souboru můžete configurate. První z nich je `INTERVAL`, která definuje časový interval mezi dvě zprávy, které odesílají do cloudu. Pro druhou kolekci `SIMULATED_DATA`, což je logická hodnota, pro, jestli se má použít data ze simulovaných senzorů nebo ne.

   Pokud jste **nemají senzor**, nastavte `SIMULATED_DATA` hodnota, která se `1` ukázkové aplikaci vytvořit a používat data ze simulovaných senzorů.

2. Uložte a zavřete stisknutím kombinace kláves ovládacího prvku-O > Enter > X ovládacího prvku.

### <a name="build-and-run-the-sample-application"></a>Sestavení a spuštění ukázkové aplikace

1. Vytvoření ukázkové aplikace spuštěním následujícího příkazu:

   ```bash
   cmake . && make
   ```
   ![Výstup sestavení](media/iot-hub-intel-edison-kit-c-get-started/14_build_output.png)

1. Spuštění ukázkové aplikace spuštěním následujícího příkazu:

   ```bash
   sudo ./app '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Ujistěte se, že je kopírování a vkládání připojovací řetězec zařízení v jednoduchých uvozovkách.

Měli byste vidět následující výstup, který zobrazuje data ze senzorů a zprávy, které se odesílají do služby IoT hub.

![Výstup – data ze senzorů odeslaný Intel Edison k centru IoT](media/iot-hub-intel-edison-kit-c-get-started/15_message_sent.png)

## <a name="next-steps"></a>Další postup

Jste spustili ukázkovou aplikaci shromažďovat data ze senzorů a odesílat je do služby IoT hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
