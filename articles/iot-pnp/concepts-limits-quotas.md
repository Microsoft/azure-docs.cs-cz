---
title: Omezení a kvóty technologie Plug and Play IoT | Microsoft Docs
description: Seznamte se s limity, kvótami a omezeními, které platí při použití IoT technologie Plug and Play.
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d965d9cb8b87ce0b67f4fe0c07b660fdfd69cc07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577981"
---
# <a name="iot-plug-and-play-limits-quotas-and-throttles"></a>Omezení, kvóty a omezení technologie Plug and Play IoT

V tomto článku se dozvíte o omezeních, kvótách a omezeních specifických pro IoT technologie Plug and Play. Existují stávající [kvóty IoT Hub a omezení](../iot-hub/iot-hub-devguide-quotas-throttling.md) , které platí i pro ně.

## <a name="iot-hub"></a>IoT Hub

Následující omezení a kvóty platí pro Centrum IoT:

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
