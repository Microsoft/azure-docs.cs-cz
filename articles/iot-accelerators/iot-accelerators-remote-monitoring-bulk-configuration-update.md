---
title: Správa zařízení připojených k vzdálené sledování hromadně – Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak spravovat zařízení připojených k řešení vzdáleného monitorování hromadně.
author: aditidugar
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: adugar
ms.openlocfilehash: 640eb8800f9593aef510d99713595bdd0c844263
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52684425"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>Kurz: Správa připojených zařízení hromadné

V tomto kurzu použijete ke správě konfigurace z připojených zařízení hromadné akcelerátor řešení vzdálené monitorování.

Jako operátor ve společnosti Contoso musíte nakonfigurovat skupinu zařízení pomocí nové verze firmwaru. Nechcete k aktualizaci firmwaru na každé zařízení zvlášť. K aktualizaci firmwaru na skupinu zařízení, můžete použít skupiny zařízení nebo správu automatické zařízení v akcelerátoru řešení vzdáleného monitorování. Jakékoli zařízení, které přidáte do skupiny zařízení získá nejnovější firmware, jakmile zařízení online.

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Vytvoření skupiny zařízení
> * Příprava a firmware hostitele
> * Vytvoření konfigurace zařízení na portálu Azure portal
> * Importovat konfiguraci zařízení do řešení vzdáleného monitorování
> * Konfigurace nasadit do zařízení ve skupině zařízení
> * Monitorování nasazení

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>Požadavky

Abyste mohli postupovat podle tohoto kurzu, musíte ve svém předplatném Azure mít nasazenou instanci akcelerátoru řešení vzdáleného monitorování.

Pokud jste akcelerátor řešení vzdáleného monitorování ještě nenasadili, měli byste dokončit rychlý start [Nasazení cloudového řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md).

Budete potřebovat účet úložiště Azure k hostování souborů firmwaru. Můžete použít existující účet úložiště, nebo [vytvořit nový účet úložiště](../storage/common/storage-quickstart-create-account.md) ve vašem předplatném.

V tomto kurzu použijete [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) zařízení jako ukázka.

Je třeba na místním počítači nainstalovaný následující software:

* [Visual Studio Code (VS Code)](https://code.visualstudio.com/).
* [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) rozšíření VS Code.

Než začnete, potřebujete:

* Ujistěte se, [zaváděcího programu pro spouštění na vašem zařízení IoT DevKit je verze 1.4.0 nebo novější](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Zajistěte, aby že se sadou SDK IoT DevKit na stejnou verzi jako spouštěcí zavaděč. Můžete aktualizovat sady SDK IoT DevKit pomocí nástroje Azure IoT v nástroji VS Code. Otevřete paletu příkazů a zadejte **Arduino: panel Správce**. Další informace najdete v tématu [Příprava vývojového prostředí](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment).

Také budete muset připojit alespoň jedno zařízení IoT DevKit akcelerátor řešení vzdálené monitorování. Pokud jste se nepřipojili IoT DevKit zařízení, přečtěte si téma [připojení MXChip IoT DevKit AZ3166 k akcelerátoru řešení vzdáleného monitorování IoT](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md).

## <a name="navigate-to-the-dashboard"></a>Přechod na řídicí panel

K zobrazení řídicího panelu řešení vzdáleného monitorování v prohlížeči je potřeba nejdřív přejít na [Akcelerátory řešení Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#dashboard). Může se zobrazit výzva k přihlášení pomocí vašich přihlašovacích údajů k předplatnému Azure.

Potom na dlaždici akcelerátoru řešení vzdáleného monitorování, který jste nasadili v [rychlém startu](quickstart-remote-monitoring-deploy.md), klikněte na **Spustit**.

## <a name="create-a-device-group"></a>Vytvoření skupiny zařízení

K automatické aktualizaci firmwaru na skupinu zařízení, zařízení musí být členem skupiny zařízení v řešení vzdáleného monitorování:

1. Na **zařízení** stránky, vyberte všechny **IoT DevKit** zařízení jste se připojili k akcelerátoru řešení. Pak klikněte na **Úlohy**.

1. V **úlohy** panelu, vyberte **značky**, nastavte název úlohy **AddDevKitTag**a pak přidejte značku text volá **IsDevKitDevice** s hodnotou **Y**. Pak klikněte na tlačítko **použít**.

1. Teď můžete použít hodnoty značek můžete vytvořit skupinu zařízení. Na **zařízení** klikněte na **Správa skupin zařízení**.

1. Vytvořit filtr text, který používá název značky **IsDevKitDevice** a hodnota **Y** v podmínce. Uložit skupinu zařízení jako **zařízení IoT DevKit**.

Později v tomto kurzu použijete k použití konfigurace zařízení, která aktualizuje firmware všichni členové této skupiny zařízení.

## <a name="prepare-and-host-the-firmware"></a>Příprava a firmware hostitele

[Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) rozšíření VS Code obsahuje ukázkový kód zařízení k aktualizaci firmwaru.

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>Otevřete ukázku OTA firmwaru v nástroji VS Code

1. Zajistěte, aby že vaše IoT DevKit není připojený k počítači. Spusťte VS Code a DevKit připojte se k počítači.

1. Stisknutím klávesy **F1** otevřete paletu příkazů, typ a vyberte **IoT Workbench: Příklady**. Potom vyberte **IoT DevKit** jako panelu.

1. Najít **Firmware OTA** a klikněte na tlačítko **otevřít ukázkové**. Otevře se nové okno VS Code a zobrazí **firmware_ota** složky projektu:

    ![Aplikace IoT Workbench, vyberte příklad OTA firmwaru](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>Vytvoření nového firmwaru

Počáteční verze firmwaru zařízení je 1.0.0. Nový firmware by měly mít vyšší číslo verze.

1. Ve VS Code, Otevřít **FirmwareOTA.ino** soubor a změňte `currentFirmwareVersion` z `1.0.0` k `1.0.1`:

    ![Změna verze firmwaru](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Otevřete paletu příkazů zadejte a vyberte **IoT Workbench: zařízení**. Potom vyberte **zařízení kompilaci** pro kompilaci kódu:

    ![Kompilace zařízení](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    Uloží zkompilovaný soubor v nástroji VS Code `.build` složky v projektu. V závislosti na nastavení může skrýt VS Code `.build` složku v Průzkumníku zobrazení.

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>Generování hodnoty CRC a výpočet velikosti souboru firmwaru

1. Otevřete paletu příkazů zadejte a vyberte **IoT Workbench: zařízení**. Potom vyberte **generovat CRC**:

    ![Generovat CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. VS Code generuje a vytiskne hodnotu CRC, firmwaru název souboru a cestu a velikost souborů v okně výstup. Poznamenejte si tyto hodnoty pro pozdější:

    ![Informace o CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>Firmware nahrávání do cloudu

Pomocí svého účtu úložiště Azure pro hostování nového firmwaru souboru v cloudu.

1. Na webu Azure Portal přejděte na svůj účet úložiště. V části služby vyberte **objekty BLOB**. Vytvoření veřejného kontejneru s názvem **firmware** pro ukládání souborů firmwaru:

    ![Vytvořit složku](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. Pokud chcete nahrát soubor firmwaru do kontejneru, vyberte **firmware** kontejneru a klikněte na **nahrát**.

1. Vyberte **FirmwareOTA.ino.bin**. Jste si poznamenali úplnou cestu k tomuto souboru v předchozí části.

1. Nahrávání je dokončené. po souboru firmwaru, poznamenejte si adresu URL souboru.

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>Vytvoření a nahrání původní firmwaru do zařízení IoT DevKit

1. Ve VS Code, Otevřít **FirmwareOTA.ino** soubor a změňte `currentFirmwareVersion` zpět `1.0.0`:

    ![Verze 1.0.0](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Otevřete paletu příkazů zadejte a vyberte **IoT Workbench: zařízení**. Potom vyberte **zařízení nahrát**:

    ![Nahrávání zařízení](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. VS Code ověří a odešle do zařízení IoT DevKit kód.

1. Až se nahrávání dokončí, zařízení IoT DevKit se restartuje. Po dokončení restartování se zobrazí na obrazovce IoT DevKit **FW verze: 1.0.0**, a, který kontroluje nových firmwaru:

    ![OTA-1](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>Vytvoření konfigurace zařízení

Konfigurace zařízení určuje požadovaný stav svých zařízení. Obvykle, Vývojář [vytvoří konfiguraci](../iot-hub/iot-hub-auto-device-config.md#create-a-configuration) na **konfigurace zařízení IoT** stránky na webu Azure Portal. Konfigurace zařízení je dokument JSON, který určuje požadovaný stav svých zařízení a sadu metriky.

Následující konfiguraci uložíte jako soubor s názvem **firmware update.json** na místním počítači. Nahradit `YOURSTRORAGEACCOUNTNAME`, `YOURCHECKSUM`, a `YOURPACKAGESIZE` zástupné symboly hodnotami, které jste si poznamenali dříve:

```json
{
  "id": "firmware-update",
  "schemaVersion": null,
  "labels": {
    "Version": "1.0.1",
    "Devices": "IoT DevKit"
  },
  "content": {
    "deviceContent": {
      "properties.desired.firmware": {
        "fwVersion": "1.0.1",
        "fwPackageURI": "https://YOURSTRORAGEACCOUNTNAME.blob.core.windows.net/firmware/FirmwareOTA.ino.bin",
        "fwPackageCheckValue": "YOURCHECKSUM",
        "fwSize": YOURPACKAGESIZE
      }
    }
  },
  "targetCondition": "",
  "priority": 10,
  "systemMetrics": {
    "results": {
      "targetedCount": 0,
      "appliedCount": 0
    },
    "queries": {
      "targetedCount": "",
      "appliedCount": "select deviceId from devices where configurations.[[firmware-update]].status = 'Applied'"
    }
  },
  "metrics": {
    "results": {
      "Current": 1
    },
    "queries": {
      "Current": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Current' AND properties.reported.firmware.type='IoTDevKit'",
      "Downloading": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Downloading' AND properties.reported.firmware.type='IoTDevKit'",
      "Verifying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Verifying' AND properties.reported.firmware.type='IoTDevKit'",
      "Applying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Applying' AND properties.reported.firmware.type='IoTDevKit'",
      "Error": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Error' AND properties.reported.firmware.type='IoTDevKit'"
    }
  }
}
```

V následující části použijete tento konfigurační soubor.

## <a name="import-a-configuration"></a>Importovat konfiguraci

V této části můžete importovat konfiguraci zařízení jako balíček do akcelerátoru řešení vzdáleného monitorování. Operátor obvykle dokončení této úlohy.

1. Vzdálené monitorování webového uživatelského rozhraní, přejděte na **balíčky** stránky a klikněte na tlačítko **+ nový balíček pro**:

    ![Nový balíček](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. V **nový balíček** panelu, vyberte **konfigurace zařízení** jako typ balíčku a **Firmware** jako typ konfigurace. Klikněte na tlačítko **Procházet** najít **firmware update.json** souboru na místním počítači a potom klikněte na tlačítko **nahrát**:

    ![Nahrání balíčku](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. Seznam balíčků teď zahrnuje **aktualizace firmwaru** balíčku.

## <a name="deploy-the-configuration-to-your-devices"></a>Konfigurace nasadit do zařízení

V této části vytvořit a spustit nasazení, které platí pro zařízení IoT DevKit konfigurace zařízení.

1. Vzdálené monitorování webového uživatelského rozhraní, přejděte na **nasazení** stránky a klikněte na tlačítko **+ nové nasazení**:

    ![Nové nasazení](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. V **nové nasazení** panelu, vytvořit nasazení s následujícími nastaveními:

    |Možnost|Hodnota|
    |---|---|
    |Název|Nasadit aktualizace firmwaru|
    |Typ balíčku|Konfigurace zařízení|
    |Typ konfigurace|Firmware|
    |Balíček|firmware update.json|
    |Skupiny zařízení|Zařízení IoT DevKit|
    |Priorita|10|

    ![Vytvoření nasazení](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    Klikněte na tlačítko **Použít**. Uvidíte nové nasazení v **nasazení** stránku, která zobrazuje následující metriky:

    * **Cílem** zobrazuje počet zařízení ve skupině zařízení.
    * **Použít** zobrazuje počet zařízení, které byly aktualizovány s obsahem konfigurace.
    * **Úspěšné** zobrazuje počet zařízení v nasazení této sestavy úspěch.
    * **Nepovedlo** zobrazuje počet zařízení v nasazení selhání této sestavy.

## <a name="monitor-the-deployment"></a>Monitorování nasazení

Po několika minutách IoT DevKit načte informace o nový firmware a začne stahování do zařízení:

![OTA-2](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

V závislosti na rychlosti sítě stahování může trvat až na několik minut. Po stažení firmwaru zařízení ověřuje velikost souboru a hodnota. Zobrazí obrazovku na MXChip **předaný** Pokud je ověření úspěšné.

![OTA 3](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

Pokud je kontrola je úspěšná, zařízení se restartuje. Zobrazí odpočítávání z **5** k **0** předtím, než se stane při restartování.

![OTA 4](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

Po restartování počítače spouštěcí zavaděč IoT DevKit upgrade firmwaru na novou verzi. Upgrade může trvat několik sekund. Během této fáze je red RGB LED v zařízení a na obrazovce je prázdný.

![OTA 5](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

Po dokončení restartování zařízení IoT DevKit teď běží verze 1.0.1 firmware.

![OTA 6](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

Na **nasazení** stránky, klikněte na nasazení a zjistit stav zařízení podle jejich aktualizace. Zobrazí se stav jednotlivých zařízení ve vaší skupině zařízení a vlastní metriky, které jste definovali.

![Podrobnosti nasazení](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Další postup

Tento kurz ukázal, jak k aktualizaci firmwaru skupiny zařízení připojených k řešení. Aktualizace zařízení, vaše řešení používá automatické správy. Další informace o funkci automatické správy ve službě IoT hub základní vašeho řešení, najdete v článku [konfigurovat a monitorovat zařízení IoT ve velkém měřítku pomocí webu Azure portal](../iot-hub/iot-hub-auto-device-config.md).