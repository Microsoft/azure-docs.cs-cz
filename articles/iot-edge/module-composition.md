---
title: Nasazení modulu & trasy s manifesty nasazení – Azure IoT Edge
description: Zjistěte, jak manifest nasazení deklaruje, které moduly nasadit, jak je nasadit a jak mezi nimi vytvořit trasy zpráv.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8eb24fe878638853cd8519c08045552a91f0c190
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271392"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Nasazování modulů a vytváření tras ve službě IoT Edge

Každé zařízení IoT Edge běží alespoň dva moduly: $edgeAgent a $edgeHub, které jsou součástí běhu IoT Edge. Zařízení IoT Edge může spustit více dalších modulů pro libovolný počet procesů. Pomocí manifestu nasazení sdělte zařízení, které moduly nainstalovat a jak je nakonfigurovat tak, aby spolupracovaly.

*Manifest nasazení* je dokument JSON, který popisuje:

* Dvojče **modulu agenta IoT Edge,** který obsahuje tři součásti:
  * Image kontejneru pro každý modul, který běží na zařízení.
  * Pověření pro přístup k registrům soukromých kontejnerů, které obsahují image modulu.
  * Pokyny pro vytvoření a spravování jednotlivých modulů.
* Dvojče **modulu IoT Edge hub,** který zahrnuje tok zpráv mezi moduly a nakonec do ioT hubu.
* Požadované vlastnosti všech dalších dvojčat modulu (volitelné).

Všechna zařízení IoT Edge musí být nakonfigurována s manifestem nasazení. Nově nainstalovaný běhový čas IoT Edge hlásí kód chyby, dokud není nakonfigurován s platným manifestem.

V kurzech Azure IoT Edge vytvoříte manifest nasazení procházením průvodce na portálu Azure IoT Edge. Manifest nasazení můžete také použít programově pomocí sady REST nebo sady SDK služby IoT Hub. Další informace naleznete [v tématu Understand IoT Edge deployments](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Vytvoření manifestu nasazení

Na vysoké úrovni manifest nasazení je seznam dvojčat modulu, které jsou nakonfigurovány s jejich požadované vlastnosti. Manifest nasazení říká zařízení IoT Edge (nebo skupině zařízení), které moduly nainstalovat a jak je nakonfigurovat. Manifesty nasazení zahrnují *požadované vlastnosti* pro každý dvojče modulu. Zařízení IoT Edge hlásí *ohlášené vlastnosti* pro každý modul.

V každém manifestu nasazení jsou `$edgeAgent`vyžadovány dva moduly: a `$edgeHub`. Tyto moduly jsou součástí modulu runtime IoT Edge, který spravuje zařízení IoT Edge a moduly na něm spuštěné. Další informace o těchto modulech naleznete [v tématu Principy modulu runtime IoT Edge a jeho architektury](iot-edge-runtime.md).

Kromě dvou modulů runtime můžete přidat až 20 vlastních modulů, které můžete spustit na zařízení IoT Edge.

Manifest nasazení, který obsahuje pouze runtime IoT Edge (edgeAgent a edgeHub) je platný.

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

## <a name="configure-modules"></a>Konfigurace modulů

Definujte, jak moduly IoT Edge nainstaluje do vašeho nasazení. Agent IoT Edge je součást runtime, která spravuje instalaci, aktualizace a hlášení stavu pro zařízení IoT Edge. Dvojče modulu $edgeAgent proto obsahuje informace o konfiguraci a správě pro všechny moduly. Tyto informace zahrnují parametry konfigurace samotného agenta IoT Edge.

Úplný seznam vlastností, které mohou nebo musí být zahrnuty, naleznete [v tématu Vlastnosti agenta IoT Edge a centra IoT Edge](module-edgeagent-edgehub.md).

Vlastnosti $edgeAgent postupujte podle této struktury:

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

Centrum IoT Edge spravuje komunikaci mezi moduly, službou IoT Hub a všemi zařízeními s listy. Dvojče modulu $edgeHub proto obsahuje požadovanou vlastnost nazvanou *trasy,* která deklaruje, jak jsou zprávy předávány v rámci nasazení. V rámci stejného nasazení můžete mít více tras.

Trasy jsou deklarovány v **$edgeHub** požadované vlastnosti s následující syntaxí:

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

Každá trasa potřebuje zdroj a jímku, ale podmínka je volitelný kus, který můžete použít k filtrování zpráv.

### <a name="source"></a>Zdroj

Zdroj určuje, odkud zprávy pocházejí. IoT Edge může směrovat zprávy z modulů nebo listových zařízení.

Pomocí sad IoT SDK mohou moduly deklarovat konkrétní výstupní fronty pro své zprávy pomocí třídy ModuleClient. Výstupní fronty nejsou nutné, ale jsou užitečné pro správu více tras. Zařízení Leaf můžou používat třídu DeviceClient sad SDK IoT k odesílání zpráv do zařízení brány IoT Edge stejným způsobem, jako by odesílaly zprávy do služby IoT Hub. Další informace najdete [v tématu Principy a používání sad SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).

Source vlastnost může být některou z následujících hodnot:

| Zdroj | Popis |
| ------ | ----------- |
| `/*` | Všechny zprávy mezi zařízeními a cloudy nebo oznámení o změně dvojčete z libovolného modulu nebo zařízení list |
| `/twinChangeNotifications` | Jakákoli změna dvojčete (hlášené vlastnosti) pocházející z libovolného modulu nebo listového zařízení |
| `/messages/*` | Jakákoli zpráva mezi zařízeními a cloudy odeslaná modulem prostřednictvím některého nebo žádného výstupu nebo listového zařízení |
| `/messages/modules/*` | Jakákoli zpráva mezi zařízeními a cloudy odeslaná modulem prostřednictvím některého nebo žádného výstupu |
| `/messages/modules/<moduleId>/*` | Jakákoli zpráva mezi zařízeními a cloudy odeslaná určitým modulem prostřednictvím některého nebo žádného výstupu |
| `/messages/modules/<moduleId>/outputs/*` | Jakákoli zpráva mezi zařízeními a cloudy odeslaná určitým modulem prostřednictvím |
| `/messages/modules/<moduleId>/outputs/<output>` | Jakákoli zpráva mezi zařízeními a cloudy odeslaná určitým modulem prostřednictvím konkrétního výstupu |

### <a name="condition"></a>Podmínka

Podmínka je nepovinná v deklaraci postupu. Pokud chcete předat všechny zprávy ze zdroje do jímky, stačí vynechat **klauzuli WHERE** úplně. Nebo můžete použít [dotazovací jazyk služby IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md) k filtrování určitých zpráv nebo typů zpráv, které splňují podmínku. Trasy IoT Edge nepodporují filtrování zpráv na základě značek dvojčete nebo vlastností.

Zprávy, které procházejí mezi moduly v IoT Edge jsou formátovány stejně jako zprávy, které procházejí mezi vašimi zařízeními a Azure IoT Hub. Všechny zprávy jsou formátovány jako JSON a mají **systemProperties**, **appProperties**a **body** parametry.

Můžete vytvářet dotazy kolem některého ze tří parametrů s následující syntaxí:

* Vlastnosti `$<propertyName>` systému: nebo`{$<propertyName>}`
* Vlastnosti aplikace:`<propertyName>`
* Vlastnosti těla:`$body.<propertyName>`

Příklady o vytváření dotazů na vlastnosti zprávy naleznete v [tématu Zpráva zařízení cloud směruje výrazy dotazu](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Příkladem, který je specifický pro IoT Edge je, když chcete filtrovat zprávy, které dorazily na zařízení brány z listového zařízení. Zprávy, které pocházejí z modulů patří vlastnost systému s názvem **connectionModuleId**. Pokud tedy chcete směrovat zprávy z listových zařízení přímo do ioT hubu, použijte následující trasu k vyloučení zpráv modulu:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Jímka

Jímka definuje, kde jsou odesílány zprávy. Zprávy můžou přijímat pouze moduly a IoT Hub. Zprávy nelze směrovat na jiná zařízení. Ve vlastnosti jímky nejsou žádné možnosti zástupných symbolů.

Vlastnost jímky může být některá z následujících hodnot:

| Jímka | Popis |
| ---- | ----------- |
| `$upstream` | Odeslání zprávy do služby IoT Hub |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Odeslat zprávu na konkrétní vstup konkrétního modulu |

IoT Edge poskytuje alespoň jednou záruky. Centrum IoT Edge ukládá zprávy místně v případě, že trasa nemůže doručit zprávu do jímky. Například pokud se centrum IoT Edge nemůže připojit k centru IoT Hub nebo cílový modul není připojený.

Centrum IoT Edge ukládá zprávy až do `storeAndForwardConfiguration.timeToLiveSecs` doby zadané ve vlastnostech [požadovaných vlastností centra IoT Edge](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definování nebo aktualizace požadovaných vlastností

Manifest nasazení určuje požadované vlastnosti pro každý modul nasazený do zařízení IoT Edge. Požadované vlastnosti v manifestu nasazení přepsat všechny požadované vlastnosti aktuálně v dvojčete modulu.

Pokud nezadáte požadované vlastnosti dvojčete modulu v manifestu nasazení, služba IoT Hub žádným způsobem nezmění dvojče modulu. Místo toho můžete nastavit požadované vlastnosti programově.

Stejné mechanismy, které umožňují upravit dvojčata zařízení se používají k úpravě dvojčata modulu. Další informace naleznete v [průvodci pro vývojáře dvojčat modulu](../iot-hub/iot-hub-devguide-module-twins.md).

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

* Úplný seznam vlastností, které mohou nebo musí být zahrnuty do $edgeAgent a $edgeHub, naleznete v [tématu Vlastnosti agenta IoT Edge a centra IoT Edge](module-edgeagent-edgehub.md).

* Teď, když víte, jak se používají moduly IoT Edge, [pochopte požadavky a nástroje pro vývoj modulů IoT Edge](module-development.md).
