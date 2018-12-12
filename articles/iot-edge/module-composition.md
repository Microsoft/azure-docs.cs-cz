---
title: Deklarovat moduly a trasy s manifesty nasazení – Azure IoT Edge | Dokumentace Microsoftu
description: Zjistěte, jak manifest nasazení deklaruje které moduly chcete nasadit, jak je nasadit a jak vytvořit směrování zpráv mezi nimi.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0b221274923a6270e980d027aadc58154c7054b9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099966"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Zjistěte, jak nasadit moduly a vytvářet ve službě IoT Edge

Každé zařízení IoT Edge běží aspoň dva moduly: $edgeAgent a $edgeHub, které jsou součástí modulu runtime IoT Edge. Kromě toho libovolného zařízení IoT Edge můžete spustit více modulů k provedení libovolného počtu procesů. Tyto moduly se nasazují zařízení najednou, takže IoT Edge poskytuje způsob, jak deklarovat, které moduly Chcete-li nainstalovat a způsob jejich spolupráce konfigurace. 

*Manifest nasazení* je dokument JSON, který popisuje:

* **Agenta IoT Edge** dvojčete modulu, který obsahuje image kontejneru pro každý modul, přihlašovací údaje do registrů kontejnerů privátní přístup a pokyny, jak by měl vytvářet a spravovat každý modul.
* **Centrum IoT Edge** dvojče zařízení, která zahrnuje jak tok zpráv mezi moduly a nakonec do služby IoT Hub.
* Volitelně můžete požadované vlastnosti všech dvojčat dalších modulů.

Manifest nasazení musí mít nakonfigurovanou všechna zařízení IoT Edge. Nově instalovaný modul runtime IoT Edge sestavy kód chyby, dokud nebude nakonfigurován s platným manifestem. 

V kurzech Azure IoT Edge sestavení manifestu nasazení prostřednictvím Průvodce na portálu Azure IoT Edge. Můžete také použít manifest nasazení prostřednictvím kódu programu pomocí REST nebo sady SDK služby IoT Hub. Další informace najdete v tématu [vysvětlení nasazení IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Vytvoření manifestu nasazení

Manifest nasazení na vysoké úrovni, je seznam dvojčaty modulů, které jsou nakonfigurovány s jejich požadované vlastnosti. Manifest nasazení říká zařízení IoT Edge (nebo skupinu zařízení), které moduly Chcete-li nainstalovat a způsob jejich konfigurace. Manifesty nasazení zahrnout *požadované vlastnosti* pro každý dvojčete modulu. Zařízení IoT Edge nahlásit *ohlášené vlastnosti* jednotlivých modulů. 

Dva moduly jsou nutné v každé manifestu nasazení: `$edgeAgent`, a `$edgeHub`. Tyto moduly jsou součástí modulu runtime IoT Edge, který spravuje zařízení IoT Edge a modulů v něm spuštěný. Další informace o těchto modulů najdete v tématu [pochopit modul runtime IoT Edge a jeho architektura](iot-edge-runtime.md).

Kromě dva moduly runtime můžete přidat až 20 moduly vlastní ke spuštění na zařízení IoT Edge. 

Manifest nasazení, který obsahuje pouze modul runtime IoT Edge (edgeAgent a edgeHub) je neplatný.

Manifesty nasazení mají následující strukturu:

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
        "{module1}": {  // optional
            "properties.desired": {
                // desired properties of {module1}
            }
        },
        "{module2}": {  // optional
            "properties.desired": {
                // desired properties of {module2}
            }
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Konfigurovat moduly

Definujte, jak modul runtime IoT Edge nainstaluje moduly ve vašem nasazení. Agenta IoT Edge je komponenta modulu runtime, který spravuje instalaci, aktualizace a vytváření stavových zpráv pro zařízení IoT Edge. Dvojče modulu $edgeAgent proto vyžaduje konfiguraci a informace o správě pro všechny moduly. Tyto informace zahrnují parametry konfigurace pro samotný agent Edge. 

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

Centrum IoT Edge skladuje komunikaci mezi moduly, IoT Hub a všechna zařízení typu list. Proto dvojčete modulu $edgeHub obsahuje požadovanou vlastnost s názvem *trasy* , který deklaruje, jak jsou předány zprávy v rámci nasazení. Může mít několik tras uvnitř stejného nasazení.

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

Zdroj Určuje, odkud pochází zprávy. IoT Edge může směrovat zprávy ze zařízení typu list nebo moduly.

Vlastnost Zdroj může být některý z následujících hodnot:

| Zdroj | Popis |
| ------ | ----------- |
| `/*` | Všechny zprávy typu zařízení cloud nebo dvojčete změnit oznámení z jakéhokoli zařízení modulu nebo listu |
| `/twinChangeNotifications` | Změny dvojčat (ohlášené vlastnosti) pocházející z libovolného zařízení modulu nebo listu |
| `/messages/*` | Všechny zprávy typu zařízení cloud pomocí modulu nebo listu zařízení některé nebo žádný výstup |
| `/messages/modules/*` | Všechny zprávy typu zařízení cloud modulu pro některé nebo žádný výstup |
| `/messages/modules/{moduleId}/*` | Všechny zprávy typu zařízení cloud pomocí modulu pro konkrétní některé nebo žádný výstup |
| `/messages/modules/{moduleId}/outputs/*` | Všechny zprávy typu zařízení cloud pomocí modulu pro konkrétní některé výstup |
| `/messages/modules/{moduleId}/outputs/{output}` | Všechny zprávy typu zařízení cloud odesílaných konkrétní modul pomocí konkrétní výstupu |

### <a name="condition"></a>Podmínka
Podmínka je volitelné v deklaraci trasy. Pokud chcete předat všechny zprávy z jímka ke zdroji, nechte **kde** klauzule úplně. Nebo můžete použít [dotazovací jazyk služby IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md) k filtrování pro určité zprávy nebo typy zpráv, které splňují zadanou podmínku. IoT Edge trasy nepodporují filtrování zpráv na základě značky dvojčat nebo vlastnosti. 

Zprávy, které se předají mezi moduly ve službě IoT Edge jsou formátovány stejně jako zprávy, které předávají mezi zařízeními a Azure IoT Hub. Všechny zprávy jsou formátovány jako dokumenty JSON a mít **systemProperties**, **objekt appProperties**, a **tělo** parametry. 

Můžete vytvářet dotazy kolem libovolné ze tří parametrů s následující syntaxí: 

* Vlastnosti systému: `$<propertyName>` nebo `{$<propertyName>}`
* Vlastnosti aplikace: `<propertyName>`
* Vlastnosti textu: `$body.<propertyName>` 

Příklady o tom, jak vytvářet dotazy na vlastnosti zprávy, najdete v článku [trasy výrazech dotazů zprávy typu zařízení cloud](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Příklad, který je specifický pro IoT Edge je, když chcete filtrovat zprávy, které byly přijaty zařízení brány ze zařízení typu list. Zahrnout zprávy, které pocházejí z modulů systému vlastnost s názvem **connectionModuleId**. Takže pokud chcete pro směrování zpráv ze zařízení typu list přímo do služby IoT Hub, použijte k vyloučení zprávy modulu pro následující trasy:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Jímka
Jímka definuje, které jsou odesílány zprávy. Pouze moduly a IoT Hub může přijímat zprávy. Zprávy nejde směrovat do jiných zařízení. Se nedá nijak zástupných znaků ve vlastnosti jímky. 

Vlastnost jímky může být některý z následujících hodnot:

| Jímka | Popis |
| ---- | ----------- |
| `$upstream` | Odeslání zprávy do služby IoT Hub |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Odeslání zprávy pro určitý vstup konkrétního modulu |

IoT Edge poskytuje záruky v alespoň jedno. Centrum Edge ukládá zprávy místně, v případě trasu zprávu nelze doručit její jímkou. Například pokud Centrum Edge se nemůže připojit k službě IoT Hub nebo cílový modul nepřipojené.

Centrum Edge uloží zpráv až po dobu určenou v `storeAndForwardConfiguration.timeToLiveSecs` vlastnost [Centrum Edge požadované vlastnosti](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definovat nebo aktualizace požadovaných vlastností 

Manifest nasazení určuje požadované vlastnosti pro každý modul nasazený do zařízení IoT Edge. Požadované vlastnosti v manifestu nasazení přepsat všechny požadované vlastnosti v dvojčeti modulu.

Pokud nezadáte požadované vlastnosti dvojčete modulu v manifestu nasazení, IoT Hub dvojčete modulu nijak nezmění. Místo toho můžete nastavit požadované vlastnosti prostřednictvím kódu programu.

Upravit dvojče modulu se používají stejné mechanismy, které umožňují upravit dvojče zařízení. Další informace najdete v tématu [dvojčete modulu – Příručka pro vývojáře](../iot-hub/iot-hub-devguide-module-twins.md).   

## <a name="deployment-manifest-example"></a>Příklad nasazení manifestu

Následující příklad ukazuje, jak může vypadat dokumentu manifestu nasazení platné.

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

* Teď, když víte, jak se používají moduly IoT Edge, [pochopení požadavků a nástroje pro vývoj modulů IoT Edge](module-development.md).
