---
title: Zjistěte, jak moduly spouštět logiku na vaše zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Moduly Azure IoT Edge jsou kontejnerizovaných jednotky logiku, která je možné nasadit a spravovat vzdáleně, takže můžete spustit obchodní logiku na hraničních zařízeních IoT zařízení
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 744a901c6b0260f4fc14a2f06b88dfb36973b0f8
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456578"
---
# <a name="understand-azure-iot-edge-modules"></a>Vysvětlení modulů Azure IoT Edge

Azure IoT Edge umožňuje nasadit a spravovat obchodní logiku na hraničních zařízeních ve formě *modulů*. Moduly Azure IoT Edge je nejmenší jednotka výpočtu spravuje IoT Edge a může obsahovat služeb Azure (jako je Azure Stream Analytics) nebo vlastní kód specifický pro řešení. Chcete-li pochopit, jak jsou moduly vyvíjeny, nasazeny a udržovány, pomůže si představit čtyři koncepční prvky modulu:

* **Image modulu** je balíček obsahující software, který definuje modul.
* **Instance modulu** je specifická jednotka výpočtu, na které je spuštěná image modulu, na zařízení IoT Edge. Instance modulu je spuštěn modul runtime IoT Edge.
* **Identita modulu** je část informací (včetně zabezpečovacích přihlašovacích údajů) uložených v IoT Hub, která je přidružená ke každé instanci modulu.
* **Nevlákenný modul** je dokument JSON uložený v IoT Hub, který obsahuje informace o stavu instance modulu, včetně metadat, konfigurací a podmínek. 

## <a name="module-images-and-instances"></a>Bitové kopie modulu a instance

Bitové kopie modulu IoT Edge obsahují aplikace, které budou využívat správu, zabezpečení a komunikační funkce modulu runtime IoT Edge. Můžete vyvíjet vlastní bitové kopie modulu nebo exportovat z podporované služby Azure, jako je Azure Stream Analytics.
Obrázky existovat v cloudu a může být aktualizován, změnit a nasazené v různých řešeních pro. Například modul, který využívá strojové učení k předpovědi výrobní linky výstup existuje jako samostatná bitová kopie než modul, který se používá k řízení dron pro počítačové zpracování obrazu. 

Pokaždé, když modul image je nasazená na zařízení a tím, že modul runtime IoT Edge je vytvořena nová instance tohoto modulu. Stejná image modulu můžou používat dvě zařízení v různých částech světa. Každé zařízení by ale mělo vlastní instanci modulu, když je modul na zařízení spuštěný. 

![Diagram - bitové kopie modulu v cloudu, instancí modulu na zařízeních](./media/iot-edge-modules/image_instance.png)

V implementaci moduly imagí existovat jako imagí kontejnerů v úložišti a instance modulu jsou kontejnery na zařízeních. 

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Modul identity

Když je vytvořena nová instance modulu modulem runtime IoT Edge, instance je spojené s odpovídající identitu modulu. Modul identity je uložená ve službě IoT Hub a pracuje jako obor adresování a zabezpečení pro všechny místní a cloudové komunikaci pro danou instanci modulu pro konkrétní.

Identita spojenou s instancí modulu závisí na identitě zařízení, na kterém běží instance a název zadáte do tohoto modulu ve vašem řešení. Například pokud voláte `insight` modul, který používá Azure Stream Analytics a nasadíte ho do zařízení s názvem `Hannover01`, modul runtime IoT Edge vytvoří odpovídající identitu modulu nazvanou `/devices/Hannover01/modules/insight`.

Je zřejmé ve scénářích když potřebujete nasadit jednu image modul více než jednou na jednom zařízení můžete nasadit stejnou bitovou kopii několikrát s různými názvy.

![Diagram – modul identity musí být jedinečné v rámci zařízení a napříč zařízeními](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Dvojčaty modulů

Každá instance modulu má také odpovídající dvojčete modulu, který vám pomůže nakonfigurovat instanci modulu. Instance a dvojčeti jsou spojeny s navzájem prostřednictvím modulu identity. 

Dvojče modulu je dokument JSON, který ukládá vlastnosti informace a konfigurace modulu. Tento koncept se paralelně dojedná o neIoT Hubý koncept [zařízení](../iot-hub/iot-hub-devguide-device-twins.md) . Struktura vlákna modulu je stejná jako nevlákenná zařízení. Rozhraní API používaná pro interakci s oběma typy dvojčat jsou také stejné. Jediným rozdílem mezi těmito dvěma je identity použité k vytvoření instance klientskou sadou SDK. 

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings); 
await client.OpenAsync(); 
 
// Get the module twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="offline-capabilities"></a>Offline možnosti

Azure IoT Edge moduly můžou pracovat offline po neomezenou dobu po synchronizaci s IoT Hub aspoň jednou. Zařízení IoT Edge můžou také tuto funkci offline rozšiřuje na jiná zařízení IoT. Další informace najdete v tématu [vysvětlení rozšířených funkcí offline pro IoT Edge zařízení, moduly a podřízená zařízení](offline-capabilities.md).

## <a name="next-steps"></a>Další kroky
 - [Pochopení požadavků a nástrojů pro vývoj IoT Edgech modulů](module-development.md)
 - [Pochopení Azure IoT Edge runtime a jeho architektury](iot-edge-runtime.md)

