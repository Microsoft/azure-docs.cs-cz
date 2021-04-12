---
title: Vytvoření mostu zařízení v Azure IoT Central | Microsoft Docs
description: Sestavte most IoT Central zařízení pro propojení dalších cloudů IoT (Sigfox, částic, sítě atd.) do aplikace IoT Central.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: how-to
manager: peterpr
ms.custom: device-developer
ms.openlocfilehash: fc8ea41e804344735cfa2400d5d763622d8811c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026246"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Sestavení mostu IoT Central zařízení pro připojení dalších cloudů IoT k IoT Central

*Toto téma se týká správců.*

Most IoT Central zařízení je open source řešení, které spojuje vaše Sigfox, částice, síť a další cloudy do vaší IoT Central aplikace. Bez ohledu na to, jestli používáte zařízení pro sledování prostředků připojená k síti s nízkou spotřebou (Sigfox), nebo pomocí zařízení pro monitorování kvality ovzduší v cloudu zařízení (částic) nebo pomocí zařízení pro monitorování vlhkosti v půdě TTN, můžete přímo využít sílu IoT Central pomocí mostu zařízení IoT Central. Most zařízení spojuje další cloudy IoT s IoT Central tím, že předává data, která vaše zařízení odesílají do ostatních cloudů, prostřednictvím aplikace IoT Central. Ve vaší aplikaci IoT Central můžete vytvářet pravidla a spouštět analýzy těchto dat, vytvářet pracovní postupy v Power automatu a Azure Logic Apps, exportovat tato data a spoustu dalšího. Získání [mostu IoT Central zařízení](https://aka.ms/iotcentralgithubdevicebridge) z GitHubu

## <a name="what-is-it-and-how-does-it-work"></a>Co to je a jak to funguje?
Most zařízení IoT Central je open source řešení na GitHubu. Můžete přejít pomocí tlačítka "nasadit do Azure", které nasadí vlastní šablonu Azure Resource Manager s několika prostředky Azure do vašeho předplatného Azure. Mezi prostředky patří:
-    Aplikace funkcí Azure
-    Účet služby Azure Storage
-    Plán Consumption
-    Azure Key Vault

Aplikace Function App je kritická část mostu zařízení. Přijímá požadavky HTTP POST z jiných platforem IoT nebo jakýchkoli vlastních platforem prostřednictvím jednoduché integrace Webhooku. Poskytli jsme příklady, které ukazují, jak se připojit k cloudům Sigfox, Částicům a TTN. Toto řešení můžete snadno roztáhnout, abyste se připojili k vlastnímu cloudu IoT, pokud vaše platforma může odesílat žádosti HTTP POST do vaší aplikace Function App.
Aplikace Function App transformuje data do formátu přijatého IoT Central a předá je spolu přes rozhraní API DPS.

![Snímek obrazovky Azure Functions](media/howto-build-iotc-device-bridge/azfunctions.png)

Pokud vaše aplikace IoT Central rozpoznala zařízení podle ID zařízení v předané zprávě, zobrazí se pro toto zařízení nové měření. Pokud vaše aplikace IoT Central vaše ID zařízení nikdy neviděla, vaše aplikace Function App se pokusí zaregistrovat nové zařízení s ID tohoto zařízení a v aplikaci IoT Central se zobrazí jako "nepřidružené zařízení". 

## <a name="how-do-i-set-it-up"></a>Návody nastavit?
Pokyny jsou podrobně uvedené v souboru READme v úložišti GitHub. 

## <a name="pricing"></a>Ceny
Prostředky Azure se budou hostovat v rámci vašeho předplatného Azure. Další informace o cenách najdete v [souboru Readme](https://aka.ms/iotcentralgithubdevicebridge).

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili, jak vytvořit most IoT Central zařízení, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [správu zařízení](howto-manage-devices.md)
