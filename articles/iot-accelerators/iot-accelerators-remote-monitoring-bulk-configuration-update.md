---
title: Hromadná Správa zařízení připojených ke vzdálenému monitorování – Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak hromadně spravovat zařízení připojená k řešení vzdáleného monitorování.
author: Philmea
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: philmea
ms.openlocfilehash: 8309b625a590131c5f521335e180967ab2c2667c
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91363150"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>Kurz: Hromadná Správa připojených zařízení

V tomto kurzu budete pomocí akcelerátoru řešení vzdáleného monitorování spravovat konfiguraci připojených zařízení hromadně.

Jako operátor ve společnosti Contoso musíte nakonfigurovat skupinu zařízení s novou verzí firmwaru. Nechcete, aby se firmware jednotlivě aktualizoval na každé zařízení. Chcete-li aktualizovat firmware pro skupinu zařízení, můžete použít skupiny zařízení a automatickou správu zařízení v akcelerátoru řešení vzdáleného monitorování. Každé zařízení, které přidáváte do skupiny zařízení, získá nejnovější firmware ihned po přepnutí zařízení do režimu online.

V tomto kurzu jste:

>[!div class="checklist"]
> * Vytvoření skupiny zařízení
> * Příprava a hostování firmwaru
> * Vytvoření konfigurace zařízení v Azure Portal
> * Import konfigurace zařízení do řešení vzdáleného monitorování
> * Nasaďte konfiguraci do zařízení ve skupině zařízení.
> * Monitorování nasazení

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>Požadavky

Abyste mohli postupovat podle tohoto kurzu, musíte ve svém předplatném Azure mít nasazenou instanci akcelerátoru řešení pro vzdálené monitorování.

Pokud jste akcelerátor řešení pro vzdálené monitorování ještě nenasadili, měli byste dokončit rychlý start [Nasazení cloudového řešení pro vzdálené monitorování](quickstart-remote-monitoring-deploy.md).

K hostování souborů firmwaru potřebujete účet služby Azure Storage. Můžete použít existující účet úložiště nebo [vytvořit nový účet úložiště](../storage/common/storage-account-create.md) v rámci svého předplatného.

V tomto kurzu se jako ukázkové zařízení používá zařízení [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) .

V místním počítači potřebujete nainstalovaný následující software:

* [Visual Studio Code (vs Code)](https://code.visualstudio.com/).
* Rozšíření VS Code [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) .

Než začnete, potřebujete:

* Ujistěte se, že [zaváděcí program pro zařízení IoT DevKit je ve verzi 1.4.0 nebo vyšší](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Ujistěte se, že sada IoT DevKit SDK má stejnou verzi jako zaváděcí program pro spouštění. Sadu IoT DevKit SDK můžete aktualizovat pomocí Azure IoT Workbench v VS Code. Otevřete paletu příkazů a zadejte **Arduino: Desk Manager**. Další informace najdete v tématu [Příprava vývojového prostředí](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment).

Musíte taky připojit aspoň jedno zařízení IoT DevKit k akcelerátoru řešení vzdáleného monitorování. Pokud jste nepřipojili zařízení IoT DevKit, přečtěte si téma [připojení MXChip IoT DEVKIT AZ3166 k akcelerátoru řešení vzdáleného monitorování IoT](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md).

## <a name="navigate-to-the-dashboard"></a>Přechod na řídicí panel

K zobrazení řídicího panelu řešení pro vzdálené monitorování v prohlížeči je potřeba nejdřív přejít na [Akcelerátory řešení Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#dashboard). Může se zobrazit výzva k přihlášení pomocí vašich přihlašovacích údajů k předplatnému Azure.

Potom na dlaždici akcelerátoru řešení pro vzdálené monitorování, který jste nasadili v [rychlém startu](quickstart-remote-monitoring-deploy.md), klikněte na **Spustit**.

## <a name="create-a-device-group"></a>Vytvoření skupiny zařízení

Pokud chcete automaticky aktualizovat firmware pro skupinu zařízení, musí být tato zařízení členem skupiny zařízení v řešení vzdáleného monitorování:

1. Na stránce **zařízení** vyberte všechna zařízení **IoT DevKit** , která jste připojili k akcelerátoru řešení. Pak klikněte na **úlohy**.

1. Na panelu **úlohy** vyberte **značky**, nastavte název úlohy na **AddDevKitTag**a pak přidejte značku text s názvem **IsDevKitDevice** s hodnotou **Y**. Pak klikněte na **použít**.

1. Nyní můžete pomocí hodnot značek vytvořit skupinu zařízení. Na stránce **zařízení** klikněte na **Spravovat skupiny zařízení**.

1. Vytvořte textový filtr, který v podmínce používá název značky **IsDevKitDevice** a hodnota **Y** . Uložte skupinu zařízení jako **zařízení IoT DevKit**.

Později v tomto kurzu použijete tuto skupinu zařízení k aplikování konfigurace zařízení, která aktualizuje firmware všech členů.

## <a name="prepare-and-host-the-firmware"></a>Příprava a hostování firmwaru

Rozšíření [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) vs Code obsahuje ukázkový kód zařízení pro aktualizaci firmwaru.

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>Otevřete ukázku OTA firmwaru v VS Code

1. Ujistěte se, že vaše aplikace IoT DevKit není připojená k vašemu počítači. Spusťte VS Code a pak připojte DevKit k počítači.

1. Stisknutím **klávesy F1** otevřete paletu příkazů, zadejte a vyberte **IoT Workbench: příklady**. Pak jako panel vyberte **IoT DevKit** .

1. Vyhledejte **firmware Ota** a klikněte na **otevřít ukázku**. Otevře se nové okno VS Code a zobrazí **firmware_ota** složku projektu:

    ![IoT Workbench, vyberte firmware OTA příklad](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>Sestavit nový firmware

Počáteční verze firmwaru zařízení je 1.0.0. Nový firmware by měl mít vyšší číslo verze.

1. V VS Code otevřete soubor **FirmwareOTA. ino** a změňte `currentFirmwareVersion` z `1.0.0` na `1.0.1` :

    ![Změnit verzi firmwaru](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Otevřete paletu příkazů a pak zadejte a vyberte **IoT Workbench: zařízení**. Pak vyberte **zkompilovat zařízení** pro zkompilování kódu:

    ![Kompilace zařízení](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    VS Code uloží kompilovaný soubor do `.build` složky v projektu. V závislosti na nastavení může VS Code skrýt `.build` složku v zobrazení Průzkumník.

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>Vygenerovat hodnotu CRC a vypočítat velikost souboru firmwaru

1. Otevřete paletu příkazů a pak zadejte a vyberte **IoT Workbench: zařízení**. Pak vyberte **Generovat CRC**:

    ![Generovat CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. VS Code generuje a vytiskne hodnotu CRC, název souboru a cestu firmwaru a velikost souboru v okně výstup. Poznamenejte si tyto hodnoty pro pozdější změny:

    ![Informace CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>Nahrát firmware do cloudu

Použijte svůj účet služby Azure Storage k hostování nového souboru firmwaru v cloudu.

1. Na webu Azure Portal přejděte na svůj účet úložiště. V části služby vyberte **objekty blob**. Vytvořte veřejný kontejner s názvem **firmware** pro ukládání souborů firmwaru:

    ![Vytvořit složku](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. Pokud chcete nahrát soubor firmwaru do kontejneru, vyberte kontejner **firmwaru** a klikněte na **nahrát**.

1. Vyberte **FirmwareOTA. ino. bin**. V předchozí části jste si poznamenali úplnou cestu k tomuto souboru.

1. Po dokončení nahrávání souboru firmwaru si poznamenejte adresu URL souboru.

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>Sestavte a nahrajte původní firmware do zařízení IoT DevKit.

1. V VS Code otevřete soubor **FirmwareOTA. ino** a změňte `currentFirmwareVersion` zpět na `1.0.0` :

    ![1.0.0 verze](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Otevřete paletu příkazů a pak zadejte a vyberte **IoT Workbench: zařízení**. Pak vyberte **nahrávání zařízení**:

    ![Nahrávání zařízení](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. VS Code ověří a nahraje kód do zařízení IoT DevKit.

1. Až se nahrávání dokončí, zařízení IoT DevKit se restartuje. Po dokončení restartování zobrazí obrazovka služby IoT DevKit verzi firmwaru **: 1.0.0**a kontroluje nový firmware:

    ![Fotografie zobrazuje zařízení I o T DevKit s obrazovkou, která zobrazuje verzi firmwaru a hledá nový firmware.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>Vytvoření konfigurace zařízení

Konfigurace zařízení určuje požadovaný stav vašich zařízení. Obvykle vývojář [vytvoří konfiguraci](../iot-hub/iot-hub-automatic-device-management.md#create-a-configuration) na stránce **Konfigurace zařízení IoT** v Azure Portal. Konfigurace zařízení je dokument JSON, který určuje požadovaný stav vašich zařízení a sadu metrik.

Do místního počítače uložte následující konfigurační soubor s názvem **firmware-update.js** . Nahraďte `YOURSTRORAGEACCOUNTNAME` `YOURCHECKSUM` `YOURPACKAGESIZE` zástupné znaky, a jako hodnoty, které jste si poznamenali dříve:

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

Tento konfigurační soubor použijete v následující části.

## <a name="import-a-configuration"></a>Import konfigurace

V této části naimportujete konfiguraci zařízení jako balíček do akcelerátoru řešení vzdáleného monitorování. Obvykle operátor dokončí tuto úlohu.

1. Ve webovém uživatelském rozhraní vzdáleného monitorování přejděte na stránku **balíčky** a klikněte na **+ nový balíček**:

    ![Nový balíček](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. Na panelu **nový balíček** vyberte možnost **Konfigurace zařízení** jako typ balíčku a jako typ konfigurace zadejte **firmware** . Klikněte na **Procházet** a vyhledejte **firmware-update.jsv** souboru na místním počítači a pak klikněte na **nahrát**:

    ![Nahrát balíček](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. Seznam balíčků teď obsahuje balíček **aktualizace firmwaru** .

## <a name="deploy-the-configuration-to-your-devices"></a>Nasazení konfigurace do zařízení

V této části vytvoříte a spustíte nasazení, které aplikuje konfiguraci zařízení na vaše zařízení IoT DevKit.

1. Ve webovém uživatelském rozhraní vzdáleného monitorování přejděte na stránku **nasazení** a klikněte na **+ nové nasazení**:

    ![Nové nasazení](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. Na **novém panelu nasazení** vytvořte nasazení s následujícími nastaveními:

    |Možnost|Hodnota|
    |---|---|
    |Název|Nasadit aktualizaci firmwaru|
    |Typ balíčku|Konfigurace zařízení|
    |Typ konfigurace|Firmware|
    |Balíček|firmware-update.jsna|
    |Skupina zařízení|Zařízení IoT DevKit|
    |Priorita|10|

    ![Vytvoření nasazení](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    Klikněte na **Použít**. Na stránce **nasazení** se zobrazí nové nasazení, které zobrazuje následující metriky:

    * **Cílová** hodnota zobrazuje počet zařízení ve skupině zařízení.
    * **Použito** : zobrazuje počet zařízení, která byla aktualizována pomocí obsahu konfigurace.
    * **Úspěch zobrazuje počet** zařízení v nasazení, která nahlásila úspěch.
    * **Chyba** zobrazuje počet zařízení v nasazení, která hlásí chybu.

## <a name="monitor-the-deployment"></a>Monitorování nasazení

Po několika minutách DevKit IoT načte nové informace o firmwaru a začne je stahovat do zařízení:

![Fotografie zobrazuje zařízení I o T DevKit s obrazovkou, která zobrazuje novou verzi firmwaru.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

V závislosti na rychlosti sítě může stahování trvat až několik minut. Po stažení firmwaru zařízení ověří velikost souboru a hodnotu CRC. Pokud je ověření úspěšné, zobrazí se na obrazovce MXChip obrazovka **předaná** .

![Fotografie zobrazuje zařízení I o T DevKit s obrazovkou, která zobrazuje verzi firmwaru a byla úspěšná pro úspěšné ověření.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

Pokud je ověření úspěšné, zařízení se restartuje. V případě, že dojde k restartování, zobrazí se odpočítávání od **5** do **0** .

![Fotografie zobrazuje zařízení I o T DevKit, které připravuje restart.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

Po restartování aplikace IoT DevKit zaváděcí program upgraduje firmware na novou verzi. Upgrade může trvat několik sekund. V průběhu této fáze je indikátor RGB v zařízení červený a obrazovka je prázdná.

![Fotografie zobrazuje zařízení I o T DevKit, které provádí upgrade. K dispozici není žádný displej, ale je k dispozici záře R G B L E D.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

Po dokončení restartování se v zařízení IoT DevKit nyní spouští verze 1.0.1 firmwaru.

![Fotografie zobrazuje zařízení I o T DevKit s jeho obrazovkou, která zobrazuje novou verzi firmwaru.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

Na stránce **nasazení** kliknutím na nasazení zobrazte stav svých zařízení při jejich aktualizaci. Můžete zobrazit stav jednotlivých zařízení ve skupině zařízení a vlastní metriky, které jste definovali.

![Podrobnosti o nasazení](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste si ukázali, jak aktualizovat firmware skupiny zařízení, která jsou připojená k vašemu řešení. K aktualizaci zařízení používá vaše řešení automatickou správu zařízení. Další informace o funkci automatické správy zařízení ve službě IoT Hub vašeho řešení najdete v tématu [Konfigurace a monitorování škálování zařízení IoT pomocí Azure Portal](../iot-hub/iot-hub-auto-device-config.md).