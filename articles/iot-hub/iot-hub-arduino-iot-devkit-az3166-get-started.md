---
title: IoT DevKit do cloudu – IoT DevKit AZ3166 se připojit ke službě Azure IoT Hub | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak nastavit a připojte IoT DevKit AZ3166 se do služby Azure IoT Hub, kterou může odesílat data do cloudové platformy Azure.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.openlocfilehash: 4017a3be5e03e1a9b85b4002b8069a1adc3a6b83
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551573"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>IoT DevKit AZ3166 se připojit ke službě Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Můžete použít [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) k vývoji a řešení Internetu věcí (IoT) prototypu, které budou využívat služeb Microsoft Azure. Zahrnuje kompatibilní s Arduino panel s bohatou periferní zařízení a senzorů, balíček s open source panelu a bohaté [Galerie ukázek](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>Co se naučíte

* Postup vytvoření služby IoT hub a registrace zařízení pro MXChip IoT DevKit.
* Jak se připojit IoT DevKit připojení Wi-Fi a nakonfigurovat připojovací řetězec služby IoT Hub.
* Postup odesílání telemetrických dat ze senzorů DevKit ke službě IoT hub.
* Jak připravit vývojové prostředí a vývoj aplikací pro IoT DevKit.

DevKit ještě nemáte? Zkuste [DevKit simulátor](https://azure-samples.github.io/iot-devkit-web-simulator/) nebo [nákupu DevKit](https://aka.ms/iot-devkit-purchase).

Můžete najít zdrojový kód pro všechny kurzy DevKit na [IoTDevEnvExamples](https://github.com/IoTDevEnvExamples) úložiště.

## <a name="what-you-need"></a>Co potřebujete

* Panel MXChip IoT DevKit s Micro USB kabelu. [Získat](https://aka.ms/iot-devkit-purchase).
* Počítač se systémem Windows 10, macOS 10.10 + nebo Ubuntu 18.04 +.
* Aktivní předplatné Azure. [Aktivace bezplatné 30denní zkušební verze Microsoft Azure účtu](https://azureinfo.microsoft.com/us-freetrial.html).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>Připravte svůj hardware

Připojení následující hardware v počítači:

* Panel DevKit
* Micro USB kabel

![Požadovaný hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

DevKit se připojte k počítači, postupujte podle těchto kroků:

1. Připojte USB end k počítači.

2. Připojení k DevKit end Micro USB.

3. Zelená LED výkon potvrdí připojení.

   ![Připojení hardwaru](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Rychlý start: Odesílání telemetrických dat z DevKit do služby IoT Hub

Rychlý start používá předem kompilovaných firmware DevKit odesílání telemetrických dat do služby IoT Hub. Předtím, než ji spustíte, vytvoření služby IoT hub a registrace zařízení v centru.

### <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spusťte následující příkaz v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste vybrali pro službu IoT hub.

   **MyNodeDevice**: Název zařízení, které se registrace. Použití **MyNodeDevice** jak je znázorněno. Pokud zvolíte jiný název pro vaše zařízení, musíte použít tento název v rámci tohoto článku a aktualizujte název zařízení v ukázkové aplikace před spuštěním je.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```
   > [!NOTE]
   > Pokud dojde k chybě spuštění `device-identity`, nainstalujte [rozšíření Azure IOT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md) další podrobnosti.
  
1. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

   **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste vybrali pro službu IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

### <a name="send-devkit-telemetry"></a>Odesílání telemetrických dat DevKit

DevKit se připojí k koncový bod specifický pro zařízení ve službě IoT hub a odesílá telemetrická data teploty a vlhkosti.

1. Stáhněte si nejnovější verzi [GetStarted firmware](https://aka.ms/devkit/prod/getstarted/latest) pro IoT DevKit.

1. Ujistěte se, že IoT DevKit připojte k počítači přes USB. Otevřete Průzkumníka souborů je velkokapacitní paměťové zařízení USB, volá **AZ3166**.
    ![Otevřít Windows Explorer](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Operace přetažení firmware si právě stáhli do velkokapacitní paměťové zařízení a bude automaticky flash.
    ![Zkopírujte firmwaru](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. Na DevKit, podržte tlačítko **B**, push a verzi **resetování** tlačítka a potom verzi **B**. Vaše DevKit přejde do režimu přístupový bod. Pokud chcete potvrdit, na obrazovce se zobrazí identifikátor service set identifier (SSID) DevKit a konfigurace portálu IP adresy.
    ![Resetovat tlačítko, tlačítko B a SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Nastavení režimu Asie a Tichomoří](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Použít webový prohlížeč v různých Wi-Fi povolené nastavení zařízení (počítač nebo mobilní telefon) pro připojení k IoT DevKit SSID zobrazí v předchozím kroku. Pokud ho vyzve k zadání hesla, nevyplňujte.
    ![Připojit SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Otevřít **192.168.0.1** v prohlížeči. Vyberte Wi-Fi, který chcete, aby IoT DevKit připojit, zadejte heslo Wi-Fi a pak vložte připojovací řetězec zařízení, které jste provedli, mějte na paměti z dříve. Potom klikněte na Uložit.
    ![Konfigurace uživatelského rozhraní](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit podporuje pouze sítě 2,4 GHz. Zkontrolujte [nejčastější dotazy k](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) další podrobnosti.

1. Wi-Fi zařízení a informace o připojovacím řetězci se uloží do IoT DevKit po zobrazení stránky výsledků.
    ![Konfigurace výsledků](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Po dokončení konfigurace Wi-Fi svoje přihlašovací údaje se uloží na zařízení pro toto připojení i v případě, zařízení je odpojen.

1. IoT DevKit restartuje za pár sekund. Na obrazovce DevKit zobrazí IP adresa DevKit následuje telemetrická data, včetně teploty a vlhkosti hodnotu s počet zpráv odeslat do služby Azure IoT Hub.
    ![Wi-Fi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Odesílání dat](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Pokud chcete ověřit telemetrická data poslaná do Azure, spusťte následující příkaz v Azure Cloud Shell:
    ```bash
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

Použijte následující postup DevKit Příprava vývojového prostředí:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Instalace Visual Studio Code pomocí balíčku rozšíření nástroje Azure IoT

1. Nainstalujte [rozhraním Arduino IDE](https://www.arduino.cc/en/Main/Software). Poskytuje nezbytné sady nástrojů pro kompilaci a nahrání kódu Arduino.
    * **Windows:** Používejte verzi Instalační služby systému Windows. Neinstalujte z App Store.
    * **macOS**: Přetáhnout myší extrahované **Arduino.app** do `/Applications` složky.
    * **Ubuntu**: Například rozbalte do složky `$HOME/Downloads/arduino-1.8.8`

2. Nainstalujte [Visual Studio Code](https://code.visualstudio.com/), editor zdrojového kódu napříč platformami s výkonné funkci intellisense, dokončování kódu a ladění, podpora, jakož i bohatá rozšíření lze nainstalovat z webu marketplace.

3. Spusťte VS Code, vyhledejte **Arduino** v marketplace pro rozšíření a nainstalujte ho. Toto rozšíření nabízí vylepšené prostředí pro vývoj na platformě Arduino.
    ![Nainstalujte Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Vyhledejte [nástroje Azure IoT](https://aka.ms/azure-iot-tools) v marketplace pro rozšíření a nainstalujte ho.
    ![Instalace nástrojů Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Nebo použijte tento odkaz s přímým přístupem:
    > [!div class="nextstepaction"]
    > [Nainstalovat balíček rozšíření pro nástroje Azure IoT](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Balíček rozšíření Azure IoT nástroje obsahuje [Workbench zařízení Azure IoT](https://aka.ms/iot-workbench) sloužící k vývoji a ladění na různých zařízeních IoT devkit. [Azure IoT Hub Toolkit](https://aka.ms/iot-toolkit), také součástí balíček rozšíření pro nástroje Azure IoT, se používá ke správě a interakci s Azure IoT Hubs.

5. Konfigurace VS Code s rozhraním Arduino nastavení.

    Ve Visual Studio Code, klikněte na tlačítko **soubor > Předvolby > Nastavení**. Klikněte **...**  a **otevřete settings.json**.
    ![Instalace nástrojů Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)
    
    Přidejte následující řádky do konfigurace Arduino v závislosti na vaší platformě: 

    * **Windows:**
      
        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:
    
        Nahradit **{username}** zástupný symbol níže se svým uživatelským jménem.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Klikněte na tlačítko `F1` otevřete paletu příkazů, zadejte a vyberte **Arduino: Panel Správce**. Vyhledejte **AZ3166** a nainstalujte nejnovější verzi.
    ![Nainstalujte sadu SDK DevKit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Instalace ovladačů ST – odkaz

[Sv-odkaz/V2](https://www.st.com/en/development-tools/st-link-v2.html) je rozhraní USB používající IoT DevKit ke komunikaci s počítači pro vývoj. Budete muset nainstalovat na Windows tak, aby zařízení zkompilovaný kód, který DevKit. Postupujte podle kroků specifické pro operační systém umožňující počítači přístup k zařízení.

* **Windows:** Stáhnout a nainstalovat ovladač USB z [STMicroelectronics webu](https://www.st.com/en/development-tools/stsw-link009.html) nebo [přímý odkaz](https://aka.ms/stlink-v2-windows).
* **macOS**: Žádný ovladač je vyžadován pro macOS.
* **Ubuntu**: Spuštění příkazů v terminálu a odhlásit se a přihlásit projeví se změna skupiny:
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Nyní vše je nastaveno s Příprava a konfigurace vývojového prostředí. Dejte nám sestavte ukázku GetStarted, které jste právě spustili.

## <a name="build-your-first-project"></a>Svůj první projekt sestavit

### <a name="open-sample-code-from-sample-gallery"></a>Otevřete ukázkový kód z Galerie ukázek

IoT DevKit obsahuje rozsáhlé Galerii ukázek, které můžete použít další připojení DevKit k různým službám Azure.

1. Ujistěte se, že je vaše IoT DevKit **Nepřipojeno** k vašemu počítači. Nejprve spusťte VS Code a DevKit připojte se k počítači.

1. Klikněte na tlačítko `F1` otevřete paletu příkazů, zadejte a vyberte **Workbench zařízení Azure IoT: Otevřít příklady...** . Potom vyberte **IoT DevKit** jako panel.

1. Na stránce IoT aplikace Workbench příklady najít **Začínáme** a klikněte na tlačítko **otevřít ukázkové**. Potom vybere výchozí cestu pro stažení ukázkového kódu.
    ![Otevřete ukázkový](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Zřízení Azure IoT Hub a zařízení

Namísto zřizování Azure IoT Hub a zařízení z portálu Azure portal vám pomůžou ho v nástroji VS Code aniž byste museli opustit vývojové prostředí.

1. V novém okně otevřeném projektu, klikněte na tlačítko `F1` otevřete paletu příkazů, zadejte a vyberte **Workbench zařízení Azure IoT: Zřízení služby Azure...** . Postupujte podle podrobného průvodce na dokončení zřízení služby Azure IoT Hub a vytvoření zařízení IoT Hub.
    ![Příkaz zřizování](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Pokud nejste v Azure. Použijte místní oznámení pro přihlášení.

1. Vyberte předplatné, které chcete použít.
    ![Vyberte sub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Vyberte nebo vytvořte novou [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).
    ![Vyberte skupinu prostředků.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. Ve skupině prostředků, které jste zadali postupujte podle průvodce a vyberte nebo vytvořte novou službu Azure IoT Hub.
    ![Vyberte službu IoT Hub kroky](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Vyberte službu IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Vybranému centru IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. V okně výstupu uvidíte Azure IoT Hub, zřídit ![zřízené centra IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Vyberte nebo vytvořte nové zařízení ve službě Azure IoT Hub jste zřídili.
    ![Vyberte zařízení IoT kroky](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Vyberte možnost pro zřízení zařízení IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Nyní máte zřízené služby Azure IoT Hub a zařízení se vytvořilo v ní. Také připojovací řetězec zařízení se uloží v nástroji VS Code pro konfiguraci IoT DevKit později.
    ![Zřízení Hotovo](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Konfigurace a kompilaci kódu zařízení

1. V pravém dolním stavovém, zkontrolujte **MXCHIP AZ3166** se zobrazuje jako vybraný panelu a sériového portu pomocí **STMicroelectronics** se používá.
    ![Vyberte panelu a modelu COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Klikněte na tlačítko `F1` otevřete paletu příkazů, zadejte a vyberte **Workbench zařízení Azure IoT: Konfigurace nastavení zařízení...** a pak vyberte **připojovací řetězec zařízení Config > Vyberte připojovací řetězec zařízení IoT Hub**.

1. U DevKit, podržte klávesu **tlačítko A**, push a verzi **resetování** tlačítko a pak uvolněte **tlačítko A**. Vaše DevKit přejde do režimu konfigurace a uloží připojovací řetězec.
    ![Připojovací řetězec](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Klikněte na tlačítko `F1` znovu, zadejte a vyberte **Workbench zařízení Azure IoT: Nahrát kód zařízení**. Spustí se kompilace a nahrajte kód do DevKit.
    ![Nahrávání Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit restartuje a spustí kód.

> [!NOTE]
> Pokud je nějaké chyby nebo přerušení, můžete vždy obnovit opětovným spuštěním příkazu.

## <a name="test-the-project"></a>Testování projektu

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Zobrazit telemetrická data odesílaná do služby Azure IoT Hub

Klikněte na ikonu power moduly na stavovém řádku otevřete sériového portu monitorování: ![Monitorování sériového portu](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

Ukázková aplikace pracuje správně, když se zobrazí následující výsledky:

* Monitorování sériového portu zobrazuje zpráva odeslaná do služby IoT Hub.
* Je bliká vám kontrolka LED na MXChip IoT DevKit.

![Výstup sériové monitorování](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Zobrazení telemetrických dat přijatých službou Azure IoT Hub

Můžete použít [nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pro monitorování zpráv typu zařízení cloud (D2C) ve službě IoT Hub.

1. Přihlaste se [webu Azure portal](https://portal.azure.com/), najít IoT Hub, který jste vytvořili.
    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. V **zásady sdíleného přístupu** podokně klikněte na tlačítko **iothubowner zásady**a poznamenejte si připojovací řetězec služby IoT hub.
    ![Azure IoT Hub připojovacího řetězce](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. V nástroji VS Code, klikněte na tlačítko `F1`zadejte a vyberte **Azure IoT Hub: Nastavit připojovací řetězec služby IoT Hub**. Zkopírujte připojovací řetězec do něj.
    ![Nastavit připojovací řetězec centra IoT Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Rozbalte **zařízení AZURE IOT HUB** podokno na pravé straně, klikněte pravým tlačítkem myši klikněte na název zařízení, které jste vytvořili a vyberte **spustit monitorování integrovaných událostí koncový bod**.
    ![Monitorování zprávy D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. V **výstup** podokně, zobrazí se příchozí zprávy D2C do služby IoT Hub.
    ![Zpráva D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Kontrola kódu

`GetStarted.ino` Je hlavní soubor sketch Arduino.

![Zpráva D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Chcete-li zjistit, jak se odesílají telemetrii zařízení do služby Azure IoT Hub, otevřete `utility.cpp` soubor ve stejné složce. Zobrazení [Reference k rozhraní API](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) se naučíte používat senzory a periferní zařízení v IoT DevKit.

`DevKitMQTTClient` Použitá je obálku **iothub_client** z [sadami SDK služby Microsoft Azure IoT a knihovny pro jazyk C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) k interakci s Azure IoT Hub.

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na potíže, můžete zkontrolovat v řešení [nejčastější dotazy týkající se IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo kontaktujte nás z [Gitteru](https://gitter.im/Microsoft/azure-iot-developer-kit). Můžete také udělit nám zpětnou vazbu přenechte komentář na této stránce.

## <a name="next-steps"></a>Další postup

Právě jste úspěšně propojili MXChip IoT DevKit ke službě IoT hub a data ze snímačů zachycené jste odeslali do služby IoT hub.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
