---
title: Modul zabezpečení a vlákna zařízení
description: Přečtěte si o konceptu nevláken modulu zabezpečení a o tom, jak se používají v Azure Security Center pro IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: d598d291612c6e4f58caf77e1b213b2bc3f42820
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311451"
---
# <a name="security-module"></a>Modul zabezpečení

Tento článek vysvětluje, jak Azure Security Center pro IoT používá vlákna a moduly zařízení.

## <a name="device-twins"></a>Vlákna zařízení

V případě řešení IoT, která jsou integrovaná v Azure, jsou vlákna zařízení v automatizaci správy zařízení a v automatizaci procesů klíčovou rolí.

Azure Security Center pro IoT nabízí úplnou integraci s vaší stávající platformou správy zařízení IoT, což vám umožní spravovat stav zabezpečení zařízení a využívat stávající možnosti řízení zařízení. Integrace se dosahuje pomocí IoT Hubho mechanismu vláken.

Přečtěte si další informace o pojmu [nevláken zařízení](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) v Azure IoT Hub.

## <a name="security-module-twins"></a>Modul zabezpečení je nevlákenný.

Azure Security Center pro IoT udržuje modul zabezpečení pro každé zařízení ve službě.
Modul zabezpečení obsahuje všechny informace týkající se zabezpečení zařízení pro každé konkrétní zařízení ve vašem řešení.
Vlastnosti zabezpečení zařízení se udržují ve vyhrazeném modulu zabezpečení, který je v bezpečí pro bezpečnější komunikaci a povolování aktualizací a údržby, které vyžadují méně prostředků.

Informace o tom, jak vytvořit, přizpůsobit a nakonfigurovat vlákna, najdete v tématu [vytvoření vlákna zabezpečení](quickstart-create-security-twin.md) a [Konfigurace agentů zabezpečení](how-to-agent-configuration.md) . Další informace o pojmu nevláken modulu v IoT Hub najdete v tématu [Principy vláken modulů](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) .

## <a name="see-also"></a>Viz také

- [Přehled Azure Security Center pro IoT](overview.md)
- [Nasazení agentů zabezpečení](how-to-deploy-agent.md)
- [Metody ověřování agenta zabezpečení](concept-security-agent-authentication-methods.md)
