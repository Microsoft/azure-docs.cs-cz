---
title: Sestavení mostu zařízení Azure IoT Central | Dokumenty společnosti Microsoft
description: Sestavte most zařízení IoT Central a připojte další cloudy IoT (Sigfox, Particle, The Things Network atd.) s vaší aplikací IoT Central.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: how-to
manager: peterpr
ms.openlocfilehash: 6499c9c29d10a2056b0af5499b68b5edd67d82cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158414"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Sestavte most zařízení IoT Central pro připojení dalších cloudů IoT k IoT Central

*Toto téma platí pro správce.*

Most zařízení IoT Central je open source řešení, které spojuje vaše Sigfox, Částice, The Things Network a další cloudy s vaší aplikací IoT Central. Ať už používáte zařízení pro sledování majetku připojená k síti Sigfox Low-Power-Wide Area Network, nebo používáte zařízení pro sledování kvality ovzduší v cloudu částicových zařízení nebo používáte zařízení pro sledování vlhkosti půdy na TTN, můžete přímo využít sílu IoT Centrální pomocí mostu zařízení IoT Central. Most zařízení propojuje další cloudy IoT s IoT Central přeposíláním dat, která vaše zařízení odesílají do ostatních cloudů, do vaší aplikace IoT Central. V aplikaci IoT Central můžete vytvářet pravidla a spouštět analýzy na tato data, vytvářet pracovní postupy v aplikacích Microsoft Flow a Azure Logic, exportovat tato data a mnoho dalšího. Získání [mostu zařízení IoT Central](https://aka.ms/iotcentralgithubdevicebridge) z GitHubu

## <a name="what-is-it-and-how-does-it-work"></a>Co to je a jak to funguje?
Most zařízení IoT Central je open source řešení v GitHubu. Je připravena k přístupu k tlačítku "Nasazení do Azure", které nasadí vlastní šablonu Azure Resource Manager u několika prostředků Azure do vašeho předplatného Azure. Zdroje zahrnují:
-    Aplikace Azure Function
-    Účet služby Azure Storage
-    Plán Consumption
-    Azure Key Vault

Aplikace funkce je kritická část mostu zařízení. Přijímá požadavky HTTP POST z jiných platforem IoT nebo jakýchkoli vlastních platforem prostřednictvím jednoduché integrace webhooku. Poskytli jsme příklady, které ukazují, jak se připojit k Sigfox, Částic a TTN mraky. Toto řešení můžete snadno rozšířit a připojit se k vlastnímu cloudu IoT, pokud vaše platforma může odesílat požadavky HTTP POST do vaší aplikace funkcí.
Aplikace Function transformuje data do formátu přijatého IoT Central a předává je prostřednictvím dps api.

![Snímek obrazovky s funkcemi Azure](media/howto-build-iotc-device-bridge/azfunctions.png)

Pokud vaše aplikace IoT Central rozpozná zařízení podle ID zařízení v předané zprávě, zobrazí se pro toto zařízení nové měření. Pokud vaše aplikace IoT Central nikdy neviděla ID zařízení, pokusí se vaše aplikace funkce zaregistrovat nové zařízení s tímto ID zařízení a ve vaší aplikaci IoT Central se zobrazí jako "nepřidružené zařízení". 

## <a name="how-do-i-set-it-up"></a>Jak to nastavím?
Pokyny jsou podrobně uvedeny v souboru README v úložišti GitHub. 

## <a name="pricing"></a>Ceny
Prostředky Azure se budou hostovat ve vašem předplatném Azure. Další informace o cenách naleznete v [souboru README](https://aka.ms/iotcentralgithubdevicebridge).

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili, jak vytvořit most zařízení IoT Central, tady je navrhovaný další krok:

> [!div class="nextstepaction"]
> [Správa zařízení](howto-manage-devices.md)
