---
title: Omezení a kvóty IoT Plug and Play Preview | Dokumenty společnosti Microsoft
description: Seznamte se s omezeními, kvótami a omezeními, které platí při použití náhledu IoT Plug and Play.
author: miagdp
ms.author: miag
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 48ecaaba6d956efd9da75d0582fa06d231cb3f80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75531373"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Limity, kvóty a omezení náhledu ioT Plug and Play

Tento článek vysvětluje omezení specifické pro ioT plug and play, kvóty a omezení, které platí ve verzi Public Preview. Existují existující [kvóty ioT hub a omezení,](../iot-hub/iot-hub-devguide-quotas-throttling.md) které také platí.

## <a name="iot-hub"></a>IoT Hub

Pro verzi Public Preview platí pro centrum IoT následující omezení a kvóty:

| Limity, omezení a omezení | Hodnota | Poznámky |
|-----|-----|-----|
| Počet modelů schopností zařízení (DCM) nebo rozhraní, která lze zaregistrovat na rozbočovač | 1 500 ||
| Maximální počet rozhraní, která lze zaregistrovat na zařízení | 40 ||
| Maximální počet DCM, které lze zaregistrovat na zařízení | 1 ||
| Maximální velikost souboru rozhraní/DCM | 512 znaků ||
| Maximální velikost názvu rozhraní | 256 znaků ||
| Maximální velikost názvu vlastnosti  | 64 bajtů, 7 úrovní do hloubky (a první úroveň je vyhrazena pro) `$iotin` | Povolené znaky: a-z, A-Z, 0-9 (ne jako první znak) a podtržítko. |
| Maximální velikost hodnoty vlastnosti | 512 bajtů ||
| Maximální velikost názvu příkazu | 100 bajtů ||
| Velikost dvojčete zařízení | Stejné jako [limity ioT hubu](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| Volání rozhraní API řešení napříč skladovou položkou (bez ohledu na jednotky) | 100 žádostí za sekundu ||

## <a name="model-repository"></a>Úložiště modelů

Pro verzi Public Preview platí pro úložiště modelu následující omezení a kvóty:

| Limity, omezení a omezení| Hodnota |
|-----|-----|
| Počet úložišť firemních modelů na klienta Služby Azure Active Directory | 1 |
| Počet autorizačních klíčů na úložiště modelu | 10  |
| Počet modelů (DCM nebo rozhraní) podle úložiště modelů společnosti| 1 500  |
| Počet modelů (DCM nebo rozhraní) v úložišti veřejných modelů na klienta Služby Azure Active Directory| 1 500  |
| Počet odstraněných modulů DCM nebo rozhraní v úložišti modelu společnosti | 10 dotazů za sekundu (QPS)|
| Počet úložišť modelů, které vytváří nebo aktualizuje klient| 1 QPS |
| Počet vytvářených/aktualizovaných/odstraněných autorizačních klíčů v úložišti modelů | 1 QPS|
| Počet dcmů vytvořených v úložišti modelu společnosti | 10 QPS |
| Počet rozhraní vytvořených v úložišti modelu společnosti | 10 QPS|
| Počet dcmů vytvořených v úložišti veřejných modelů | 10 QPS|
| Počet rozhraní vytvářených v úložišti veřejných modelů | 10 QPS|

## <a name="parser-library"></a>Knihovna analyzátorů

Knihovna analyzátoru dodržuje omezení, která platí pro [jazyk digital twin definition .](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)

## <a name="next-steps"></a>Další kroky

Dalším doporučeným krokem je naučit se [připojit k zařízení IoT Plug and Play a pracovat s ním](./howto-develop-solution.md).
