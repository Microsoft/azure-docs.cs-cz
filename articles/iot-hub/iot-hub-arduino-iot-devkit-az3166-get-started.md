---
title: IoT DevKit do cloudu – IoT DevKit AZ3166 se připojit ke službě Azure IoT Hub | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak nastavit a připojte IoT DevKit AZ3166 se do služby Azure IoT Hub, kterou může odesílat data do cloudové platformy Azure.
author: rangv
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 34088440bf723fc65f4ff5c49f62182f405fa35a
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344541"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>IoT DevKit AZ3166 se připojit ke službě Azure IoT Hub v cloudu

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Můžete použít [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) k vývoji a řešení Internetu věcí (IoT) prototypu, které budou využívat služeb Microsoft Azure. Zahrnuje kompatibilní s Arduino panel s bohatou periferní zařízení a senzorů, balíček panel open source a stále se rozšiřující [katalogu projektů](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>Co můžete dělat
Připojení [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) do služby Azure IoT hub, kterou vytvoříte, shromažďovat teploty a vlhkosti data ze senzorů a odesílání dat do služby IoT hub.

DevKit ještě nemáte? Zkuste [DevKit simulátor](https://azure-samples.github.io/iot-devkit-web-simulator/) nebo [získat](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Co se naučíte

* Jak se připojit IoT DevKit k dispozici bezdrátový přístupový bod a příprava vývojového prostředí.
* Postup vytvoření služby IoT hub a registrace zařízení pro MXChip IoT DevKit.
* Jak shromažďovat data ze senzorů pomocí Průvodce ukázkovou aplikaci na MXChip IoT DevKit.
* Jak odeslat data ze senzorů do služby IoT hub.

## <a name="what-you-need"></a>Co potřebujete

* Deska MXChip IoT DevKit pomocí kabelu Micro USB. [Získat](https://aka.ms/iot-devkit-purchase).
* Počítač se systémem Windows 10 nebo macOS 10.10 +.
* Aktivní předplatné Azure. [Aktivace bezplatné 30denní zkušební verze Microsoft Azure účtu](https://azureinfo.microsoft.com/us-freetrial.html).
  

## <a name="prepare-your-hardware"></a>Připravte svůj hardware

Připojení hardwaru k vašemu počítači.

Je nutné tento hardware:

* Panel DevKit
* Micro USB kabel

![Požadovaný hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

DevKit připojení k počítači:

1. Připojte USB end k počítači.
2. Připojení k DevKit end Micro USB.
3. Zelená LED výkon potvrdí připojení.

![Připojení hardwaru](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Konfigurace Wi-Fi

Spolehněte se na připojení k Internetu projekty IoT. Použijte následující pokyny ke konfiguraci DevKit pro připojení k Wi-Fi.

### <a name="enter-ap-mode"></a>Zadejte režim Asie a Tichomoří

Podržte tlačítko B, nabízených oznámení a uvolněte tlačítko resetovat a pak uvolněte tlačítko B. Vaše DevKit přejde do režimu přístupový bod ke konfiguraci Wi-Fi. Na obrazovce se zobrazí identifikátor service set identifier (SSID) DevKit a konfigurace portálu IP adresy.

![Resetovat tlačítko, tlačítko B a SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Připojte se k DevKit Asie a Tichomoří

Nyní použijte jiné povolené Wi-Fi zařízení (počítač nebo mobilní telefon) pro připojení k SSID DevKit (zvýrazněný na předchozím obrázku). Ponechte prázdné heslo.

![Informace o síti a tlačítko pro připojení](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Konfigurace Wi-Fi DevKit

Otevřete IP adresou uvedenou na obrazovce DevKit do svého počítače nebo mobilního telefonu prohlížeče, vyberte, který chcete DevKit pro připojení k síti Wi-Fi a pak zadejte heslo. Vyberte **Connect** (Připojit).

![Pole pro heslo a tlačítko pro připojení](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Když je připojení úspěšné, DevKit restartuje za pár sekund. Zobrazí název sítě Wi-Fi a IP adresu na obrazovce:

![Název sítě Wi-Fi a IP adresy](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> Vlastní IP adresu, přiřadit a zobrazí na obrazovce DevKit nemusí odpovídat zobrazená v fotografie IP adresa. To je normální, protože Wi-Fi používající službu DHCP k dynamickému přidělení IP adresy.

Po dokončení konfigurace Wi-Fi svoje přihlašovací údaje se uloží na zařízení pro toto připojení i v případě, zařízení je odpojen. Pokud nakonfigurujete DevKit pro Wi-Fi v domácnosti a pak proveďte DevKit kanceláři, je potřeba překonfigurovat režimu Asie a Tichomoří (cena začíná na krok v části "Zadejte přístupový bod režim") pro připojení DevKit pro pobočku Wi-Fi. 

## <a name="start-using-the-devkit"></a>Začněte používat DevKit

Výchozí aplikaci spuštěnou v prostředí DevKit zkontroluje nejnovější verzi firmwaru a zobrazí data ze senzorů diagnostiku za vás.

### <a name="upgrade-to-the-latest-firmware"></a>Upgrade na nejnovější firmware

> [!NOTE] 
> Od verze 1.1 DevKit umožňuje BEZPEČNÉ ST v zaváděcího programu pro spouštění. Budete muset upgradovat firmware, pokud jsou spuštěny v v1.1, aby byla pravděpodobně fungovat.

Pokud potřebujete upgrade firmwaru, se na obrazovce zobrazil firmware aktuální a nejnovější verze. Chcete-li provést upgrade, postupujte [Upgrade firmwaru](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) průvodce.

![Zobrazení aktuálních a nejnovější firmware verzí](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> Toto je jednorázová úsilí. Poté, co začít s vývojem pro DevKit a nahrát aplikaci, budou přicházet nejnovější firmware s vaší aplikací.

### <a name="test-various-sensors"></a>Testování různých senzorů

Kliknutím na tlačítko B testování senzory. Pokračujte stisknutím a uvolněním tlačítka B k cyklování skrze každý senzoru.

![Zobrazit tlačítko B a snímače](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

Teď je čas nastavit vývojové prostředí: nástroje a balíčky pro vám umožní vytvářet působivé aplikace IoT. Můžete podle operačního systému Windows nebo macOS verze.

### <a name="windows"></a>Windows

Doporučujeme vám použít instalační balíček do Příprava vývojového prostředí. Pokud zaznamenáte jakékoli problémy, můžete postupovat podle [vyžadováno provedení ručních kroků](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) její dokončení.

#### <a name="download-the-latest-package"></a>Stáhněte si nejnovější balíček

Který stáhnete soubor ZIP obsahuje všechny potřebné nástroje a balíčky pro vývoj DevKit.

> [!div class="button"]
[Stáhnout](https://aka.ms/devkit/prod/installpackage/latest)

Soubor ZIP obsahuje následující nástroje a balíčky. Pokud už máte některé součásti nainstalované, skript zjišťování a je přeskočit.

* Node.js a Yarn: modul Runtime pro instalační skript a automatizovaných úloh.
* [Azure CLI 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): – multiplatformního prostředí příkazového řádku pro správu prostředků Azure. Soubor MSI obsahuje závislé Python a pip.
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): zjednodušený editor kódu pro vývoj DevKit.
* [Rozšíření sady Visual Studio Code pro Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): rozšíření, které umožňuje vývoj Arduino ve Visual Studio Code.
* [Rozhraním Arduino IDE](https://www.arduino.cc/en/Main/Software): nástroj, který využívá rozšíření pro Arduino.
* Panel DevKit balíček: Nástroj pro DevKit řetězců, knihovny a projekty.
* Nástroj ST odkaz: Základní nástroje a ovladače.

#### <a name="run-the-installation-script"></a>Spusťte instalační skript

V Průzkumníku souborů Windows vyhledejte soubor ZIP a rozbalte ho. Najít `install.cmd`, pravým tlačítkem myši a vyberte **spustit jako správce**.

![Průzkumník souborů](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Během instalace se zobrazí průběh každého nástroj nebo balíčku.

![Průběh instalace](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

> [!NOTE] 
> V závislosti na vašem prostředí v některých případech bude selhání při instalaci zobrazí rozhraním Arduino IDE. V takovém případě můžete zkusit [rozhraním Arduino IDE instalovat jednotlivě](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) a znovu spusťte soubor install.cmd. V opačném případě postupujte [vyžadováno provedení ručních kroků](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) nainstalujte všechny potřebné nástroje a balíčky.

#### <a name="install-drivers"></a>Instalace ovladačů

VS Code pro rozšíření Arduino spoléhá na rozhraním Arduino IDE. Pokud je to poprvé nainstalujete rozhraním Arduino IDE, budete vyzváni k instalaci příslušných ovladačů:

![získávání – zahájeno – ovladače](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

Instalace zabere přibližně 10 minut v závislosti na rychlosti Internetu. Po dokončení instalace byste měli vidět Visual Studio Code a rozhraním Arduino IDE zástupce na ploše.

> [!NOTE] 
> V některých případech při spuštění VS Code, se zobrazí výzva k s chybou, že nelze najít rozhraním Arduino IDE nebo související Rady balíčku. K jeho řešení, zavřete VS Code a restartujte rozhraním Arduino IDE. VS Code by měl pak vyhledejte cestu rozhraním Arduino IDE správně.

### <a name="macos"></a>macOS

Doporučujeme vám, jak pomocí prostředí instalace jedním kliknutím Příprava vývojového prostředí. Pokud zaznamenáte jakékoli problémy, můžete postupovat podle [vyžadováno provedení ručních kroků](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) její dokončení.

#### <a name="install-homebrew"></a>Nainstalujte Homebrew

> [!NOTE] 
> Pokud jste nainstalovali Homebrew, můžete tento krok přeskočit.

Postupujte podle [pokyny k instalaci Homebrew](https://docs.brew.sh/Installation.html) k její instalaci.

#### <a name="download-the-latest-package"></a>Stáhněte si nejnovější balíček
Který stáhnete soubor ZIP obsahuje všechny potřebné nástroje a balíčky pro vývoj DevKit.

> [!div class="button"]
[Stáhnout](https://aka.ms/devkit/prod/installpackage/mac/latest)

Soubor ZIP obsahuje následující nástroje a balíčky. Pokud už máte některé součásti nainstalované, skript zjišťování a je přeskočit.

* Node.js a Yarn: modul Runtime pro instalační skript a automatizovaných úloh.
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest#a-namemacosinstall-on-macos): – multiplatformního prostředí příkazového řádku pro správu prostředků Azure.
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): zjednodušený editor kódu pro vývoj DevKit.
* [Rozšíření sady Visual Studio Code pro Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): rozšíření, které umožňuje vývoj Arduino ve Visual Studio Code.
* [Rozhraním Arduino IDE](https://www.arduino.cc/en/Main/Software): nástroj, který využívá rozšíření pro Arduino.
* Panel DevKit balíček: Nástroj pro DevKit řetězců, knihovny a projekty.
* Nástroj ST odkaz: Základní nástroje a ovladače.

#### <a name="run-the-installation-script"></a>Spusťte instalační skript

Ve Finderu vyhledejte ZIP a rozbalte ho:

![macOS finder](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-finder.png)

Spusťte aplikaci terminál, vyhledejte ve složce extrahujte soubor .zip a spusťte:

```bash
./install.sh
```

![pro instalaci macOS](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-install-sh.png)

> [!NOTE] 
> Pokud narazíte na chyby oprávnění Homebrew, spusťte `brew doctor` aby problém odstranil. Zkontrolujte [nejčastější dotazy k](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#homebrew-permission-error-on-macos) další podrobnosti.

Nyní máte všechny potřebné nástroje a balíčky nainstalované pro macOS.


## <a name="open-the-project-folder"></a>Otevřít složku projektu

### <a name="start-vs-code"></a>Spusťte VS Code

Zajistěte, aby že vaše DevKit není připojený. Nejprve spusťte VS Code a připojení DevKit k vašemu počítači. VS Code automaticky zjistí, DevKit a otevře se úvodní stránka:

![Úvodní stránka](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

> [!NOTE] 
> V některých případech při spuštění VS Code, se zobrazí výzva k s chybou, že nelze najít rozhraním Arduino IDE nebo související Rady balíčku. VS Code zavřete a znovu spusťte rozhraním Arduino IDE. VS Code by měl pak vyhledejte cestu rozhraním Arduino IDE správně.


### <a name="open-the-arduino-examples-folder"></a>Otevřete složku příklady Arduino

Na **Arduino příklady** kartu, přejděte do **příklady MXCHIP AZ3166** > **AzureIoT**a vyberte **GetStarted**.

![Karta příklady Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

Pokud máte náhodou zavřete podokno, můžete ho znovu otevřít. Použití `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) otevřete paletu příkazů, zadejte **Arduino**a poté vyhledejte a vyberte **Arduino: Příklady**.

## <a name="provision-azure-services"></a>Zřízení služby Azure

V okně řešení spustit úlohu `Ctrl+P` (macOS: `Cmd+P`) tak, že zadáte `task cloud-provision`.

V terminálu VS Code interaktivního příkazového řádku vás provede zřizování požadovaných služeb Azure:

![Interaktivního příkazového řádku](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Vytváření a odesílání Arduino sketch

### <a name="windows"></a>Windows

1. Použití `Ctrl+P` spuštění `task device-upload`.
2. Terminálu zobrazí výzvu k zadání režim konfigurace. Uděláte to tak, podržte tlačítko A pak push a uvolněte tlačítko Obnovení nastavení. Na obrazovce se zobrazí DevKit id a "Configuration".

Toto je nastavit připojovací řetězec, který načte z `task cloud-provision` kroku.

VS Code pak začne ověřování a odesílání Arduino sketch:

![Ověřování a odesílání Arduino sketch](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

DevKit restartuje a spustí kód.

> [!NOTE] 
> V některých případech se zobrazí chyba "Chyba: AZ3166: Neznámý balíček". Je to z důvodu panelu nedojde index balíčku. Zaškrtnutím tohoto políčka [nejčastější dotazy týkající se kroků](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) k jeho řešení.

### <a name="macos"></a>macOS

1. Přepnout do režimu konfigurace, DevKit: podržte tlačítko A pak nabízených oznámení a uvolněte tlačítko Obnovení nastavení. Na obrazovce se zobrazí "Configuration".
2. Použití `Cmd+P` spuštění `task device-upload`.

Toto je nastavit připojovací řetězec, který načte z `task cloud-provision` kroku.

VS Code pak začne ověřování a odesílání Arduino sketch:

![nahrávání zařízení](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

DevKit restartuje a spustí kód.

> [!NOTE] 
> V některých případech se zobrazí chyba "Chyba: AZ3166: Neznámý balíček". Je to z důvodu panelu nedojde index balíčku. Zaškrtnutím tohoto políčka [nejčastější dotazy týkající se kroků](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) k jeho řešení.


## <a name="test-the-project"></a>Testování projektu

V nástroji VS Code použijte následující postup otevřete a nastavte monitorování sériového portu:

1. Klikněte na tlačítko `COM[X]` word na stavovém řádku na nastavte správný port COM s `STMicroelectronics`: ![com port](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/com-port.png)

2. Klepněte na ikonu power moduly na stavovém řádku otevřete sériového portu monitorování: ![sériové monitorování](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution//connect-iothub/serial-monitor.png)

3. Na stavovém řádku, klikněte na číslo představující přenosovou rychlostí a nastavte `115200`: ![přenosová rychlost](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/baud-rate.png)

Ukázková aplikace pracuje správně, když se zobrazí následující výsledky:

* Monitorování sériového portu zobrazuje stejné informace jako obsah na snímku obrazovky níže.
* RGB LED na MXChip IoT DevKit se spustí.

![Finální výstup v nástroji VS Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na potíže, můžete vyhledat [nejčastějších dotazech týkajících se](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/). Můžete také udělit nám zpětnou vazbu přenechte komentář na této stránce.

## <a name="next-steps"></a>Další postup

Právě jste úspěšně propojili MXChip IoT DevKit ke službě IoT hub a data ze snímačů zachycené jste odeslali do služby IoT hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
