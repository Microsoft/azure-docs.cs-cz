---
title: 'Úvodní příručka: Vytvoření dvojčete modulu zabezpečení'
description: V tomto rychlém startu se dozvíte, jak vytvořit dvojče modulu Azure Security Center pro IoT pro použití s Azure Security Center pro IoT.
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310867"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Úvodní příručka: Vytvoření dvojčete modulu azureiotsecurity

Tento rychlý start vysvětluje, jak vytvořit jednotlivá dvojčata _modulu azureiotsecurity_ pro nová zařízení nebo dávková dvojčata modulů pro všechna zařízení v centru IoT Hub.

## <a name="understanding-azureiotsecurity-module-twins"></a>Principy dvojčat modulů azureiotsecurity

U řešení IoT integrovaných v Azure hrají dvojčata zařízení klíčovou roli jak při správě zařízení, tak v automatizaci procesů.

Azure Security Center pro IoT nabízí plnou integraci s vaší stávající platformou pro správu zařízení IoT, což vám umožní spravovat stav zabezpečení zařízení a využívat stávající možnosti řízení zařízení.
Integrace Azure Security Center pro IoT je dosažena využitím mechanismu dvojčete služby IoT Hub.

Další informace o obecném konceptu dvojčat modulů v Azure IoT Hub najdete v tématu [Dvojčata modulů služby IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) Hub.

Azure Security Center pro IoT využívá mechanismus dvojčete modulu a udržuje dvojče modulu zabezpečení s názvem _azureiotsecurity_ pro každé z vašich zařízení.

Dvojče modulu zabezpečení obsahuje všechny informace týkající se zabezpečení zařízení pro každé z vašich zařízení.

Pokud chcete plně využít funkce Azure Security Center for IoT, budete muset vytvořit, nakonfigurovat a používat tato dvojčata modulů zabezpečení pro každé zařízení ve službě.

## <a name="create-azureiotsecurity-module-twin"></a>Vytvoření dvojčete modulu azureiotsecurity

_Azureiotsecurity_ module twins lze vytvořit dvěma způsoby:

1. [Modul dávkový skript](https://aka.ms/iot-security-github-create-module) - automaticky vytvoří dvojče modulu pro nová zařízení nebo zařízení bez dvojčete modulu pomocí výchozí konfigurace.
1. Ruční úprava každého dvojčete modulu jednotlivě s konkrétními konfiguracemi pro každé zařízení.

>[!NOTE]
> Pomocí dávkové metody nebude přepsat existující azureiotsecurity modul dvojčata. Pomocí dávkové metody POUZE vytvoří nová dvojčata modulu pro zařízení, která ještě nemají dvojče modulu zabezpečení.

Informace o tom, jak upravit nebo změnit konfiguraci existujícího dvojčete modulu, naleznete v [tématu konfigurace agenta.](how-to-agent-configuration.md)

Chcete-li ručně vytvořit nový dvojče _modulu azureiotsecurity_ pro zařízení, postupujte podle následujících pokynů:

1. Ve svém IoT Hubu vyhledejte a vyberte zařízení, pro které chcete vytvořit dvojče modulu zabezpečení.
1. Klikněte na zařízení a potom na **Přidat identitu modulu**.
1. Do pole **Název identity modulu** zadejte **azureiotsecurity**.

1. Klikněte na **Uložit**.

## <a name="verify-creation-of-a-module-twin"></a>Ověření vytvoření dvojčete modulu

Ověření, zda pro určité zařízení existuje dvojče modulu zabezpečení:

1. Ve službě Azure IoT Hub vyberte **zařízení IoT** z nabídky **Průzkumníky.**
1. Zadejte ID zařízení nebo vyberte možnost v **poli Dotazovací zařízení** a klikněte na **Dotazová zařízení**.
    ![Dotazovací zařízení](./media/quickstart/verify-security-module-twin.png)
1. Vyberte zařízení nebo na něj poklepejte a otevřete stránku podrobností o zařízení.
1. Vyberte nabídku **Identity modulu** a potvrďte existenci modulu **azureiotsecurity** v seznamu identit modulu přidružených k zařízení.
    ![Moduly přidružené k zařízení](./media/quickstart/verify-security-module-twin-3.png)

Další informace o přizpůsobení vlastností Azure Security Center pro dvojčata modulů IoT najdete v [tématu konfigurace agenta](how-to-agent-configuration.md).

## <a name="next-steps"></a>Další kroky

Přejdete k dalšímu článku a dozvíte se, jak konfigurovat vlastní výstrahy...

> [!div class="nextstepaction"]
> [Konfigurace vlastních výstrah](quickstart-create-custom-alerts.md)
