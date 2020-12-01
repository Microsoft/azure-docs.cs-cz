---
title: Modul zabezpečení a vlákna zařízení
description: Přečtěte si o konceptu nevláken modulu zabezpečení a o tom, jak se používají v programu Defender pro IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: b33c456d47426a3721e8582f24ffd603db0429c9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340029"
---
# <a name="security-module"></a>Modul zabezpečení

Tento článek vysvětluje, jak Defender for IoT používá vlákna a moduly zařízení.

## <a name="device-twins"></a>Vlákna zařízení

V případě řešení IoT, která jsou integrovaná v Azure, jsou vlákna zařízení v automatizaci správy zařízení a v automatizaci procesů klíčovou rolí.

Defender for IoT nabízí úplnou integraci s vaší stávající platformou správy zařízení IoT a umožňuje vám spravovat stav zabezpečení zařízení a využívat stávající možnosti řízení zařízení. Integrace se dosahuje pomocí IoT Hubho mechanismu vláken.

Přečtěte si další informace o pojmu [nevláken zařízení](../iot-hub/iot-hub-devguide-device-twins.md) v Azure IoT Hub.

## <a name="security-module-twins"></a>Modul zabezpečení je nevlákenný.

Defender for IoT udržuje pro každé zařízení v této službě dvojitou bezpečnostní modul.
Modul zabezpečení obsahuje všechny informace týkající se zabezpečení zařízení pro každé konkrétní zařízení ve vašem řešení.
Vlastnosti zabezpečení zařízení se udržují ve vyhrazeném modulu zabezpečení, který je v bezpečí pro bezpečnější komunikaci a povolování aktualizací a údržby, které vyžadují méně prostředků.

Informace o tom, jak vytvořit, přizpůsobit a nakonfigurovat vlákna, najdete v tématu [vytvoření vlákna zabezpečení](quickstart-create-security-twin.md) a [Konfigurace agentů zabezpečení](how-to-agent-configuration.md) . Další informace o pojmu nevláken modulu v IoT Hub najdete v tématu [Principy vláken modulů](../iot-hub/iot-hub-devguide-module-twins.md) .

## <a name="see-also"></a>Viz také

- [Přehled programu Defender for IoT](overview.md)
- [Nasazení agentů zabezpečení](how-to-deploy-agent.md)
- [Metody ověřování agenta zabezpečení](concept-security-agent-authentication-methods.md)