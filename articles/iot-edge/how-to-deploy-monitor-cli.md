---
title: Nasazení a monitorování modulů pro Azure IoT Edge (CLI) | Dokumentace Microsoftu
description: Správa modulů, které běží na hraničních zařízeních
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 07/25/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a3d073e9fd7c535ea84d6e4dbbf8003a6c55725b
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394608"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Nasazení a monitorování modulů IoT Edge ve velkém měřítku pomocí Azure CLI

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

Azure IoT Edge umožňuje přesunout analytics na hraničních zařízeních a poskytuje cloudové rozhraní, takže můžete spravovat a monitorovat zařízení IoT Edge vzdáleně. Možnost vzdáleně spravovat zařízení se nabývá na důležitosti jako řešení Internetu věcí rostou větší a složitější. Azure IoT Edge je navržená pro podporu vašich obchodních cílů, bez ohledu na to, kolik zařízení je přidat.

Můžete spravovat jednotlivá zařízení a nasaďte na ně moduly postupně po jednom. Ale pokud budete chtít provést změny na zařízení ve velkém měřítku, můžete vytvořit **automatického nasazení IoT Edge**, který je součástí Automatická správa zařízení ve službě IoT Hub. Nasazení jsou dynamické procesy, které vám umožní nasadit více modulů najednou pro víc zařízení, sledovat stav a stav modulů a provést změny, pokud je to nezbytné. 

V tomto článku se nastavení rozhraní příkazového řádku Azure a rozšíření IoT. Potom se dozvíte, jak nasadit moduly na sadu zařízení IoT Edge a sledovat průběh pomocí příkazů rozhraní příkazového řádku k dispozici.

## <a name="cli-prerequisites"></a>Požadavky na rozhraní příkazového řádku

* [Služby IoT hub](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure. 
* [Zařízení IoT Edge](how-to-register-device-cli.md) nainstalován modul runtime IoT Edge.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Minimálně musí být vaše Azure CLI verze 2.0.24 nebo novější. Ke kontrole použijte příkaz `az –-version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack. 
* [Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly chcete nasadit, tok dat mezi moduly a požadované vlastnosti dvojčat modulů. Další informace o způsobu práce manifesty nasazení a o tom, k jejich vytvoření najdete v tématu [pochopit, jak můžete použít moduly IoT Edge a způsob jejich konfiguraci a znovu použít](module-composition.md).

Můžete nasadit moduly pomocí Azure CLI, uložte jako soubor .txt místně manifest nasazení. Použijete cestu k souboru v další části, když spustíte příkaz pro použití v konfiguraci zařízení. 

Tady je manifest základní nasazení s jeden modul jako příklad:

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


## <a name="identify-devices-using-tags"></a>Identifikace zařízení pomocí značek

Před vytvořením nasazení, budete muset mít k určení zařízení, která chcete ovlivnit. Azure IoT Edge identifikuje zařízení pomocí **značky** ve dvojčeti zařízení. Každé zařízení může mít více značek a můžete je definovat způsobem, který dává smysl pro vaše řešení. Například pokud spravujete areálu Chytré budovy, můžete přidat následující značky k zařízení:

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

Další informace o značky a dvojčata zařízení, najdete v části [principy a použití dvojčat zařízení ve službě IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Vytvoření nasazení

Můžete nasadit moduly pro cílová zařízení vytvořením nasazení, které se skládá z manifestu nasazení, stejně jako ostatní parametry. 

Vytvoření nasazení pomocí následujícího příkazu:

   ```cli
   az iot edge deployment create --deployment-id [deployment id] --labels [labels] --content [file path] --hub-name [hub name] --target-condition [target query] --priority [int]
   ```

* **– id nasazení** – název nasazení, které se vytvoří ve službě IoT hub. Zadejte jedinečný název, který je malá písmena až 128 vašeho nasazení. Vyhněte se mezery a následující neplatné znaky: `& ^ [ ] { } \ | " < > /`.
* **--popisky** -přidat popisky pro sledování vašich nasazení. Popisky jsou název, páry hodnota, která popisují vaše nasazení. Například `HostPlatform, Linux` nebo `Version, 3.0.1`
* **--obsah** -Filepath nasazení manifestu JSON. 
* **– název centra** – název služby IoT hub, ve kterém se vytvoří nasazení. Centrum musí být v rámci aktuálního předplatného. Přepnutí na požadované předplatné příkazem `az account set -s [subscription name]`
* **--cílovou podmínku** – zadejte cílovou podmínku k určení zařízení, která budou cílem s tímto nasazením. Podmínka je založen na značky dvojčat zařízení nebo ohlášené vlastnosti dvojčete zařízení a by měl odpovídat formátu výrazu. Například `tags.environment='test'` nebo `properties.reported.devicemodel='4000x'`. 
* **--priority** -kladné celé číslo. V případě, že dvě nebo víc nasazení cílí na stejném zařízení, budou platit nasazení s nejvyšší číselnou hodnotou priority.

## <a name="monitor-a-deployment"></a>Monitorování nasazení

K zobrazení obsahu nasazení použijte následující příkaz:

   ```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
   ```
* **– id nasazení** – název nasazení, který existuje ve službě IoT hub.
* **– název centra** – název služby IoT hub, ve které existuje nasazení. Centrum musí být v rámci aktuálního předplatného. Přepnutí na požadované předplatné příkazem `az account set -s [subscription name]`

Zkontrolujte nasazení v příkazovém okně. **Metriky** počet pro každou metriku, která se vyhodnocuje na základě každé centrum seznamů vlastností:
* **targetedCount** – systém metriku, která určuje počet dvojčat zařízení ve službě IoT Hub, která splňují cílovou podmínku.
* **appliedCount** -metrika systému určuje počet zařízení, která jste využili obsahu nasazení u jejich dvojčaty modulů ve službě IoT Hub.
* **reportedSuccessfulCount** -metriku zařízení, která určuje počet hraničních zařízení v nasazení generování sestav úspěšnost modul runtime IoT Edge klienta.
* **reportedFailedCount** -metriku zařízení, která určuje počet hraničních zařízení v nasazení generování sestav chyby z modulu runtime IoT Edge klienta.

Pomocí následujícího příkazu můžete zobrazit seznam ID zařízení nebo objekty pro každý z metrik:

   ```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
   ```

* **– id nasazení** – název nasazení, který existuje ve službě IoT hub.
* **– id metrika** – název metriky, pro kterou chcete zobrazit seznam zařízení ID, například `reportedFailedCount`
* **– název centra** – název služby IoT hub, ve které existuje nasazení. Centrum musí být v rámci aktuálního předplatného. Přepnutí na požadované předplatné příkazem `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Upravit nasazení

Při úpravě nasazení změny se okamžitě replikují do všechna cílová zařízení. 

Pokud aktualizujete cílovou podmínku, dojde k následující aktualizace:
* Pokud nesplnilo původní cílovou podmínku zařízení, ale splňuje novou cílovou podmínku a toto nasazení je nejvyšší prioritou pro dané zařízení, se použije toto nasazení do zařízení. 
* Pokud zařízení aktuálně s tímto nasazením již splňuje cílovou podmínku, dojde k odinstalování tohoto nasazení a provede na další nejvyšší prioritu nasazení. 
* Pokud se zařízení aktuálně s tímto nasazením již splňuje cílovou podmínku a nesplňuje cílová podmínka všechna nasazení, pak nedošlo k žádné změně na zařízení. Zařízení pokračuje její aktuální moduly v jejich aktuální stav, ale jako součást tohoto nasazení už nespravuje. Jakmile splňuje cílovou podmínku jakékoli jiné nasazení, dojde k odinstalování tohoto nasazení a provede na novou. 

Použijte následující příkaz k aktualizaci nasazení:

   ```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
   ```
* **– id nasazení** – název nasazení, který existuje ve službě IoT hub.
* **– název centra** – název služby IoT hub, ve které existuje nasazení. Centrum musí být v rámci aktuálního předplatného. Přepnutí na požadované předplatné příkazem `az account set -s [subscription name]`
* **– Nastavte** – umožňuje aktualizovat vlastnost v nasazení. Můžete aktualizovat následujícími vlastnostmi:
    * targetCondition – například `targetCondition=tags.location.state='Oregon'`
    * popisky 
    * priorita


## <a name="delete-a-deployment"></a>Odstranit nasazení

Při odstranění nasazení nějaká zařízení provést další nejvyšší prioritu nasazení. Pokud vaše zařízení nesplňuje cílová podmínka jakékoli jiné nasazení, moduly se neodeberou při nasazení se odstraní. 

Pomocí následujícího příkazu odstraňte nasazení:

   ```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
   ```
* **– id nasazení** – název nasazení, který existuje ve službě IoT hub.
* **– název centra** – název služby IoT hub, ve které existuje nasazení. Centrum musí být v rámci aktuálního předplatného. Přepnutí na požadované předplatné příkazem `az account set -s [subscription name]`

## <a name="next-steps"></a>Další postup

Další informace o [moduly nasazení do hraničních zařízení](module-deployment-monitoring.md).
