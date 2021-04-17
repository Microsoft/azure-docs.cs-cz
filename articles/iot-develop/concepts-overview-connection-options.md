---
title: Seznamte se s možnostmi připojení pro vývojáře zařízení Azure IoT.
description: Přečtěte si o běžně používaných možnostech a nástrojích připojení zařízení pro vývojáře zařízení Azure IoT.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 8669919192b1e6394043842d7d23f8829ec7c71e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589546"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>Přehled: možnosti připojení pro vývojáře zařízení Azure IoT
Jako vývojář, který pracuje se zařízeními, máte k dispozici několik možností pro připojení a správu zařízení Azure IoT. V tomto článku najdete přehled nejčastěji používaných možností a nástrojů, které vám pomůžou s připojením a správou zařízení.

Při hodnocení možností připojení Azure IoT pro vaše zařízení je užitečné porovnat tyto položky:
- Aplikační platformy pro zařízení Azure IoT
- Nástroje pro připojení a správu zařízení

## <a name="application-platforms-iot-central-and-iot-hub"></a>Aplikační platformy: IoT Central a IoT Hub
Azure IoT obsahuje dvě služby, které jsou platforem pro cloudové aplikace s podporou zařízení: Azure IoT Central a Azure IoT Hub. Pro hostování aplikace IoT a připojení zařízení můžete použít buď jednu.
- [IoT Central](../iot-central/core/overview-iot-central.md) je navržená tak, aby snižovala složitost a náklady na práci s řešeními IoT. Jedná se o aplikaci SaaS (software jako služba), která poskytuje kompletní platformu pro hostování aplikací IoT. Pomocí webového uživatelského rozhraní IoT Central můžete zjednodušit celý životní cyklus vytváření a správy aplikací IoT. Webové uživatelské rozhraní usnadňuje vytváření aplikací a připojování a správu až po několika až milionů zařízení. IoT Central používá IoT Hub k vytváření a správě aplikací, ale zachovává pro uživatele podrobnosti transparentní. Obecně platí, IoT Central poskytuje omezenou složitost a vývojové úsilí a zjednodušenou správu zařízení prostřednictvím webového uživatelského rozhraní.
- [IoT Hub](../iot-hub/about-iot-hub.md) slouží jako centrální Centrum zpráv pro obousměrnou komunikaci mezi aplikacemi IoT a připojenými zařízeními. Je to aplikace typu platforma jako služba (PaaS), která také poskytuje platformu pro hostování aplikací IoT. Stejně jako u IoT Central se může škálovat na podporu milionů zařízení. Obecně platí, IoT Hub nabízí větší kontrolu a přizpůsobení pro návrh aplikace a další možnosti vývojářských nástrojů pro práci se službou, a to za cenu určitého nárůstu vývoje a složitosti správy.

## <a name="tools-to-connect-and-manage-devices"></a>Nástroje pro připojení a správu zařízení
Po výběru možnosti IoT Hub nebo IoT Central hostování aplikace IoT máte několik možností vývojářských nástrojů. Pomocí těchto nástrojů se můžete připojit k vybrané aplikační platformě IoT a vytvářet a spravovat aplikace a zařízení. Následující tabulka shrnuje běžné možnosti nástrojů. 

> [!NOTE]
> Kromě následujících nástrojů můžete programově vytvářet a spravovat aplikace IoT pomocí REST API, sad Azure SDK nebo šablon Azure Resource Manager. Další informace najdete v dokumentaci ke službě [IoT Hub](../iot-hub/about-iot-hub.md) a [IoT Central](../iot-central/core/overview-iot-central.md) .

|Nástroj  |Podporuje platformu &nbsp; &nbsp; &nbsp; IoT &nbsp; |Dokumentace  |Description  |
|---------|---------|---------|---------|
|Centrální webové uživatelské rozhraní     | Central | [Centrální rychlé zprovoznění](../iot-central/core/quick-deploy-iot-central.md) | Portál založený na prohlížeči pro IoT Central. |
|portál Azure     | Střed, střed      | [Vytvoření centra IoT pomocí Azure Portal](../iot-hub/iot-hub-create-through-portal.md), [správa IoT Central z Azure Portal](../iot-central/core/howto-manage-iot-central-from-portal.md)| Portál založený na prohlížeči pro IoT Hub a zařízení. Funguje taky s dalšími prostředky Azure, včetně IoT Central. |
|Průzkumník Azure IoT     | Rozbočovač | [Průzkumník Azure IoT](https://github.com/Azure/azure-iot-explorer#azure-iot-explorer-preview) | Nejde vytvořit centra IoT Hub. Připojí se ke stávajícímu centru IoT ke správě zařízení. Často se používá v rozhraní příkazového řádku nebo portálu.|
|Azure CLI     | Střed, střed          | [Vytvoření služby IoT Hub pomocí](../iot-hub/iot-hub-create-using-cli.md)rozhraní příkazového řádku, [Správa IoT Central z Azure CLI](../iot-central/core/howto-manage-iot-central-from-cli.md) | Rozhraní příkazového řádku pro vytváření a správu aplikací IoT. |
|Azure PowerShell     | Střed, střed   | [Vytvoření služby IoT Hub pomocí prostředí PowerShell](../iot-hub/iot-hub-create-using-powershell.md), [správa IoT Central z Azure PowerShell](../iot-central/core/howto-manage-iot-central-from-powershell.md) | Rozhraní PowerShell pro vytváření a správu aplikací IoT |
|Azure IoT Tools pro VS Code  | Rozbočovač | [Vytvoření centra IoT pomocí nástrojů pro VS Code](../iot-hub/iot-hub-create-use-iot-toolkit.md) | VS Code rozšíření pro IoT Hub aplikace |

## <a name="next-steps"></a>Další kroky
Pokud chcete získat další informace o možnostech připojení zařízení k Azure IoT, prozkoumejte následující rychlé starty:
- IoT Central: [Vytvoření aplikace Azure IoT Central](../iot-central/core/quick-deploy-iot-central.md)
- IoT Hub: [umožňuje odeslat telemetrii ze zařízení do služby IoT Hub a monitorovat ji pomocí Azure CLI](../iot-hub/quickstart-send-telemetry-cli.md) .