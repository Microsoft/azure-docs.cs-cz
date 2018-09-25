---
title: Složení modulu Azure IoT Edge | Dokumentace Microsoftu
description: Zjistěte, jak manifest nasazení deklaruje které moduly chcete nasadit, jak je nasadit a jak vytvořit směrování zpráv mezi nimi.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a65eb029dbf10b194bd28bf7ad82f5aa839338a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990616"
---
# <a name="learn-how-to-use-deployment-manifests-to-deploy-modules-and-establish-routes"></a>Zjistěte, jak můžete nasadit moduly a vytvářet manifesty nasazení

Každé zařízení IoT Edge běží aspoň dva moduly: $edgeAgent a $edgeHub, které tvoří modul runtime IoT Edge. Kromě těchto dvou standardní libovolného zařízení IoT Edge můžete spustit více modulů k provedení libovolného počtu procesů. Při nasazování těchto modulů do zařízení najednou, budete potřebovat způsob, jak deklarovat, které moduly jsou zahrnuty a jejich vzájemné interakce mezi sebou. 

*Manifest nasazení* je dokument JSON, který popisuje:

* Konfigurace agenta Edge, který obsahuje image kontejneru pro každý modul, přihlašovací údaje do registrů kontejnerů privátní přístup a pokyny, jak by měl vytvářet a spravovat každý modul.
* Konfigurace Centrum Edge, která zahrnuje jak tok zpráv mezi moduly a nakonec do služby IoT Hub.
* Volitelně můžete požadované vlastnosti dvojčat modulů.

Všechna zařízení IoT Edge je potřeba nakonfigurovat s manifestem nasazení. Nově instalovaný modul runtime IoT Edge sestavy kód chyby, dokud nebude nakonfigurován s platným manifestem. 

V kurzech Azure IoT Edge sestavení manifestu nasazení prostřednictvím Průvodce na portálu Azure IoT Edge. Můžete také použít manifest nasazení prostřednictvím kódu programu pomocí REST nebo sady SDK služby IoT Hub. Další informace najdete v tématu [vysvětlení nasazení IoT Edge][lnk-deploy].

## <a name="create-a-deployment-manifest"></a>Vytvoření manifestu nasazení

Na vysoké úrovni manifest nasazení nakonfiguruje požadované vlastnosti dvojčete modulu pro moduly IoT Edge, které jsou nasazené na zařízení IoT Edge. Dva z těchto modulech jsou vždy k dispozici: `$edgeAgent`, a `$edgeHub`.

Manifest nasazení, která obsahuje pouze modul runtime IoT Edge (agent a centra) je neplatný.

Manifest následující tuto strukturu:

```json
{
    "modulesContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": {
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "{module1}": {  // optional
            "properties.desired": {
                // desired properties of module with id {module1}
            }
        },
        "{module2}": {  // optional
            ...
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Konfigurovat moduly

Budete muset zjistit modul runtime IoT Edge, jak nainstalovat moduly ve vašem nasazení. Informace o konfiguraci a správu pro všechny moduly přejde uvnitř **$edgeAgent** požadované vlastnosti. Tyto informace zahrnují parametry konfigurace pro samotný agent Edge. 

Úplný seznam vlastností, které mohou nebo musí být zahrnut, naleznete v tématu [vlastnosti agenta Edge a Centrum Edge](module-edgeagent-edgehub.md).

Vlastnosti $edgeAgent mají následující strukturu:

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
            "{module1}": { // optional
                // configuration and management details
            },
            "{module2}": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Deklarovat trasy

Centrum Edge poskytuje způsob, jak deklarativní směrování zpráv mezi moduly a mezi moduly a IoT Hub. Centrum Edge spravuje veškerá komunikace, tak informace o postupu přejde do **$edgeHub** požadované vlastnosti. Může mít několik tras uvnitř stejného nasazení.

Trasy, které jsou deklarovány v **$edgeHub** požadované vlastnosti s následující syntaxí:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "{route1}": "FROM <source> WHERE <condition> INTO <sink>",
            "{route2}": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Každý směrování vyžaduje zdroje a jímky, ale je podmínka vyhodnocena jako volitelnou informaci, které můžete použít k filtrování zprávy. 


### <a name="source"></a>Zdroj
Zdroj Určuje, odkud pochází zprávy. Může být některý z následujících hodnot:

| Zdroj | Popis |
| ------ | ----------- |
| `/*` | Všechny zprávy typu zařízení cloud z jakéhokoli zařízení nebo modul |
| `/messages/*` | Všechny zprávy typu zařízení cloud odesílaných ze zařízení nebo modul pomocí některé nebo žádné výstupní |
| `/messages/modules/*` | Všechny zprávy typu zařízení cloud modulu pro některé nebo žádný výstup |
| `/messages/modules/{moduleId}/*` | Všechny zprávy typu zařízení cloud odesílaných {moduleId} se žádný výstup |
| `/messages/modules/{moduleId}/outputs/*` | Všechny zprávy typu zařízení cloud odesílaných {ID} modulu se některé výstupem |
| `/messages/modules/{moduleId}/outputs/{output}` | Všechny zprávy typu zařízení cloud odeslané s použitím {moduleId} {výstupní} |

### <a name="condition"></a>Podmínka
Podmínka je volitelné v deklaraci trasy. Pokud chcete předat všechny zprávy z jímka ke zdroji, nechte **kde** klauzule úplně. Nebo můžete použít [dotazovací jazyk služby IoT Hub] [ lnk-iothub-query] k filtrování pro určité zprávy nebo typy zpráv, které splňují zadanou podmínku.

Zprávy, které se předají mezi moduly ve službě IoT Edge jsou formátovány stejně jako zprávy, které předávají mezi zařízeními a Azure IoT Hub. Všechny zprávy jsou formátovány jako dokumenty JSON a mít **systemProperties**, **objekt appProperties**, a **tělo** parametry. 

Můžete vytvářet dotazy kolem všechny tři parametry s následující syntaxí: 

* Vlastnosti systému: `$<propertyName>` nebo `{$<propertyName>}`
* Vlastnosti aplikace: `<propertyName>`
* Vlastnosti textu: `$body.<propertyName>` 

Příklady o tom, jak vytvářet dotazy na vlastnosti zprávy, najdete v článku [trasy výrazech dotazů zprávy typu zařízení cloud](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Příklad, který je specifický pro IoT Edge je, když chcete filtrovat zprávy, které byly přijaty zařízení brány ze zařízení typu list. Zprávy, které pocházejí z modulů obsahují systém vlastnost s názvem **connectionModuleId**. Takže pokud chcete pro směrování zpráv ze zařízení typu list přímo do služby IoT Hub, použijte k vyloučení zprávy modulu pro následující trasy:

```sql
FROM /messages/\* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Jímka
Jímka definuje, které jsou odesílány zprávy. Může být některý z následujících hodnot:

| Jímka | Popis |
| ---- | ----------- |
| `$upstream` | Odeslání zprávy do služby IoT Hub |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Odeslání zprávy na vstupu `{input}` modulu `{moduleId}` |

IoT Edge poskytuje záruky v alespoň jedno. Centrum Edge ukládá zprávy místně, v případě trasu zprávu nelze doručit její jímkou. Například pokud Centrum Edge se nemůže připojit k službě IoT Hub nebo cílový modul připojen.

Centrum Edge uloží zpráv až po dobu určenou v `storeAndForwardConfiguration.timeToLiveSecs` vlastnost [Centrum Edge požadované vlastnosti](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definovat nebo aktualizace požadovaných vlastností 

Manifest nasazení můžete zadat požadované vlastnosti pro dvojče každého modulu, nasadit do zařízení IoT Edge. Pokud jsou požadované vlastnosti zadaný v manifestu nasazení, se přepsat všechny požadované vlastnosti v dvojčeti modulu.

Pokud nezadáte požadované vlastnosti dvojčete modulu v manifestu nasazení, služby IoT Hub dvojčete modulu nijak nezmění a bude moct nastavte požadované vlastnosti prostřednictvím kódu programu.

Upravit dvojče modulu se používají stejné mechanismy, které umožňují upravit dvojče zařízení. Další informace najdete v tématu [dvojče zařízení – Příručka pro vývojáře](../iot-hub/iot-hub-devguide-device-twins.md).   

## <a name="deployment-manifest-example"></a>Příklad nasazení manifestu

Tento příklad dokumentu JSON manifestu nasazení.

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
                "password": "{password}",
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
              "createOptions": ""
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
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
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
          "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
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

## <a name="next-steps"></a>Další postup

* Úplný seznam vlastností, které mohou nebo musí být součástí $edgeAgent a $edgeHub, naleznete v tématu [vlastnosti agenta Edge a Centrum Edge](module-edgeagent-edgehub.md).

* Teď, když víte, jak se používají moduly IoT Edge, [pochopení požadavků a nástroje pro vývoj modulů IoT Edge][lnk-module-dev].

[lnk-deploy]: module-deployment-monitoring.md
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-routing-query-syntax.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
