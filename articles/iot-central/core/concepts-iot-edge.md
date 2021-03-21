---
title: Azure IoT Edge a Azure IoT Central | Microsoft Docs
description: Pochopte, jak použít Azure IoT Edge s aplikací IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: e0f3464420c5cb429f780999bf5983b2ab142567
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608627"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Připojení zařízení Azure IoT Edge k aplikaci Azure IoT Central

*Tento článek se týká tvůrců řešení a vývojářů zařízení.*

Azure IoT Edge přesouvá cloudovou analýzu a vlastní obchodní logiku do zařízení, aby se vaše organizace mohla soustředit na obchodní přehledy namísto správy dat. Škálujte své řešení IoT tak, že zabalíte svoji obchodní logiku do standardních kontejnerů, nasadíte tyto kontejnery do svých zařízení a budete je monitorovat z cloudu.

Tento článek popisuje:

* Jak IoT Edge zařízení se připojují k aplikaci IoT Central.
* Jak používat IoT Central ke správě zařízení IoT Edge.

Další informace o IoT Edge najdete v tématu [co je Azure IoT Edge?](../../iot-edge/about-iot-edge.md)

## <a name="iot-edge"></a>IoT Edge

IoT Edge se skládá ze tří součástí:

* *Moduly IoT Edge* jsou kontejnery, ve kterých běží služby Azure, partnerské služby nebo vlastní kód. Moduly se nasazují na zařízení IoT Edge a spustí se místně na těchto zařízeních. Další informace najdete v tématu [principy Azure IoT Edgech modulů](../../iot-edge/iot-edge-modules.md).
* Modul *runtime IoT Edge* běží na každém zařízení IoT Edge a spravuje moduly nasazené na každé zařízení. Modul runtime se skládá ze dvou IoT Edge modulů: *IoT Edge agenta* a *centra IoT Edge*. Další informace najdete v tématu [pochopení Azure IoT Edge runtime a jeho architektury](../../iot-edge/iot-edge-runtime.md).
* *Cloudové rozhraní* umožňuje vzdáleně monitorovat a spravovat IoT Edge zařízení. IoT Central je příkladem cloudového rozhraní.

IoT Edge zařízení může být:

* Samostatné zařízení složené z modulů.
* *Zařízení brány* s připojenými zařízeními, která se k němu připojují.

## <a name="iot-edge-as-a-gateway"></a>IoT Edge jako brána

Zařízení IoT Edge může fungovat jako brána, která poskytuje připojení mezi ostatními zařízeními pro příjem dat v síti a vaší IoT Centralou aplikací.

Existují dva způsoby brány:

* V modelu *transparentní brány* se modul IoT Edge hub chová jako IoT Central a zpracovává připojení ze zařízení zaregistrovaných v IoT Central. Zprávy přecházejí ze zařízení pro příjem dat, aby se IoT Central, jako by mezi nimi nebyla žádná brána.

* Zařízení, která se nemůžou připojovat k IoT Central vlastním způsobem ve vzoru *brány překladu* , se místo toho připojí k vlastnímu modulu IoT Edge. Modul v IoT Edge zařízení zpracovává příchozí zprávy o příchozím zařízení a pak je přepošle do IoT Central.

Modely transparentních a překladatelských bran se vzájemně vylučují. Jedno zařízení IoT Edge může fungovat jako transparentní brána i jako brána pro překlad.

Další informace o vzorech IoT Edge brány najdete v tématu [jak se dá zařízení IoT Edge použít jako brána](../../iot-edge/iot-edge-as-gateway.md).

### <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Vztahy navazujících zařízení s bránou a moduly

Podřízená zařízení se můžou připojit k zařízení IoT Edge brány prostřednictvím modulu *IoT Edge hub*  . V tomto scénáři je IoT Edge zařízení transparentní bránou:

:::image type="content" source="media/concepts-iot-edge/gateway-transparent.png" alt-text="Diagram transparentní brány" border="false":::

Podřízená zařízení se taky můžou připojit k zařízení IoT Edge brány prostřednictvím vlastního modulu. V následujícím scénáři se pro zařízení se zařízením připojí vlastní modul *Modbus* . V tomto scénáři je IoT Edge zařízení pro překladovou bránu:

:::image type="content" source="media/concepts-iot-edge/gateway-module.png" alt-text="Diagram připojení vlastního modulu" border="false":::

Následující diagram znázorňuje připojení k zařízení IoT Edge brány prostřednictvím obou typů modulů. V tomto scénáři je IoT Edge zařízení transparentním i bránou pro překlad:

:::image type="content" source="media/concepts-iot-edge/gateway-module-transparent.png" alt-text="Diagram připojení pomocí obou připojovacích modulů" border="false":::

Podřízená zařízení se můžou připojit k zařízení IoT Edge brány prostřednictvím několika vlastních modulů. Následující diagram zobrazuje zařízení, která se připojují prostřednictvím vlastního modulu Modbus, vlastního modulu v police a modulu *centra IoT Edge*  :

:::image type="content" source="media/concepts-iot-edge/gateway-two-modules-transparent.png" alt-text="Diagram připojení pomocí více vlastních modulů" border="false":::

## <a name="iot-edge-devices-and-iot-central"></a>IoT Edge zařízení a IoT Central

Zařízení IoT Edge můžou k ověřování pomocí IoT Central používat tokeny *sdíleného přístupového podpisu* nebo certifikáty X. 509. Zařízení IoT Edge můžete ručně zaregistrovat v IoT Central předtím, než se poprvé připojíte, nebo můžete použít službu Device Provisioning Service ke zpracování registrace. Další informace najdete v tématu věnovaném [připojení k Azure IoT Central](concepts-get-connected.md).

IoT Central používá [šablony zařízení](concepts-device-templates.md) k definování způsobu, jakým IoT Central komunikuje se zařízením. Například šablona zařízení určuje:

* Typy telemetrie a vlastností, které zařízení odesílá, aby IoT Central mohl interpretovat a vytvářet vizualizace.
* Příkazy, na které zařízení reaguje, aby IoT Central mohl zobrazit uživatelské rozhraní, které má operátor použít k volání příkazů.

Zařízení IoT Edge může odesílat telemetrii, synchronizovat hodnoty vlastností a reagovat na příkazy stejným způsobem jako standardní zařízení. Zařízení IoT Edge například potřebuje šablonu zařízení v IoT Central.

### <a name="iot-edge-device-templates"></a>IoT Edge šablony zařízení

IoT Central šablony zařízení používají modely k popisu možností zařízení. Následující diagram znázorňuje strukturu modelu pro IoT Edge zařízení:

:::image type="content" source="media/concepts-iot-edge/iot-edge-model.png" alt-text="Struktura modelu IoT Edgeho zařízení připojeného k IoT Central" border="false":::

IoT Central modeluje IoT Edge zařízení následujícím způsobem:

* Každá šablona zařízení IoT Edge má model schopností.
* Pro každý vlastní modul uvedený v manifestu nasazení je vygenerován model schopností modulu.
* Mezi jednotlivými modely schopností modulu a modelem zařízení je vytvořen vztah.
* Model schopností modulu implementuje jedno nebo více rozhraní modulů.
* Každé rozhraní modulů obsahuje telemetrii, vlastnosti a příkazy.

### <a name="iot-edge-deployment-manifests-and-iot-central-device-templates"></a>IoT Edge manifestů nasazení a IoT Central šablon zařízení

V IoT Edge nasadíte a spravujete obchodní logiku ve formě modulů. IoT Edge moduly jsou nejmenší jednotkou výpočtu spravovanou pomocí IoT Edge a můžou obsahovat služby Azure, jako je například Azure Stream Analytics nebo vlastní kód specifický pro řešení.

V *manifestu nasazení* IoT Edge jsou uvedeny IoT Edge moduly, které se mají nasadit na zařízení a jak je nakonfigurovat. Další informace najdete v tématu [Naučte se nasazovat moduly a navázat trasy v IoT Edge](../../iot-edge/module-composition.md).

V Azure IoT Central naimportujete manifest nasazení, abyste vytvořili šablonu pro zařízení IoT Edge.

Následující fragment kódu ukazuje příklad IoT Edge manifestu nasazení:

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
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
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
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10
      }
    }
  }
}
```

V předchozím fragmentu kódu uvidíte:

* Existují tři moduly. *Agenti IoT Edge* a systémové moduly *IoT Edge centra* , které jsou k dispozici v každém manifestu nasazení. Vlastní modul **SimulatedTemperatureSensor**
* Image veřejného modulu se nasazuje z Azure Container Registryho úložiště, které nevyžaduje žádné přihlašovací údaje pro připojení. V případě imagí privátního modulu nastavte přihlašovací údaje registru kontejneru tak, aby se používaly v `registryCredentials` nastavení pro modul *IoT Edge agent* .
* Vlastní modul **SimulatedTemperatureSensor** má dvě vlastnosti `"SendData": true` a `"SendInterval": 10` .

Při importu tohoto manifestu nasazení do aplikace IoT Central vygeneruje následující šablonu zařízení:

:::image type="content" source="media/concepts-iot-edge/device-template.png" alt-text="Snímek obrazovky znázorňující šablonu zařízení vytvořenou z manifestu nasazení.":::

Na předchozím snímku obrazovky vidíte:

* Modul s názvem **SimulatedTemperatureSensor**. V šabloně se nezobrazují systémové moduly *agenta IoT Edge* a *centra IoT Edge* .
* Rozhraní označované jako **Správa** , které obsahuje dvě vlastnosti s možností zápisu s názvem **SendData** a **SendInterval**.

Manifest nasazení neobsahuje informace o telemetrie, kterou modul **SimulatedTemperatureSensor** odesílá, nebo příkazy, na které reaguje. Přidejte tyto definice do šablony zařízení ručně, než je publikujete.

Další informace najdete v tématu [kurz: Přidání zařízení Azure IoT Edge do aplikace Azure IoT Central](tutorial-add-edge-as-leaf-device.md).

### <a name="update-a-deployment-manifest"></a>Aktualizace manifestu nasazení

Pokud vytvoříte novou [verzi](howto-version-device-template.md) šablony zařízení, můžete nahradit manifest nasazení novou verzí:

Když nahradíte manifest nasazení, všechna připojená IoT Edge zařízení stáhnou nový manifest a aktualizují jejich moduly. IoT Central ale neaktualizuje rozhraní v šabloně zařízení o žádné změny v konfiguraci modulu. Například pokud nahradíte manifest uvedený v předchozím fragmentu kódu následujícím manifestem, neuvidíte automaticky vlastnost **SendUnits** v rozhraní **pro správu** v šabloně zařízení. Ručně přidejte novou vlastnost do rozhraní pro **správu** pro IoT Central pro rozpoznání:

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
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
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
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10,
           "SendUnits": "Celsius"
      }
    }
  }
}
```

## <a name="deploy-the-iot-edge-runtime"></a>Nasazení modulu runtime IoT Edge

Pokud se chcete dozvědět, kde můžete spustit modul runtime IoT Edge, přečtěte si téma [Azure IoT Edge podporované systémy](../../iot-edge/support.md).

IoT Edge runtime můžete také nainstalovat v následujících prostředích:

* [Instalace nebo odinstalace Azure IoT Edge pro Linux](../../iot-edge/how-to-install-iot-edge.md)
* [Instalace a zřízení Azure IoT Edge pro Linux na zařízení s Windows (Preview)](../../iot-edge/how-to-install-iot-edge-on-windows.md)
* [Spuštění Azure IoT Edge v Virtual Machines Ubuntu v Azure](../../iot-edge/how-to-install-iot-edge-ubuntuvm.md)

## <a name="iot-edge-gateway-devices"></a>Zařízení IoT Edge brány

Pokud jste jako zařízení brány vybrali IoT Edge zařízení, můžete pro zařízení, která chcete připojit k zařízení brány, přidat podřízené vztahy k modelům zařízení.

Další informace najdete v tématu [Postup připojení zařízení přes IoT Edge transparentní bránu](how-to-connect-iot-edge-transparent-gateway.md).

## <a name="next-steps"></a>Další kroky

Pokud jste vývojářem zařízení, navržený další krok se naučíte [vyvíjet vlastní moduly IoT Edge](../../iot-edge/module-development.md).
