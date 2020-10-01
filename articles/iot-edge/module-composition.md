---
title: Nasazení modulu & trasy s manifesty nasazení – Azure IoT Edge
description: Přečtěte si, jak manifest nasazení deklaruje, které moduly se mají nasadit, jak je nasadit a jak mezi nimi vytvořit směrování zpráv.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7a9f4f165f457dfb902a4c0ecce3f4a9b13e2ec8
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91611533"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Nasazování modulů a vytváření tras ve službě IoT Edge

Každé zařízení IoT Edge spouští alespoň dva moduly: $edgeAgent a $edgeHub, které jsou součástí modulu runtime IoT Edge. Zařízení IoT Edge může spustit více dalších modulů pro libovolný počet procesů. Pomocí manifestu nasazení sdělte vašemu zařízení, které moduly se mají nainstalovat, a jak je nakonfigurovat tak, aby společně spolupracovaly.

*Manifest nasazení* je dokument JSON, který popisuje:

* Modul **IoT Edge agenta** je vyzdvojený, což zahrnuje tři komponenty:
  * Obrázek kontejneru pro každý modul, který běží na zařízení.
  * Přihlašovací údaje pro přístup k privátním registrům kontejnerů, které obsahují image modulů.
  * Pokyny, jak by se měl každý modul vytvářet a spravovat.
* Modul **IoT Edge hub** je nevlákenný, což zahrnuje způsob toku zpráv mezi moduly a nakonec IoT Hub.
* Požadované vlastnosti všech dalších vláken modulu (volitelné).

Všechna IoT Edge zařízení musí být nakonfigurována pomocí manifestu nasazení. Nově instalovaný IoT Edge modul runtime hlásí kód chyby, dokud není nakonfigurován s platným manifestem.

V kurzech Azure IoT Edge sestavíte manifest nasazení prostřednictvím Průvodce na portálu Azure IoT Edge. Můžete také použít manifest nasazení programově pomocí REST nebo sady SDK služby IoT Hub. Další informace najdete v tématu [vysvětlení nasazení IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Vytvoření manifestu nasazení

Manifest nasazení je na vysoké úrovni seznamem vláken modulu, které jsou nakonfigurovány s požadovanými vlastnostmi. Manifest nasazení oznamuje IoT Edge zařízení (nebo skupině zařízení), které moduly se mají nainstalovat a jak je nakonfigurovat. Manifesty nasazení obsahují *požadované vlastnosti* pro každý modul s dvojitou vlastností. IoT Edge zařízení hlásí zpět *ohlášené vlastnosti* pro každý modul.

V každém manifestu nasazení jsou vyžadovány dva moduly: `$edgeAgent` a `$edgeHub` . Tyto moduly jsou součástí modulu runtime IoT Edge, který spravuje zařízení IoT Edge a moduly, které jsou v něm spuštěné. Další informace o těchto modulech naleznete v tématu [pochopení IoT Edge runtime a jeho architektury](iot-edge-runtime.md).

Kromě těchto dvou běhových modulů můžete přidat až 50 moduly, které mají být spouštěny na IoT Edgem zařízení.

Manifest nasazení, který obsahuje pouze modul runtime IoT Edge (edgeAgent a edgeHub), je platný.

Manifesty nasazení se řídí touto strukturou:

```json
{
    "modulesContent": {
        "$edgeAgent": { // required
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": { //required
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "module1": {  // optional
            "properties.desired": {
                // desired properties of module1
            }
        },
        "module2": {  // optional
            "properties.desired": {
                // desired properties of module2
            }
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Konfigurovat moduly

Definujte způsob, jakým modul runtime IoT Edge nainstaluje moduly do nasazení. Agent IoT Edge je komponenta modulu runtime, která spravuje instalaci, aktualizace a vytváření sestav o stavu pro IoT Edge zařízení. Proto modul $edgeAgent s dvojitou příponou obsahuje informace o konfiguraci a správě všech modulů. Tyto informace zahrnují parametry konfigurace pro agenta IoT Edge.

Úplný seznam vlastností, které mohou nebo musí být zahrnuté, najdete v tématu [vlastnosti IoT Edge agenta a centra IoT Edge](module-edgeagent-edgehub.md).

Vlastnosti $edgeAgent se řídí touto strukturou:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "module1": { // optional
                // configuration and management details
            },
            "module2": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Deklarovat trasy

Centrum IoT Edge spravuje komunikaci mezi moduly, IoT Hub a všemi koncovými zařízeními. Proto modul $edgeHub s dvojitou přesností obsahuje požadovanou vlastnost nazvanou *trasy* , která deklaruje, jak jsou zprávy předávány v rámci nasazení. Můžete mít několik tras v rámci stejného nasazení.

Trasy jsou deklarovány v **$edgeHub** požadovaných vlastností s následující syntaxí:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "route1": "FROM <source> WHERE <condition> INTO <sink>",
            "route2": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Každá trasa potřebuje zdroj a jímku, ale podmínka je volitelným kamenem, který můžete použít k filtrování zpráv.

### <a name="source"></a>Zdroj

Zdroj Určuje, odkud zprávy pocházejí. IoT Edge může směrovat zprávy z modulů nebo na listových zařízeních.

Pomocí sad SDK pro IoT můžou moduly deklarovat konkrétní výstupní fronty pro své zprávy pomocí třídy ModuleClient. Výstupní fronty nejsou nutné, ale jsou užitečné pro správu více tras. Koncová zařízení můžou používat třídu DeviceClient sad IoT SDK k posílání zpráv do zařízení IoT Edge brány stejným způsobem, jakým odesílají zprávy IoT Hub. Další informace najdete v tématu [pochopení a používání sad Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).

Zdrojovou vlastností může být libovolná z následujících hodnot:

| Zdroj | Popis |
| ------ | ----------- |
| `/*` | Všechna zprávy typu zařízení-Cloud nebo dopředné oznámení o změnách z libovolného modulu nebo na listovém zařízení |
| `/twinChangeNotifications` | Všechny nedokončené změny (hlášené vlastnosti) pocházející z libovolného modulu nebo ze zařízení v listech |
| `/messages/*` | Jakákoli zpráva typu zařízení-Cloud, kterou modul odesílá prostřednictvím nějakého nebo žádného výstupu, nebo na listovém zařízení |
| `/messages/modules/*` | Jakákoli zpráva typu zařízení-Cloud, kterou modul odesílá pomocí nějakého nebo žádného výstupu |
| `/messages/modules/<moduleId>/*` | Jakákoli zpráva typu zařízení-Cloud odeslaná konkrétním modulem prostřednictvím nějakého nebo žádného výstupu |
| `/messages/modules/<moduleId>/outputs/*` | Jakákoli zpráva typu zařízení-Cloud odeslaná konkrétním modulem prostřednictvím nějakého výstupu |
| `/messages/modules/<moduleId>/outputs/<output>` | Jakákoli zpráva typu zařízení-Cloud odeslaná konkrétním modulem prostřednictvím konkrétního výstupu |

### <a name="condition"></a>Stav

Podmínka je v deklaraci trasy volitelná. Pokud chcete předat všechny zprávy ze zdroje do jímky, stačí opustit klauzuli **WHERE** úplně. Nebo můžete použít [jazyk dotazů IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md) k filtrování určitých zpráv nebo typů zpráv, které podmínku splní. Trasy IoT Edge nepodporují filtrování zpráv na základě dvojitých značek nebo vlastností.

Zprávy, které jsou předávány mezi moduly v IoT Edge jsou formátovány stejně jako zprávy, které jsou předávány mezi vašimi zařízeními a službou Azure IoT Hub. Všechny zprávy jsou formátovány jako JSON a mají parametry **systemProperties**, **appProperties**a **text** .

Dotazy můžete vytvářet kolem libovolného ze tří parametrů s následující syntaxí:

* Vlastnosti systému: `$<propertyName>` nebo `{$<propertyName>}`
* Vlastnosti aplikace: `<propertyName>`
* Vlastnosti textu: `$body.<propertyName>`

Příklady, jak vytvářet dotazy na vlastnosti zpráv, najdete v tématu [výrazy dotazů směrování zpráv ze zařízení do cloudu](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Příkladem, který je specifický pro IoT Edge, je, že chcete vyfiltrovat zprávy doručené do zařízení brány ze zařízení typu list. Zprávy, které pocházejí z modulů, zahrnují vlastnost System s názvem **connectionModuleId**. Takže pokud chcete směrovat zprávy ze zařízení na listech přímo na IoT Hub, pomocí následujícího postupu vylučte zprávy modulu:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Jímka

Jímka definuje, kam se zprávy odesílají. Zprávy mohou přijímat pouze moduly a IoT Hub. Zprávy nelze směrovat do jiných zařízení. Vlastnost jímky neobsahuje žádné možnosti zástupných znaků.

Vlastnost jímky může být libovolná z následujících hodnot:

| Jímka | Popis |
| ---- | ----------- |
| `$upstream` | Odeslat zprávu do IoT Hub |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Odeslání zprávy do konkrétního vstupu konkrétního modulu |

IoT Edge poskytuje záruky alespoň jednou. Centrum IoT Edge ukládá místní zprávy pro případ, že trasa nemůže zprávu doručit do jímky. Pokud se například Centrum IoT Edge nemůže připojit k IoT Hub nebo cílový modul není připojen.

Centrum IoT Edge ukládá zprávy až do doby zadané ve `storeAndForwardConfiguration.timeToLiveSecs` vlastnosti [IoT Edge centra požadované vlastnosti](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definovat nebo aktualizovat požadované vlastnosti

Manifest nasazení určuje požadované vlastnosti pro každý modul nasazený do zařízení IoT Edge. Požadované vlastnosti v manifestu nasazení přepíšou všechny požadované vlastnosti, které jsou aktuálně v modulu vlákna.

Pokud v manifestu nasazení nezadáte požadované vlastnosti nevláken modulu, IoT Hub nebude modul pracovat jakýmkoli způsobem. Místo toho můžete nastavit požadované vlastnosti programově.

Stejné mechanismy, které umožňují změnit vlákna zařízení, se používají ke změně vláken modulu. Další informace najdete v tématu [Průvodce pro vývojáře s vyzdvojeným modulem](../iot-hub/iot-hub-devguide-module-twins.md).

## <a name="deployment-manifest-example"></a>Příklad manifestu nasazení

Následující příklad ukazuje, jak může vypadat platný dokument manifestu nasazení.

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
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
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
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "env": {
              "tempLimit": {"value": "100"}
            },
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
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
          "sensorToFilter": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Další kroky

* Úplný seznam vlastností, které mohou nebo musí být součástí $edgeAgent a $edgeHub, najdete v tématu [Vlastnosti agenta IoT Edge a centra IoT Edge](module-edgeagent-edgehub.md).

* Když teď víte, jak se používají IoT Edge moduly, [Pochopte požadavky a nástroje pro vývoj IoT Edge modulů](module-development.md).
