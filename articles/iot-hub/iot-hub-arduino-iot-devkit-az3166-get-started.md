---
title: Připojení IoT DevKit AZ3166 k Azure IoT Hub
description: V tomto kurzu se naučíte, jak nastavit a propojit službu IoT DevKit AZ3166 s Azure IoT Hub tak, aby mohla odesílat data na cloudovou platformu Azure.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.custom:
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: d5147d6d7f1bd1fcd43835f6a66196c7642ad4ff
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151938"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Připojení IoT DevKit AZ3166 k Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

[MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) můžete použít k vývoji a prototypování řešení Internet věcí (IoT), která využívají služby Microsoft Azure. Obsahuje desku kompatibilní s Arduino s bohatými periferními a senzory, otevřenými zdrojovými balíčky a s bohatou [galerii ukázek](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>Co se naučíte

* Jak vytvořit centrum IoT a zaregistrovat zařízení pro MXChip IoT DevKit.
* Jak připojit DevKit IoT pro Wi-Fi a nakonfigurovat připojovací řetězec IoT Hub
* Jak odesílat data telemetrie senzorů DevKit do služby IoT Hub.
* Příprava vývojového prostředí a vývoj aplikací pro IoT DevKit.

Ještě nemáte DevKit? Vyzkoušejte [simulátor DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) nebo si [Kupte DevKit](https://aka.ms/iot-devkit-purchase).

Zdrojový kód pro všechny kurzy DevKit můžete najít z [Galerie ukázek kódu](/samples/browse/?term=mxchip).

## <a name="what-you-need"></a>Co budete potřebovat

* MXChip IoT DevKit Board s kabelem Micro USB. [Získejte ho hned teď](https://aka.ms/iot-devkit-purchase).
* Počítač se systémem Windows 10, macOS 10.10 + nebo Ubuntu 18.04 +.
* Musíte mít aktivní předplatné Azure. [Aktivujte si bezplatný 30denní zkušební účet Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>Příprava hardwaru

Zapojte na počítač následující hardware:

* DevKit panel
* Kabel Micro USB

![Požadovaný hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Chcete-li připojit DevKit k počítači, postupujte podle následujících kroků:

1. Připojte koncovku USB k počítači.

2. Připojte koncovku mikro USB k sadě DevKit.

3. Zelený indikátor LED pro napájení potvrdí připojení.

   ![Hardwarová připojení](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Rychlý Start: odeslání telemetrie z DevKit do IoT Hub

Rychlý Start používá předem kompilovaný firmware DevKit k odeslání telemetrie do IoT Hub. Než ho spustíte, vytvoříte centrum IoT a zaregistrujete zařízení do centra.

### <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Registrace zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spuštěním následujícího příkazu v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který zvolíte pro Centrum IoT.

   **MyNodeDevice**: název zařízení, které registrujete. Použijte **MyNodeDevice** , jak je znázorněno na obrázku. Pokud pro své zařízení zvolíte jiný název, budete ho muset použít v celém rámci tohoto článku a před jeho spuštěním aktualizovat název zařízení v ukázkových aplikacích.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Pokud se zobrazí chyba při spuštění `device-identity` , nainstalujte [rozšíření Azure IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md).
   > Spuštěním následujícího příkazu přidejte do instance služby Cloud Shell Microsoft Azure rozšíření IoT pro rozhraní příkazového řádku Azure. Rozšíření IoT přidá příkazy, které jsou specifické pro IoT Hub, IoT Edge a službu IoT Device Provisioning (DPS) do Azure CLI.
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který zvolíte pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

### <a name="send-devkit-telemetry"></a>Poslat telemetrii DevKit

DevKit se připojí ke koncovému bodu specifickému pro zařízení ve službě IoT Hub a pošle telemetrii teploty a vlhkosti.

1. Stáhněte si nejnovější verzi [getstarted firmwaru](https://aka.ms/devkit/prod/getstarted/latest) pro IoT DevKit.

1. Ujistěte se, že IoT DevKit se k vašemu počítači připojuje přes USB. Otevřete Průzkumníka souborů. je k dispozici velkokapacitní paměťové zařízení USB s názvem **AZ3166**.

    ![Otevřít Průzkumníka Windows](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Přetáhněte firmware, který jste právě stáhli do velkokapacitního paměťového zařízení, a automaticky se zabliká.

    ![Kopírovat firmware](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. V DevKit podržte stisknuté tlačítko **b**, nahrajte a uvolněte tlačítko **obnovit** a pak uvolněte tlačítko **b**. Váš DevKit vstoupí do režimu AP. Na obrazovce se zobrazí obrazovka s identifikátorem SSID (Service Set Identifier) pro DevKit a IP adresou konfiguračního portálu.

    ![Resetovat tlačítko, tlačítko B a SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Nastavit režim AP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Pomocí webového prohlížeče na jiném zařízení s podporou Wi-Fi (počítač nebo mobilní telefon) se připojte k identifikátoru SSID sady IoT DevKit, který jste zobrazili v předchozím kroku. Pokud se zobrazí výzva k zadání hesla, ponechte prázdné.

    ![Připojit SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. V prohlížeči otevřete **192.168.0.1** . Vyberte Wi-Fi, ke kterému chcete IoT DevKit připojit, zadejte heslo Wi-Fi a pak vložte připojovací řetězec zařízení, který jste si poznamenali dříve. Potom klikněte na Uložit.

    ![Uživatelské rozhraní konfigurace](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit podporuje jenom síť 2,4 GHz. Další podrobnosti najdete v [nejčastějších dotazech](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) .

1. V případě, že se zobrazí stránka s výsledky, uloží se informace o připojení Wi-Fi a připojovací řetězec zařízení do IoT DevKit.

    ![Výsledek konfigurace](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Po nakonfigurování Wi-Fi zůstanou vaše přihlašovací údaje v zařízení pro toto připojení, a to i v případě, že je zařízení odpojené.

1. IoT DevKit se restartuje během několik sekund. Na obrazovce DevKit se pod daty telemetrie zobrazí IP adresa pro DevKit, včetně teploty a hodnoty vlhkosti s hodnotou odeslat do Azure IoT Hub.

    ![IP adresa Wi-Fi](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Odesílání dat](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Pokud chcete ověřit data telemetrie odesílaná do Azure, spusťte v Azure Cloud Shell následující příkaz:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

Pomocí těchto kroků Připravte vývojové prostředí pro DevKit:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Instalace Visual Studio Code s balíčkem rozšíření nástrojů Azure IoT

1. Nainstalujte [integrované vývojové prostředí (Arduino](https://www.arduino.cc/en/Main/Software)). Poskytuje potřebný sada nástrojů pro kompilaci a nahrávání Arduino kódu.
    * **Windows**: použijte instalační služba systému Windows verzi. Neinstalujte z App Storu.
    * **MacOS**: přetáhněte extrahovanou **Arduino. app** do `/Applications` složky.
    * **Ubuntu**: rozbalte ho do složky, jako je například `$HOME/Downloads/arduino-1.8.8`

2. Nainstalujte [Visual Studio Code](https://code.visualstudio.com/), Editor zdrojového kódu pro různé platformy s výkonným IntelliSense, doplňováním kódu a podporou ladění a také bohatá rozšíření, která můžete nainstalovat z webu Marketplace.

3. Spusťte VS Code, hledejte **Arduino** na webu Extension Marketplace a nainstalujte ho. Toto rozšíření poskytuje vylepšené prostředí pro vývoj na platformě Arduino.

    ![Nainstalovat Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Podívejte se na [Azure IoT Tools](https://aka.ms/azure-iot-tools) na webu rozšíření Marketplace a nainstalujte ho.

    ![Snímek obrazovky, který zobrazuje nástroje Azure IoT na webu rozšíření Marketplace.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Nebo zkopírujte a vložte tuto adresu URL do okna prohlížeče: `vscode:extension/vsciot-vscode.azure-iot-tools`

    > [!NOTE]
    > Balíček rozšíření Azure IoT Tools obsahuje [Workbench Azure IoT](https://aka.ms/iot-workbench) , která se používá k vývoji a ladění různých zařízení IoT DevKit. [Rozšíření Azure IoT Hub](https://aka.ms/iot-toolkit), které je součástí sady rozšíření Azure IoT Tools, se používá ke správě a interakci s centry Azure IoT Hub.

5. Nakonfigurujte VS Code s nastavením Arduino.

    V Visual Studio Code klikněte na možnost **soubor > předvolby > nastavení** (na MacOS, **předvolby > kódu > nastavení**). Pak klikněte na ikonu **Otevřít nastavení (JSON)** v pravém horním rohu stránky *Nastavení* .

    ![Instalace nástrojů Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    V závislosti na platformě přidejte následující řádky pro konfiguraci platformy Arduino: 

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

        Nahraďte zástupný symbol **{username}** níže vaším uživatelským jménem.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Kliknutím `F1` otevřete paletu příkazů, zadáte a vyberete **Arduino: správce panelu**. Vyhledejte **AZ3166** a nainstalujte nejnovější verzi.

    ![Nainstalovat sadu DevKit SDK](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Nainstalovat ST-Link ovladače

[St-Link/v2](https://www.st.com/en/development-tools/st-link-v2.html) je rozhraní USB, které IoT DevKit používá ke komunikaci s vývojovým počítačem. Je potřeba ho nainstalovat ve Windows, aby se kompilovaný kód zařízení vyslal do sady DevKit. Postupujte podle kroků pro konkrétní operační systém a umožněte počítači přístup k vašemu zařízení.

* **Windows**: Stáhněte a nainstalujte si ovladač USB z [webu STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html).
* **MacOS**: pro MacOS se nevyžaduje žádný ovladač.
* **Ubuntu**: Spusťte příkazy na terminálu, odhlaste se a přihlaste se, aby se změna skupiny projevila:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Teď jste všechno nastavili pomocí přípravy a konfigurace vývojového prostředí. Můžeme nám vytvořit ukázku getstarted, kterou jste právě spustili.

## <a name="build-your-first-project"></a>Sestavení prvního projektu

### <a name="open-sample-code-from-sample-gallery"></a>Otevřít vzorový kód z Galerie ukázek

IoT DevKit obsahuje bohatou galerii ukázek, pomocí kterých se naučíte připojit DevKit k různým službám Azure.

1. Ujistěte se, že vaše aplikace IoT DevKit není **připojená** k vašemu počítači. Nejprve začněte VS Code a pak připojte DevKit k počítači.

1. Kliknutím `F1` otevřete paletu příkazů, zadáte a vyberete **Azure IoT Device Workbench: otevřít příklady...**. Pak jako panel vyberte **IoT DevKit** .

1. Na stránce příklady IoT Workbench Najděte **Začínáme** a klikněte na **otevřít ukázku**. Pak vybere výchozí cestu pro stažení ukázkového kódu.

    ![Otevřít ukázku](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Zřízení IoT Hub a zařízení v Azure

Místo zřízení IoT Hub a zařízení Azure z Azure Portal to můžete udělat v VS Code, aniž byste opustili vývojové prostředí.

1. V okně Nový otevřený projekt kliknutím `F1` otevřete paletu příkazů, zadejte a vyberte **Azure IoT Device Workbench: zřízení služeb Azure...**. Postupujte podle podrobných pokynů k dokončení zřizování Azure IoT Hub a vytvoření IoT Hubho zařízení.

    ![Zřídit příkaz](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Pokud jste se nezaregistrovali v Azure. Při přihlašování použijte místní oznámení.

1. Vyberte předplatné, které chcete použít.

    ![Vybrat sub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Pak vyberte nebo vytvořte novou [skupinu prostředků](../azure-resource-manager/management/overview.md#terminology).

    ![Výběr skupiny prostředků](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. Ve skupině prostředků, kterou jste zadali, postupujte podle pokynů průvodce a vyberte nebo vytvořte novou IoT Hub Azure.

    ![Vybrat IoT Hub kroky](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Vyberte IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Vybraná IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. V okně výstup se zobrazí IoT Hub zřízené Azure.

    ![IoT Hub zřízené](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Vyberte nebo vytvořte nové zařízení ve službě Azure IoT Hub jste zřídili.

    ![Výběr kroků zařízení IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Vyberte zařízení IoT zřízené.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Teď máte zřízený Azure IoT Hub a v něm vytvořené zařízení. Připojovací řetězec zařízení se taky uloží v VS Code pro pozdější konfiguraci IoT DevKit.

    ![Zřizování dokončeno](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Konfigurace a kompilování kódu zařízení

1. V pravém dolním stavovém řádku se podívejte na **MXCHIP AZ3166** se zobrazuje jako vybraná deska a sériový port s **STMicroelectronics** se používá.

    ![Výběr panelu a modelu COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Kliknutím `F1` otevřete paletu příkazů, potom zadejte a vyberte **Azure IoT Device Workbench: konfigurovat nastavení zařízení...**, pak vyberte **Config Device connection string > vyberte IoT Hub připojovací řetězec zařízení**.

1. V DevKit stiskněte **tlačítko**a, nahrajte a uvolněte tlačítko **obnovit** a pak uvolněte **tlačítko**a. Vaše DevKit vstoupí do režimu konfigurace a uloží připojovací řetězec.

    ![Připojovací řetězec](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Klikněte `F1` znovu na znovu, zadejte a vyberte **Azure IoT Device Workbench: nahrání kódu zařízení**. Spustí zkompilování a nahrání kódu do DevKit.

    ![Nahrávání Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit se restartuje a začne používat kód.

> [!NOTE]
> Pokud dojde k chybám nebo přerušením, můžete je kdykoli obnovit opětovným spuštěním příkazu.

## <a name="test-the-project"></a>Testování projektu

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Zobrazit telemetrii odeslanou do Azure IoT Hub

Kliknutím na ikonu plug-in na stavovém řádku otevřete Monitor sériového portu:

![Sledování sériového portu](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

Ukázková aplikace se úspěšně spustí, když se zobrazí následující výsledky:

* Monitor sériového portu zobrazí zprávu odeslanou do IoT Hub.
* Indikátor LED v MXChip IoT DevKit je blikající.

![Výstup sledování sériového portu](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

> [!NOTE]
> Při testování, ve kterém se indikátor LED nebliká, může dojít k chybě, Azure Portal nezobrazuje příchozí data ze zařízení, ale obrazovka OLED zařízení se zobrazuje jako **spuštěná...**. Problém vyřešíte tak, že v Azure Portal přejdete do zařízení ve službě IoT Hub a odešlete zprávu do zařízení. Pokud se v programu sériového monitorování v VS Code zobrazí následující odpověď, je možné, že se na úrovni směrovače zablokuje přímá komunikace ze zařízení. Ověřte pravidla brány firewall a směrovačů, která jsou nakonfigurovaná pro připojení zařízení. Také se ujistěte, že je otevřený odchozí port 1833.
> 
> Chyba: mqtt_client. c (ln 454): Chyba: Chyba při otevírání připojení ke koncovému bodu  
> INFORMACE:  >>>stav připojení: odpojeno  
> Chyba: tlsio_mbedtls. c (ln 604): neúspěšná podkladová otevření v/v  
> Chyba: mqtt_client. c (ln 1042): Chyba: io_open se nezdařilo.  
> Chyba: iothubtransport_mqtt_common. c (ln 2283): Chyba při připojování k adrese atcsliothub.azure-devices.net.  
> INFORMACE:  >>>znovu připojit.  
> INFORMACE: verze IoThub: 1.3.6  

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Zobrazení telemetrie přijatého službou Azure IoT Hub

Pomocí [nástrojů Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) můžete monitorovat zprávy ze zařízení do cloudu (D2C) v IoT Hub.

1. Přihlaste se [Azure Portal](https://portal.azure.com/), najděte IoT Hub, který jste vytvořili.

    ![portál Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. V podokně **zásady sdíleného přístupu** klikněte na **zásadu iothubowner**a zapište připojovací řetězec služby IoT Hub.

    ![Připojovací řetězec služby Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. V VS Code klikněte na `F1` , zadejte a vyberte **Azure IoT Hub: nastavte IoT Hub připojovací řetězec**. Zkopírujte do něj připojovací řetězec.

    ![Nastavit připojovací řetězec služby Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. V levém podokně rozbalte podokno **zařízení Azure IoT Hub** , klikněte pravým tlačítkem na název zařízení, které jste vytvořili, a vyberte **Spustit sledování integrovaný koncový bod události**.

    ![Monitorovat zprávu D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. V podokně **výstup** můžete zobrazit příchozí zprávy D2C na IoT Hub.

    ![Snímek obrazovky zobrazující příchozí zprávy D2C IoT Hub.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Kontrola kódu

`GetStarted.ino`Je hlavní soubor náčrtu Arduino.

![Zpráva D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Pokud chcete zjistit, jak se telemetrie zařízení odešlou do IoT Hub Azure, otevřete `utility.cpp` soubor ve stejné složce. Podívejte se na [odkaz rozhraní API](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) , kde se dozvíte, jak používat senzory a periferní zařízení v IoT DevKit.

`DevKitMQTTClient`Použitý je obálka **iothub_client** ze [Microsoft Azure sad SDK a knihoven IoT pro C pro](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) interakci s Azure IoT Hub.

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, můžete vyhledat řešení v části [Nejčastější dotazy ke službě IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo se na nás obrátit z [gitteru](https://gitter.im/Microsoft/azure-iot-developer-kit). Můžete nám také sdělit svůj názor tím, že na této stránce odejdete komentář.

## <a name="next-steps"></a>Další kroky

Úspěšně jste se připojili ke službě IoT Hub MXChip IoT DevKit a do služby IoT Hub jste odeslali data zaznamenaného senzoru.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]