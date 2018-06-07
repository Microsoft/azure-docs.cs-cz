---
title: Intel Edison do cloudu (C) - Edison Intel připojit ke službě Azure IoT Hub | Microsoft Docs
description: Zjistěte, jak nastavit a Intel Edison připojit ke službě Azure IoT Hub pro Intel Edison k odesílání dat do Azure Cloudová platforma v tomto kurzu.
author: rangv
manager: ''
keywords: Azure iot intel edison, intel edison iot hub, intel edison odesílat data do cloudu, intel edison do cloudu
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 2efea8054320323df0e0eb603a20a5773d03cad8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634875"
---
# <a name="connect-intel-edison-to-azure-iot-hub-c"></a>Připojení k Azure IoT Hub (C) Intel Edison

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

V tomto kurzu zahájíte učení základní informace o práci s Intel Edison. Pak zjistíte, jak bezproblémově připojení zařízení do cloudu pomocí [Azure IoT Hub](iot-hub-what-is-iot-hub.md).

Nemáte sady ještě? Spustit [sem](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>Co dělat

* Instalační program Intel Edison a a Groove moduly.
* Vytvoření služby IoT hub.
* Registrovat zařízení pro Edison ve službě IoT hub.
* Spuštění ukázkové aplikace na Edison k odesílání dat snímačů do služby IoT hub.

Intel Edison připojte ke službě IoT hub, který vytvoříte. Pak spusťte ukázkovou aplikaci na Edison ke shromažďování dat teploty a vlhkosti z teplotní snímač Groove. Nakonec odeslat data snímačů do služby IoT hub.

## <a name="what-you-learn"></a>Co se naučíte

* Postup vytvoření služby Azure IoT hub a získat nový připojovací řetězec zařízení.
* Postup připojení Edison s teplotní snímač Groove.
* Postup shromažďování dat snímačů spuštěním ukázkovou aplikaci na Edison.
* Jak odesílat data snímačů do služby IoT hub.

## <a name="what-you-need"></a>Co potřebujete

![Co potřebujete](media/iot-hub-intel-edison-kit-c-get-started/0_kit.png)

* Panel Intel Edison
* Panel Arduino rozšíření
* Aktivní předplatné Azure. Pokud nemáte účet Azure [vytvořit Bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) za několik minut.
* Mac nebo počítači se systémem Windows nebo Linux.
* Připojení k Internetu.
* Micro B kabelem USB typ A
* Zdroj napájení přímo aktuální (DC). Zdroj napájení by měl být hodnocení následujícím způsobem:
  - ŘADIČ DOMÉNY 7 15V
  - Alespoň 1500mA
  - PIN kód center nebo vnitřní by měla být kladná pólu napájení

Následující položky jsou volitelné:

* Groove základní štítu V2
* Groove - teplotní snímač
* Kabel Groove
* Jako mezeru mezi řádky nebo šrouby, zahrnout v balení, včetně dvou šrouby připevnit modulu Tabule rozšíření a čtyři sady šrouby a plastové vymezovače.

> [!NOTE] 
Tyto položky jsou volitelné, protože data snímačů simulated podporu ukázkový kód.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-intel-edison"></a>Instalační program Intel Edison

### <a name="assemble-your-board"></a>Sestavte panel

Tato část obsahuje kroky pro připojení k vaší karty rozšíření modulu Intel® Edison.

1. Místní modul Intel® Edison v rámci bílé osnovy na vaší kartě rozšíření zarovnání díry na modul s šrouby na kartě rozšíření.

2. Klepnout na modul pod slova `What will you make?` dokud si myslíte, že během.

   ![Sestavte Tabule 2](media/iot-hub-intel-edison-kit-c-get-started/1_assemble_board2.jpg)

3. Dva šestnáctkových matice (součástí balíčku) použijte k zabezpečení modulu Tabule rozšíření.

   ![Sestavte Tabule 3](media/iot-hub-intel-edison-kit-c-get-started/2_assemble_board3.jpg)

4. Vložte šroubu v jednom z díry čtyři rohu na kartě rozšíření. Otočením a posílit mezi bílé plastové nosníků do šroubek.

   ![Sestavte Tabule 4](media/iot-hub-intel-edison-kit-c-get-started/3_assemble_board4.jpg)

5. Pro další tři rohu nosníků opakujte.

   ![Sestavte Tabule 5](media/iot-hub-intel-edison-kit-c-get-started/4_assemble_board5.jpg)

Nyní je několik panel.

   ![sestavený panelu](media/iot-hub-intel-edison-kit-c-get-started/5_assembled_board.jpg)

### <a name="connect-the-grove-base-shield-and-the-temperature-sensor"></a>Připojit štítu základní Groove a teplotní snímač.

1. Místní štítu základní Groove k vaší karty. Ujistěte se, že všechny kódy PIN jsou úzce připojovány panel.
   
   ![Základní štítu Groove](media/iot-hub-intel-edison-kit-c-get-started/6_grove_base_sheild.jpg)

2. Použít pro připojení Groove teplotní snímač do štítu základní Groove Groove kabel **A0** portu.

   ![Připojení k teplotní snímač](media/iot-hub-intel-edison-kit-c-get-started/7_temperature_sensor.jpg)
   
   ![Edison a senzor připojení](media/iot-hub-intel-edison-kit-c-get-started/16_edion_sensor.png)

Vaše senzor je nyní připraven.

### <a name="power-up-edison"></a>Napájení až Edison

1. Zařaďte napájení.

   ![Zařadit napájení](media/iot-hub-intel-edison-kit-c-get-started/8_plug_power.jpg)

2. Zelená DIODU (s názvem bez přípony DS1 na panelu rozšíření Arduino *), by měla zůstat po a light.

3. Počkejte, než jedna minuta pro panel na dokončení spuštění.

   > [!NOTE]
   > Pokud nemáte ke zdroji napájení řadiče domény, může stále spotřeby Tabule přes USB port. V tématu `Connect Edison to your computer` podrobnosti. Pohánějící panel tímto způsobem může způsobit nepředvídatelné chování z vaší karty, zejména v případě, že pomocí sítě Wi-Fi nebo řídí motory.

### <a name="connect-edison-to-your-computer"></a>Připojte k počítači Edison

1. Přepněte dolů mikrospínače směrem dva malých USB porty, takže Edison je v režimu zařízení. Rozdíly mezi zařízení režim a režim hostitele, prosím odkazovat [zde](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode).

   ![Přepněte dolů mikrospínače](media/iot-hub-intel-edison-kit-c-get-started/9_toggle_down_microswitch.jpg)

2. Připojte kabel USB malých nejvyšší malých portu USB.

   ![Horní malých portu USB](media/iot-hub-intel-edison-kit-c-get-started/10_top_usbport.jpg)

3. Druhém konci kabelu USB připojte k počítači.

   ![Počítač USB](media/iot-hub-intel-edison-kit-c-get-started/11_computer_usb.jpg)

4. Budete vědět, že panel úplné inicializace když se počítač připojí na nový disk (podobně jako vkládání SD karty do počítače).

## <a name="download-and-run-the-configuration-tool"></a>Stáhněte a spusťte nástroj Konfigurace
Získat nejnovější nástroje Konfigurace z [tento odkaz](https://software.intel.com/en-us/iot/hardware/edison/downloads) uvedené v části `Installers` záhlaví. Spusťte nástroj a postupujte podle jeho na obrazovce pokyny, kde je potřeba klepnutím na tlačítko Další

### <a name="flash-firmware"></a>Flash firmwaru
1. Na `Set up options` klikněte na tlačítko `Flash Firmware`.
2. Vyberte bitovou kopii na flash na panel jedním z následujících akcí:
   - Chcete-li stáhnout a flash panel s nejnovější bitové kopie firmwaru, které jsou k dispozici z Intel, vyberte `Download the latest image version xxxx`.
   - Chcete-li flash panel s bitovou kopií již uložení v počítači, vyberte `Select the local image`. Vyhledejte a vyberte bitovou kopii, kterou chcete flash pro panel.
3. Nástroj instalační program se pokusí o flash panel. Celý proces blikající může trvat až 10 minut.

### <a name="set-password"></a>Nastavit heslo
1. Na `Set up options` klikněte na tlačítko `Enable Security`.
2. Můžete nastavit vlastní název pro panel Intel® Edison. Tato položka je nepovinná.
3. Zadejte heslo pro panel a pak klikněte na `Set password`.
4. Známku výpadku heslo, které se později používá.

### <a name="connect-wi-fi"></a>Připojení Wi-Fi
1. Na `Set up options` klikněte na tlačítko `Connect Wi-Fi`. Počkejte, až na jednu minutu jako počítač hledá dostupných sítí Wi-Fi.
2. Z `Detected Networks` rozevíracího seznamu vyberte vaší sítě.
3. Z `Security` rozevíracího seznamu vyberte typ zabezpečení sítě.
4. Poskytnout vaše údaje přihlašovací jméno a heslo a pak klikněte na `Configure Wi-Fi`.
5. Známku výpadku IP adresy, která se později používá.

> [!NOTE]
> Ujistěte se, že Edison je připojený ke stejné síti jako počítače. Váš počítač připojí k vaší Edison pomocí IP adresy.

   ![Připojení k teplotní snímač](media/iot-hub-intel-edison-kit-c-get-started/12_configuration_tool.png)

Blahopřejeme! Úspěšně jste nakonfigurovali Edison.

## <a name="run-a-sample-application-on-intel-edison"></a>Spuštění ukázkové aplikace na Intel Edison

### <a name="prepare-the-azure-iot-device-sdk"></a>Připravte zařízení Azure IoT SDK

1. Použijte jednu z následujících klientů SSH z hostitelského počítače pro připojení k vaší Edison Intel. IP adresa je z nástroje pro konfiguraci a heslo je ten, který jste nastavili v tohoto nástroje.
    - [PuTTY](http://www.putty.org/) pro systém Windows.
    - Integrovaného klienta SSH na Ubuntu nebo systému macOS (Spustit `ssh root@"the IP address"`).

2. Klonování vzorku klientskou aplikaci do vašeho zařízení. 
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-edison-client-app.git
   ```

3. Pak přejděte do složky úložiště, spusťte následující příkaz k sestavení sady SDK Azure IoT

   ```bash
   cd iot-hub-c-intel-edison-client-app
   sed -i -e 's/\r$//' buildSDK.sh
   chmod 755 buildSDK.sh
   ./buildSDK.sh
   ```

### <a name="configure-the-sample-application"></a>Nakonfigurujte ukázkovou aplikaci

1. Otevřete konfigurační soubor spuštěním následujících příkazů:

   ```bash
   nano config.h
   ```

   ![Konfigurační soubor](media/iot-hub-intel-edison-kit-c-get-started/13_configure_file.png)

   Existují dvě makra v tomto souboru můžete configurate. První z nich je `INTERVAL`, která definuje časový interval mezi dvě zprávy, které odesílají do cloudu. Druhý `SIMULATED_DATA`, což je logickou hodnotu pro jestli se má používat data simulované snímačů, nebo ne.

   Pokud jste **nemají senzoru**, nastavte `SIMULATED_DATA` hodnotu `1` aby ukázkovou aplikaci, vytváření a používání dat snímačů simulované.

2. Uložte a zavřete stisknutím řízení-O > zadejte > CTRL-X.

### <a name="build-and-run-the-sample-application"></a>Sestavení a spuštění ukázkové aplikace

1. Vytvoření ukázkové aplikace tak, že spustíte následující příkaz:

   ```bash
   cmake . && make
   ```
   ![Sestavení výstupu](media/iot-hub-intel-edison-kit-c-get-started/14_build_output.png)

1. Spuštění ukázkové aplikace tak, že spustíte následující příkaz:

   ```bash
   sudo ./app '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Zkontrolujte, zda jste způsobené kopírováním a vkládáním zařízení připojovací řetězec do jednoduchých uvozovek.

Měli byste vidět následující výstup, který popisuje data snímačů a zprávy, které se odesílají do služby IoT hub.

![Výstup – data snímačů odeslaný Intel Edison do služby IoT hub](media/iot-hub-intel-edison-kit-c-get-started/15_message_sent.png)

## <a name="next-steps"></a>Další postup

Spustíte ukázkovou aplikaci pro shromažďování dat snímačů a odeslat do služby IoT hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
