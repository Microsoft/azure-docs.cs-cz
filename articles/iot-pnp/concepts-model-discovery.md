---
title: Implementace zjišťování modelu IoT technologie Plug and Play Preview | Microsoft Docs
description: Jako vývojář řešení se dozvíte, jak můžete ve vašem řešení implementovat zjišťování modelu IoT technologie Plug and Play.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 66da0321930ac38217a336380c9889963a433e67
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531356"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Implementace zjišťování modelu IoT technologie Plug and Play Preview v řešení IoT

Tento článek popisuje, jak jako vývojář řešení můžete implementovat zjišťování modelu IoT technologie Plug and Play Preview v řešení IoT.  Zjišťování modelu IoT technologie Plug and Play je způsob, jakým zařízení technologie Plug and Play IoT identifikují své podporované modely a rozhraní schopností a jak řešení IoT načítá tyto modely a rozhraní schopností.

Existují dvě široké kategorie řešení IoT: účelově vytvořená řešení, která pracují se známou sadou IoT technologie Plug and Play zařízení a modelem řízená řešení, která fungují s jakýmkoli zařízením IoT technologie Plug and Play.

Tento článek konceptu popisuje, jak implementovat zjišťování modelu v obou typech řešení.

## <a name="model-discovery"></a>Zjišťování modelů

Když se zařízení IoT technologie Plug and Play poprvé připojí ke službě IoT Hub, pošle zprávu o modelu informace o telemetrie. Tato zpráva obsahuje ID rozhraní, které zařízení implementuje. Aby vaše řešení fungovalo se zařízením, musí tyto identifikátory vyřešit a načíst definice pro každé rozhraní.

Tady jsou kroky, které zařízení IoT technologie Plug and Play přijímá, když používá službu Device Provisioning (DPS) pro připojení k centru:

1. Když je zařízení zapnuté, připojí se ke globálnímu koncovému bodu pro DPS a ověří se pomocí jedné z povolených metod.
1. DPS pak ověří zařízení a vyhledá pravidlo, které oznamuje, ke kterému centru IoT se má zařízení přiřadit. DPS pak zařízení zaregistruje do tohoto centra.
1. DPS vrátí IoT Hub připojovací řetězec k zařízení.
1. Zařízení pak pošle zprávu o telemetrie zjišťování do vašeho IoT Hub. Zpráva telemetrie zjišťování obsahuje ID rozhraní, které zařízení implementuje.
1. Zařízení IoT technologie Plug and Play je teď připravené k práci s řešením, které používá vaše centrum IoT.

Pokud se zařízení připojuje přímo ke službě IoT Hub, připojuje se pomocí připojovacího řetězce, který je vložený v kódu zařízení. Zařízení pak pošle zprávu o telemetrie zjišťování do vašeho IoT Hub.

Další informace o zprávě modelu informace o telemetrie najdete v rozhraní [ModelInformation](concepts-common-interfaces.md) .

### <a name="purpose-built-iot-solutions"></a>Účelově vytvořená řešení IoT

Účelově sestavené řešení IoT funguje se známou sadou IoT technologie Plug and Play modely a rozhraními schopností zařízení.

Budete mít k dispozici model schopností a rozhraní pro zařízení, která se budou k vašemu řešení připojovat předem. K přípravě svého řešení použijte následující postup:

1. Soubory JSON rozhraní uložte do Azure v umístění, kde je vaše řešení může číst.
1. V řešení IoT založeném na očekávaných modelech a rozhraních schopností služby IoT technologie Plug and Play zapište logiku.
1. Přihlaste se k odběru oznámení ze služby IoT Hub, které vaše řešení používá.

Když obdržíte oznámení o novém připojení zařízení, postupujte takto:

1. Přečtěte si zprávu telemetrie zjišťování, která načte ID modelu schopností a rozhraní implementovaná zařízením.
1. Porovnejte ID modelu schopností s ID modelů schopností, které jste si uložili před časem.
1. Nyní víte, jaký typ zařízení se připojil. Pomocí logiky, kterou jste napsali dříve, umožníte uživatelům správně pracovat se zařízením.

### <a name="model-driven-solutions"></a>Modelem řízená řešení

Řešení IoT řízené modelem může pracovat s jakýmkoli zařízením IoT technologie Plug and Play. Sestavování řešení IoT řízené modelem je složitější, ale výhoda spočívá v tom, že vaše řešení funguje se všemi zařízeními přidanými v budoucnu.

Chcete-li vytvořit modelem řízené řešení IoT, je nutné vytvořit logiku proti primitivním rozhraním technologie Plug and Play IoT: telemetrie, vlastnosti a příkazy. Logika vašeho řešení IoT představuje zařízení tak, že kombinuje více možností telemetrie, vlastností a příkazů.

Vaše řešení se taky musí přihlásit k odběru oznámení ze služby IoT Hub, které používá.

Když vaše řešení obdrží oznámení pro nové připojení zařízení, postupujte podle těchto kroků:

1. Přečtěte si zprávu telemetrie zjišťování, která načte ID modelu schopností a rozhraní implementovaná zařízením.
1. Pro každé ID si přečtěte úplný soubor JSON, ve kterém najdete funkce zařízení.
1. Zkontrolujte, jestli se každé rozhraní nachází v mezipamětech, které jste vytvořili pro ukládání souborů JSON načtených dříve v rámci vašeho řešení.
1. Potom zkontrolujte, zda se v úložišti veřejného modelu nachází rozhraní s tímto ID. Další informace najdete v tématu [úložiště veřejného modelu](howto-manage-models.md).
1. Pokud rozhraní není k dispozici v úložišti veřejného modelu, zkuste ho vyhledat v jakýchkoli úložištích modelů společnosti známých ve vašem řešení. Potřebujete připojovací řetězec pro přístup k úložišti podnikového modelu. Další informace najdete v tématu [úložiště modelu společnosti](howto-manage-models.md).
1. Pokud nemůžete najít všechna rozhraní buď v úložišti veřejného modelu, nebo v úložišti podnikového modelu, můžete zjistit, jestli zařízení může definici rozhraní poskytnout. Zařízení může implementovat standardní rozhraní [ModelDefinition](concepts-common-interfaces.md) pro publikování informací o tom, jak načítat soubory rozhraní pomocí příkazu.
1. Pokud jste našli soubory JSON pro každé rozhraní implementované zařízením, můžete vytvořit výčet možností zařízení. Pomocí logiky, kterou jste napsali dříve, můžete uživatelům umožnit interakci se zařízením.
1. V každém okamžiku můžete zavolat rozhraní API digitálního vlákna a načíst ID modelu schopností a ID rozhraní pro dané zařízení.

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o zjišťování modelů v řešení IoT, se dozvíte víc o [platformě Azure IoT](overview-iot-plug-and-play.md) , která využívá další možnosti pro vaše řešení.
