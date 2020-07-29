---
title: Omezení a kvóty IoT technologie Plug and Play Preview | Microsoft Docs
description: Seznamte se s limity, kvótami a omezeními, které platí při použití IoT technologie Plug and Play ve verzi Preview.
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5c4377120f61792b580225a22b9f5ff51b5e1b64
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337394"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Limity, kvóty a omezení pro IoT technologie Plug and Play Preview

V tomto článku se dozvíte o omezeních, kvótách a omezeních specifických pro IoT technologie Plug and Play, která se vztahují ve verzi Public Preview. Existují stávající [kvóty IoT Hub a omezení](../iot-hub/iot-hub-devguide-quotas-throttling.md) , které platí i pro ně.

## <a name="iot-hub"></a>IoT Hub

Ve verzi Public Preview platí následující omezení a kvóty pro Centrum IoT:

| Omezení, omezení a omezení | Hodnota | Poznámky |
|-----|-----|-----|
| Počet rozhraní, která se dají zaregistrovat pro jednotlivé rozbočovače | 1 500 ||
| Maximální velikost názvu součásti | 1-64 znaků | Povolené znaky: a-z, A-Z, 0-9 (nikoli jako první znak) a podtržítko (nikoli jako první nebo poslední znak). |
| Maximální velikost názvu vlastnosti | 1-64 znaků | Povolené znaky: a-z, A-Z, 0-9 (nikoli jako první znak) a podtržítko (nikoli jako první nebo poslední znak). |
| Maximální velikost hodnoty vlastnosti | Stejné jako [vlastnosti](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property) jazyka definice digitálního vlákna | 5 úrovní hloubky a nemůže být pole nebo žádné komplexní schéma, které obsahuje pole. |
| Maximální velikost názvu příkazu | 1-64 znaků | Povolené znaky: a-z, A-Z, 0-9 (nikoli jako první znak) a podtržítko (nikoli jako první nebo poslední znak).|
| Velikost vlákna zařízení | Stejné jako [omezení IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||

## <a name="parser-library"></a>Knihovna analyzátoru

Knihovna analyzátoru se řídí omezeními, která se vztahují na [jazyk definice digitálních vláken](https://github.com/Azure/opendigitaltwins-dtdl).

## <a name="next-steps"></a>Další kroky

Doporučeným dalším krokem je kontrola [architektury IoT technologie Plug and Play](concepts-architecture.md).
