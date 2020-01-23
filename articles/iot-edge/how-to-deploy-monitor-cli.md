---
title: Nasazení modulů ve velkém měřítku pomocí Azure CLI – Azure IoT Edge
description: Použití rozšíření IoT pro Azure CLI k vytvoření automatického nasazení pro skupiny služby IoT Edge zařízení
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fffe1ebda0103b3ed2cd8f76642ecb2967d23069
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510290"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Nasazení a monitorování modulů IoT Edge ve velkém měřítku pomocí Azure CLI

Pomocí rozhraní příkazového řádku Azure můžete vytvořit **IoT Edge automatické nasazení** , abyste mohli spravovat průběžná nasazení v mnoha zařízeních najednou. Automatická nasazení pro IoT Edge jsou součástí funkce [automatické správy zařízení](/azure/iot-hub/iot-hub-automatic-device-management) v IoT Hub. Nasazení jsou dynamické procesy, které umožňují nasadit více modulů na více zařízení, sledovat stav a stav modulů a v případě potřeby provádět změny.

Další informace najdete v tématu [vysvětlení IoT Edge automatického nasazení pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

V tomto článku se nastavení rozhraní příkazového řádku Azure a rozšíření IoT. Pak se dozvíte, jak nasadit moduly do sady IoT Edge zařízení a jak sledovat průběh pomocí dostupných příkazů rozhraní příkazového řádku.

## <a name="cli-prerequisites"></a>Požadavky na rozhraní příkazového řádku

* [Služby IoT hub](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure.
* [Zařízení IoT Edge](how-to-register-device.md#prerequisites-for-the-azure-cli) nainstalován modul runtime IoT Edge.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Minimálně musí být vaše Azure CLI verze 2.0.24 nebo novější. Ke kontrole použijte příkaz `az --version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack.
* [Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly chcete nasadit, tok dat mezi moduly a požadované vlastnosti dvojčat modulů. Další informace najdete v tématu [Naučte se nasazovat moduly a navázat trasy v IoT Edge](module-composition.md).

Můžete nasadit moduly pomocí Azure CLI, uložte jako soubor .txt místně manifest nasazení. Cestu k souboru použijete v další části, když spustíte příkaz, který aplikuje konfiguraci na vaše zařízení.

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

## <a name="layered-deployment"></a>Vrstvené nasazení

Navrstvená nasazení představují typ automatického nasazení, který je možné naskládat navzájem na sebe navrchu. Další informace o vrstveném nasazení najdete v tématu [vysvětlení IoT Edge automatického nasazení pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

Navrstvená nasazení je možné vytvářet a spravovat pomocí rozhraní příkazového řádku Azure CLI, jako je jakékoli automatické nasazení, a to jenom s několika rozdíly. Po vytvoření vrstveného nasazení je stejná práce Azure CLI pro vrstvená nasazení stejná jako u libovolného nasazení. Chcete-li vytvořit vrstvené nasazení, přidejte příznak `--layered` do příkazu CREATE.

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

Před vytvořením nasazení, budete muset mít k určení zařízení, která chcete ovlivnit. Azure IoT Edge identifikuje zařízení pomocí **značky** ve dvojčeti zařízení. Každé zařízení může mít několik značek, které můžete definovat jakýmkoli způsobem, který dává smysl pro vaše řešení. Například pokud spravujete areálu Chytré budovy, můžete přidat následující značky k zařízení:

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

Pomocí příkazu [AZ IoT Edge Deployment Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) vytvořte nasazení:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

K vytvoření vrstveného deploymet použijte stejný příkaz s příznakem `--layered`.

Příkaz pro vytvoření nasazení má následující parametry:

* **--vrstvený** – volitelný příznak k identifikaci nasazení v podobě vrstveného nasazení.
* **– id nasazení** – název nasazení, které se vytvoří ve službě IoT hub. Zadejte jedinečný název, který je malá písmena až 128 vašeho nasazení. Vyhněte se mezery a následující neplatné znaky: `& ^ [ ] { } \ | " < > /`. Jedná se o požadovaný parametr.
* **--obsah** -Filepath nasazení manifestu JSON. Jedná se o požadovaný parametr.
* **– název centra** – název služby IoT hub, ve kterém se vytvoří nasazení. Centrum musí být v rámci aktuálního předplatného. Pomocí příkazu `az account set -s [subscription name]` změňte své aktuální předplatné.
* **--popisky** -přidat popisky pro sledování vašich nasazení. Popisky jsou název, páry hodnota, která popisují vaše nasazení. Pro názvy a hodnoty mají popisky formát JSON. Například `{"HostPlatform":"Linux", "Version:"3.0.1"}`.
* **--cílovou podmínku** – zadejte cílovou podmínku k určení zařízení, která budou cílem s tímto nasazením. Podmínka je založena na nevyhovujících značkách zařízení nebo na dohlášených vlastnostech zařízení, které se musí shodovat s formátem výrazu. Například `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--priority** -kladné celé číslo. V případě, že dvě nebo víc nasazení cílí na stejném zařízení, budou platit nasazení s nejvyšší číselnou hodnotou priority.
* **--metriky** – vytvoří metriky, které dotazují vlastnosti edgeHub hlášené na sledování stavu nasazení. Metriky přebírají vstup JSON nebo FilePath. Například, `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

## <a name="monitor-a-deployment"></a>Monitorování nasazení

Pomocí příkazu [AZ IoT Edge Deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) zobrazíte podrobnosti o jednom nasazení:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Příkaz pro zobrazení nasazení má následující parametry:

* **– id nasazení** – název nasazení, který existuje ve službě IoT hub. Jedná se o požadovaný parametr.
* **– název centra** – název služby IoT hub, ve které existuje nasazení. Centrum musí být v rámci aktuálního předplatného. Přepnutí na požadované předplatné příkazem `az account set -s [subscription name]`

Zkontrolujte nasazení v příkazovém okně. Vlastnost **metriky** uvádí počet pro každou metriku, která je vyhodnocována jednotlivými rozbočovači:

* **targetedCount** – systém metriku, která určuje počet dvojčat zařízení ve službě IoT Hub, která splňují cílovou podmínku.
* **appliedCount** -metrika systému určuje počet zařízení, která jste využili obsahu nasazení u jejich dvojčaty modulů ve službě IoT Hub.
* **reportedSuccessfulCount** – metrika zařízení, která určuje počet IoT Edge zařízení v rámci generování sestav nasazení v IoT Edge modulu runtime klienta.
* **reportedFailedCount** – metrika zařízení, která určuje počet IoT Edge zařízení v hlášení nasazení, které se nezdařily z modulu runtime klienta IoT Edge.

Seznam ID zařízení nebo objektů pro každou z těchto metrik můžete zobrazit pomocí příkazu [AZ IoT Edge Deployment show-metric](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) :

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

Příkaz pro zobrazení metriky nasazení má následující parametry:

* **– id nasazení** – název nasazení, který existuje ve službě IoT hub.
* **--metrika-ID** – název metriky, pro kterou chcete zobrazit seznam ID zařízení, například `reportedFailedCount`.
* **– název centra** – název služby IoT hub, ve které existuje nasazení. Centrum musí být v rámci aktuálního předplatného. Přepněte na požadované předplatné s příkazem `az account set -s [subscription name]`.

## <a name="modify-a-deployment"></a>Upravit nasazení

Při úpravě nasazení změny se okamžitě replikují do všechna cílová zařízení.

Pokud aktualizujete cílovou podmínku, dojde k následující aktualizace:

* Pokud nesplnilo původní cílovou podmínku zařízení, ale splňuje novou cílovou podmínku a toto nasazení je nejvyšší prioritou pro dané zařízení, se použije toto nasazení do zařízení.
* Pokud zařízení aktuálně s tímto nasazením již splňuje cílovou podmínku, dojde k odinstalování tohoto nasazení a provede na další nejvyšší prioritu nasazení.
* Pokud se zařízení aktuálně s tímto nasazením již splňuje cílovou podmínku a nesplňuje cílová podmínka všechna nasazení, pak nedošlo k žádné změně na zařízení. Zařízení pokračuje její aktuální moduly v jejich aktuální stav, ale jako součást tohoto nasazení už nespravuje. Jakmile splňuje cílovou podmínku jakékoli jiné nasazení, dojde k odinstalování tohoto nasazení a provede na novou.

Nemůžete aktualizovat obsah nasazení, který zahrnuje moduly a trasy definované v manifestu nasazení. Pokud chcete aktualizovat obsah nasazení, provedete to tak, že vytvoříte nové nasazení, které cílí na stejná zařízení s vyšší prioritou. Můžete upravit některé vlastnosti existujícího modulu, včetně cílové podmínky, popisků, metrik a priority.

Pomocí příkazu [AZ IoT Edge Deployment Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) aktualizujte nasazení:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Příkaz pro aktualizaci nasazení má následující parametry:

* **– id nasazení** – název nasazení, který existuje ve službě IoT hub.
* **– název centra** – název služby IoT hub, ve které existuje nasazení. Centrum musí být v rámci aktuálního předplatného. Přepnutí na požadované předplatné příkazem `az account set -s [subscription name]`
* **– Nastavte** – umožňuje aktualizovat vlastnost v nasazení. Můžete aktualizovat následujícími vlastnostmi:
  * targetCondition – například `targetCondition=tags.location.state='Oregon'`
  * popisky
  * priorita
* **--Přidat** – přidá do nasazení novou vlastnost, včetně cílových podmínek nebo popisků.
* **--Remove** -odebere existující vlastnost, včetně cílových podmínek nebo popisků.

## <a name="delete-a-deployment"></a>Odstranit nasazení

Při odstranění nasazení nějaká zařízení provést další nejvyšší prioritu nasazení. Pokud vaše zařízení nesplňuje cílová podmínka jakékoli jiné nasazení, moduly se neodeberou při nasazení se odstraní.

Pomocí příkazu [AZ IoT Edge Deployment Delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) odstraňte nasazení:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

Příkaz pro odstranění nasazení má následující parametry:

* **– id nasazení** – název nasazení, který existuje ve službě IoT hub.
* **– název centra** – název služby IoT hub, ve které existuje nasazení. Centrum musí být v rámci aktuálního předplatného. Přepnutí na požadované předplatné příkazem `az account set -s [subscription name]`

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [nasazení modulů do zařízení IoT Edge](module-deployment-monitoring.md).
