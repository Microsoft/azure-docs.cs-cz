---
title: Nasazení modulu & trasy s manifesty nasazení – Azure IoT Edge
description: Zjistěte, jak manifest nasazení deklaruje které moduly chcete nasadit, jak je nasadit a jak vytvořit směrování zpráv mezi nimi.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8eb24fe878638853cd8519c08045552a91f0c190
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379403"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Zjistěte, jak nasadit moduly a vytvářet ve službě IoT Edge

Každé zařízení IoT Edge běží aspoň dva moduly: $edgeAgent a $edgeHub, které jsou součástí modulu runtime IoT Edge. Zařízení IoT Edge může spustit více dalších modulů pro libovolný počet procesů. Pomocí manifestu nasazení sdělte vašemu zařízení, které moduly se mají nainstalovat, a jak je nakonfigurovat tak, aby společně spolupracovaly.

*Manifest nasazení* je dokument JSON, který popisuje:

* Modul **IoT Edge agenta** je vyzdvojený, což zahrnuje tři komponenty:
  * Obrázek kontejneru pro každý modul, který běží na zařízení.
  * Přihlašovací údaje pro přístup k privátním registrům kontejnerů, které obsahují image modulů.
  * Pokyny, jak by se měl každý modul vytvářet a spravovat.
* Modul **IoT Edge hub** je nevlákenný, což zahrnuje způsob toku zpráv mezi moduly a nakonec IoT Hub.
* Požadované vlastnosti všech dalších vláken modulu (volitelné).

Manifest nasazení musí mít nakonfigurovanou všechna zařízení IoT Edge. Nově instalovaný modul runtime IoT Edge sestavy kód chyby, dokud nebude nakonfigurován s platným manifestem.

V kurzech Azure IoT Edge sestavení manifestu nasazení prostřednictvím Průvodce na portálu Azure IoT Edge. Můžete také použít manifest nasazení prostřednictvím kódu programu pomocí REST nebo sady SDK služby IoT Hub. Další informace najdete v tématu [vysvětlení nasazení IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Vytvoření manifestu nasazení

Manifest nasazení na vysoké úrovni, je seznam dvojčaty modulů, které jsou nakonfigurovány s jejich požadované vlastnosti. Manifest nasazení říká zařízení IoT Edge (nebo skupinu zařízení), které moduly Chcete-li nainstalovat a způsob jejich konfigurace. Manifesty nasazení obsahují *požadované vlastnosti* pro každý modul s dvojitou vlastností. IoT Edge zařízení hlásí zpět *ohlášené vlastnosti* pro každý modul.

V každém manifestu nasazení jsou vyžadovány dva moduly: `$edgeAgent`a `$edgeHub`. Tyto moduly jsou součástí modulu runtime IoT Edge, který spravuje zařízení IoT Edge a modulů v něm spuštěný. Další informace o těchto modulech naleznete v tématu [pochopení IoT Edge runtime a jeho architektury](iot-edge-runtime.md).

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

Definujte, jak modul runtime IoT Edge nainstaluje moduly ve vašem nasazení. Agenta IoT Edge je komponenta modulu runtime, který spravuje instalaci, aktualizace a vytváření stavových zpráv pro zařízení IoT Edge. Proto modul $edgeAgent s dvojitou příponou obsahuje informace o konfiguraci a správě všech modulů. Tyto informace zahrnují parametry konfigurace pro agenta IoT Edge.

Úplný seznam vlastností, které mohou nebo musí být zahrnuté, najdete v tématu [vlastnosti IoT Edge agenta a centra IoT Edge](module-edgeagent-edgehub.md).

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

Centrum IoT Edge skladuje komunikaci mezi moduly, IoT Hub a všechna zařízení typu list. Proto modul $edgeHub s dvojitou přesností obsahuje požadovanou vlastnost nazvanou *trasy* , která deklaruje, jak jsou zprávy předávány v rámci nasazení. Může mít několik tras uvnitř stejného nasazení.

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

Každý směrování vyžaduje zdroje a jímky, ale je podmínka vyhodnocena jako volitelnou informaci, které můžete použít k filtrování zprávy.

### <a name="source"></a>Zdroj

Zdroj Určuje, odkud pochází zprávy. IoT Edge může směrovat zprávy z modulů nebo na listových zařízeních.

Pomocí sad SDK pro IoT můžou moduly deklarovat konkrétní výstupní fronty pro své zprávy pomocí třídy ModuleClient. Výstupní fronty nejsou nutné, ale jsou užitečné pro správu více tras. Koncová zařízení můžou používat třídu DeviceClient sad IoT SDK k posílání zpráv do zařízení IoT Edge brány stejným způsobem, jakým odesílají zprávy IoT Hub. Další informace najdete v tématu [pochopení a používání sad Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).

Vlastnost Zdroj může být některý z následujících hodnot:

| Zdroj | Popis |
| ------ | ----------- |
| `/*` | Všechny zprávy typu zařízení cloud nebo dvojčete změnit oznámení z jakéhokoli zařízení modulu nebo listu |
| `/twinChangeNotifications` | Změny dvojčat (ohlášené vlastnosti) pocházející z libovolného zařízení modulu nebo listu |
| `/messages/*` | Jakákoli zpráva typu zařízení-Cloud, kterou modul odesílá prostřednictvím nějakého nebo žádného výstupu, nebo na listovém zařízení |
| `/messages/modules/*` | Všechny zprávy typu zařízení cloud modulu pro některé nebo žádný výstup |
| `/messages/modules/<moduleId>/*` | Všechny zprávy typu zařízení cloud pomocí modulu pro konkrétní některé nebo žádný výstup |
| `/messages/modules/<moduleId>/outputs/*` | Všechny zprávy typu zařízení cloud pomocí modulu pro konkrétní některé výstup |
| `/messages/modules/<moduleId>/outputs/<output>` | Všechny zprávy typu zařízení cloud odesílaných konkrétní modul pomocí konkrétní výstupu |

### <a name="condition"></a>Podmínka

Podmínka je volitelné v deklaraci trasy. Pokud chcete předat všechny zprávy ze zdroje do jímky, stačí opustit klauzuli **WHERE** úplně. Nebo můžete použít [jazyk dotazů IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md) k filtrování určitých zpráv nebo typů zpráv, které podmínku splní. IoT Edge trasy nepodporují filtrování zpráv na základě značky dvojčat nebo vlastnosti.

Zprávy, které se předají mezi moduly ve službě IoT Edge jsou formátovány stejně jako zprávy, které předávají mezi zařízeními a Azure IoT Hub. Všechny zprávy jsou formátovány jako JSON a mají parametry **systemProperties**, **appProperties**a **text** .

Můžete vytvářet dotazy kolem libovolné ze tří parametrů s následující syntaxí:

* Vlastnosti systému: `$<propertyName>` nebo `{$<propertyName>}`
* Vlastnosti aplikace: `<propertyName>`
* Vlastnosti těla: `$body.<propertyName>`

Příklady, jak vytvářet dotazy na vlastnosti zpráv, najdete v tématu [výrazy dotazů směrování zpráv ze zařízení do cloudu](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Příklad, který je specifický pro IoT Edge je, když chcete filtrovat zprávy, které byly přijaty zařízení brány ze zařízení typu list. Zprávy, které pocházejí z modulů, zahrnují vlastnost System s názvem **connectionModuleId**. Takže pokud chcete pro směrování zpráv ze zařízení typu list přímo do služby IoT Hub, použijte k vyloučení zprávy modulu pro následující trasy:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Jímka

Jímka definuje, které jsou odesílány zprávy. Pouze moduly a IoT Hub může přijímat zprávy. Zprávy nejde směrovat do jiných zařízení. Se nedá nijak zástupných znaků ve vlastnosti jímky.

Vlastnost jímky může být některý z následujících hodnot:

| Jímka | Popis |
| ---- | ----------- |
| `$upstream` | Odeslání zprávy do služby IoT Hub |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Odeslání zprávy pro určitý vstup konkrétního modulu |

IoT Edge poskytuje záruky v alespoň jedno. Centrum IoT Edge ukládá místní zprávy pro případ, že trasa nemůže zprávu doručit do jímky. Pokud se například Centrum IoT Edge nemůže připojit k IoT Hub nebo cílový modul není připojen.

Centrum IoT Edge ukládá zprávy až do doby zadané ve vlastnosti `storeAndForwardConfiguration.timeToLiveSecs` [požadované vlastnosti centra IoT Edge](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definovat nebo aktualizace požadovaných vlastností

Manifest nasazení určuje požadované vlastnosti pro každý modul nasazený do zařízení IoT Edge. Požadované vlastnosti v manifestu nasazení přepsat všechny požadované vlastnosti v dvojčeti modulu.

Pokud nezadáte požadované vlastnosti dvojčete modulu v manifestu nasazení, IoT Hub dvojčete modulu nijak nezmění. Místo toho můžete nastavit požadované vlastnosti prostřednictvím kódu programu.

Upravit dvojče modulu se používají stejné mechanismy, které umožňují upravit dvojče zařízení. Další informace najdete v tématu [Průvodce pro vývojáře s vyzdvojeným modulem](../iot-hub/iot-hub-devguide-module-twins.md).

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

* Úplný seznam vlastností, které mohou nebo musí být součástí $edgeAgent a $edgeHub, najdete v tématu [Vlastnosti agenta IoT Edge a centra IoT Edge](module-edgeagent-edgehub.md).

* Když teď víte, jak se používají IoT Edge moduly, [Pochopte požadavky a nástroje pro vývoj IoT Edge modulů](module-development.md).
