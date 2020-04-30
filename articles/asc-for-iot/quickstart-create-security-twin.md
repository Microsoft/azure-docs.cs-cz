---
title: 'Rychlý Start: vytvoření vlákna modulu zabezpečení'
description: V tomto rychlém startu se dozvíte, jak vytvořit Azure Security Center pro práci s modulem IoT pro použití s Azure Security Center for IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: e9c4470ea16f1840274a6fa8613822b20d6772b3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81310867"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Rychlý Start: vytvoření vlákna modulu azureiotsecurity

V tomto rychlém startu se dozvíte, jak vytvořit jednotlivé _azureiotsecurityy_ modulu pro nová zařízení a vytvořit z něj vlákna pro všechna zařízení v IoT Hub.

## <a name="understanding-azureiotsecurity-module-twins"></a>Principy vláken modulu azureiotsecurity

V případě řešení IoT, která jsou integrovaná v Azure, jsou vlákna zařízení v automatizaci správy zařízení a v automatizaci procesů klíčovou rolí.

Azure Security Center pro IoT nabízí úplnou integraci s vaší stávající platformou správy zařízení IoT, což vám umožní spravovat stav zabezpečení zařízení a využívat stávající možnosti řízení zařízení.
Azure Security Center pro integraci IoT se dosahuje tím, že využívá mechanismus IoT Hub vláken.

Další informace o obecném konceptu vláken v modulech Azure IoT Hub najdete v tématu [vlákna IoT Hubch modulů](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) .

Azure Security Center pro IoT využívá modul s dvojitou zátěží a udržuje v modulu zabezpečení pro každé vaše zařízení dvojitou _azureiotsecurityi_ .

Modul zabezpečení obsahuje všechny informace týkající se zabezpečení zařízení pro každé vaše zařízení.

Aby bylo možné plně využívat Azure Security Center pro funkce IoT, budete muset vytvořit, nakonfigurovat a použít tyto prvky zabezpečení pro každé zařízení ve službě.

## <a name="create-azureiotsecurity-module-twin"></a>Vytvořit dvojitou azureiotsecurity modul

vlákna modulu _azureiotsecurity_ je možné vytvořit dvěma způsoby:

1. [Modul Batch Script](https://aka.ms/iot-security-github-create-module) – automaticky vytvoří vlákna modulu pro nová zařízení nebo zařízení bez toho, aby modul používal výchozí konfiguraci.
1. Ruční úprava každého modulu se zpracovává jednotlivě s konkrétní konfigurací pro každé zařízení.

>[!NOTE]
> Použití metody Batch nepřepíše stávající vlákna modulu azureiotsecurity. Použití metody Batch vytvoří pouze nové moduly v zařízeních, které ještě nemají modul zabezpečení.

V tématu [Konfigurace agenta](how-to-agent-configuration.md) se dozvíte, jak upravit nebo změnit konfiguraci existujícího modulu s dvojitou platností.

K ručnímu vytvoření nového vlákna modulu _azureiotsecurity_ pro zařízení použijte následující pokyny:

1. V IoT Hub vyhledejte a vyberte zařízení, pro které chcete vytvořit modul zabezpečení.
1. Klikněte na své zařízení a pak na **Přidat identitu modulu**.
1. Do pole **název identity modulu** zadejte **azureiotsecurity**.

1. Klikněte na **Uložit**.

## <a name="verify-creation-of-a-module-twin"></a>Ověření vytvoření vlákna modulu

Ověření, jestli pro konkrétní zařízení existuje nějaký modul zabezpečení:

1. V IoT Hub Azure vyberte v nabídce **průzkumníky** možnost **zařízení IoT** .
1. Zadejte ID zařízení nebo vyberte možnost v **poli dotazovací zařízení** a klikněte na **dotazovat zařízení**.
    ![Dotazování na zařízení](./media/quickstart/verify-security-module-twin.png)
1. Vyberte zařízení nebo dvakrát klikněte na něj a otevřete stránku s podrobnostmi o zařízení.
1. Vyberte nabídku **identity modulu** a v seznamu identit modulu přidružených k danému zařízení potvrďte existenci modulu **azureiotsecurity** .
    ![Moduly přidružené k zařízení](./media/quickstart/verify-security-module-twin-3.png)

Další informace o přizpůsobení vlastností Azure Security Center pro vlákna modulu IoT najdete v tématu [Konfigurace agenta](how-to-agent-configuration.md).

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak nakonfigurovat vlastní výstrahy...

> [!div class="nextstepaction"]
> [Konfigurace vlastních výstrah](quickstart-create-custom-alerts.md)
