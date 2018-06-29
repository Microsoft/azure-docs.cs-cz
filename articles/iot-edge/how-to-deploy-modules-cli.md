---
title: Nasazení Azure IoT Edge moduly (CLI) | Microsoft Docs
description: Použijte k nasazení modulů na IoT hraniční zařízení IoT rozšíření pro Azure CLI 2.0
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/08/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 98a4be02188f7e0462979792a6061d535a64a18d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37095969"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli-20"></a>Nasazení Azure IoT Edge modulů s Azure CLI 2.0

Po vytvoření IoT Edge moduly s obchodní logiky, budete chtít nasadit do zařízení tak, aby provoz na hranici. Pokud máte více modulů, které vzájemně spolupracují ke sběru a zpracování dat, můžete je nasadit všechny najednou a deklarovat pravidla směrování, které je propojují. 

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) je open source nástroj příkazového řádku pro různé platformy určený ke správě prostředků Azure, jako je služba IoT Edge. Umožňuje spravovat prostředky Azure IoT Hub, instance služby zřizování zařízení a propojené centra mimo pole. Nové rozšíření IoT vylepšuje rozhraní Azure CLI 2.0 o funkce, jako je správa zařízení a plně funkční IoT Edge.

Tento článek ukazuje, jak vytvořit JSON manifestu nasazení, pak tento soubor použít k nasazení IoT hraniční zařízení. Informace o vytváření nasazení, které cílí více zařízení podle jejich sdílené značky najdete v tématu [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Požadavky

* [Služby IoT hub](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure. 
* [IoT hraniční zařízení](how-to-register-device-cli.md) s modulem runtime IoT Edge nainstalována.
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Vaše verze Azure CLI 2.0 musí být minimálně 2.0.24 nebo novější. Ke kontrole použijte příkaz `az –-version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack. 
* [IoT rozšíření pro Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifest nasazení

Manifest nasazení je dokument JSON, který popisuje, jaké moduly pro nasazení, tok dat mezi moduly a požadované vlastnosti dvojčata modulu. Další informace o tom, jak manifesty nasazení pracovních a jak při jejich vytváření najdete v tématu [pochopit, jak IoT Edge moduly můžete použít, nakonfigurovat a znovu použít](module-composition.md).

Pokud chcete nasadit modulů pomocí Azure CLI 2.0, uložte místně manifest nasazení jako soubor .txt. Použijete cestu k souboru v další části, když spustíte příkaz pro použití v konfiguraci zařízení. 

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

## <a name="deploy-to-your-device"></a>Nasadit do zařízení

Můžete nasadit moduly pro zařízení s použitím manifest nasazení, který jste nakonfigurovali s informacemi o modulu. 

Použít konfiguraci IoT hraniční zařízení, použijte následující příkaz:

   ```cli
   az iot hub apply-configuration --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Parametr id zařízení rozlišuje velká a malá písmena. Obsahu parametr odkazuje na nasazení manifestu souboru, který jste uložili. 

## <a name="view-modules-on-your-device"></a>Zobrazení modulů ve vašem zařízení

Jakmile nasadíte modulů do svého zařízení můžete zobrazit všechny z nich pomocí následujícího příkazu: 

Zobrazení modulů v zařízení IoT Edge:
    
   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Parametr id zařízení rozlišuje velká a malá písmena.

   ![Seznam modulů](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nasazení a monitorování modulů IoT Edge ve velkém měřítku](how-to-deploy-monitor.md)