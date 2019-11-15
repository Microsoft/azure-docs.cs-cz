---
title: Jak vytvořit a najít kotvy pomocí senzorů na zařízení v C++/NDK | Microsoft Docs
description: Podrobné vysvětlení způsobu vytváření a hledání kotev pomocí senzorů zařízení v C++/NDK.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/19/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: f5de4ae050ff01bc86f8c1e11a2afb2887fd8bd7
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093157"
---
# <a name="how-to-create-and-locate-anchors-using-on-device-sensors-in-cndk"></a>Postup vytváření a hledání kotev pomocí senzorů na zařízení v C++/NDK

> [!div  class="op_single_selector"]
> * [Jednot](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android Java](set-up-coarse-reloc-java.md)
> * [C++/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Prostorové kotvy Azure můžou přidružit zařízení k datům snímače a umístit je na kotvy, které vytvoříte. Tato data je také možné použít k rychlému zjištění, zda jsou v zařízení ukotveny žádné kotvy. Další informace najdete v tématu [hrubá reprostředí](../concepts/coarse-reloc.md).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce se ujistěte, že máte následující:

- Základní znalostní báze C++ a <a href="https://developer.android.com/ndk/" target="_blank">Android Native Development Kit</a>.
- Přečtěte si [Přehled prostorových kotev Azure](../overview.md).
- Bylo dokončeno jedno z [5 minut rychlých startů](../index.yml).
- Přečtěte si [postupy pro vytváření a hledání kotev](../create-locate-anchors-overview.md).

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Allow GPS
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);

// Allow WiFi scanning
sensors->WifiEnabled(true);

// Populate the set of known BLE beacons' UUIDs
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

// Allow the set of known BLE beacons
sensors->BluetoothEnabled(true);
sensors->KnownBeaconProximityUuids(uuids);
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```cpp
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);

// Configure the near-device criteria
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();
nearDeviceCriteria->DistanceInMeters(5.0f);
nearDeviceCriteria->MaxResultCount(25);

// Set the session's locate criteria
auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]