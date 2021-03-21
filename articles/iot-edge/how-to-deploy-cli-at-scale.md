---
title: Nasazení modulů ve velkém měřítku pomocí Azure CLI – Azure IoT Edge
description: Vytvoření automatických nasazení pro skupiny IoT Edgech zařízení pomocí rozšíření IoT pro Azure CLI
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 7b12b6c1cdc85eaba531f34b23aa74bee6b38f7b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201129"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Nasazení a sledování IoT Edgech modulů ve velkém měřítku pomocí rozhraní příkazového řádku Azure

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Pomocí rozhraní příkazového řádku Azure můžete vytvořit **IoT Edge automatické nasazení** , abyste mohli spravovat průběžná nasazení v mnoha zařízeních najednou. Automatická nasazení pro IoT Edge jsou součástí funkce [automatické správy zařízení](../iot-hub/iot-hub-automatic-device-management.md) v IoT Hub. Nasazení jsou dynamické procesy, které umožňují nasadit více modulů na více zařízení, sledovat stav a stav modulů a v případě potřeby provádět změny.

Další informace najdete v tématu [vysvětlení IoT Edge automatického nasazení pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

V tomto článku nastavíte rozhraní příkazového řádku Azure a rozšíření IoT. Pak se dozvíte, jak nasadit moduly do sady IoT Edge zařízení a jak sledovat průběh pomocí dostupných příkazů rozhraní příkazového řádku.

## <a name="prerequisites"></a>Předpoklady

* [IoT Hub](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure.
* Nejméně jedno IoT Edge zařízení.

  Pokud nemáte nastavené zařízení IoT Edge, můžete ho vytvořit na virtuálním počítači Azure. Použijte postup v jednom z článků rychlý Start k [Vytvoření virtuálního zařízení](quickstart-linux.md) se systémem Linux nebo [Vytvoření virtuálního zařízení s Windows](quickstart.md).

* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) ve vašem prostředí. Minimální verze rozhraní příkazového řádku Azure CLI musí být 2.0.70 nebo vyšší. Ke kontrole použijte příkaz `az --version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack.
* [Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension)

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly se mají nasadit, způsob, jakým jsou toky dat mezi moduly a požadované vlastnosti v modulu vlákna. Další informace najdete v tématu [Naučte se nasazovat moduly a navázat trasy v IoT Edge](module-composition.md).

Pokud chcete nasadit moduly pomocí Azure CLI, uložte manifest nasazení lokálně jako soubor. txt. Cestu k souboru použijete v další části, když spustíte příkaz, který aplikuje konfiguraci na vaše zařízení.

Tady je základní manifest nasazení s jedním modulem jako příklad:

>[!NOTE]
>Tento ukázkový manifest nasazení používá schéma verze 1,1 pro agenta IoT Edge a centrum. Verze schématu 1,1 byla vydána společně s IoT Edge verze 1.0.10 a umožňuje funkce, jako je pořadí spouštění modulu a stanovení priorit směrování.

```json
{
  "content": {
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
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.1",
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
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="layered-deployment"></a>Vrstvené nasazení

Navrstvená nasazení představují typ automatického nasazení, který je možné naskládat navzájem na sebe navrchu. Další informace o vrstveném nasazení najdete v tématu [vysvětlení IoT Edge automatického nasazení pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

Navrstvená nasazení je možné vytvářet a spravovat pomocí rozhraní příkazového řádku Azure CLI, jako je jakékoli automatické nasazení, a to jenom s několika rozdíly. Po vytvoření vrstveného nasazení je stejná práce Azure CLI pro vrstvená nasazení stejná jako u libovolného nasazení. Chcete-li vytvořit vrstvené nasazení, přidejte `--layered` příznak do příkazu CREATE.

Druhý rozdíl je v konstrukci manifestu nasazení. Standardní automatické nasazení musí kromě libovolných uživatelských modulů obsahovat i moduly modulu runtime systému, navrstvená nasazení mohou obsahovat pouze uživatelské moduly. Místo toho navrstvená nasazení potřebují na zařízení standardní automatické nasazení, aby bylo možné dodat požadované součásti každého zařízení IoT Edge, jako jsou moduly runtime systému.

Tady je základní navrstvený manifest nasazení s jedním modulem jako příklad:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": ""
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

Předchozí příklad ukázal nastavení vrstveného nasazení `properties.desired` pro modul. Pokud toto navrstvené nasazení cílí na zařízení, kde už byl stejný modul použit, přepíše všechny existující požadované vlastnosti. Chcete-li aktualizovat místo přepsání, požadované vlastnosti, můžete definovat nový pododdíl. Například:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Další informace o konfiguraci vláken modulů v vrstveném nasazení najdete v tématu [vrstvené nasazení](module-deployment-monitoring.md#layered-deployment) .

## <a name="identify-devices-using-tags"></a>Identifikace zařízení pomocí značek

Než budete moct vytvořit nasazení, musíte být schopni určit, která zařízení mají mít vliv. Azure IoT Edge identifikuje zařízení pomocí **značek** v zařízení s dvojitou signalizací. Každé zařízení může mít několik značek, které můžete definovat jakýmkoli způsobem, který dává smysl pro vaše řešení. Pokud například spravujete areály inteligentních budov, můžete do zařízení přidat následující značky:

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

Další informace o tom, jaké jsou vlákna a značky zařízení, najdete [v tématu pochopení a používání nevláken zařízení v IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Vytvoření nasazení

Moduly můžete nasadit do cílových zařízení vytvořením nasazení, které se skládá z manifestu nasazení a také dalších parametrů.

Pomocí příkazu [AZ IoT Edge Deployment Create](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-create) vytvořte nasazení:

```azurecli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

`--layered`K vytvoření vrstveného nasazení použijte stejný příkaz s příznakem.

Příkaz pro vytvoření nasazení má následující parametry:

* **--vrstvený** – volitelný příznak k identifikaci nasazení v podobě vrstveného nasazení.
* **--Deployment-ID** – název nasazení, které se vytvoří ve službě IoT Hub. Dejte vašemu nasazení jedinečný název, který bude obsahovat až 128 malých písmen. Vyhněte se mezerám a následujícími neplatnými znaky: `& ^ [ ] { } \ | " < > /` . Jedná se o požadovaný parametr.
* **--Content** -FilePath pro manifest nasazení JSON. Jedná se o požadovaný parametr.
* **--hub-název** – název centra IoT, ve kterém se nasazení vytvoří. Centrum musí být v aktuálním předplatném. Pomocí příkazu změňte aktuální předplatné `az account set -s [subscription name]` .
* **--Labels** – přidejte popisky, které vám pomůžou sledovat vaše nasazení. Popisky jsou názvy, páry hodnot, které popisují vaše nasazení. Pro názvy a hodnoty mají popisky formát JSON. Například `{"HostPlatform":"Linux", "Version:"3.0.1"}`.
* **--target-Condition** -zadejte cílovou podmínku, abyste zjistili, která zařízení budou cílem tohoto nasazení. Podmínka je založena na nevyhovujících značkách zařízení nebo na dohlášených vlastnostech zařízení, které se musí shodovat s formátem výrazu. Například, `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--priority** – kladné celé číslo. V případě, že dva nebo více nasazení cílí na stejné zařízení, bude použito nasazení s nejvyšší číselnou hodnotou priority.
* **--metriky** – vytvoří metriky, které dotazují vlastnosti edgeHub hlášené na sledování stavu nasazení. Metriky přebírají vstup JSON nebo FilePath. Například, `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

Informace o monitorování nasazení pomocí rozhraní příkazového řádku Azure najdete v tématu [monitorování nasazení IoT Edge](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli).

## <a name="modify-a-deployment"></a>Úprava nasazení

Když upravíte nasazení, změny se okamžitě replikují na všechna cílová zařízení.

Pokud aktualizujete cílovou podmínku, dojde k následujícím aktualizacím:

* Pokud zařízení nesplňuje starou cílovou podmínku, ale splňuje novou cílovou podmínku a toto nasazení je nejvyšší prioritou pro toto zařízení, pak se toto nasazení aplikuje na zařízení.
* Pokud zařízení aktuálně spuštěné v tomto nasazení už nesplňuje cílovou podmínku, odinstaluje toto nasazení a provede nasazení s další nejvyšší prioritou.
* Pokud zařízení aktuálně spuštěné toto nasazení už nesplňuje cílovou podmínku a nesplňuje cílovou podmínku žádného jiného nasazení, neproběhne na zařízení žádná změna. Zařízení pokračuje v běhu svých aktuálních modulů v aktuálním stavu, ale už se nespravuje jako součást tohoto nasazení. Jakmile splňuje cílovou podmínku jakéhokoli jiného nasazení, odinstaluje toto nasazení a provede nový.

Nemůžete aktualizovat obsah nasazení, který zahrnuje moduly a trasy definované v manifestu nasazení. Pokud chcete aktualizovat obsah nasazení, provedete to tak, že vytvoříte nové nasazení, které cílí na stejná zařízení s vyšší prioritou. Můžete upravit některé vlastnosti existujícího modulu, včetně cílové podmínky, popisků, metrik a priority.

Pomocí příkazu [AZ IoT Edge Deployment Update](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-update) aktualizujte nasazení:

```azurecli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Příkaz pro aktualizaci nasazení má následující parametry:

* **--Deployment-ID** – název nasazení, které existuje ve službě IoT Hub.
* **--hub – název** – název centra IoT, ve kterém existuje nasazení. Centrum musí být v aktuálním předplatném. Přepněte na požadované předplatné pomocí příkazu. `az account set -s [subscription name]`
* **--set** -aktualizuje vlastnost v nasazení. Můžete aktualizovat tyto vlastnosti:
  * targetCondition – například `targetCondition=tags.location.state='Oregon'`
  * popisky
  * upřednostněn
* **--Přidat** – přidá do nasazení novou vlastnost, včetně cílových podmínek nebo popisků.
* **--Remove** -odebere existující vlastnost, včetně cílových podmínek nebo popisků.

## <a name="delete-a-deployment"></a>Odstranění nasazení

Při odstranění nasazení převezmou všechna zařízení nasazení s další nejvyšší prioritou. Pokud zařízení nesplňuje cílovou podmínku jakéhokoli jiného nasazení, moduly se po odstranění nasazení neodeberou.

Pomocí příkazu [AZ IoT Edge Deployment Delete](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-delete) odstraňte nasazení:

```azurecli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

Příkaz pro odstranění nasazení má následující parametry:

* **--Deployment-ID** – název nasazení, které existuje ve službě IoT Hub.
* **--hub – název** – název centra IoT, ve kterém existuje nasazení. Centrum musí být v aktuálním předplatném. Přepněte na požadované předplatné pomocí příkazu. `az account set -s [subscription name]`

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [nasazení modulů do zařízení IoT Edge](module-deployment-monitoring.md).