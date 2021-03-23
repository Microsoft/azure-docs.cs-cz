---
title: 'Rychlý Start: vytvoření vlákna modulu zabezpečení'
description: V tomto rychlém startu se dozvíte, jak vytvořit Defender pro modul IoT pro použití s Azure Defenderem pro IoT.
ms.topic: quickstart
ms.date: 1/21/2021
ms.openlocfilehash: 2ee88bd23b7d125ef9244f8ff630ee5eb8cdd015
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782669"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Rychlý Start: vytvoření vlákna modulu azureiotsecurity

V tomto rychlém startu se dozvíte, jak vytvořit jednotlivé _azureiotsecurityy_ modulu pro nová zařízení a vytvořit z něj vlákna pro všechna zařízení v IoT Hub.

## <a name="prerequisites"></a>Předpoklady

Žádné

## <a name="understanding-azureiotsecurity-module-twins"></a>Principy vláken modulu azureiotsecurity

V případě řešení IoT, která jsou integrovaná v Azure, jsou vlákna zařízení v automatizaci správy zařízení a v automatizaci procesů klíčovou rolí.

Defender for IoT nabízí úplnou integraci s vaší stávající platformou správy zařízení IoT a umožňuje vám spravovat stav zabezpečení zařízení a využívat stávající možnosti řízení zařízení.
Integraci programu Defender for IoT se dosahuje pomocí IoT Hubho mechanismu vláken.

Další informace o obecném konceptu vláken v modulech Azure IoT Hub najdete v tématu [vlákna IoT Hubch modulů](../iot-hub/iot-hub-devguide-module-twins.md) .

Defender pro IoT používá modul s dvojitou zátěží a udržuje v modulu zabezpečení pro každé vaše zařízení s dvojitou _azureiotsecurityi_ .

Program Defender-IoT-mikro-agent obsahuje všechny informace týkající se zabezpečení zařízení pro každé vaše zařízení.

Aby bylo možné plně využívat Defender pro funkce IoT, budete muset vytvořit, nakonfigurovat a používat tento program v programu Defender – IoT-Micro pro všechna zařízení v této službě.

## <a name="create-azureiotsecurity-module-twin"></a>Vytvořit dvojitou azureiotsecurity modul

vlákna modulu _azureiotsecurity_ je možné vytvořit dvěma způsoby:

1. [Modul Batch Script](https://aka.ms/iot-security-github-create-module) – automaticky vytvoří vlákna modulu pro nová zařízení nebo zařízení bez toho, aby modul používal výchozí konfiguraci.
1. Ruční úprava každého modulu se zpracovává jednotlivě s konkrétní konfigurací pro každé zařízení.

>[!NOTE]
> Použití metody Batch nepřepíše stávající vlákna modulu azureiotsecurity. Použití metody Batch vytvoří pouze nové moduly v zařízeních, které ještě nemají modul zabezpečení.

V tématu [Konfigurace agenta](how-to-agent-configuration.md) se dozvíte, jak upravit nebo změnit konfiguraci existujícího modulu s dvojitou platností.

Ruční vytvoření nového vlákna modulu _azureiotsecurity_ pro zařízení:

1. V IoT Hub vyhledejte a vyberte zařízení, pro které chcete vytvořit modul zabezpečení.

1. V zařízení vyberte a pak na **Přidat identitu modulu**.

1. Do pole **název identity modulu** zadejte **azureiotsecurity**.

1. Vyberte **Uložit**.

## <a name="verify-creation-of-a-module-twin"></a>Ověření vytvoření vlákna modulu

Ověření, jestli pro konkrétní zařízení existuje nějaký modul zabezpečení:

1. V IoT Hub Azure vyberte v nabídce **průzkumníky** možnost **zařízení IoT** .

1. Zadejte ID zařízení nebo vyberte možnost v **poli dotazovací zařízení** a vyberte možnost **dotazovat zařízení**.

    :::image type="content" source="./media/quickstart/verify-security-module-twin.png" alt-text="Dotazování na zařízení":::

1. Vyberte zařízení nebo ho poklikejte a otevřete tak stránku s podrobnostmi o zařízení.

1. Vyberte nabídku **identity modulu** a v seznamu identit modulu přidružených k danému zařízení potvrďte existenci modulu **azureiotsecurity** .

    :::image type="content" source="./media/quickstart/verify-security-module-twin-3.png" alt-text="Moduly přidružené k zařízení":::

Další informace o přizpůsobení vlastností programu Defender pro vlákna modulu IoT najdete v tématu [Konfigurace agenta](how-to-agent-configuration.md).

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak prozkoumat doporučení zabezpečení...

> [!div class="nextstepaction"]
> [Prozkoumání doporučení pro zabezpečení](quickstart-investigate-security-recommendations.md)