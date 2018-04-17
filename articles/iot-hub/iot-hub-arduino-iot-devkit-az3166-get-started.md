---
title: 'IoT DevKit do cloudu: připojit IoT DevKit AZ3166 do služby Azure IoT Hub | Microsoft Docs'
description: V tomto kurzu zjistěte, jak nastavit a IoT DevKit AZ3166 připojit ke službě Azure IoT Hub, kterou může odesílat data na platformu Azure cloud.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 3d83875171c7c097bac5a70852f049a6ce4219b3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>IoT DevKit AZ3166 se připojit ke službě Azure IoT Hub v cloudu

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Můžete použít [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) k vývoji a prototypu řešení Internetu věcí (IoT), které využít výhod služby Microsoft Azure. Obsahuje panelu Arduino kompatibilní s bohatou periferních zařízení a senzorů, balíček Tabule open source a rozšiřujících se [projekty katalogu](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>Co dělat
Připojení [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) do služby Azure IoT hub, který vytvoříte, shromažďování dat teploty a vlhkosti ze senzorů a odesílání dat do služby IoT hub.

Nemáte DevKit ještě? Zkuste [DevKit simulátoru](https://azure-samples.github.io/iot-devkit-web-simulator/) nebo [získat](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Co se naučíte

* Postup připojení k bezdrátovému přístupovému bodu IoT DevKit a příprava vývojového prostředí.
* Postup vytvoření služby IoT hub a registrovat zařízení pro MXChip IoT DevKit.
* Postup shromažďování dat snímačů spuštěním ukázkovou aplikaci na MXChip IoT DevKit.
* Jak odesílat data snímačů do služby IoT hub.

## <a name="what-you-need"></a>Co potřebujete

* Panelu MXChip IoT DevKit pomocí kabelu Micro USB. [Získejte nyní](https://aka.ms/iot-devkit-purchase).
* Počítač se systémem Windows 10 nebo systému macOS 10.10 +.
* Aktivní předplatné Azure. [Aktivace bezplatné 30denní zkušební verze Microsoft Azure účtu](https://azureinfo.microsoft.com/us-freetrial.html).
  

## <a name="prepare-your-hardware"></a>Připravte svůj hardware

Propojte hardwaru do vašeho počítače.

Je třeba tento hardware:

* DevKit panelu
* Kabel Micro USB

![Požadovaný hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Připojení DevKit do počítače:

1. Element end USB připojte k počítači.
2. Připojte k DevKit end Micro USB.
3. Zelená DIODU pro power potvrdí připojení.

![Připojení hardwaru](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Konfigurace sítě Wi-Fi

Projekty IoT spoléhají na připojení k Internetu. Použijte následující pokyny ke konfiguraci DevKit pro připojení k Wi-Fi.

### <a name="enter-ap-mode"></a>Zadejte režim Asie a Tichomoří

Podržte tlačítko B, nabízené verze na tlačítko Obnovit a uvolněním tlačítka B. Vaše DevKit přejde do režimu přístupový bod pro konfiguraci sítě Wi-Fi. Na obrazovce zobrazuje identifikátor service set identifier (SSID) DevKit a konfigurace portálu IP adresy.

![Resetování tlačítko, tlačítko B a SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Připojení k DevKit Asie a Tichomoří

Nyní použijte jiné zařízení povolit Wi-Fi (počítač nebo mobilní telefon) pro připojení k DevKit SSID (zvýrazněných v předchozí obrázek). Ponechte prázdné heslo.

![Informace o síti a tlačítko Připojit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Konfigurace sítě Wi-Fi pro DevKit

Otevřete adresu IP uvedené na obrazovce DevKit v počítači nebo v prohlížeči mobilního telefonu, vyberte, které chcete DevKit pro připojení k síti Wi-Fi a pak zadejte heslo. Vyberte **Connect** (Připojit).

![Pole pro heslo a tlačítko Připojit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Když je připojení úspěšné, DevKit restartuje za několik sekund. Zobrazí se název sítě Wi-Fi a IP adresu na obrazovce:

![Název sítě Wi-Fi a IP adresu](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> IP adresa, zobrazí v fotografie nemusí odpovídat skutečné IP adresu přiřadit a zobrazují na obrazovce DevKit. To je normální, protože sítě Wi-Fi používá protokol DHCP k dynamickému přidělení IP adresy.

Po dokončení konfigurace sítě Wi-Fi, přihlašovací údaje se uchová v zařízení pro toto připojení, i v případě, že zařízení je odpojen. Například pokud nakonfigurujete DevKit pro Wi-Fi v domácnosti a pak proveďte DevKit kanceláři, musíte znovu nakonfigurovat Asie režimu (od kroku v části "Zadejte Asie režim") pro připojení DevKit pobočku Wi-Fi. 

## <a name="start-using-the-devkit"></a>Začít používat DevKit

Výchozí aplikaci spuštěnou na DevKit zkontroluje nejnovější verzi firmwaru a zobrazí některé senzor diagnostická data za vás.

### <a name="upgrade-to-the-latest-firmware"></a>Upgrade na nejnovější firmware

> [!NOTE] 
> Od verze 1.1 umožňuje DevKit ST BEZPEČNÝCH v zaváděcí program pro spouštění. Je třeba upgradovat firmware, pokud běží pod v1.1, aby bylo možné pravděpodobně fungovat.

Pokud potřebujete upgrade firmwaru, se zobrazí na obrazovce firmware aktuální a nejnovější verze. Chcete-li provést upgrade, postupujte [upgradovat firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) průvodce.

![Zobrazení aktuální a nejnovější firmware verze](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> Toto je jednorázové úsilí. Jakmile začít vyvíjet na DevKit a nahrát aplikaci, vrátí se nejnovější firmware s vaší aplikací.

### <a name="test-various-sensors"></a>Testování různých senzorů

Stiskněte tlačítko B můžete otestovat senzorů. Pokračujte stisknutím a uvolněním tlačítka B cyklicky každý senzor.

![Tlačítko B a senzor zobrazení](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

Nyní je čas k nastavení vývojového prostředí: nástroje a balíčky pro sestavit poutavých aplikace či aplikace IoT. Můžete podle operačního systému Windows nebo systému macOS verze.

### <a name="windows"></a>Windows

Doporučujeme použít instalační balíček Příprava vývojového prostředí. Pokud narazíte na potíže, můžete podle [ruční kroky](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) ho provést.

#### <a name="download-the-latest-package"></a>Stáhněte si nejnovější balíček

Souboru .zip, který si stáhnout obsahuje všechny nezbytné nástroje a balíčky pro vývoj DevKit.

> [!div class="button"]
[Stáhnout](https://aka.ms/devkit/prod/installpackage/latest)

Soubor ZIP obsahuje následující nástroje a balíčků. Pokud již máte některé součásti nainstalované, skript rozpozná a jejich přeskočit.

* Node.js a Yarn: modul Runtime pro instalační skript a automatizované úlohy.
* [Azure CLI 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): prostředí příkazového řádku a platformy pro správu prostředků Azure. Soubor MSI obsahuje závislé Python a pip.
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): editor lehký kód pro vývoj DevKit.
* [Rozšíření sady Visual Studio Code pro Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): rozšíření, která umožňuje Arduino vývoj v aplikaci Visual Studio Code.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): nástroj, který využívá rozšíření pro Arduino.
* Balíček DevKit Tabule: Nástroj řetězy, knihovny a projekty pro DevKit.
* Nástroj ST odkaz: Základní nástroje a ovladače.

#### <a name="run-the-installation-script"></a>Spusťte instalační skript

V Průzkumníku souborů Windows vyhledejte soubor .zip a rozbalte ho. Najít `install.cmd`, pravým tlačítkem a vyberte **spustit jako správce**.

![Průzkumníka souborů](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Během instalace můžete sledovat průběh každé nástroj nebo balíčku.

![Průběh instalace](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

> [!NOTE] 
> V závislosti na vašem prostředí někdy zobrazí se chyba při instalaci Arduino IDE. V takovém případě můžete zkusit akci [Arduino IDE instalovat jednotlivě](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) a znovu spusťte soubor install.cmd. Jinak, postupujte [ruční kroky](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) nainstalovat všechny nezbytné nástroje a balíčky.

#### <a name="install-drivers"></a>Instalace ovladačů

Kód VS pro rozšíření Arduino spoléhá na Arduino IDE. Pokud je prvním instalujete Arduino IDE, se zobrazí výzva k instalaci příslušné ovladače:

![získávání spuštění – ovladače](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

Instalace by měl trvat přibližně 10 minut v závislosti na rychlosti sítě internet. Po dokončení instalace byste měli vidět Visual Studio Code a Arduino IDE zástupce na ploše.

> [!NOTE] 
> Občas stát když spustíte VS Code, se zobrazí výzva s chybou nebyla nalezena Arduino IDE nebo balíček Příbuzná panelu. Abyste vyřešili ho, zavřete VS Code a restartujte Arduino IDE. VS kód by pak vyhledejte cestu Arduino IDE správně.

### <a name="macos"></a>macOS

Doporučujeme vám používat možnosti instalace jedním kliknutím Příprava vývojového prostředí. Pokud narazíte na potíže, můžete podle [ruční kroky](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) ho provést.

#### <a name="install-homebrew"></a>Nainstalujte Homebrew

> [!NOTE] 
> Pokud jste nainstalovali Homebrew, můžete tento krok přeskočit.

Postupujte podle [pokyny k instalaci Homebrew](https://docs.brew.sh/Installation.html) k její instalaci.

#### <a name="download-the-latest-package"></a>Stáhněte si nejnovější balíček
Souboru .zip, který si stáhnout obsahuje všechny nezbytné nástroje a balíčky pro vývoj DevKit.

> [!div class="button"]
[Stáhnout](https://aka.ms/devkit/prod/installpackage/mac/latest)

Soubor ZIP obsahuje následující nástroje a balíčků. Pokud již máte některé součásti nainstalované, skript rozpozná a jejich přeskočit.

* Node.js a Yarn: modul Runtime pro instalační skript a automatizované úlohy.
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest#a-namemacosinstall-on-macos): prostředí příkazového řádku a platformy pro správu prostředků Azure.
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): editor lehký kód pro vývoj DevKit.
* [Rozšíření sady Visual Studio Code pro Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): rozšíření, která umožňuje Arduino vývoj v aplikaci Visual Studio Code.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): nástroj, který využívá rozšíření pro Arduino.
* Balíček DevKit Tabule: Nástroj řetězy, knihovny a projekty pro DevKit.
* Nástroj ST odkaz: Základní nástroje a ovladače.

#### <a name="run-the-installation-script"></a>Spusťte instalační skript

V nástroji hledání vyhledejte ZIP a rozbalte ho:

![Vyhledávací systému macOS](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-finder.png)

Spusťte aplikaci terminálu, vyhledejte složku extrahujte soubor .zip a spusťte:

```bash
./install.sh
```

![instalace systému macOS](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-install-sh.png)

> [!NOTE] 
> Pokud splňujete Homebrew chybu oprávnění, spusťte `brew doctor` získat ji opravil. Zkontrolujte [– nejčastější dotazy](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#homebrew-permission-error-on-macos) další podrobnosti.

Nyní máte všechny potřebné nástroje a balíčky pro systému macOS nainstalována.


## <a name="open-the-project-folder"></a>Otevřete složku projektu

### <a name="start-vs-code"></a>Kód pro spuštění VS

Ujistěte se, že vaše DevKit není připojený. Nejprve spusťte VS Code a připojte DevKit do vašeho počítače. VS Code automaticky vyhledá DevKit a otevře úvodní stránka:

![Úvodní stránka](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

> [!NOTE] 
> Občas stát když spustíte VS Code, se zobrazí výzva s chybou nebyla nalezena Arduino IDE nebo balíček Příbuzná panelu. Zavřete VS Code a restartujte Arduino IDE. VS kód by pak vyhledejte cestu Arduino IDE správně.


### <a name="open-the-arduino-examples-folder"></a>Otevřete složku Arduino příklady

Na **Arduino příklady** kartě, přejděte na **příklady MXCHIP AZ3166** > **AzureIoT**a vyberte **GetStarted**.

![Příklady Arduino karta](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

Pokud jste dojít zavřete podokno, můžete ho znovu otevřít. Použití `Ctrl+Shift+P` (systému macOS: `Cmd+Shift+P`) Chcete-li spustit příkaz palety, zadejte **Arduino**a potom najděte a vyberte **Arduino: Příklady**.

## <a name="provision-azure-services"></a>Zřídit služby Azure

V okně řešení spuštění úkolu prostřednictvím `Ctrl+P` (systému macOS: `Cmd+P`) tak, že zadáte `task cloud-provision`.

V terminálu VS Code interaktivního příkazového řádku vás provede zřizování požadované služby Azure:

![Interaktivního příkazového řádku](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Vytvoření a nahrání nákresu Arduino

### <a name="windows"></a>Windows

1. Použití `Ctrl+P` ke spuštění `task device-upload`.
2. Terminálu zobrazí výzvu k zadání režim konfigurace. Uděláte to tak, podržte tlačítko A pak push a verzí na tlačítko Obnovit. Na obrazovce zobrazí DevKit id a "Konfigurace".

To je nastavit připojovací řetězec, který načte z `task cloud-provision` krok.

Pak spustí VS Code ověření a odeslání nákresu Arduino:

![Ověření a odeslání nákresu Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

DevKit restartuje a spuštění kódu.

> [!NOTE] 
> V některých případech se zobrazí chyba "Chyba: AZ3166: Neznámý balíček". Je to z důvodu panel indexu balíčků neobnoví. Zaškrtněte toto políčko [– nejčastější dotazy kroky](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) jeho řešení.

### <a name="macos"></a>macOS

1. Přepnout do režimu konfigurace, DevKit: podržte tlačítko A pak nabízené a verze na tlačítko Obnovit. Na obrazovce zobrazí "Konfigurace".
2. Použití `Cmd+P` ke spuštění `task device-upload`.

To je nastavit připojovací řetězec, který načte z `task cloud-provision` krok.

Pak spustí VS Code ověření a odeslání nákresu Arduino:

![nahrávání zařízení](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

DevKit restartuje a spuštění kódu.

> [!NOTE] 
> V některých případech se zobrazí chyba "Chyba: AZ3166: Neznámý balíček". Je to z důvodu panel indexu balíčků neobnoví. Zaškrtněte toto políčko [– nejčastější dotazy kroky](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) jeho řešení.


## <a name="test-the-project"></a>Testování projektu

V produktu VS Code, si otevřete a nastavit sériové monitorování těchto kroků:

1. Klikněte `COM[X]` word na stavovém řádku nastavit pravý port COM s `STMicroelectronics`: ![com port](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/com-port.png)

2. Klikněte na tlačítko power moduly ikonu na stavovém řádku otevřete sériové monitorování: ![sériové monitorování](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution//connect-iothub/serial-monitor.png)

3. Na stavovém řádku, klikněte na číslo, které představuje přenosová rychlost a nastavte `115200`: ![přenosová rychlost](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/baud-rate.png)

Ukázkové aplikace pracuje správně, pokud jste se zobrazit následující výsledky:

* Sériové monitorování zobrazuje stejné informace jako obsah na tomto snímku obrazovky.
* Indikátor LED na MXChip IoT DevKit bliká.

![Závěrečný výstup v produktu VS Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Problémy a zpětné vazby

Pokud narazíte na potíže, můžete nějakého najít [nejčastější dotazy k](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/). Můžete také odeslat názor nám ponecháním komentář na této stránce.

## <a name="next-steps"></a>Další postup

Jste úspěšně jste se připojili IoT DevKit MXChip do služby IoT hub a jste odeslali data zaznamenaná snímače do služby IoT hub.

Chcete-li pokračovat, Začínáme se službou Azure IoT Hub a prozkoumat dalších scénářů platformy IoT najdete v části:

- [Správa zasílání zpráv cloudových zařízení pomocí iothub-exploreru](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [Uložení zpráv IoT Hub do úložiště dat Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Vizualizovat data snímačů v reálném čase ze služby Azure IoT Hub pomocí Power BI](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Použít webové aplikace k vizualizaci dat snímačů v reálném čase ze služby Azure IoT Hub](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Počasí prognózy používající senzor data ze služby IoT hub v Azure Machine Learning](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [Správa zařízení s využitím iothub-exploreru](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Vzdálené monitorování a oznámení s využitím Logic Apps](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
