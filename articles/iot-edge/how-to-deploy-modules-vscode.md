---
title: Nasazení modulů z kódu Visual Studia – Azure IoT Edge
description: Pomocí kódu Visual Studia s nástroji Azure IoT můžete vysunout modul IoT Edge z vašeho ioT hubu do zařízení IoT Edge, jak je nakonfigurovaný podle manifestu nasazení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/8/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e1b2e2a80670cf0409f8f8477563b9a209cc8706
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209201"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Nasazení modulů Azure IoT Edge z kódu Visual Studia

Jakmile vytvoříte moduly IoT Edge s vaší obchodní logikou, chcete je nasadit do svých zařízení, aby fungovaly na hraničních zařízeních. Pokud máte více modulů, které spolupracují na shromažďování a zpracování dat, můžete je nasadit všechny najednou a deklarovat pravidla směrování, která je spojují.

Tento článek ukazuje, jak vytvořit manifest nasazení JSON, pak pomocí tohoto souboru k nabízení nasazení zařízení IoT Edge. Informace o vytvoření nasazení, které cílí na více zařízení na základě jejich sdílených značek, najdete [v tématu Nasazení modulů IoT Edge ve velkém měřítku pomocí kódu Visual Studia](how-to-deploy-monitor-vscode.md).

## <a name="prerequisites"></a>Požadavky

* Centrum [IoT v](../iot-hub/iot-hub-create-through-portal.md) předplacenéazure.
* [Zařízení IoT Edge](how-to-register-device.md#register-with-visual-studio-code) s nainstalovaným runtimem IoT Edge.
* [Visual Studio kód](https://code.visualstudio.com/).
* [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) pro kód Visual Studia.

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly nasadit, jak toky dat mezi moduly a požadované vlastnosti dvojčat modulu. Další informace o tom, jak manifesty nasazení fungují a jak je vytvořit, najdete v [tématu Informace o tom, jak lze moduly IoT Edge používat, konfigurovat a znovu používat](module-composition.md).

Chcete-li nasadit moduly pomocí kódu sady Visual Studio, uložte manifest nasazení místně jako . Soubor JSON. Cestu k souboru použijete v další části při spuštění příkazu k použití konfigurace zařízení.

Zde je základní manifest nasazení s jedním modulem jako příklad:

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /messages/* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>Přihlášení pro přístup k centru IoT hub

Rozšíření Azure IoT pro Visual Studio Code můžete použít k provádění operací s centrem IoT. Aby tyto operace fungovaly, musíte se přihlásit ke svému účtu Azure a vybrat centrum IoT hub, na kterém pracujete.

1. V kódu Visual Studia otevřete zobrazení **Průzkumníka.**

1. V dolní části Průzkumníka rozbalte část **Azure IoT Hub.**

   ![Rozšíření části Azure IoT Hub](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Klikněte na **...** v záhlaví oddílu **Azure IoT Hub.** Pokud nevidíte tři tečky, najeďte nad záhlaví.

1. Zvolte **Vybrat centrum IoT .**

1. Pokud nejste přihlášení ke svému účtu Azure, postupujte podle pokynů k tomu.

1. Vyberte své předplatné Azure.

1. Vyberte svůj ioT hub.

## <a name="deploy-to-your-device"></a>Nasazení zařízení

Moduly nasadíte do zařízení použitím manifestu nasazení, který jste nakonfigurovali s informacemi o modulu.

1. V zobrazení Průzkumník kódu Visual Studia rozbalte část **Azure IoT Hub** a pak rozbalte uzel **Zařízení.**

1. Klikněte pravým tlačítkem myši na zařízení IoT Edge, které chcete nakonfigurovat pomocí manifestu nasazení.

    > [!TIP]
    > Chcete-li ověřit, zda je vybrané zařízení zařízení IoT Edge, vyberte ho, chcete-li rozbalit seznam modulů a ověřit přítomnost **$edgeHub** a **$edgeAgent**. Každé zařízení IoT Edge obsahuje tyto dva moduly.

1. Vyberte **Vytvořit nasazení pro jedno zařízení**.

1. Přejděte na soubor JSON manifestu nasazení, který chcete použít, a klepněte na **tlačítko Vybrat manifest nasazení okraje**.

   ![Vybrat manifest nasazení okraje](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Výsledky vašeho nasazení jsou vytištěny ve výstupu Kódu VS. Úspěšná nasazení jsou použita během několika minut, pokud je cílové zařízení spuštěno a připojeno k internetu.

## <a name="view-modules-on-your-device"></a>Zobrazení modulů v zařízení

Po nasazení modulů do zařízení je můžete všechny zobrazit v části **Azure IoT Hub.** Vyberte šipku vedle zařízení IoT Edge a rozbalte ji. Zobrazí se všechny aktuálně spuštěné moduly.

Pokud jste nedávno nasadili nové moduly do zařízení, najeďte na záhlaví oddílu **Zařízení Služby Azure IoT Hub** a vyberte ikonu aktualizace pro aktualizaci zobrazení.

Kliknutím pravým tlačítkem myši na název modulu zobrazíte a upravíte dvojče modulu.

## <a name="next-steps"></a>Další kroky

Naučte se [nasazovat a monitorovat moduly IoT Edge ve velkém měřítku pomocí kódu Visual Studia](how-to-deploy-monitor.md)
