---
title: Hromadná správa zařízení připojených ke vzdálenému monitorování – Azure | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak spravovat zařízení připojená k řešení vzdáleného monitorování hromadně.
author: Philmea
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: philmea
ms.openlocfilehash: eaca93ac8a4e8c660be9618aefb27921a4e0a2eb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77565574"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>Kurz: Hromadná správa připojených zařízení

V tomto kurzu použijete akcelerátor řešení vzdáleného monitorování ke správě konfigurace připojených zařízení hromadně.

Jako operátor společnosti Contoso je třeba nakonfigurovat skupinu zařízení s novou verzí firmwaru. Nechcete, aby bylo třeba aktualizovat firmware na každém zařízení jednotlivě. Chcete-li aktualizovat firmware na skupině zařízení, můžete použít skupiny zařízení a automatickou správu zařízení v akcelerátoru řešení vzdáleného monitorování. Každé zařízení, které přidáte do skupiny zařízení, získá nejnovější firmware, jakmile se zařízení přepne do režimu online.

V tomto kurzu jste:

>[!div class="checklist"]
> * Vytvoření skupiny zařízení
> * Příprava a hostování firmwaru
> * Vytvoření konfigurace zařízení na webu Azure Portal
> * Import konfigurace zařízení do řešení vzdáleného monitorování
> * Nasazení konfigurace do zařízení ve skupině zařízení
> * Monitorování nasazení

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>Požadavky

Abyste mohli postupovat podle tohoto kurzu, musíte ve svém předplatném Azure mít nasazenou instanci akcelerátoru řešení pro vzdálené monitorování.

Pokud jste akcelerátor řešení pro vzdálené monitorování ještě nenasadili, měli byste dokončit rychlý start [Nasazení cloudového řešení pro vzdálené monitorování](quickstart-remote-monitoring-deploy.md).

K hostování souborů firmwaru potřebujete účet úložiště Azure. Můžete použít existující účet úložiště nebo [vytvořit nový účet úložiště](../storage/common/storage-account-create.md) ve vašem předplatném.

Kurz používá zařízení [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) jako ukázkové zařízení.

V místním počítači potřebujete nainstalovaný následující software:

* [Visual Studio Kód (VS kód)](https://code.visualstudio.com/).
* Rozšíření kódu [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) VS.

Než začnete, potřebujete:

* Ujistěte se, že [zavaděč na zařízení IoT DevKit je ve verzi 1.4.0 nebo vyšší](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Ujistěte se, že sada IoT DevKit SDK je ve stejné verzi jako zavaděč. Sdk IoT DevKit SDK můžete aktualizovat pomocí azure iot workbench v kódu VS. Otevřete paletu příkazů a zadejte **Arduino: Board Manager**. Další informace naleznete [v tématu Příprava vývojového prostředí](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment).

K akcelerátoru řešení vzdáleného monitorování je také potřeba připojit alespoň jedno zařízení IoT DevKit. Pokud jste zařízení IoT DevKit nepřipojili, přečtěte [si informace o připojení MXChip IoT DevKit AZ3166 k akcelerátoru řešení Vzdáleného monitorování IoT](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md).

## <a name="navigate-to-the-dashboard"></a>Přechod na řídicí panel

K zobrazení řídicího panelu řešení pro vzdálené monitorování v prohlížeči je potřeba nejdřív přejít na [Akcelerátory řešení Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#dashboard). Může se zobrazit výzva k přihlášení pomocí vašich přihlašovacích údajů k předplatnému Azure.

Potom na dlaždici akcelerátoru řešení pro vzdálené monitorování, který jste nasadili v [rychlém startu](quickstart-remote-monitoring-deploy.md), klikněte na **Spustit**.

## <a name="create-a-device-group"></a>Vytvoření skupiny zařízení

Chcete-li automaticky aktualizovat firmware na skupině zařízení, musí být zařízení členy skupiny zařízení v řešení vzdáleného monitorování:

1. Na stránce **Zařízení** vyberte všechna zařízení **IoT DevKit,** která jste připojili k akcelerátoru řešení. Potom klepněte na **položku Úlohy**.

1. V panelu **Úlohy** vyberte **Tagy**, nastavte název úlohy na **AddDevKitTag**a pak přidejte textovou značku s názvem **IsDevKitDevice** s hodnotou **Y**. Potom klepněte na **tlačítko Použít**.

1. Nyní můžete použít hodnoty značek k vytvoření skupiny zařízení. Na stránce **Zařízení** klikněte na **Spravovat skupiny zařízení**.

1. Vytvořte textový filtr, který používá název značky **IsDevKitDevice** a hodnotu **Y** v podmínce. Uložte skupinu zařízení jako **zařízení IoT DevKit**.

Později v tomto kurzu použijete tuto skupinu zařízení k použití konfigurace zařízení, která aktualizuje firmware všech členů.

## <a name="prepare-and-host-the-firmware"></a>Příprava a hostování firmwaru

Rozšíření [Kódu Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) VS code obsahuje ukázkový kód zařízení pro aktualizaci firmwaru.

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>Otevření ukázky FIRMWARE OTA v kódu VS

1. Ujistěte se, že váš IoT DevKit není připojený k počítači. Spusťte Kód VS a připojte devKit k počítači.

1. Stisknutím **klávesy F1** otevřete paletu příkazů, zadejte a vyberte **IoT Workbench: Examples**. Pak vyberte **IoT DevKit** jako desku.

1. Vyhledejte **firmware OTA** a klepněte na tlačítko **Otevřít ukázku**. Otevře se nové okno VS Code a zobrazí **firmware_ota** složku projektu:

    ![IoT Workbench, vyberte příklad Firmware OTA](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>Sestavte nový firmware

Původní verze firmwaru zařízení je 1.0.0. Nový firmware by měl mít vyšší číslo verze.

1. V kódu VS otevřete soubor **FirmwareOTA.ino** a změňte `currentFirmwareVersion` z `1.0.0` `1.0.1`na :

    ![Změna verze firmwaru](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Otevřete paletu příkazů, zadejte a vyberte **Pracovní plocha IoT: Zařízení**. Pak vyberte **Kompilace zařízení** pro kompilaci kódu:

    ![Kompilace zařízení](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    VS Code uloží zkompilovaný `.build` soubor do složky v projektu. V závislosti na vašem nastavení může `.build` Kód VS skrýt složku v zobrazení průzkumníka.

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>Generovat hodnotu CRC a vypočítat velikost souboru firmwaru

1. Otevřete paletu příkazů, zadejte a vyberte **Pracovní plocha IoT: Zařízení**. Pak vyberte **Generovat CRC**:

    ![Generovat CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. VS Code generuje a tiskne hodnotu CRC, název souboru firmwaru a cestu a velikost souboru ve výstupním okně. Poznamenejte si tyto hodnoty pro pozdější:

    ![Informace o CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>Nahrání firmwaru do cloudu

Použijte svůj účet úložiště Azure k hostování nového souboru firmwaru v cloudu.

1. Na webu Azure Portal přejděte na svůj účet úložiště. V části Služby vyberte **objekty BLOB**. Vytvořte veřejný kontejner s názvem **firmware** pro ukládání souborů firmwaru:

    ![Vytvořit složku](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. Chcete-li soubor firmwaru nahrát do kontejneru, vyberte kontejner **firmwaru** a klepněte na tlačítko **Nahrát**.

1. Vyberte **soubor FirmwareOTA.ino .bin**. V předchozí části jste si poznamenejte úplnou cestu k tomuto souboru.

1. Po dokončení nahrávání souboru firmwaru si poznamenejte adresu URL souboru.

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>Sestavení a nahrání původního firmwaru do zařízení IoT DevKit

1. V kódu VS otevřete soubor **FirmwareOTA.ino** a změňte `currentFirmwareVersion` zpět na `1.0.0`:

    ![Verze 1.0.0](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Otevřete paletu příkazů, zadejte a vyberte **Pracovní plocha IoT: Zařízení**. Pak vyberte **Nahrát zařízení**:

    ![Nahrávání zařízení](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. Kód VS ověří a nahraje kód do vašeho zařízení IoT DevKit.

1. Po dokončení nahrávání se zařízení IoT DevKit restartuje. Po dokončení restartu se na obrazovce IoT DevKit zobrazí **verze FW: 1.0.0**a že kontroluje nový firmware:

    ![ota-1](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>Vytvoření konfigurace zařízení

Konfigurace zařízení určuje požadovaný stav vašich zařízení. Vývojář obvykle [vytvoří konfiguraci](../iot-hub/iot-hub-automatic-device-management.md#create-a-configuration) na **stránce konfigurace zařízení IoT** na webu Azure Portal. Konfigurace zařízení je dokument JSON, který určuje požadovaný stav vašich zařízení a sadu metrik.

Následující konfiguraci uložte jako soubor nazývaný **firmware-update.json** v místním počítači. `YOURSTRORAGEACCOUNTNAME`Nahraďte `YOURCHECKSUM`zástupné symboly , `YOURPACKAGESIZE` a hodnoty, které jste si dříve poznamenali:

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

Tento konfigurační soubor se používá v následující části.

## <a name="import-a-configuration"></a>Import konfigurace

V této části importujete konfiguraci zařízení jako balíček do akcelerátoru řešení vzdáleného monitorování. Obvykle operátor dokončí tento úkol.

1. Ve webovém uživatelském uživatelském uživatelském uživatelském tlačítkě Vzdálené monitorování přejděte na stránku **Balíčky** a klikněte na **+ Nový balíček**:

    ![Nový balíček](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. V panelu **Nový balíček** zvolte jako typ balíčku **konfiguraci zařízení** a **firmware** jako typ konfigurace. Chcete-li najít soubor **firmware-update.json** v místním počítači, klepněte na tlačítko **Procházet** a potom klepněte na tlačítko **Nahrát**:

    ![Nahrát balíček](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. Seznam balíčků nyní obsahuje balíček **aktualizace firmwaru.**

## <a name="deploy-the-configuration-to-your-devices"></a>Nasazení konfigurace do zařízení

V této části vytvoříte a spustíte nasazení, které použije konfiguraci zařízení pro vaše zařízení IoT DevKit.

1. Ve webovém uživatelském uživatelském uživatelském tlačítkě Vzdálené monitorování přejděte na stránku **Nasazení** a klikněte na **+ Nové nasazení**:

    ![Nové nasazení](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. V panelu **Nové nasazení** vytvořte nasazení s následujícím nastavením:

    |Možnost|Hodnota|
    |---|---|
    |Name (Název)|Nasazení aktualizace firmwaru|
    |Typ balíčku|Konfigurace zařízení|
    |Typ konfigurace|Firmware|
    |Balíček|firmware-update.json|
    |Skupina zařízení|Zařízení IoT DevKit|
    |Priorita|10|

    ![Vytvoření nasazení](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    Klikněte na **Použít**. Na stránce **Nasazení** se zobrazí nové nasazení, které zobrazuje následující metriky:

    * **Cílený** zobrazuje počet zařízení ve skupině zařízení.
    * **Použitý** zobrazuje počet zařízení, která byla aktualizována obsahem konfigurace.
    * **Úspěšné** zobrazuje počet zařízení v nasazení, které hlásí úspěch.
    * **Neúspěšný** zobrazuje počet zařízení v nasazení, které hlásí selhání.

## <a name="monitor-the-deployment"></a>Monitorování nasazení

Po několika minutách ioT DevKit načte nové informace o firmwaru a začne je stahovat do zařízení:

![ota-2](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

V závislosti na rychlosti sítě může stahování trvat až několik minut. Po stažení firmwaru zařízení ověří velikost souboru a hodnotu CRC. Obrazovka na displeji MXChip **proběhla,** pokud je ověření úspěšné.

![ota-3](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

Pokud je kontrola úspěšná, zařízení se restartuje. Zobrazí se odpočítávání od **5** do **0** před restartováníse stane.

![ota-4](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

Po restartu, IoT DevKit zavaděč upgraduje firmware na novou verzi. Upgrade může trvat několik sekund. Během této fáze je rgb LED v zařízení červená a obrazovka je prázdná.

![ota-5](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

Po dokončení restartování zařízení IoT DevKit je nyní spuštěna verze 1.0.1 firmwaru.

![ota-6](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

Na stránce **Nasazení** klikněte na nasazení a podívejte se na stav vašich zařízení při jejich aktualizaci. Můžete zobrazit stav jednotlivých zařízení ve skupině zařízení a vlastní metriky, které jste definovali.

![Podrobnosti o nasazení](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Tento výukový program vám ukázal, jak aktualizovat firmware skupiny zařízení připojených k vašemu řešení. Chcete-li aktualizovat zařízení, vaše řešení používá automatickou správu zařízení. Další informace o funkci automatické správy zařízení v základním centru IoT vašeho řešení najdete v [tématu Konfigurace a monitorování zařízení IoT ve velkém měřítku pomocí portálu Azure](../iot-hub/iot-hub-auto-device-config.md).