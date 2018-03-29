---
title: Složení modulu Azure IoT Edge | Microsoft Docs
description: Zjistěte, co na moduly Azure IoT okraj a jak může být znovu
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/23/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 7df566ced755e1e817b3107dac8f17e9f6e9b8e4
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>Pochopení IoT Edge moduly použití, nakonfigurovaná a znovu použít – náhled

Každé zařízení IoT Edge spustí aspoň dva moduly: $edgeAgent a $edgeHub, které tvoří runtime IoT okraj. Kromě těchto dvou standardní žádné IoT hraniční zařízení můžete spustit více modulů provést libovolný počet procesů. Při nasazení těchto modulů zařízení najednou, je nutné způsob, jak deklarace, které moduly jsou zahrnuty jejich vzájemné interakce mezi sebou. 

*– Manifest nasazení* je dokument JSON, který popisuje:

* Které moduly IoT Edge mají být nasazeny, spolu s jejich možnosti vytváření a správu.
* Konfigurace centra Edge, která zahrnuje jak toku zpráv mezi moduly a nakonec do služby IoT Hub.
* Hodnoty, Volitelně můžete nastavit v požadované vlastnosti dvojčata modulu, ke konfiguraci jednotlivých modulu aplikací.

Všechny IoT hraniční zařízení musí být nakonfigurované manifest nasazení. Nově instalovaný modul runtime IoT okraj sestavy kód chyby, dokud nebude nakonfigurována s platný manifest. 

V kurzů k Azure IoT Edge sestavení manifest nasazení tak, že přejdete v průvodci na portálu Azure IoT okraj. Můžete také použít nasazení manifestu programově pomocí REST nebo sady SDK služby IoT Hub. Odkazovat na [nasazení a monitorování] [ lnk-deploy] Další informace o nasazení IoT okraj.

## <a name="create-a-deployment-manifest"></a>Vytvoření manifestu nasazení

Na vysoké úrovni nakonfiguruje manifest nasazení modul twin požadované vlastnosti pro IoT Edge moduly nasazené na IoT hraniční zařízení. Dva z těchto modulů nejsou vždy: agent okraj a okraj rozbočovače.

Manifest nasazení, který obsahuje pouze IoT Edge runtime (agenta a rozbočovače) je platná.

Manifest dodržuje tuto strukturu:

```json
{
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
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

Kromě vytvoření požadované vlastnosti všechny moduly, které chcete nasadit, budete muset informování IoT Edge runtime, jak je nainstalovat. Informace o konfiguraci a správu všechny moduly přejde uvnitř **$edgeAgent** požadovaných vlastností. Tyto informace zahrnují parametry konfigurace pro vlastní agent okraj. 

Úplný seznam vlastností, které mohou nebo musí být zahrnut, najdete v části [vlastnosti agenta okraj a okraj rozbočovače](module-edgeagent-edgehub.md).

Vlastnosti $edgeAgent následující strukturu:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
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
            "{module1}": { //optional
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

Hraniční rozbočovače poskytuje způsob, jak deklarativně směrování zpráv mezi moduly a mezi moduly a IoT Hub. Hraniční rozbočovače spravuje veškerou komunikaci, informace o postupu přejde uvnitř **$edgeHub** požadovaných vlastností. Můžete mít víc tras v rámci stejného nasazení.

Trasy jsou deklarované v **$edgeHub** požadovaných vlastností s následující syntaxí:

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

Všechny trasy musí mít zdroj a jímka, ale je podmínka vyhodnocena jako volitelnou informaci, můžete použít k filtrování zprávy. 


### <a name="source"></a>Zdroj
Zdroj Určuje, odkud pocházejí zprávy. Může být libovolná z následujících hodnot:

| Zdroj | Popis |
| ------ | ----------- |
| `/*` | Všechny zprávy typu zařízení cloud ze všech zařízení nebo modulu |
| `/messages/*` | Jakékoli zařízení cloud zprávy odeslané zařízením nebo modul prostřednictvím některé nebo žádný výstup |
| `/messages/modules/*` | Všechny zprávy ve zařízení cloud modulem prostřednictvím některé nebo žádný výstup. |
| `/messages/modules/{moduleId}/*` | Jakékoli zprávy typu zařízení cloud poslal {moduleId} se žádný výstup |
| `/messages/modules/{moduleId}/outputs/*` | Jakékoli zprávy typu zařízení cloud poslal {moduleId} některé výstup |
| `/messages/modules/{moduleId}/outputs/{output}` | Žádné zařízení cloud zpráva byla odeslána pomocí {moduleId} {výstupní} |

### <a name="condition"></a>Podmínka
Je podmínka vyhodnocena jako volitelný v deklaraci trasy. Pokud chcete předat všechny zprávy z jímky ke zdroji, nechte **kde** klauzule úplně. Nebo můžete použít [IoT Hub dotazovací jazyk] [ lnk-iothub-query] vyfiltrujete pro některé zprávy nebo typ zprávy, které splňují zadanou podmínku.

Zprávy, které se předají mezi moduly v IoT Edge jsou formátovaná stejně jako zprávy, které předávají mezi zařízeními a Azure IoT Hub. Všechny zprávy jsou formátovány jako JSON a mít **systemProperties**, **objekt appProperties**, a **textu** parametry. 

Můžete vytvořit dotazy ohledně všechny tři parametry s následující syntaxí: 

* Vlastnosti systému: `$<propertyName>` nebo `{$<propertyName>}`
* Vlastnosti aplikace: `<propertyName>`
* Vlastnosti textu: `$body.<propertyName>` 

Příklady o tom, jak vytvářet dotazy na vlastnosti zprávy naleznete v tématu [zpráv typu zařízení cloud trasy dotaz výrazy](../iot-hub/iot-hub-devguide-query-language.md#device-to-cloud-message-routes-query-expressions).

Příklad, který je specifický pro IoT okraj je, pokud chcete filtrovat zprávy, které byly přijaty zařízení brány ze zařízení typu list. Zprávy, které pocházejí z modulů obsahovat systému vlastnost s názvem **connectionModuleId**. Takže pokud chcete směrovat zprávy ze zařízení listu přímo do služby IoT Hub, použijte následující trasy pro vyloučení zprávy modulu:

```sql
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Jímka
Jímky definuje, které jsou odesílány zprávy. Může být libovolná z následujících hodnot:

| Jímka | Popis |
| ---- | ----------- |
| `$upstream` | Odeslání zprávy do služby IoT Hub |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Odeslat zprávu jako vstup `{input}` modulu `{moduleId}` |

Je důležité si uvědomit, že hraniční hub zajišťuje záruky na aspoň jednou, to znamená, že zprávy ukládat místně v případě, že trasu nelze doručení zprávy do jeho podřízený, například hraniční centra se nemůže připojit ke službě IoT Hub, nebo není připojen cílový modul.

Hraniční rozbočovače ukládá zprávy až za dobu určenou v `storeAndForwardConfiguration.timeToLiveSecs` vlastnost [Edge rozbočovače potřeby vlastnosti](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definování nebo aktualizovat požadované vlastnosti 

Manifest nasazení můžete zadat požadované vlastnosti pro dvojici modulu každého modulu nasazené na zařízení IoT okraj. Pokud jsou požadované vlastnosti zadané v manifestu nasazení, jejich přepsat všechny požadované vlastnosti právě twin modulu.

Pokud nezadáte požadované vlastnosti modul twin v manifestu nasazení, IoT Hub nezmění twin modulu jakýmkoli způsobem a nebudete moci nastavit požadované vlastnosti prostřednictvím kódu programu.

Stejné mechanismy, které vám umožní změnit dvojčata zařízení se používají k úpravě dvojčata modulu. Odkazovat [twin zařízení – Příručka vývojáře](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-device-twins) Další informace.   

## <a name="deployment-manifest-example"></a>Příklad nasazení manifestu

Tento příklad nasazení manifestu dokumentu JSON.

```json
{
"moduleContent": {
    "$edgeAgent": {
        "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
                "type": "docker",
                "settings": {
                    "minDockerVersion": "v1.25",
                    "loggingOptions": ""
                }
            },
            "systemModules": {
                "edgeAgent": {
                    "type": "docker",
                    "settings": {
                    "image": "microsoft/azureiotedge-agent:1.0-preview",
                    "createOptions": ""
                    }
                },
                "edgeHub": {
                    "type": "docker",
                    "status": "running",
                    "restartPolicy": "always",
                    "settings": {
                    "image": "microsoft/azureiotedge-hub:1.0-preview",
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
                    "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
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

* Úplný seznam vlastností, které mohou nebo musí být součástí $edgeAgent a $edgeHub najdete v tématu [vlastnosti agenta okraj a okraj rozbočovače](module-edgeagent-edgehub.md).

* Nyní když znáte použití modulů IoT Edge, [pochopení požadavků a nástrojů pro vývoj modulů IoT Edge][lnk-module-dev].

[lnk-deploy]: module-deployment-monitoring.md
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
