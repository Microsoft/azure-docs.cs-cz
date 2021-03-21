---
title: Informace o tom, jak moduly spouštějí logiku na zařízeních – Azure IoT Edge | Microsoft Docs
description: Moduly Azure IoT Edge jsou kontejnerové jednotky logiky, které se dají nasadit a spravovat vzdáleně, abyste mohli spustit obchodní logiku na IoT Edge zařízeních.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: a9b1ffb2dbcbd6e81856277f4b672cf876cc75f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492366"
---
# <a name="understand-azure-iot-edge-modules"></a>Vysvětlení modulů Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge umožňuje nasadit a spravovat obchodní logiku na hraničních zařízeních ve formě *modulů*. Azure IoT Edge moduly jsou nejmenší jednotkou výpočtu spravovanou pomocí IoT Edge a můžou obsahovat služby Azure (například Azure Stream Analytics) nebo vlastní kód specifický pro řešení. Chcete-li pochopit, jak jsou moduly vyvíjeny, nasazeny a udržovány, vezměte v úvahu čtyři koncepční prvky modulu:

* **Image modulu** je balíček obsahující software, který definuje modul.
* **Instance modulu** je specifická jednotka výpočtu, na které je spuštěná image modulu, na zařízení IoT Edge. Instanci modulu spouští modul runtime IoT Edge.
* **Identita modulu** je část informací (včetně zabezpečovacích přihlašovacích údajů) uložených v IoT Hub, která je přidružená ke každé instanci modulu.
* **Nevlákenný modul** je dokument JSON uložený v IoT Hub, který obsahuje informace o stavu instance modulu, včetně metadat, konfigurací a podmínek.

## <a name="module-images-and-instances"></a>Image a instance modulů

Image modulu IoT Edge obsahují aplikace, které využívají funkce pro správu, zabezpečení a komunikaci IoT Edge modulu runtime. Můžete vyvíjet vlastní image modulu nebo je exportovat z podporované služby Azure, jako je například Azure Stream Analytics.
Image existují v cloudu a dají se aktualizovat, měnit a nasazovat v různých řešeních. Například modul, který používá strojové učení k předpovídání výstupu z produkčního řádku, existuje jako samostatná image, než modul, který k řízení pomocí dronů využívá počítačovou vizi.

Pokaždé, když se image modulu nasadí do zařízení a spustí se IoT Edge runtime, vytvoří se nová instance tohoto modulu. Stejná image modulu můžou používat dvě zařízení v různých částech světa. Každé zařízení by ale mělo vlastní instanci modulu, když je modul na zařízení spuštěný.

![Image modulu v cloudu, instance modulů na zařízeních](./media/iot-edge-modules/image_instance.png)

V implementaci nástroje existují v úložišti image kontejnerů a instance modulů jsou kontejnery na zařízeních.

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Identity modulů

Když IoT Edge modul runtime vytvoří novou instanci modulu, získá odpovídající identitu modulu. Identita modulu je uložena v IoT Hub a slouží jako obor adresování a zabezpečení pro veškerou místní a cloudovou komunikaci pro danou instanci modulu.

Identita přidružená k instanci modulu závisí na identitě zařízení, na kterém je instance spuštěná, a názvu, který do tohoto modulu zadáte, do svého řešení. Například pokud zavoláte `insight` modul, který používá Azure Stream Analytics a nasadíte ho do zařízení s názvem `Hannover01` , modul runtime IoT Edge vytvoří odpovídající identitu modulu s názvem `/devices/Hannover01/modules/insight` .

Ve scénářích, kdy potřebujete na jednom zařízení několikrát nasadit image jednoho modulu, můžete stejný obrázek nasadit několikrát s různými názvy.

![Diagram – identity modulů jsou jedinečné v rámci zařízení i napříč zařízeními.](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Nevlákenná modul

Každá instance modulu má také odpovídající modul s dvojitou dvojicí, kterou můžete použít ke konfiguraci instance modulu. Instance a vlákna jsou vzájemně spojeny prostřednictvím identity modulu.

Nevlákenný modul je dokument JSON, který ukládá informace o modulu a vlastnosti konfigurace. Tento koncept se paralelně dojedná o neIoT Hubý koncept [zařízení](../iot-hub/iot-hub-devguide-device-twins.md) . Struktura vlákna modulu je stejná jako nevlákenná zařízení. Rozhraní API použitá pro interakci s oběma typy vláken jsou také stejná. Jediným rozdílem mezi těmito dvěma je identita, která se používá k vytvoření instance klientské sady SDK.

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

* [Pochopení požadavků a nástrojů pro vývoj IoT Edgech modulů](module-development.md)
* [Pochopení Azure IoT Edge runtime a jeho architektury](iot-edge-runtime.md)
