---
title: Vytvoření vlákna programu IoT Micro agent v programu Defender (Preview)
titleSuffix: Azure Defender for IoT
description: Naučte se, jak pro nová zařízení vytvořit jednotlivé vlákna DefenderIotMicroAgent modulu.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 7935db0a78db59abaf2e9983c5ebb56bb4365a7e
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99810131"
---
# <a name="create-a-defender-iot-micro-agent-module-twin-preview"></a>Vytvoření vlákna programu IoT Micro agent v programu Defender (Preview)

Pro nová zařízení můžete vytvořit vlastní vlákna **DefenderIotMicroAgent** modulu. Pro všechna zařízení v IoT Hub můžete také dávkově vytvořit vlákna. 

## <a name="device-twins"></a>Vlákna zařízení 

V případě řešení IoT, která jsou integrovaná v Azure, jsou vlákna zařízení v automatizaci správy zařízení a v automatizaci procesů klíčovou rolí. 

Defender pro IoT nabízí úplnou integraci s vaší stávající platformou správy zařízení IoT. Plná integrace umožňuje spravovat stav zabezpečení vašeho zařízení a umožňuje využívat všechny existující možnosti ovládání zařízení. Integrace se dosahuje pomocí IoT Hubho mechanismu vláken. 

Přečtěte si další informace o pojmu [nevláken zařízení](../iot-hub/iot-hub-devguide-device-twins.md)   v Azure IoT Hub. 

## <a name="security-module-twins"></a>Modul zabezpečení je nevlákenný. 

Defender pro IoT používá pro každé zařízení dvojitou bezpečnostní modul. Modul zabezpečení obsahuje všechny informace, které jsou důležité pro zabezpečení zařízení, a to pro každé konkrétní zařízení ve vašem řešení. Vlastnosti zabezpečení zařízení jsou nakonfigurovány prostřednictvím vyhrazeného modulu zabezpečení pro bezpečnější komunikaci, k povolení aktualizací a údržbě, která vyžaduje méně prostředků. 

## <a name="understanding-defenderiotmicroagent-module-twins"></a>Principy vláken modulu DefenderIotMicroAgent 

Vlákna zařízení hrají klíčovou roli v automatizaci správy a zpracování zařízení pro řešení IoT, která jsou integrovaná do Azure.

Defender pro IoT nabízí možnost plně integrovat stávající platformu pro správu zařízení IoT, která vám umožní spravovat stav zabezpečení zařízení a využívat stávající možnosti ovládání zařízení. Svůj Defender pro IoT můžete integrovat pomocí IoT Hub mechanismu vláken.  

Další informace o obecném konceptu nevláken modulů v Azure IoT Hub najdete v tématu [vlákna IoT Hub modulu](../iot-hub/iot-hub-devguide-module-twins.md).

Defender pro IoT používá modul s dvojitou zátěží a udržuje v modulu zabezpečení `DefenderIotMicroAgent` pro každé vaše zařízení dvojitou hodnotu. 

Abyste mohli plně využít všech výhod programu Defender for IoT, musíte pro každé zařízení ve službě vytvořit, nakonfigurovat a použít modul zabezpečení. 

## <a name="create-defenderiotmicroagent-module-twin"></a>Vytvořit dvojitou DefenderIotMicroAgent modul 

Vlákna modulu **DefenderIotMicroAgent** se dají vytvořit ruční úpravou každého modulu, aby zahrnoval konkrétní konfiguraci pro každé zařízení. 

Ruční vytvoření nového vlákna modulu **DefenderIotMicroAgent** pro zařízení: 

1. V IoT Hub vyhledejte a vyberte zařízení, na kterém chcete vytvořit modul zabezpečení. 

1. Vyberte **Přidat identitu modulu**. 

1. Do pole **název identity modulu**   a zadejte  `DefenderIotMicroAgent` . 

1. Vyberte **Uložit**. 

## <a name="verify-the-creation-of-a-module-twin"></a>Ověření vytvoření vlákna modulu 

Ověření, jestli pro konkrétní zařízení existuje nějaký modul zabezpečení: 

1. V IoT Hub Azure vyberte v ****   nabídce **průzkumníky** možnost zařízení IoT   . 

1. Zadejte ID zařízení nebo vyberte možnost v poli **dotazovací zařízení** a vyberte možnost **dotazovat zařízení**.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/iot-devices.png" alt-text="Vyberte zařízení dotazů, abyste získali seznam zařízení.":::

1. Vyberte zařízení a otevřete stránku s **podrobnostmi o zařízení** . 

1. Vyberte nabídku **identity modulu**   a v seznamu identit modulu přidružených k danému zařízení potvrďte existenci modulu **DefenderIotMicroAgent** .  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/device-details-module.png" alt-text="Z karty vyberte identity modulu.":::

## <a name="next-steps"></a>Další kroky 

V dalším článku se dozvíte, jak [prozkoumat doporučení zabezpečení](quickstart-investigate-security-recommendations.md).
