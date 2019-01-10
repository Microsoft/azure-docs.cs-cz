---
title: Nasadit moduly z Visual Studio Code – Azure IoT Edge | Dokumentace Microsoftu
description: Můžete nasadit moduly do zařízení IoT Edge pomocí Visual Studio Code
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 91a074cf98291b105864a69730314efff3482254
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54155010"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Nasadit moduly Azure IoT Edge z Visual Studio Code

Po vytvoření IoT Edge modulů s obchodní logikou, budete chtít nasadit do zařízení pracovat na hraničních zařízeních. Pokud máte více modulů, které vzájemně spolupracují ke shromáždění a zpracování dat, můžete je nasadit všechny najednou a deklarovat pravidla směrování, které je propojují.

Tento článek ukazuje, jak vytvořit manifest nasazení JSON a pak jej použít pro vložení nasazení do zařízení IoT Edge. Informace o vytváření nasazení, které cílí na více zařízeních na základě jejich sdílené značek, naleznete v tématu [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Požadavky

* [Služby IoT hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.
* [Zařízení IoT Edge](how-to-register-device-portal.md) nainstalován modul runtime IoT Edge.
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
             "tempSensor": {
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
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "tempSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>Přihlaste se ke službě IoT hub

Rozšíření Azure IoT pro Visual Studio Code můžete použít k provádění operací s centrem IoT. Pro tyto operace pro práci budete muset přihlásit ke svému účtu Azure a vyberte službu IoT hub, kterou právě pracujete.

1. V sadě Visual Studio Code otevřete **Explorer** zobrazení.

1. V dolní části Průzkumníku, rozbalte **zařízení Azure IoT Hub** oddílu.

   ![Rozbalte část zařízení Azure IoT Hub](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Klikněte na **...**  v **zařízení Azure IoT Hub** hlavičku oddílu. Pokud nevidíte tři tečky, najeďte myší na záhlaví.

1. Zvolte **vyberte službu IoT Hub**.

1. Pokud nejste přihlášení k účtu Azure, postupujte podle pokynů k tomu.

1. Vyberte své předplatné Azure.

1. Vyberte své Centrum IoT.

## <a name="deploy-to-your-device"></a>Nasazení zařízení

Moduly nasadíte do zařízení s použitím manifestu nasazení, který jste nakonfigurovali pomocí informací o modulech.

1. V zobrazení Průzkumníka Visual Studio Code, rozbalte **zařízení Azure IoT Hub** oddílu.

1. Klikněte pravým tlačítkem na zařízení IoT Edge, které chcete nakonfigurovat s manifestem nasazení.

    > [!TIP]
    > Abyste se ujistili, že zařízení, kterou jste zvolili zařízení IoT Edge, vyberte ji a rozbalte seznam modulů a ověřte existenci **$edgeHub** a **$edgeAgent**. Každé zařízení IoT Edge zahrnuje tyto dva moduly.

1. Vyberte **vytvoření nasazení pro jedno zařízení**.

1. Přejděte na soubor manifestu nasazení JSON, který chcete použít a klikněte na tlačítko **vyberte Manifest nasazení Edge**.

   ![Manifest nasazení vyberte Edge](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Výsledky nasazení jsou zobrazeny ve výstupu VS Code. Úspěšné nasazení jsou použity během několika minut, pokud cílové zarízení je spuštena a připojený k Internetu.

## <a name="view-modules-on-your-device"></a>Zobrazení modulů ve vašem zařízení

Po nasazení modulů do svého zařízení, můžete zobrazit všechny z nich **zařízení Azure IoT Hub** oddílu. Vyberte šipku vedle zařízení IoT Edge a rozbalte ho. Zobrazí se všechny aktuálně spuštěné moduly.

Pokud se nedávno nové moduly nasazené na zařízení, najeďte myší **zařízení Azure IoT Hub** záhlaví a vyberte ikonu aktualizace a aktualizace zobrazení.

Klikněte pravým tlačítkem na název modulu zobrazení a úprava dvojčete modulu.

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md)
