---
title: Nasazení modulů z Visual Studio Code-Azure IoT Edge
description: Pomocí Visual Studio Code s nástroji Azure IoT nahrajte modul IoT Edge z IoT Hub na vaše IoT Edge zařízení, jak je nakonfigurované v manifestu nasazení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/8/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d8c90c2a13d111d01a1e7a1bd9b63da180621ded
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772087"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Nasadit moduly Azure IoT Edge z Visual Studio Code

Po vytvoření IoT Edge modulů s obchodní logikou, budete chtít nasadit do zařízení pracovat na hraničních zařízeních. Pokud máte více modulů, které vzájemně spolupracují ke shromáždění a zpracování dat, můžete je nasadit všechny najednou a deklarovat pravidla směrování, které je propojují.

Tento článek ukazuje, jak vytvořit manifest nasazení JSON a pak jej použít pro vložení nasazení do zařízení IoT Edge. Informace o vytvoření nasazení, které cílí na více zařízení na základě jejich sdílených značek, najdete v tématu [nasazení IoT Edgech modulů ve velkém měřítku pomocí Visual Studio Code](how-to-deploy-monitor-vscode.md).

## <a name="prerequisites"></a>Požadavky

* [Služby IoT hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.
* [Zařízení IoT Edge](how-to-register-device.md#register-with-visual-studio-code) nainstalován modul runtime IoT Edge.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) pro Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly chcete nasadit, tok dat mezi moduly a požadované vlastnosti dvojčat modulů. Další informace o způsobu práce manifesty nasazení a o tom, k jejich vytvoření najdete v tématu [pochopit, jak můžete použít moduly IoT Edge a způsob jejich konfiguraci a znovu použít](module-composition.md).

Pokud chcete nasadit moduly pomocí Visual Studio Code, uložit manifest nasazení místně jako. Soubor JSON. Použijete cestu k souboru v další části, když spustíte příkaz pro použití v konfiguraci zařízení.

Tady je manifest základní nasazení s jeden modul jako příklad:

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
                 "createOptions": "{}"
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

## <a name="sign-in-to-access-your-iot-hub"></a>Přihlaste se ke službě IoT hub

Rozšíření Azure IoT pro Visual Studio Code můžete použít k provádění operací s centrem IoT. Pro tyto operace pro práci budete muset přihlásit ke svému účtu Azure a vyberte službu IoT hub, kterou právě pracujete.

1. V sadě Visual Studio Code otevřete **Explorer** zobrazení.

1. V dolní části Průzkumníka rozbalte část **Azure IoT Hub** .

   ![Rozbalte oddíl Azure IoT Hub.](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Klikněte na **...** v hlavičce oddílu **Azure IoT Hub** . Pokud nevidíte tři tečky, najeďte myší na záhlaví.

1. Zvolte **vyberte službu IoT Hub**.

1. Pokud nejste přihlášení k účtu Azure, postupujte podle pokynů k tomu.

1. Vyberte své předplatné Azure.

1. Vyberte své Centrum IoT.

## <a name="deploy-to-your-device"></a>Nasazení zařízení

Moduly nasadíte do zařízení s použitím manifestu nasazení, který jste nakonfigurovali pomocí informací o modulech.

1. V zobrazení Průzkumník Visual Studio Code rozbalte část **Azure IoT Hub** a potom rozbalte uzel **zařízení** .

1. Pravým tlačítkem myši klikněte na zařízení IoT Edge, které chcete nakonfigurovat pomocí manifestu nasazení.

    > [!TIP]
    > Pokud chcete potvrdit, že zařízení, které jste zvolili, je IoT Edge zařízení, vyberte ho a rozbalte seznam modulů a ověřte přítomnost **$edgeHub** a **$edgeAgent**. Každé zařízení IoT Edge zahrnuje tyto dva moduly.

1. Vyberte **vytvoření nasazení pro jedno zařízení**.

1. Přejděte na soubor manifestu nasazení JSON, který chcete použít a klikněte na tlačítko **vyberte Manifest nasazení Edge**.

   ![Manifest nasazení vyberte Edge](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Výsledky nasazení jsou zobrazeny ve výstupu VS Code. Úspěšné nasazení jsou použity během několika minut, pokud cílové zarízení je spuštena a připojený k Internetu.

## <a name="view-modules-on-your-device"></a>Zobrazení modulů ve vašem zařízení

Až nasadíte moduly do svého zařízení, můžete je zobrazit v části **Azure IoT Hub** . Vyberte šipku vedle zařízení IoT Edge a rozbalte ho. Zobrazí se všechny aktuálně spuštěné moduly.

Pokud se nedávno nové moduly nasazené na zařízení, najeďte myší **zařízení Azure IoT Hub** záhlaví a vyberte ikonu aktualizace a aktualizace zobrazení.

Klikněte pravým tlačítkem na název modulu zobrazení a úprava dvojčete modulu.

## <a name="next-steps"></a>Další kroky

Naučte se [, jak nasadit a monitorovat IoT Edge moduly ve velkém rozsahu pomocí Visual Studio Code](how-to-deploy-monitor.md)
