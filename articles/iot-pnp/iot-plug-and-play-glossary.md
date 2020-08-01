---
title: Glosář pojmů – IoT technologie Plug and Play Preview | Microsoft Docs
description: Koncepty – Glosář běžných termínů týkajících se IoT technologie Plug and Play ve verzi Preview.
author: dominicbetts
ms.author: dobett
ms.date: 07/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d8efec0517cd0b4bdcba643a0936b474593d58c4
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475460"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Glosář pojmů pro IoT technologie Plug and Play Preview

Definice běžných termínů, jak se používají v článcích technologie Plug and Play IoT.

## <a name="azure-iot-explorer-tool"></a>Nástroj Azure IoT Explorer

Azure IoT Explorer je grafický nástroj, který můžete použít k interakci a testování [zařízení technologie Plug and Play IoT](#iot-plug-and-play-device). Po instalaci nástroje na místní počítač ho můžete použít k těmto akcím:

- Umožňuje zobrazit zařízení připojená ke službě [IoT Hub](#azure-iot-hub).
- Připojte se k zařízení IoT technologie Plug and Play.
- Zobrazit [součásti](#component)zařízení.
- Podívejte se na [telemetrii](#telemetry) , kterou zařízení odesílá.
- Práce s [vlastnostmi](#properties)zařízení
- Zavolejte [příkazy](#commands)zařízení.

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT Hub je spravovaná služba hostovaná v cloudu, která funguje jako centrum zpráv pro obousměrnou komunikaci mezi aplikací IoT a zařízeními, která spravuje. [Zařízení iot technologie Plug and Play](#iot-plug-and-play-device) se můžou připojit ke službě IoT Hub. Řešení IoT používá ke zpřístupnění služby IoT Hub:

- Zařízení pro posílání telemetrie do cloudového řešení.
- Cloudové řešení pro správu připojených zařízení.

## <a name="azure-iot-device-sdk"></a>Sada SDK pro zařízení Azure IoT

K dispozici jsou sady SDK pro zařízení, které můžete použít k vytvoření klientských aplikací IoT technologie Plug and Play zařízení.

## <a name="commands"></a>Příkazy

Příkazy definované v [rozhraní](#interface) představuje metody, které lze provést na základě [digitálního vlákna](#digital-twin). Například příkaz pro restartování zařízení.

## <a name="component"></a>Komponenta

Komponenty umožňují vytvořit model [rozhraní](#interface) jako sestavení jiných rozhraní. [Model zařízení](#device-model) může kombinovat více rozhraní jako komponenty. Model může například zahrnovat komponentu Switch a termostat. Více komponent v modelu může také používat stejný typ rozhraní. Model může například zahrnovat dvě komponenty termostatu.

## <a name="connection-string"></a>Připojovací řetězec

Připojovací řetězec zapouzdřuje informace potřebné pro připojení ke koncovému bodu. Připojovací řetězec obvykle obsahuje adresu koncového bodu a informace o zabezpečení, ale formáty připojovacích řetězců se liší v rámci služeb. Existují dva typy připojovacího řetězce přidruženého ke službě IoT Hub:

- Připojovací řetězce zařízení umožňují [zařízením iot technologie Plug and Play](#iot-plug-and-play-device) připojit se k koncovým bodům připojeným k zařízení ve službě IoT Hub. Klientský kód v zařízení používá připojovací řetězec k navázání zabezpečeného připojení ke službě IoT Hub.
- Připojovací řetězce IoT Hub umožňují zabezpečené připojení k koncovým bodům pro služby v centru IoT. Tato řešení a nástroje spravují centrum IoT a zařízení, která jsou k němu připojená.

## <a name="device-model"></a>Model zařízení

Model zařízení popisuje [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device) a definuje [součásti](#component) , které tvoří zařízení. Jednoduchý model zařízení nemá žádné samostatné komponenty a obsahuje definici pro jedno rozhraní na kořenové úrovni. Složitější model zařízení obsahuje několik komponent. Model zařízení obvykle odpovídá fyzickému zařízení, produktu nebo SKU. K definování modelu zařízení použijete [jazyk Digital s definicí verze 2](#digital-twins-definition-language) .

## <a name="device-builder"></a>Tvůrce zařízení

Nástroj pro sestavovatele zařízení používá model a [rozhraní](#interface) [zařízení](#device-model) při implementaci kódu ke spuštění na [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device). Tvůrci zařízení obvykle používají jednu ze [sad SDK pro zařízení Azure IoT](#azure-iot-device-sdk) k implementaci klienta zařízení, ale to se nevyžaduje.

## <a name="device-modeling"></a>Modelování zařízení

Nástroj pro [sestavovatele zařízení](#device-builder) používá [jazyk definice digitálních vláken](#digital-twins-definition-language) , který umožňuje modelovat schopnosti [zařízení technologie Plug and Play IoT](#iot-plug-and-play-device). [Tvůrce řešení](#solution-builder) může nakonfigurovat řešení IoT z modelu.

## <a name="digital-twin"></a>Digitální vlákna

Digitální vlákna je model [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device). Digitální vlákna je modelováno pomocí [jazyka definice digitálních vláken](#digital-twins-definition-language). Sady [SDK pro zařízení Azure IoT](#azure-iot-device-sdk) můžete použít k interakci s digitálními interakcemi v době běhu. Například můžete nastavit hodnotu vlastnosti v poli digitálního vlákna na zařízení a sada SDK oznámí tuto změnu řešení IoT v cloudu.

## <a name="digital-twin-change-events"></a>Události změny digitálního vlákna

Když je [zařízení iot technologie Plug and Play](#iot-plug-and-play-device) připojené ke službě [IoT Hub](#azure-iot-hub), může centrum využít schopnost směrování k posílání oznámení o digitálních prostředcích, které se mění. Například pokaždé, když se v zařízení změní hodnota [vlastnosti](#properties) , IoT Hub může odeslat oznámení na koncový bod, jako je například centrum událostí.

## <a name="digital-twins-definition-language"></a>Jazyk definice digitálních vláken

Jazyk popisující modely a rozhraní pro [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device). K popisu možností [digitálního vlákna](#digital-twin) a k tomu, aby mohla platforma IoT a řešení IoT využívat sémantiku této entity, použijte [jazyk Digital Definition Language verze 2](https://github.com/Azure/opendigitaltwins-dtdl) .

## <a name="digital-twin-route"></a>Digitální dvojitá trasa

Trasa nastavená ve [IoT Hub](#azure-iot-hub) pro doručování [událostí digitální události změny](#digital-twin-change-events) do a koncového bodu, jako je například centrum událostí.

## <a name="interface"></a>Rozhraní

Rozhraní popisuje související možnosti, které jsou implementovány [zařízením IoT technologie Plug and Play](#iot-plug-and-play-device) nebo z [digitálního vlákna](#digital-twin). Můžete opakovaně používat rozhraní napříč různými [modely zařízení](#device-model). Když se v modelu zařízení používá rozhraní, definuje [komponentu](#component) zařízení.

## <a name="iot-hub-query-language"></a>Dotazovací jazyk IoT Hub

Dotazovací jazyk IoT Hub slouží k více účelům. Jazyk můžete například použít k vyhledání zařízení zaregistrovaných ve službě IoT Hub nebo k upřesnění chování [digitálního zdvojeného směrování](#digital-twin-route) .

## <a name="iot-plug-and-play-device"></a>Zařízení IoT technologie Plug and Play

Zařízení IoT technologie Plug and Play je typicky malé škálované samostatné výpočetní zařízení, které shromažďuje data nebo řídí jiná zařízení a spouští software nebo firmware, který implementuje [model zařízení](#device-model).  Například zařízení IoT technologie Plug and Play může být zařízením pro monitorování prostředí nebo kontroler pro zavlažovací systémy s inteligentním zemědělstvím. Řešení IoT hostované v cloudu můžete napsat pro příkazy, řízení a příjem dat ze zařízení IoT technologie Plug and Play.

## <a name="iot-plug-and-play-conventions"></a>Konvence IoT Plug and Play

U [zařízení](#iot-plug-and-play-device) IoT technologie Plug and Play se očekává, že se při výměně dat s řešením dodržujte se sadou [konvencí](concepts-convention.md) .

## <a name="model-id"></a>ID modelu

Když se zařízení IoT technologie Plug and Play připojuje k IoT Hub, pošle **ID modelu** [DTDL](#digital-twins-definition-language) modelu, který implementuje. To umožňuje řešení najít model zařízení.

## <a name="model-repository"></a>Úložiště modelů

[Úložiště modelu](concepts-model-repository.md) ukládá [modely zařízení](#device-model) a [rozhraní](#interface).

## <a name="model-repository-rest-api"></a>REST API úložiště modelu

Rozhraní API pro správu a interakci s úložištěm modelu. Rozhraní API můžete například použít k přidání a hledání [modelů zařízení](#device-model).

## <a name="properties"></a>Vlastnosti

Vlastnosti jsou datová pole definovaná v [rozhraní](#interface) , které představuje nějaký stav digitálního vlákna. Vlastnosti můžete deklarovat jako jen pro čtení nebo zapisovatelné. Vlastnosti jen pro čtení, například sériové číslo, jsou nastaveny pomocí kódu spuštěného v rámci samotného [zařízení technologie Plug and Play IoT](#iot-plug-and-play-device) .  Vlastnosti s možností zápisu, jako je například prahová hodnota pro alarm, jsou obvykle nastaveny z cloudového řešení IoT.

## <a name="shared-access-signature"></a>Sdílený přístupový podpis

Signatury sdíleného přístupu jsou mechanismy ověřování založené na zabezpečených hodnotách hash nebo identifikátorech URI SHA-256. Ověřování pomocí sdíleného přístupového podpisu má dvě komponenty: zásady sdíleného přístupu a sdílený přístupový podpis (často se označuje jako token). [Zařízení iot technologie Plug and Play](#iot-plug-and-play-device) používá ke ověřování pomocí služby [IoT Hub](#azure-iot-hub)sdílený přístupový podpis.

## <a name="solution-builder"></a>Tvůrce řešení

Tvůrce řešení vytvoří back-end řešení. Tvůrce řešení obvykle funguje s prostředky Azure, jako jsou [IoT Hub](#azure-iot-hub) a [úložiště modelu](#model-repository).

## <a name="telemetry"></a>Telemetrie

Pole telemetrie definovaná v [rozhraní](#interface) reprezentují měření. Tato měření jsou obvykle hodnoty, jako jsou například čtení senzorů, které jsou odesílány [zařízením IoT technologie Plug and Play](#iot-plug-and-play-device) jako datový proud dat.
