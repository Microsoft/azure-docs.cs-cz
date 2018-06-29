---
title: Nasazení a monitorování modulů pro Azure IoT okraj (CLI) | Microsoft Docs
description: Správa modulů, které se spouštějí na hraniční zařízení
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/07/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3dfb0fe0227fdd0ff1a43cb7b0a89eb9d3e066f4
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097933"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Nasazení a monitorování modulů IoT Edge ve velkém měřítku pomocí rozhraní příkazového řádku Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

Azure IoT Edge umožňuje přesunout analytics na okraj a poskytuje rozhraní, které je cloudu, takže můžete spravovat a monitorovat vaši IoT hraniční zařízení bez nutnosti fyzicky přístup každé z nich. Možnost vzdáleně spravovat zařízení je velmi důležité jako řešení Internetu věcí stále roste větší a složitější. Azure IoT okraj je navržen pro podporu obchodních cílů, bez ohledu na to, kolik zařízení přidáte.

Můžete spravovat jednotlivých zařízení a nasadit modulů na jeden po druhém. Ale pokud chcete provést změny zařízení ve velkém měřítku, můžete vytvořit **IoT okraj automatického nasazení**, který je součástí Automatická správa zařízení IoT hub. Nasazení jsou dynamické procesy, které vám umožní nasadit více modulů do více zařízení najednou, sledovat stav a stav moduly a provést změny podle potřeby. 

V tomto článku nastavíte Azure CLI 2.0 a rozšíření IoT. Pak zjistíte, jak nasadit moduly k sadě IoT hraniční zařízení a sledovat průběh pomocí dostupné příkazy rozhraní příkazového řádku.

## <a name="cli-prerequisites"></a>Požadavky rozhraní příkazového řádku

* [Služby IoT hub](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure. 
* [Zařízení IoT Edge](how-to-register-device-cli.md) s modulem runtime IoT Edge nainstalována.
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Vaše verze Azure CLI 2.0 musí být minimálně 2.0.24 nebo novější. Ke kontrole použijte příkaz `az –-version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack. 
* [IoT rozšíření pro Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifest nasazení

Manifest nasazení je dokument JSON, který popisuje, jaké moduly pro nasazení, tok dat mezi moduly a požadované vlastnosti dvojčata modulu. Další informace o tom, jak manifesty nasazení pracovních a jak při jejich vytváření najdete v tématu [pochopit, jak IoT Edge moduly můžete použít, nakonfigurovat a znovu použít](module-composition.md).

Pokud chcete nasadit modulů pomocí Azure CLI 2.0, uložte místně manifest nasazení jako soubor .txt. Použijete cestu k souboru v další části, když spustíte příkaz pro použití v konfiguraci zařízení. 

Jako příklad uvádíme manifest základní nasazení s jeden modul:

   ```json
   {
        "content": {
         "modulesContent": {
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
   }
   ```


## <a name="identify-devices-using-tags"></a>Identifikovat zařízení pomocí značek

Před vytvořením nasazení, budete muset určit zařízení, která chcete ovlivnit. Azure IoT Edge identifikuje zařízení pomocí **značky** v dvojče zařízení. Každé zařízení může mít více značek a jejich můžete definovat způsobem, který dává smysl pro vaše řešení. Například pokud spravujete kanceláře inteligentní budov, může přidat těmito značkami k zařízení:

```json
"tags":{
    "location":{
        "building": "20",
        "floor": "2"
    },
    "roomtype": "conference",
    "environment": "prod"
}
```

Další informace o značky a dvojčata zařízení najdete v tématu [Rady pro pochopení a použití dvojčata zařízení IoT hub][lnk-device-twin].

## <a name="create-a-deployment"></a>Vytvoření nasazení

Moduly je nasadit do cílových zařízení vytvořením nasazení, které se skládá z manifestu nasazení, jakož i další parametry. 

K vytvoření nasazení použijte následující příkaz:

   ```cli
   az iot edge deployment create --deployment-id [deployment id] --labels [labels] --content [file path] --hub-name [hub name] --target-condition [target query] --priority [int]
   ```

* **– id nasazení** -název nasazení, které se vytvoří ve službě IoT hub. Zadejte jedinečný název, který je malá písmena až 128 vaše nasazení. Vyhněte se mezery a následující neplatné znaky: `& ^ [ ] { } \ | " < > /`.
* **--popisky** -přidat popisky, abyste mohli snadněji sledovat vaše nasazení. Popisky jsou název, hodnotu páry, které popisují vaše nasazení. Například `HostPlatform, Linux` nebo `Version, 3.0.1`
* **--obsah** -Filepath do nasazení manifest JSON. 
* **– název centra** -název centra IoT, ve kterém se vytvoří nasazení. Rozbočovače musí být v aktuálním předplatném. Přepnout na požadované předplatné pomocí příkazu `az account set -s [subscription name]`
* **--cílovou podmínku** -zadejte cílovou podmínku k určení zařízení, která budou cílem v tomto nasazení. Je založena na zařízení twin značky nebo dvojče zařízení potřeby vlastnosti a musí odpovídat formátu výraz. Například `tags.environment='test'` nebo `properties.desired.devicemodel='4000x'`. 
* **--priority** -kladné celé číslo. V případě, že dvě nebo víc nasazení se budou zaměřovat na stejném zařízení, uplatní se nasazení s nejvyšší číselnou hodnotou priority.

## <a name="monitor-a-deployment"></a>Monitorování nasazení

K zobrazení obsahu nasazení použijte následující příkaz:

   ```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
   ```
* **– id nasazení** -název nasazení, které existuje ve službě IoT hub.
* **– název centra** -název centra IoT, ve kterém nasazení existuje. Rozbočovače musí být v aktuálním předplatném. Přepnout na požadované předplatné pomocí příkazu `az account set -s [subscription name]`

Zkontrolujte nasazení v příkazovém okně. **Metriky** počet pro jednotlivé metriky, který je vyhodnocován každého rozbočovače seznamů vlastností:
* **targetedCount** -metrika systému, která určuje počet dvojčata zařízení IoT hub, které splňují podmínku cílení.
* **appliedCount** -metrika systému určuje počet zařízení, která má obsahu nasazení u jejich modulu dvojčata IoT hub.
* **reportedSuccessfulCount** -metrika zařízení, která určuje počet hraniční zařízení v nasazení generování sestav úspěch z modulu runtime klienta IoT hraniční.
* **reportedFailedCount** -metrika zařízení, která určuje počet hraniční zařízení v nasazení generování sestav chyby z modulu runtime klienta IoT okraj.

Pro každou z metriky můžete zobrazit seznam ID zařízení nebo objekty pomocí následujícího příkazu:

   ```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
   ```

* **– id nasazení** -název nasazení, které existuje ve službě IoT hub.
* **– id metrika** – název metriky, pro který chcete zobrazit seznam zařízení ID, například `reportedFailedCount`
* **– název centra** -název centra IoT, ve kterém nasazení existuje. Rozbočovače musí být v aktuálním předplatném. Přepnout na požadované předplatné pomocí příkazu `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Upravit nasazení

Při úpravě nasazení změny okamžitě replikovat do všech cílových zařízení. 

Pokud aktualizujete cílovou podmínku, provedou se následující aktualizace:
* Pokud zařízení nesplnilo původní cílovou podmínku, ale splňuje novou cílovou podmínku a toto nasazení je nejvyšší prioritou pro toto zařízení, toto nasazení se použijí pro zařízení. 
* Pokud zařízení aktuálně spuštěných toto nasazení už splňuje cílovou podmínku, odinstaluje toto nasazení a trvá na další nasazení nejvyšší prioritou. 
* Pokud zařízení aktuálně spuštěných toto nasazení už splňuje cílovou podmínku a nesplňuje cílovou podmínku další nasazení, žádná změna probíhá na zařízení. Pokračuje její aktuální moduly v jejich aktuálního stavu zařízení, ale není spravován jako součást tohoto nasazení už. Jakmile splňuje cílovou podmínku další nasazení, odinstaluje toto nasazení a trvá v novém. 

Použijte následující příkaz k aktualizaci nasazení:

   ```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
   ```
* **– id nasazení** -název nasazení, které existuje ve službě IoT hub.
* **– název centra** -název centra IoT, ve kterém nasazení existuje. Rozbočovače musí být v aktuálním předplatném. Přepnout na požadované předplatné pomocí příkazu `az account set -s [subscription name]`
* **– Nastavte** -aktualizujte vlastnost v nasazení. Můžete aktualizovat následující vlastnosti:
    * targetCondition – např. `targetCondition=tags.location.state='Oregon'`
    * popisky 
    * priorita


## <a name="delete-a-deployment"></a>Odstranění nasazení

Při odstranění nasazení aplikace na jejich další nejvyšší prioritou nasazení trvat žádná zařízení. Pokud vaše zařízení nesplňují cílovou podmínku další nasazení, moduly nejsou odebrány při odstranění nasazení. 

Pokud chcete odstranit nasazení použijte následující příkaz:

   ```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
   ```
* **– id nasazení** -název nasazení, které existuje ve službě IoT hub.
* **– název centra** -název centra IoT, ve kterém nasazení existuje. Rozbočovače musí být v aktuálním předplatném. Přepnout na požadované předplatné pomocí příkazu `az account set -s [subscription name]`

## <a name="next-steps"></a>Další postup

Další informace o [nasazení modulů do hraniční zařízení][lnk-deployments].

<!-- Images -->
[1]: ./media/how-to-deploy-monitor/iot-edge-deployments.png

<!-- Links -->
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-portal]: https://portal.azure.com
[lnk-docker-create]: https://docs.docker.com/engine/reference/commandline/create/
[lnk-deployments]: module-deployment-monitoring.md

<!-- Anchor links -->
[anchor-monitor]: #monitor-a-deployment
