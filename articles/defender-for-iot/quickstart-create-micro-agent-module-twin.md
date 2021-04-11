---
title: 'Rychlý Start: vytvoření neaplikace Defender IoT Micro agent Module (Preview)'
description: V tomto rychlém startu se dozvíte, jak vytvořit jednotlivé vlákna DefenderIotMicroAgent modulu pro nová zařízení.
ms.date: 1/20/2021
ms.topic: quickstart
ms.openlocfilehash: e1bcaa76e9bfbe417bfd0d4b539b8e0c7ff95c7b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384507"
---
# <a name="quickstart-create-a-defender-iot-micro-agent-module-twin-preview"></a>Rychlý Start: vytvoření neaplikace Defender IoT Micro agent Module (Preview)

Pro nová zařízení můžete vytvořit vlastní vlákna **DefenderIotMicroAgent** modulu. Pro všechna zařízení v IoT Hub můžete také dávkově vytvořit vlákna. 

## <a name="prerequisites"></a>Požadavky

- Žádné

## <a name="device-twins"></a>Vlákna zařízení 

V případě řešení IoT, která jsou integrovaná v Azure, jsou vlákna zařízení v automatizaci správy zařízení a v automatizaci procesů klíčovou rolí. 

Defender pro IoT nabízí úplnou integraci s vaší stávající platformou správy zařízení IoT. Plná integrace umožňuje spravovat stav zabezpečení vašeho zařízení a umožňuje využívat všechny existující možnosti ovládání zařízení. Integrace se dosahuje pomocí IoT Hubho mechanismu vláken. 

Přečtěte si další informace o pojmu [nevláken zařízení](../iot-hub/iot-hub-devguide-device-twins.md)   v Azure IoT Hub. 

## <a name="defender-iot-micro-agent-twins"></a>Defender – IoT-mikro-agenti vlákna 

Defender pro IoT používá pro každé zařízení dvojitou hodnotu typu "Defender-IoT-Micro-agent". V programu Defender-IoT-Micro-Agent se zobrazí všechny informace, které jsou důležité pro zabezpečení zařízení, pro každé konkrétní zařízení ve vašem řešení. Vlastnosti zabezpečení zařízení se konfigurují prostřednictvím vyhrazeného programu Defender-IoT-Micro-Agent pro bezpečnější komunikaci, povolení aktualizací a údržbě, která vyžaduje méně prostředků. 

## <a name="understanding-defenderiotmicroagent-module-twins"></a>Principy vláken modulu DefenderIotMicroAgent 

Vlákna zařízení hrají klíčovou roli v automatizaci správy a zpracování zařízení pro řešení IoT, která jsou integrovaná do Azure.

Defender pro IoT nabízí možnost plně integrovat stávající platformu pro správu zařízení IoT, která vám umožní spravovat stav zabezpečení zařízení a využívat stávající možnosti ovládání zařízení. Svůj Defender pro IoT můžete integrovat pomocí IoT Hub mechanismu vláken.  

Další informace o obecném konceptu nevláken modulů v Azure IoT Hub najdete v tématu [vlákna IoT Hub modulu](../iot-hub/iot-hub-devguide-module-twins.md).

Defender pro IoT používá modul s dvojitou zátěží a udržuje v programu Defender-IoT-mikro-agent s názvem `DefenderIotMicroAgent` pro každé vaše zařízení. 

Abyste mohli plně využít všech výhod programu Defender for IoT, musíte pro každé zařízení v této službě vytvořit, nakonfigurovat a používat funkce Defender-IoT-mikro-agent. 

## <a name="create-defenderiotmicroagent-module-twin"></a>Vytvořit dvojitou DefenderIotMicroAgent modul 

Vlákna modulu **DefenderIotMicroAgent** se dají vytvořit ruční úpravou každého modulu, aby zahrnoval konkrétní konfiguraci pro každé zařízení. 

Ruční vytvoření nového vlákna modulu **DefenderIotMicroAgent** pro zařízení: 

1. V IoT Hub vyhledejte a vyberte zařízení, na kterém chcete vytvořit program Defender-IoT-Micro-agent. 

1. Vyberte **Přidat identitu modulu**. 

1. Do pole **název identity modulu**   a zadejte  `DefenderIotMicroAgent` . 

1. Vyberte **Uložit**. 

## <a name="verify-the-creation-of-a-module-twin"></a>Ověření vytvoření vlákna modulu 

Ověření, zda pro konkrétní zařízení existuje program Defender-IoT-Micro-agent. 

1. V IoT Hub Azure vyberte v ****   nabídce **průzkumníky** možnost zařízení IoT   . 

1. Zadejte ID zařízení nebo vyberte možnost v poli **dotazovací zařízení** a vyberte možnost **dotazovat zařízení**.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/iot-devices.png" alt-text="Vyberte zařízení dotazů, abyste získali seznam zařízení.":::

1. Vyberte zařízení a otevřete stránku s **podrobnostmi o zařízení** . 

1. Vyberte nabídku **identity modulu**   a v seznamu identit modulu přidružených k danému zařízení potvrďte existenci modulu **DefenderIotMicroAgent** .  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/device-details-module.png" alt-text="Z karty vyberte identity modulu.":::

## <a name="next-steps"></a>Další kroky 

> [!div class="nextstepaction"]
> [prozkoumat doporučení zabezpečení](quickstart-investigate-security-recommendations.md)
