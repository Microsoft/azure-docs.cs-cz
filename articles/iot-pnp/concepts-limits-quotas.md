---
title: Omezení a kvóty IoT technologie Plug and Play Preview | Microsoft Docs
description: Seznamte se s limity, kvótami a omezeními, které platí při použití IoT technologie Plug and Play ve verzi Preview.
author: miagdp
ms.author: miag
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 48ecaaba6d956efd9da75d0582fa06d231cb3f80
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531373"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Limity, kvóty a omezení pro IoT technologie Plug and Play Preview

V tomto článku se dozvíte o omezeních, kvótách a omezeních specifických pro IoT technologie Plug and Play, která se vztahují ve verzi Public Preview. Existují stávající [kvóty IoT Hub a omezení](../iot-hub/iot-hub-devguide-quotas-throttling.md) , které platí i pro ně.

## <a name="iot-hub"></a>Centrum IoT

Ve verzi Public Preview platí následující omezení a kvóty pro Centrum IoT:

| Omezení, omezení a omezení | Hodnota | Poznámky |
|-----|-----|-----|
| Počet modelů schopností zařízení (DCMs) nebo rozhraní, která se dají zaregistrovat pro jednotlivé rozbočovače | 1 500 ||
| Maximální počet rozhraní, která se dají zaregistrovat na zařízení | 40 ||
| Maximální počet DCMs, které se můžou registrovat na zařízení | 1\. místo ||
| Maximální velikost rozhraní/souboru DCM | 512 znaků ||
| Maximální velikost názvu rozhraní | 256 znaků ||
| Maximální velikost názvu vlastnosti  | 64 bajtů, 7 úrovní hloubky (a první úroveň je vyhrazena pro `$iotin`) | Povolené znaky: a-z, A-Z, 0-9 (nikoli jako první znak) a podtržítko. |
| Maximální velikost hodnoty vlastnosti | 512 bajtů ||
| Maximální velikost názvu příkazu | 100 bajtů ||
| Velikost vlákna zařízení | Stejné jako [omezení IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| Rozlišení volání rozhraní API napříč SKU (bez ohledu na jednotky) | požadavek 100 za sekundu ||

## <a name="model-repository"></a>Úložiště modelu

Ve verzi Public Preview platí následující omezení a kvóty pro úložiště modelů:

| Omezení, omezení a omezení| Hodnota |
|-----|-----|
| Počet úložišť modelů společnosti na tenanta Azure Active Directory | 1\. místo |
| Počet autorizačních klíčů na úložiště modelu | 10  |
| Počet modelů (DCMs nebo rozhraní) na úložiště modelů společnosti| 1 500  |
| Počet modelů (DCMs nebo rozhraní) ve veřejném úložišti modelu na tenanta Azure Active Directory| 1 500  |
| Počet DCMs nebo rozhraní, která se odstraňují v úložišti podnikového modelu | 10 dotazů za sekundu (QPS)|
| Počet úložišť modelů, které vytváří nebo aktualizuje tenant| 1 QPS |
| Počet autorizačních klíčů, které se vytváří, aktualizují nebo odstraňují v úložišti modelu | 1 QPS|
| Počet DCMs, které se vytváří v úložišti podnikového modelu | 10 QPS |
| Počet rozhraní, která se vytvářejí v úložišti podnikového modelu | 10 QPS|
| Počet DCMs, které se vytváří v úložišti veřejného modelu | 10 QPS|
| Počet rozhraní, která se vytvářejí v úložišti veřejného modelu | 10 QPS|

## <a name="parser-library"></a>Knihovna analyzátoru

Knihovna analyzátoru se řídí omezeními, která se vztahují na [Jazyk digitálního zdvojeného definování](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

## <a name="next-steps"></a>Další kroky

Doporučený další krok se dozvíte, jak se [připojit k zařízení IoT technologie Plug and Play a pracovat s nimi](./howto-develop-solution.md).
