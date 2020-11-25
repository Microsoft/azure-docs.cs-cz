---
title: Kurz – připojení generické klientské aplikace k Azure IoT Central | Microsoft Docs
description: V tomto kurzu se dozvíte, jak jako vývojář zařízení připojit zařízení s klientskou aplikací v jazyce C, C#, Java, JavaScript nebo Python k vaší aplikaci Azure IoT Central. Automaticky vytvořenou šablonu zařízení upravíte přidáním zobrazení, která operátorovi umožní interakci s připojeným zařízením.
author: dominicbetts
ms.author: dobett
ms.date: 11/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 9eeb8174c900b0f548144231e65643c9559f75e3
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96127040"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application"></a>Kurz: vytvoření a připojení klientské aplikace k aplikaci Azure IoT Central

*Tento článek se týká tvůrců řešení a vývojářů zařízení.*

V tomto kurzu se dozvíte, jak jako vývojář zařízení připojit klientskou aplikaci k aplikaci Azure IoT Central. Aplikace simuluje chování termostatového zařízení. Když se aplikace připojí k IoT Central, pošle ID modelu modelu zařízení termostatu. IoT Central používá ID modelu k načtení modelu zařízení a pro vás vytvoří šablonu zařízení. Přidáte do šablony zařízení vlastní nastavení a zobrazení, která operátorovi umožní komunikovat se zařízením.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte a spusťte kód zařízení a podívejte se, jak se připojí k vaší IoT Central aplikaci.
> * Zobrazit simulovanou telemetrii odeslanou ze zařízení.
> * Přidejte vlastní zobrazení do šablony zařízení.
> * Publikujte šablonu zařízení.
> * Pomocí zobrazení můžete spravovat vlastnosti zařízení.
> * Zavolejte příkaz pro řízení zařízení.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-central-connect-device-c](../../../includes/iot-central-connect-device-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-central-connect-device-csharp](../../../includes/iot-central-connect-device-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-central-connect-device-java](../../../includes/iot-central-connect-device-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-central-connect-device-nodejs](../../../includes/iot-central-connect-device-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-central-connect-device-python](../../../includes/iot-central-connect-device-python.md)]

:::zone-end

## <a name="view-raw-data"></a>Zobrazit nezpracovaná data

Jako vývojář zařízení můžete použít zobrazení **nezpracovaných dat** k prohlédnutí nezpracovaných dat, která zařízení posílá do IoT Central:

:::image type="content" source="media/tutorial-connect-device/raw-data.png" alt-text="Zobrazení nezpracovaná data":::

V tomto zobrazení můžete vybrat sloupce, které chcete zobrazit, a nastavit časový rozsah, který chcete zobrazit. Sloupec **nemodelovaná data** zobrazuje data ze zařízení, která neodpovídají žádné definici vlastnosti nebo telemetrie v šabloně zařízení.

## <a name="next-steps"></a>Další kroky

Pokud budete chtít pokračovat v IoT Central výukových kurzů a další informace o vytváření řešení IoT Central najdete tady:

> [!div class="nextstepaction"]
> [Vytvoření šablony zařízení brány](./tutorial-define-gateway-device-type.md)

Jako vývojář pro zařízení teď, když jste se seznámili se základy vytváření zařízení pomocí Java, jste si udělali několik navrhovaných dalších kroků:

* Přečtěte si, [co jsou šablony zařízení?](./concepts-device-templates.md) Další informace o roli šablon zařízení při implementaci kódu zařízení.
* Další informace o registraci zařízení pomocí IoT Central a o tom, jak IoT Central zabezpečují připojení zařízení, najdete v tématu věnovaném [připojení k Azure IoT Central](./concepts-get-connected.md) .
* Další informace o datech, která zařízení vyměňuje pomocí IoT Central, najdete v tématu [telemetrie, vlastnosti a datové části příkazů](concepts-telemetry-properties-commands.md) .
