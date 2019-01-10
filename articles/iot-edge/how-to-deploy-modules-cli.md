---
title: Nasadit moduly z příkazového řádku – Azure IoT Edge | Dokumentace Microsoftu
description: Použití rozšíření IoT pro Azure CLI můžete nasadit moduly do zařízení IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 766b51f208e7e8f4a49109e32864f2726b8ccd63
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156438"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Nasadit moduly Azure IoT Edge pomocí Azure CLI

Po vytvoření IoT Edge modulů s obchodní logikou, budete chtít nasadit do zařízení pracovat na hraničních zařízeních. Pokud máte více modulů, které vzájemně spolupracují ke shromáždění a zpracování dat, můžete je nasadit všechny najednou a deklarovat pravidla směrování, které je propojují.

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) je open source pro různé platformy nástroj příkazového řádku pro správu prostředků Azure, jako je například IoT Edge. Umožňuje spravovat prostředky Azure IoT Hub, instance služby device provisioning a propojená centra úprav. Nové rozšíření IoT vylepšuje Azure CLI s funkcemi, jako je Správa zařízení a úplné možnosti služby IoT Edge.

Tento článek ukazuje, jak vytvořit manifest nasazení JSON a pak jej použít pro vložení nasazení do zařízení IoT Edge. Informace o vytváření nasazení, které cílí na více zařízeních na základě jejich sdílené značek, naleznete v tématu [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Požadavky

* [Služby IoT hub](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure.
* [Zařízení IoT Edge](how-to-register-device-cli.md) nainstalován modul runtime IoT Edge.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Minimálně musí být vaše Azure CLI verze 2.0.24 nebo novější. Ke kontrole použijte příkaz `az –-version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack.
* [Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly chcete nasadit, tok dat mezi moduly a požadované vlastnosti dvojčat modulů. Další informace o způsobu práce manifesty nasazení a o tom, k jejich vytvoření najdete v tématu [pochopit, jak můžete použít moduly IoT Edge a způsob jejich konfiguraci a znovu použít](module-composition.md).

Můžete nasadit moduly pomocí Azure CLI, uložte jako soubor .json místně manifest nasazení. Použijete cestu k souboru v další části, když spustíte příkaz pro použití v konfiguraci zařízení.

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

## <a name="deploy-to-your-device"></a>Nasazení zařízení

Moduly nasadíte do zařízení s použitím manifestu nasazení, který jste nakonfigurovali pomocí informací o modulech.

Změňte adresář na složku, ve kterém je uložený manifestu nasazení. Pokud jste použili jedno z šablony VS Code IoT Edge, použijte `deployment.json` ve **config** složky vašeho adresáře řešení, ne `deployment.template.json` souboru.

Chcete-li použít konfiguraci pro zařízení IoT Edge, použijte následující příkaz:

   ```cli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Parametr id zařízení rozlišuje velká a malá písmena. Obsahu parametr odkazuje na nasazení manifestu soubor, který jste uložili.

   ![AZ iot edge sada modulů výstupu](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Zobrazení modulů ve vašem zařízení

Po nasazení modulů do svého zařízení, můžete zobrazit všechny z nich pomocí následujícího příkazu:

Zobrazení modulů v zařízení IoT Edge:

   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Parametr id zařízení rozlišuje velká a malá písmena.

   ![AZ iot hub modul identity výstupu](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md)
