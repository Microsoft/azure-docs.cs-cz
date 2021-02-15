---
title: Nasazení modulů z Visual Studio Code-Azure IoT Edge
description: Pomocí Visual Studio Code s nástroji Azure IoT nahrajte modul IoT Edge z IoT Hub na vaše IoT Edge zařízení, jak je nakonfigurované v manifestu nasazení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6a6415aa55a67b37d9564398eb77dacb48cf16f0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378107"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Nasazení Azure IoT Edgech modulů z Visual Studio Code

Jakmile vytvoříte IoT Edge moduly s obchodní logikou, chcete je nasadit do svých zařízení, aby fungovaly na hraničních zařízeních. Pokud máte více modulů, které spolupracují při shromažďování a zpracování dat, můžete je nasadit najednou a deklarovat pravidla směrování, která je spojují.

Tento článek ukazuje, jak vytvořit manifest nasazení JSON a pak ho použít k nahrání nasazení do zařízení IoT Edge. Informace o vytvoření nasazení, které cílí na více zařízení na základě jejich sdílených značek, najdete v tématu [nasazení IoT Edgech modulů ve velkém měřítku pomocí Visual Studio Code](how-to-deploy-vscode-at-scale.md).

## <a name="prerequisites"></a>Požadavky

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.
* Zařízení IoT Edge

  Pokud nemáte nastavené zařízení IoT Edge, můžete ho vytvořit na virtuálním počítači Azure. Použijte postup v jednom z článků rychlý Start k [Vytvoření virtuálního zařízení](quickstart-linux.md) se systémem Linux nebo [Vytvoření virtuálního zařízení s Windows](quickstart.md).

* [Visual Studio Code](https://code.visualstudio.com/).
* [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) pro Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly se mají nasadit, způsob, jakým jsou toky dat mezi moduly a požadované vlastnosti v modulu vlákna. Další informace o tom, jak manifesty nasazení fungují a jak je vytvořit, najdete v tématu [Vysvětlení způsobu použití, konfigurace a](module-composition.md)opětovného použití modulů IoT Edge.

Chcete-li nasadit moduly pomocí Visual Studio Code, uložte manifest nasazení místně jako. Soubor JSON. Cestu k souboru použijete v další části, když spustíte příkaz pro použití konfigurace na zařízení.

Tady je základní manifest nasazení s jedním modulem jako příklad:

>[!NOTE]
>Tento ukázkový manifest nasazení používá schéma verze 1,1 pro agenta IoT Edge a centrum. Verze schématu 1,1 byla vydána společně s IoT Edge verze 1.0.10 a umožňuje funkce, jako je pořadí spouštění modulu a stanovení priorit směrování.

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.1",
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
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
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
           "schemaVersion": "1.1",
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

## <a name="sign-in-to-access-your-iot-hub"></a>Přihlaste se, abyste měli přístup ke službě IoT Hub.

Pomocí rozšíření Azure IoT pro Visual Studio Code můžete provádět operace se službou IoT Hub. Aby tyto operace fungovaly, musíte se přihlásit ke svému účtu Azure a vybrat službu IoT Hub, na které pracujete.

1. V Visual Studio Code otevřete zobrazení **Průzkumníka** .

1. V dolní části Průzkumníka rozbalte část **Azure IoT Hub** .

   ![Rozbalte oddíl Azure IoT Hub.](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Klikněte na **...** v hlavičce oddílu **Azure IoT Hub** . Pokud se tři tečky nevidí, najeďte myší na záhlaví.

1. Zvolte **vybrat IoT Hub**.

1. Pokud nejste přihlášení ke svému účtu Azure, postupujte podle pokynů.

1. Vyberte své předplatné Azure.

1. Vyberte centrum IoT.

## <a name="deploy-to-your-device"></a>Nasazení zařízení

Moduly se nasazují do zařízení pomocí manifestu nasazení, který jste nakonfigurovali s informacemi o modulu.

1. V zobrazení Průzkumník Visual Studio Code rozbalte část **Azure IoT Hub** a potom rozbalte uzel **zařízení** .

1. Pravým tlačítkem myši klikněte na zařízení IoT Edge, které chcete nakonfigurovat pomocí manifestu nasazení.

    > [!TIP]
    > Pokud chcete potvrdit, že zařízení, které jste zvolili, je IoT Edge zařízení, vyberte ho a rozbalte seznam modulů a ověřte přítomnost **$edgeHub** a **$edgeAgent**. Každé zařízení IoT Edge zahrnuje tyto dva moduly.

1. Vyberte **vytvořit nasazení pro jedno zařízení**.

1. Přejděte do souboru JSON manifestu nasazení, který chcete použít, a klikněte na **Vybrat manifest nasazení Edge**.

   ![Vybrat manifest nasazení Edge](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Výsledky nasazení se vytisknou ve výstupu VS Code. Úspěšná nasazení se během několika minut aplikují, pokud je cílové zařízení spuštěné a připojené k Internetu.

## <a name="view-modules-on-your-device"></a>Zobrazit moduly na zařízení

Až nasadíte moduly do svého zařízení, můžete je zobrazit v části **Azure IoT Hub** . Vyberte šipku vedle zařízení IoT Edge a rozbalte ji. Zobrazí se všechny aktuálně běžící moduly.

Pokud jste v nedávné době nasadili nové moduly na zařízení, najeďte myší na záhlaví oddílu **zařízení Azure IoT Hub** a výběrem ikony Aktualizovat aktualizujte zobrazení.

Kliknutím pravým tlačítkem myši na název modulu zobrazíte a upravíte modul s dvojitou platností.

## <a name="next-steps"></a>Další kroky

Naučte se [, jak nasadit a monitorovat IoT Edge moduly ve velkém rozsahu pomocí Visual Studio Code](how-to-deploy-at-scale.md)
