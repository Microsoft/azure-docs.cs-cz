---
title: Hrubá lokální v Unity
description: Podrobné vysvětlení, jak vytvořit a najít kotvy s využitím hrubých místních rozmístění v jazyce C#.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/19/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 42a237caf03d4d3fb6dd9aa70668cbb84ff40072
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89008620"
---
# <a name="how-to-create-and-locate-anchors-using-coarse-relocalization-in-c"></a>Postup vytváření a hledání kotev pomocí hrubého použití v jazyce C #

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android Java](set-up-coarse-reloc-java.md)
> * [/NDK C++](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Prostorové kotvy Azure můžou přidružit zařízení k datům snímače a umístit je na kotvy, které vytvoříte. Tato data je také možné použít k rychlému zjištění, zda jsou v zařízení ukotveny žádné kotvy. Další informace najdete v tématu [hrubá reprostředí](../concepts/coarse-reloc.md).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce se ujistěte, že máte následující:

- Základní znalost jazyka C#.
- Přečtěte si [Přehled prostorových kotev Azure](../overview.md).
- Bylo dokončeno jedno z [5 minut rychlých startů](../index.yml).
- Přečtěte si [postupy pro vytváření a hledání kotev](../create-locate-anchors-overview.md).

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```csharp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Allow GPS
sensorProvider.Sensors.GeoLocationEnabled = true;

// Allow WiFi scanning
sensorProvider.Sensors.WifiEnabled = true;

// Allow a set of known BLE beacons
sensorProvider.Sensors.BluetoothEnabled = true;
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```csharp
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;

// Configure the near-device criteria
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();
nearDeviceCriteria.DistanceInMeters = 5;
nearDeviceCriteria.MaxResultCount = 25;

// Set the session's locate criteria
AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]