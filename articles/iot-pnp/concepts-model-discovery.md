---
title: Implementace zjišťování modelu náhledu IoT Plug and Play | Dokumenty společnosti Microsoft
description: Jako vývojář řešení se dozvíte, jak můžete implementovat zjišťování modelu IoT Plug and Play ve vašem řešení.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 66da0321930ac38217a336380c9889963a433e67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75531356"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Implementace zjišťování modelu Náhled technologie IoT Plug and Play v řešení IoT

Tento článek popisuje, jak jako vývojář řešení můžete implementovat zjišťování modelu IoT Plug and Play Preview v řešení IoT.  Zjišťování modelu IoT Plug and Play je způsob, jakým zařízení IoT Plug and Play identifikují své podporované modely a rozhraní schopností a jak řešení IoT načítá tyto modely a rozhraní schopností.

Existují dvě široké kategorie řešení IoT: účelová řešení, která pracují se známou sadou zařízení IoT Plug and Play, a modelová řešení, která pracují s jakýmkoli zařízením IoT Plug and Play.

Tento koncept článku popisuje, jak implementovat zjišťování modelu v obou typech řešení.

## <a name="model-discovery"></a>Zjišťování modelů

Když se zařízení IoT Plug and Play nejprve připojí k vašemu centru IoT Hub, odešle zprávu o telemetrii informací o modelu. Tato zpráva obsahuje ID rozhraní, které zařízení implementuje. Aby vaše řešení fungovalo se zařízením, musí vyřešit tato ID a načíst definice pro každé rozhraní.

Tady jsou kroky, které zařízení IoT Plug and Play provede, když se k rozbočovači (Device Provisioning Service) používá služba DPS:

1. Když je zařízení zapnuto, připojí se ke globálnímu koncovému bodu pro DPS a ověří se pomocí jedné z povolených metod.
1. DPS pak ověří zařízení a vyhledá pravidlo, které mu říká, které služby IoT hub přiřadit zařízení. DPS pak zaregistruje zařízení s tímto rozbočovačem.
1. DPS vrátí k zařízení připojovací řetězec ioT hubu.
1. Zařízení pak odešle telemetrickou zprávu zjišťování do vašeho služby IoT Hub. Zpráva telemetrie zjišťování obsahuje ID rozhraní, která zařízení implementuje.
1. Zařízení IoT Plug and Play je teď připravené ke práci s řešením, které používá váš iot hub.

Pokud se zařízení připojí přímo k vašemu centru IoT hub, připojí se pomocí připojovacího řetězce, který je vložený v kódu zařízení. Zařízení pak odešle telemetrickou zprávu zjišťování do vašeho služby IoT Hub.

Další informace o telemetrické zprávě informace o modelu najdete v tématu [ModelInformation](concepts-common-interfaces.md) interface.

### <a name="purpose-built-iot-solutions"></a>Účelová řešení IoT

Účelové řešení IoT funguje se známou sadou modelů a rozhraní zařízení IoT Plug and Play.

Budete mít model schopností a rozhraní pro zařízení, která se budou předem připojovat k vašemu řešení. Řešení připravíte pomocí následujících kroků:

1. Uložte soubory JSON rozhraní v Azure v umístění, kde je vaše řešení může číst.
1. Logiku napište do svého řešení IoT na základě očekávaných modelů a rozhraní schopností IoT Plug and Play.
1. Přihlaste se k odběru oznámení z centra IoT, které vaše řešení používá.

Když obdržíte oznámení o novém připojení zařízení, postupujte takto:

1. Přečtěte si zprávu o telemetrii zjišťování a načtěte ID modelu schopností a rozhraní implementovaných zařízením.
1. Porovnejte ID modelu schopností s ID modelů schopností, které jste uložili předem.
1. Nyní víte, jaký typ zařízení se připojil. Použijte logiku, kterou jste napsali dříve, abyste uživatelům umožnili vhodnou interakci se zařízením.

### <a name="model-driven-solutions"></a>Modelová řešení

Modelem řízené řešení IoT může fungovat s libovolným zařízením IoT Plug and Play. Vytváření modelového řešení IoT Solution je složitější, ale výhodou je, že vaše řešení bude fungovat se všemi zařízeními přidanými v budoucnu.

Chcete-li vytvořit modelem řízené řešení IoT, musíte vytvořit logiku proti primitivům rozhraní IoT Plug and Play: telemetrie, vlastnosti a příkazy. Logika vašeho řešení IoT představuje zařízení kombinací více telemetrických, vlastností a možností příkazů.

Vaše řešení se musí také přihlásit k odběru oznámení z centra IoT, které používá.

Když vaše řešení obdrží oznámení o novém připojení zařízení, postupujte takto:

1. Přečtěte si zprávu o telemetrii zjišťování a načtěte ID modelu schopností a rozhraní implementovaných zařízením.
1. Pro každé ID si přečtěte celý soubor JSON a najděte možnosti zařízení.
1. Zkontrolujte, zda jsou každé rozhraní k dispozici ve všech mezipamětích, které jste vytvořili pro ukládání souborů JSON načtených dříve vaším řešením.
1. Potom zkontrolujte, zda rozhraní s tímto ID je k dispozici v úložišti veřejného modelu. Další informace naleznete v [tématu Public model repository](howto-manage-models.md).
1. Pokud rozhraní není k dispozici v úložišti veřejného modelu, zkuste ho hledat v libovolném úložišti modelů společnosti, které vaše řešení znají. Potřebujete připojovací řetězec pro přístup k úložišti modelu společnosti. Další informace naleznete v tématu [Úložiště modelů společnosti](howto-manage-models.md).
1. Pokud nemůžete najít všechna rozhraní v úložišti veřejného modelu nebo v úložišti modelu společnosti, můžete zkontrolovat, zda zařízení může poskytnout definici rozhraní. Zařízení může implementovat standardní rozhraní [ModelDefinition](concepts-common-interfaces.md) publikovat informace o tom, jak načíst soubory rozhraní pomocí příkazu.
1. Pokud jste našli soubory JSON pro každé rozhraní implementované zařízením, můžete vytvořit výčet možností zařízení. Použijte logiku, kterou jste napsali dříve, abyste uživatelům umožnili interakci se zařízením.
1. Kdykoli můžete volat rozhraní API digitálních dvojčat a načíst ID modelu schopností a ID rozhraní pro zařízení.

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o zjišťování modelu řešení IoT, další informace o [platformě Azure IoT](overview-iot-plug-and-play.md) k využití dalších funkcí pro vaše řešení.
