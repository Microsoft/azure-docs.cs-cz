---
title: Nasazení Azure IoT Edge moduly (VS Code) | Microsoft Docs
description: K nasazení modulů na IoT hraniční zařízení použít Visual Studio Code
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7e75f2ff5e2df3189683d084a315ad6c8730be84
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035780"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Nasazení moduly Azure IoT Edge z Visual Studio Code

Po vytvoření IoT Edge moduly s obchodní logiky, budete chtít nasadit do zařízení tak, aby provoz na hranici. Pokud máte více modulů, které vzájemně spolupracují ke sběru a zpracování dat, můžete je nasadit všechny najednou a deklarovat pravidla směrování, které je propojují. 

Tento článek ukazuje, jak vytvořit JSON manifestu nasazení, pak tento soubor použít k nasazení IoT hraniční zařízení. Informace o vytváření nasazení, které cílí více zařízení podle jejich sdílené značky najdete v tématu [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Požadavky

* [Služby IoT hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure. 
* [IoT hraniční zařízení](how-to-register-device-portal.md) s modulem runtime IoT Edge nainstalována. 
* [Visual Studio Code](https://code.visualstudio.com/).
* [Rozšíření Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pro Visual Studio Code. 

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifest nasazení

Manifest nasazení je dokument JSON, který popisuje, jaké moduly pro nasazení, tok dat mezi moduly a požadované vlastnosti dvojčata modulu. Další informace o tom, jak manifesty nasazení pracovních a jak při jejich vytváření najdete v tématu [pochopit, jak IoT Edge moduly můžete použít, nakonfigurovat a znovu použít](module-composition.md).

Pokud chcete nasadit modulů pomocí Visual Studio Code, uložte manifest nasazení místně jako. Soubor JSON. Použijete cestu k souboru v další části, když spustíte příkaz pro použití v konfiguraci zařízení.

Jako příklad uvádíme manifest základní nasazení s jeden modul:

   ```json
   {
     "moduleContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {
                 "registryName": {
                   "username": "",
                   "password": "",
                   "address": ""
                 }
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
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
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

## <a name="sign-in-to-access-your-iot-hub"></a>Přihlaste se k službě IoT hub.

Azure IoT rozšíření pro Visual Studio Code můžete použít k provádění operací službou IoT hub. Pro tyto operace pro práci potřebujete přihlásit k účtu Azure a vyberte službu IoT hub, kterou právě pracujete.

1. Otevřete v sadě Visual Studio Code **Explorer** zobrazení.

2. V dolní části v Exploreru, rozbalte **zařízení Azure IoT Hub** části. 

   ![Rozbalte zařízení Azure IoT Hub](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

3. Klikněte na **...**  v **zařízení Azure IoT Hub** záhlaví části. Pokud nevidíte se třemi tečkami, pozastavte ukazatel myši nad záhlaví. 

4. Zvolte **vyberte IoT Hub**.

5. Pokud nejste přihlášení k účtu Azure, postupujte podle pokynů k tomu. 

6. Vyberte své předplatné Azure. 

7. Vyberte služby IoT hub. 


## <a name="deploy-to-your-device"></a>Nasadit do zařízení

Můžete nasadit moduly pro zařízení s použitím manifest nasazení, který jste nakonfigurovali s informacemi o modulu. 

1. V zobrazení Průzkumníka Visual Studio Code, rozbalte **zařízení Azure IoT Hub** části. 

2. Klikněte pravým tlačítkem myši na zařízení, které chcete nakonfigurovat manifest nasazení. 

3. Vyberte **vytvořit nasazení pro IoT hraniční zařízení**. 

4. Přejděte na soubor manifestu nasazení JSON, kterou chcete použít a klikněte na tlačítko **vyberte Manifest nasazení Edge**. 

   ![Vyberte okraj – Manifest nasazení](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)


Výsledky nasazení jsou vytištěny ve výstupu VS Code. Úspěšné nasazení jsou použita během několika minut, pokud běží cílovém zařízení a připojení k Internetu. 

## <a name="view-modules-on-your-device"></a>Zobrazení modulů ve vašem zařízení

Jakmile nasadíte modulů do svého zařízení můžete zobrazit všechny z nich **zařízení Azure IoT Hub** části. Vyberte šipku vedle zařízení IoT Edge rozbalte ho. Zobrazí se všechny aktuálně spuštěné moduly. 

Pokud jste nedávno nasadili nové moduly do zařízení, přejděte myší **zařízení Azure IoT Hub** záhlaví a vyberte ikonu aktualizace můžete zobrazení aktualizovat. 

Klikněte pravým tlačítkem na název modulu pro zobrazení a úpravy twin modulu. 

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nasazení a monitorování modulů IoT Edge ve velkém měřítku](how-to-deploy-monitor.md)