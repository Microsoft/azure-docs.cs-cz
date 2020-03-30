---
title: Nasazení modulů ve velkém měřítku pomocí rozhraní příkazového příkazu Azure – Azure IoT Edge
description: Použití rozšíření IoT pro Azure CLI k vytvoření automatických nasazení pro skupiny zařízení IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9152b38a0155b610f39f7de239bcc377ad96be5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271470"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Nasazení a monitorování modulů IoT Edge ve velkém měřítku pomocí rozhraní příkazového příkazu Azure

Vytvořte **automatické nasazení IoT Edge** pomocí rozhraní příkazového řádku Azure pro správu probíhajících nasazení pro mnoho zařízení najednou. Automatická nasazení pro IoT Edge jsou součástí funkce [automatické správy zařízení](/azure/iot-hub/iot-hub-automatic-device-management) služby IoT Hub. Nasazení jsou dynamické procesy, které umožňují nasadit více modulů do více zařízení, sledovat stav a stav modulů a v případě potřeby provádět změny.

Další informace najdete [v tématu Principy automatického nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

V tomto článku nastavíte Azure CLI a rozšíření IoT. Pak se dozvíte, jak nasadit moduly do sady zařízení IoT Edge a sledovat průběh pomocí dostupných příkazů rozhraní příkazového příkazu.

## <a name="cli-prerequisites"></a>Požadavky cli

* Centrum [IoT v](../iot-hub/iot-hub-create-using-cli.md) předplacenéazure.
* [Zařízení IoT Edge](how-to-register-device.md#prerequisites-for-the-azure-cli) s nainstalovaným runtimem IoT Edge.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Minimálně vaše verze Azure CLI musí být 2.0.70 nebo vyšší. Ke kontrole použijte příkaz `az --version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack.
* [Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly nasadit, jak toky dat mezi moduly a požadované vlastnosti dvojčat modulu. Další informace najdete v [tématu Naučte se nasazovat moduly a navazovat trasy v IoT Edge](module-composition.md).

Chcete-li nasadit moduly pomocí rozhraní příkazového příkazu Azure, uložte manifest nasazení místně jako soubor TXT. Cestu k souboru v další části použijete při spuštění příkazu k použití konfigurace zařízení.

Zde je základní manifest nasazení s jedním modulem jako příklad:

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
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
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

## <a name="layered-deployment"></a>Nasazení s vrstvami

Vrstvené nasazení jsou typ automatického nasazení, který lze stohovat na sebe. Další informace o nasazeních s vrstvami najdete [v tématu Principy automatických nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

Vrstvená nasazení lze vytvářet a spravovat pomocí azure cli jako každé automatické nasazení, s několika rozdíly. Po vytvoření vrstvené nasazení, stejné Azure CLI práce pro vrstvené nasazení stejné jako jakékoli nasazení. Chcete-li vytvořit nasazení s `--layered` vrstvami, přidejte příznak do příkazu create.

Druhý rozdíl je v konstrukci manifestu nasazení. Zatímco standardní automatické nasazení musí obsahovat moduly modulů modulů runtime systému kromě všech uživatelských modulů, vrstvená nasazení mohou obsahovat pouze uživatelské moduly. Místo toho vrstvené nasazení potřebují standardní automatické nasazení být na zařízení, také dodávat požadované součásti každého zařízení IoT Edge, jako jsou moduly runtime systému.

Zde je základní vrstvené nasazení manifest s jedním modulem jako příklad:

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

Předchozí příklad ukázal vrstvené `properties.desired` nasazení nastavení pro modul. Pokud by toto vrstvené nasazení cílilo na zařízení, kde byl již použit stejný modul, přepsalo by všechny existující požadované vlastnosti. Chcete-li aktualizovat, namísto přepsání požadovaných vlastností, můžete definovat novou podsekci. Například:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Další informace o konfiguraci dvojčat modulů v nasazeních s vrstvami naleznete v tématu [Layered deployment](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>Identifikace zařízení pomocí značek

Před vytvořením nasazení musíte být schopni určit, která zařízení chcete ovlivnit. Azure IoT Edge identifikuje zařízení pomocí **značek** v dvojčeti zařízení. Každé zařízení může mít více značek, které definujete jakýmkoli způsobem, který dává smysl pro vaše řešení. Pokud například spravujete kampus inteligentních budov, můžete do zařízení přidat následující značky:

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

Další informace o dvojčata zařízení a značky, [najdete v tématu Principy a použití dvojčatzařízení v centru IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Vytvoření nasazení

Nasadíte moduly do cílových zařízení vytvořením nasazení, které se skládá z manifestu nasazení, jakož i další parametry.

Pomocí příkazu [az iot edge deployment create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) vytvořte nasazení:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Použijte stejný příkaz `--layered` s příznakem k vytvoření layered deploymet.

Příkaz create nasazení má následující parametry:

* **--layered** - Volitelný příznak k identifikaci nasazení jako vrstvené nasazení.
* **--deployment-id** - Název nasazení, který bude vytvořen v centru IoT. Pojmenujte nasazení jedinečný název, který je až 128 malá písmena. Vyhněte se mezerám `& ^ [ ] { } \ | " < > /`a následujícím neplatným znakům: . Jedná se o požadovaný parametr.
* **--content** - Filepath do manifestu nasazení JSON. Jedná se o požadovaný parametr.
* **--název centra** – název služby IoT hub, ve kterém bude nasazení vytvořeno. Centrum musí být v aktuálním předplatném. Změňte aktuální předplatné `az account set -s [subscription name]` pomocí příkazu.
* **--labels** - Přidejte popisky, které vám pomohou sledovat vaše nasazení. Popisky jsou Název, Dvojice hodnot, které popisují vaše nasazení. Popisky přebírají formátování JSON pro názvy a hodnoty. Například `{"HostPlatform":"Linux", "Version:"3.0.1"}`.
* **--target-condition** – Zadejte cílovou podmínku k určení, která zařízení budou cílem tohoto nasazení.Podmínka je založena na značky dvojčete zařízení nebo dvojče zařízení hlášené vlastnosti a by měla odpovídat formátu výrazu.Například, `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--priority** - Kladné celé číslo. V případě, že dvě nebo více nasazení jsou zaměřeny na stejné zařízení, nasazení s nejvyšší číselnou hodnotou priority bude platit.
* **--metrics** – Vytvořte metriky, které dotaz u vlastností oznamované edgeHub sledovat stav nasazení. Metriky trvat Vstup JSON nebo filepath. Například, `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

## <a name="monitor-a-deployment"></a>Sledování nasazení

Pomocí příkazu [az iot edge deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) zobrazíte podrobnosti o jednom nasazení:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Příkaz deployment show má následující parametry:

* **--deployment-id** - Název nasazení, který existuje v centru IoT. Jedná se o požadovaný parametr.
* **--název centra** – název služby IoT hub, ve kterém existuje nasazení. Centrum musí být v aktuálním předplatném. Přepněte na požadované předplatné pomocí příkazu`az account set -s [subscription name]`

Zkontrolujte nasazení v příkazovém okně.Vlastnost **metriky** uvádí počet pro každou metriku, která je vyhodnocována každým centrem:

* **targetedCount** - systémová metrika, která určuje počet dvojčat zařízení v centru IoT Hub, které odpovídají podmínce cílení.
* **appliedCount** - systémová metrika určuje počet zařízení, u kterých byl obsah nasazení použit pro dvojčata modulů v centru IoT Hub.
* **reportedSuccessfulCount** - metrika zařízení, která určuje počet zařízení IoT Edge v systému hlášení o úspěchu z runtime klienta IoT Edge.
* **reportedFailedCount** - Metrika zařízení, která určuje počet zařízení IoT Edge v selhání hlášení nasazení z runtime klienta IoT Edge.

Seznam ID zařízení nebo objektů pro každou z metrik můžete zobrazit pomocí příkazu [az iot edge deployment show-metric:](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric)

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

Příkaz show-metric nasazení má následující parametry:

* **--deployment-id** - Název nasazení, který existuje v centru IoT.
* **--metric-id** - Název metriky, pro kterou chcete zobrazit seznam ID `reportedFailedCount`zařízení, například .
* **--název centra** – název služby IoT hub, ve kterém existuje nasazení. Centrum musí být v aktuálním předplatném. Přepněte na požadované předplatné `az account set -s [subscription name]`pomocí příkazu .

## <a name="modify-a-deployment"></a>Úprava nasazení

Když upravíte nasazení, změny se okamžitě replikují do všech cílených zařízení.

Pokud aktualizujete cílovou podmínku, dojde k následujícím aktualizacím:

* Pokud zařízení nesplnilo starou cílovou podmínku, ale splňuje novou cílovou podmínku a toto nasazení je pro toto zařízení nejvyšší prioritou, bude toto nasazení použito pro zařízení.
* Pokud zařízení, které aktuálně používá toto nasazení, již nesplňuje cílovou podmínku, odinstaluje toto nasazení a převezme další nasazení s nejvyšší prioritou.
* Pokud zařízení aktuálně spuštěné toto nasazení již nesplňuje cílovou podmínku a nesplňuje cílovou podmínku jiných nasazení, nedojde v zařízení k žádné změně. Zařízení pokračuje ve spuštění svých aktuálních modulů v aktuálním stavu, ale již není spravováno jako součást tohoto nasazení. Jakmile splní cílovou podmínku jiného nasazení, odinstaluje toto nasazení a převezme nové.

Nelze aktualizovat obsah nasazení, který zahrnuje moduly a trasy definované v manifestu nasazení. Pokud chcete aktualizovat obsah nasazení, uděláte to vytvořením nového nasazení, které se zaměřuje na stejná zařízení s vyšší prioritou. Můžete upravit určité vlastnosti existujícího modulu, včetně cílové podmínky, popisky, metriky a prioritu.

K aktualizaci nasazení použijte příkaz [aktualizace nasazení az iot edge:](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update)

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Příkaz aktualizace nasazení má následující parametry:

* **--deployment-id** - Název nasazení, který existuje v centru IoT.
* **--název centra** – název služby IoT hub, ve kterém existuje nasazení. Centrum musí být v aktuálním předplatném. Přepněte na požadované předplatné pomocí příkazu`az account set -s [subscription name]`
* **--set** - Aktualizace vlastnosti v nasazení. Můžete aktualizovat následující vlastnosti:
  * targetCondition - například`targetCondition=tags.location.state='Oregon'`
  * Popisky
  * Prioritou
* **--add** - Přidejte novou vlastnost do nasazení, včetně cílových podmínek nebo popisků.
* **--remove** - Odeberte existující vlastnost, včetně cílových podmínek nebo popisků.

## <a name="delete-a-deployment"></a>Odstranění nasazení

Když odstraníte nasazení, všechna zařízení převezmou další nasazení s nejvyšší prioritou. Pokud vaše zařízení nesplňují cílovou podmínku jiného nasazení, moduly se při odstranění nasazení neodeberou.

Pomocí příkazu [delete nasazení az iot edge odstraňte](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) nasazení:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

Příkaz delete nasazení má následující parametry:

* **--deployment-id** - Název nasazení, který existuje v centru IoT.
* **--název centra** – název služby IoT hub, ve kterém existuje nasazení. Centrum musí být v aktuálním předplatném. Přepněte na požadované předplatné pomocí příkazu`az account set -s [subscription name]`

## <a name="next-steps"></a>Další kroky

Další informace o [nasazení modulů do zařízení IoT Edge](module-deployment-monitoring.md).
