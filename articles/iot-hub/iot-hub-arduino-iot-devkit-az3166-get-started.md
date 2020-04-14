---
title: Připojení IoT DevKit AZ3166 k azure iot hubu
description: V tomto kurzu se dozvíte, jak nastavit a připojit IoT DevKit AZ3166 k Azure IoT Hub, aby mohl odesílat data na cloudovou platformu Azure.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.openlocfilehash: 631a20c7bf73aa2af363fdc0019ef24cccc58f9e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258588"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Připojení IoT DevKit AZ3166 k Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

[MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) můžete použít k vývoji a prototypu řešení Internetu věcí (IoT), která využívají služeb Microsoft Azure. Obsahuje desku kompatibilní s Arduino s bohatými periferiemi a senzory, balíček desky s otevřeným zdrojovým kódem a bohatou [galerii vzorků](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>Co se naučíte

* Jak vytvořit centrum IoT a zaregistrovat zařízení pro MXChip IoT DevKit.
* Jak připojit IoT DevKit k Wi-Fi a nakonfigurovat připojovací řetězec služby IoT Hub.
* Jak odeslat telemetrická data senzoru DevKit do centra IoT.
* Jak připravit vývojové prostředí a vyvinout aplikaci pro IoT DevKit.

Ještě nemáte DevKit? Zkuste [Simulátor DevKitu](https://azure-samples.github.io/iot-devkit-web-simulator/) nebo [si kupte DevKit](https://aka.ms/iot-devkit-purchase).

Můžete najít zdrojový kód pro všechny kurzy DevKit z [galerie ukázek kódu](https://docs.microsoft.com/samples/browse/?term=mxchip).

## <a name="what-you-need"></a>Co potřebujete

* Deska MXChip IoT DevKit s kabelem Micro-USB. [Získejte ji nyní](https://aka.ms/iot-devkit-purchase).
* Počítač se systémem Windows 10, macOS 10.10+ nebo Ubuntu 18.04+.
* Aktivní předplatné Azure. [Aktivujte bezplatnou 30denní zkušební verzi účtu Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>Příprava hardwaru

Připojte k počítači následující hardware:

* Deska DevKit
* Kabel Micro-USB

![Požadovaný hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Chcete-li aplikaci DevKit připojit k počítači, postupujte takto:

1. Připojte konec USB k počítači.

2. Připojte konec Micro-USB k DevKitu.

3. Zelená LED dioda pro napájení potvrzuje připojení.

   ![Hardwarová připojení](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Úvodní příručka: Odeslání telemetrie z DevKitu do služby IoT Hub

Rychlý start používá předkompilován DevKit firmware k odeslání telemetrie do služby IoT Hub. Před spuštěním vytvoříte centrum IoT a zaregistrujete zařízení s centrem.

### <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spusťte následující příkaz v Azure Cloud Shell a vytvořte identitu zařízení.

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který zvolíte pro svůj iot hub.

   **MyNodeDevice**: Název zařízení, které registrujete. Použijte **MyNodeDevice,** jak je znázorněno. Pokud pro své zařízení zvolíte jiný název, musíte tento název použít v celém tomto článku a aktualizovat název zařízení v ukázkových aplikacích před jejich spuštěním.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Pokud se zobrazí `device-identity`chyba spuštěna , nainstalujte [rozšíření Azure IoT extension pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md).
   > Spusťte následující příkaz a přidejte rozšíření Microsoft Azure IoT extension pro Azure CLI do instance Cloud Shellu. The IoT Extension adds commands that are specific to IoT Hub, IoT Edge, and IoT Device Provisioning Service (DPS) to Azure CLI.
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který zvolíte pro svůj iot hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

### <a name="send-devkit-telemetry"></a>Odeslat telemetrii DevKitu

DevKit se připojí ke koncovému bodu specifickému pro zařízení ve vašem centru IoT hub a odešle telemetrii teploty a vlhkosti.

1. Stáhněte si nejnovější verzi [firmwaru GetStarted](https://aka.ms/devkit/prod/getstarted/latest) pro IoT DevKit.

1. Ujistěte se, že se IoT DevKit připojuje k počítači přes USB. Otevřete Průzkumníka souborů je USB velkokapacitní paměťové zařízení s názvem **AZ3166**.

    ![Spuštění Průzkumníka Windows](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Přetáhněte firmware právě stáhli do velkokapacitního paměťového zařízení a bude blikat automaticky.

    ![Kopírovat firmware](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. Na devkitu podržte tlačítko **B**, stiskněte a uvolněte tlačítko **Reset** a poté uvolněte tlačítko **B**. DevKit přejde do režimu AP. Pro potvrzení se na obrazovce zobrazí identifikátor sady služeb (SSID) adresy IP devKitu a konfiguračního portálu.

    ![Tlačítko Reset, tlačítko B a SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Nastavení režimu AP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Pomocí webového prohlížeče na jiném zařízení s podporou Wi-Fi (počítač nebo mobilní telefon) se připojte k ssidi IoT DevKit zobrazené v předchozím kroku. Pokud požádá o heslo, nechte ho prázdné.

    ![Připojit SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Otevřete **192.168.0.1** v prohlížeči. Vyberte Wi-Fi, ke kterému se má IoT DevKit připojit, zadejte heslo k Wi-Fi a vložte připojovací řetězec zařízení, který jste si dříve poznamenali. Potom klikněte na Uložit.

    ![Konfigurační ui](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit podporuje pouze síť 2.4GHz. Další podrobnosti najdete v [častých dotazech.](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration)

1. Informace o WiFi a připojovací řetězec zařízení se uloží do IoT DevKit, když se zobrazí stránka s výsledky.

    ![Výsledek konfigurace](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Po nakonfigurování Wi-Fi budou vaše přihlašovací údaje v zařízení pro toto připojení zachovány, a to i v případě, že je zařízení odpojeno.

1. IoT DevKit se restartuje během několika sekund. Na obrazovce DevKit uvidíte IP adresu pro DevKit následuje telemetrická data, včetně teploty a vlhkosti hodnotu s počtem zpráv odeslat do Služby Azure IoT Hub.

    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Odesílání dat](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Chcete-li ověřit telemetrická data odeslaná do Azure, spusťte v Azure Cloud Shell následující příkaz:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

Podle následujících kroků připravte vývojové prostředí pro DevKit:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Instalace kódu Visual Studia s balíčkem rozšíření nástrojů Azure IoT

1. Nainstalujte [Arduino IDE](https://www.arduino.cc/en/Main/Software). Poskytuje potřebný toolchain pro kompilaci a nahrávání Arduino kódu.
    * **Windows**: Použijte verzi Instalační služby systému Windows. Neinstalujte z App Storu.
    * **macOS**: Přetáhněte extrahované **Arduino.app** do `/Applications` složky.
    * **Ubuntu**: Rozbalte jej do složky, jako je`$HOME/Downloads/arduino-1.8.8`

2. Nainstalujte [Visual Studio Code](https://code.visualstudio.com/), multiplatformní editor zdrojového kódu s výkonným intellisense, dokončení kódu a ladění podpory, stejně jako bohaté rozšíření lze nainstalovat z trhu.

3. Spusťte VS Code, podívejte se na **Arduino** na trhu rozšíření a nainstalujte jej. Toto rozšíření poskytuje rozšířené zkušenosti pro vývoj na platformě Arduino.

    ![Instalace Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Vyhledejte [nástroje Azure IoT na](https://aka.ms/azure-iot-tools) trhu rozšíření a nainstalujte je.

    ![Instalace nástrojů Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Nebo použijte tento přímý odkaz:
    > [!div class="nextstepaction"]
    > [Instalace rozšíření rozšíření nástrojů Azure IoT](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Rozšiřující balíček nástrojů Azure IoT obsahuje pracovní [plocha zařízení Azure IoT Workbench,](https://aka.ms/iot-workbench) která se používá k vývoji a ladění na různých zařízeních IoT devkit. [Rozšíření Azure IoT Hub](https://aka.ms/iot-toolkit), které je součástí rozšíření nástroje Azure IoT, se používá ke správě a interakci s Azure IoT Hubs.

5. Konfigurace kódu VS s nastavením Arduino.

    V kódu Visual Studia klikněte na **Předvolby > předvoleb > souborů (v** systému macOS, **Kód > Předvolby > nastavení).** Pak klikněte na ikonu **Otevřít nastavení (JSON)** v pravém horním rohu stránky *Nastavení.*

    ![Instalace nástrojů Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Přidejte následující řádky pro konfiguraci Arduino v závislosti na vaší platformě: 

    * **Windows**:

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

        Nahraďte níže uvedený zástupný symbol **{uživatelské jméno}** svým uživatelským jménem.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Klepnutím `F1` otevřete paletu příkazů, zadejte a vyberte **Arduino: Board Manager**. Vyhledejte **AZ3166** a nainstalujte nejnovější verzi.

    ![Instalace sady DevKit SDK](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Instalace ovladačů ST-Link

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) je rozhraní USB, které IoT DevKit používá ke komunikaci s vaším vývojovým počítačem. Musíte jej nainstalovat do systému Windows, abyste blikali kód zkompilovaného zařízení na DevKit. Postupujte podle pokynů pro konkrétní operační režim, abyste zařízení povolili přístup k vašemu zařízení.

* **Windows**: Stáhněte a nainstalujte ovladač USB z [webových stránek STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**: Pro macOS není potřeba žádný ovladač.
* **Ubuntu**: Spusťte příkazy v terminálu a odhlaste se a přihlaste se, aby se změna skupiny projevila:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Nyní jste připraveni s přípravou a konfigurací vývojového prostředí. Nechte nás vytvořit vzorek GetStarted, který jste právě spustili.

## <a name="build-your-first-project"></a>Sestavte si svůj první projekt

### <a name="open-sample-code-from-sample-gallery"></a>Otevřít ukázkový kód z ukázkové galerie

IoT DevKit obsahuje bohatou galerii ukázek, které můžete použít k učení připojit DevKit k různým službám Azure.

1. Ujistěte se, že váš IoT DevKit **není připojený** k počítači. Nejprve spusťte kód VS a potom připojte devkit k počítači.

1. Kliknutím `F1` otevřete paletu příkazů, zadejte a vyberte **Azure IoT Device Workbench: Open Examples...**. Pak vyberte **IoT DevKit** jako nástěnku.

1. Na stránce Příklady pracovní plochy IoT najděte **Začínáme** a klikněte na **Otevřít ukázku**. Potom vybere výchozí cestu ke stažení ukázkového kódu.

    ![Otevřít vzorek](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Zřízení azure iot hubu a zařízení

Místo zřizování Azure IoT Hub a zařízení z portálu Azure, můžete to udělat v kódu VS bez opuštění vývojového prostředí.

1. V novém otevřeném okně `F1` projektu kliknutím otevřete paletu příkazů, zadejte a vyberte **Pracovní plocha zařízení Azure IoT: Provision Azure Services...**. Postupujte podle pokynů krok za krokem a dokončete zřizování služby Azure IoT Hub a vytvoření zařízení IoT Hub.

    ![Příkaz Provision](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Pokud jste se v Azure nepřihlásili. Postupujte podle automaticky otevíraných oznámení pro přihlášení.

1. Vyberte předplatné, které chcete použít.

    ![Vybrat dílčí](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Potom vyberte nebo vytvořte novou [skupinu prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).

    ![Výběr skupiny prostředků](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. Ve skupině prostředků, kterou jste zadali, postupujte podle pokynů pro výběr nebo vytvoření nového centra Azure IoT Hub.

    ![Vybrat kroky centra IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Vybrat centrum IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Vybrané centrum IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. Ve výstupním okně uvidíte azure iot hub zřízené.

    ![Zřizování zřízených ioT hubů](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Vyberte nebo vytvořte nové zařízení v Azure IoT Hub, které jste zřídit.

    ![Vybrat kroky zařízení IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Vybrat zařízení IoT Zřízené](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Teď máte Azure IoT Hub zřízené a zařízení vytvořené v něm. Také připojovací řetězec zařízení budou uloženy v Kódu VS pro pozdější konfiguraci IoT DevKit.

    ![Poskytnutí provedeno](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Konfigurace a kompilace kódu zařízení

1. V pravém dolním stavovém řádku zkontrolujte, zda je **MXCHIP AZ3166** zobrazen jako vybraná deska a sériový port s **STMicroelectronics.**

    ![Vybrat vývěsku a COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Klepnutím `F1` otevřete paletu příkazů, zadejte a vyberte **pracovní plocha zařízení Azure IoT: Konfigurovat nastavení zařízení...**, pak vyberte **připojovací řetězec zařízení Konfigurace > vyberte připojovací řetězec zařízení IoT Hub**.

1. Na devKitu podržte **tlačítko A**, stiskněte a uvolněte tlačítko **reset** a poté uvolněte **tlačítko A**. DevKit přejde do konfiguračního režimu a uloží připojovací řetězec.

    ![Připojovací řetězec](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Klikněte `F1` znovu, zadejte a vyberte **Azure IoT Device Workbench: Upload Device Code**. Spustí kompilaci a nahrát kód devKit.

    ![Arduino nahrát](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit restartuje a spustí kód.

> [!NOTE]
> Pokud dojde k chybám nebo přerušením, můžete se vždy zotavit spuštěním příkazu znovu.

## <a name="test-the-project"></a>Testování projektu

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Zobrazení telemetrie odeslané do služby Azure IoT Hub

Kliknutím na ikonu elektrické zástrčky na stavovém řádku otevřete sériový monitor:

![Sériový monitor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

Ukázková aplikace je úspěšně spuštěna, když se zobrazí následující výsledky:

* Sériový monitor zobrazí zprávu odeslanou do služby IoT Hub.
* Led dioda na MXChip IoT DevKit bliká.

![Výstup sériového monitoru](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

> [!NOTE]
> Při testování, při kterém led dioda nebliká, se může vyskytne chyba, na kterém se na webu Azure portal nezobrazují příchozí data ze zařízení, ale obrazovka OLED zařízení se zobrazuje jako **Spuštěná...**. Chcete-li tento problém vyřešit, přejděte na zařízení v centru IoT a odešlete zprávu zařízení. Pokud se v sériovém monitoru v kódu VS zobrazí následující odpověď, je možné, že přímá komunikace ze zařízení je blokována na úrovni směrovače. Zkontrolujte pravidla brány firewall a směrovače, která jsou nakonfigurována pro připojovací zařízení. Zkontrolujte také, zda je otevřený výstupní port 1833.
> 
> Chyba: mqtt_client.c (ln 454): Chyba: při otevření připojení ke koncovému bodu došlo k chybě  
> INFO: >>>Stav připojení: odpojeno  
> Chyba: tlsio_mbedtls.c (ln 604): Podkladový IO open selhal  
> CHYBA: mqtt_client.c (LN 1042): Chyba: io_open selhalo  
> Chyba: iothubtransport_mqtt_common.c (ln 2283): selhání připojení k atcsliothub.azure-devices.net adres.  
> INFO: >>>Znovu připojit.  
> INFO: IoThub Verze: 1.3.6  

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Zobrazení telemetrie přijaté službou Azure IoT Hub

[Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) můžete použít ke sledování zpráv mezi zařízeními (D2C) v centru IoT Hub.

1. Přihlaste se na [Portál Azure](https://portal.azure.com/), najděte IoT Hub, který jste vytvořili.

    ![portál Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. V podokně **Zásady sdíleného přístupu** klikněte na **zásady iothubowner**a poznamenejte si řetězec připojení centra IoT Hub.

    ![Připojovací řetězec Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. V kódu VS `F1`klikněte na , zadejte a vyberte **Azure IoT Hub: Set IoT Hub Connection String**. Zkopírujte do něj připojovací řetězec.

    ![Nastavení připojovacího řetězce Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Rozbalte podokno **ZAŘÍZENÍ AZURE IOT HUB** vpravo, klikněte pravým tlačítkem myši na název zařízení, který jste vytvořili, a vyberte Spustit sledování **integrovaného koncového bodu událostí**.

    ![Monitor ovat zprávu D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. V podokně **VÝSTUP** uvidíte příchozí zprávy D2C do centra IoT Hub.

    ![Zpráva D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Kontrola kódu

Je `GetStarted.ino` to hlavní arduino skica souboru.

![Zpráva D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Chcete-li zjistit, jak se telemetrie zařízení odesílá do služby Azure IoT Hub, otevřete `utility.cpp` soubor ve stejné složce. Zobrazit [odkaz na rozhraní API,](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) kde se dozvíte, jak používat senzory a periferie na IoT DevKit.

Použité `DevKitMQTTClient` je obálka **iothub_client** z [sad Microsoft Azure IoT SDK a knihoven pro C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) pro interakci s Azure IoT Hub.

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, můžete vyhledat řešení v [Nejčastějších dotazech k IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo se k nám spojit z [Gitteru](https://gitter.im/Microsoft/azure-iot-developer-kit). Můžete nám také poskytnout zpětnou vazbu tím, že zanecháte komentář na této stránce.

## <a name="next-steps"></a>Další kroky

Úspěšně jste připojili MXChip IoT DevKit do svého centra IoT a odeslali jste data zachyceného senzoru do vašeho centra IoT hub.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
