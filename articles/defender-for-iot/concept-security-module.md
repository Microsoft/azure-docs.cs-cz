---
title: Defender – IoT-Micro-agent a vlákna zařízení
description: Přečtěte si o konceptu programu Defender – IoT-Micro – vlákna a o tom, jak se používají v programu Defender pro IoT.
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
ms.openlocfilehash: 552da329b90b102a13ef53158ec81be87684c1fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493182"
---
# <a name="defender-iot-micro-agent"></a>Defender – IoT-Micro Agent

Tento článek vysvětluje, jak Defender for IoT používá vlákna a moduly zařízení.

## <a name="device-twins"></a>Vlákna zařízení

V případě řešení IoT, která jsou integrovaná v Azure, jsou vlákna zařízení v automatizaci správy zařízení a v automatizaci procesů klíčovou rolí.

Defender for IoT nabízí úplnou integraci s vaší stávající platformou správy zařízení IoT a umožňuje vám spravovat stav zabezpečení zařízení a využívat stávající možnosti řízení zařízení. Integrace se dosahuje pomocí IoT Hubho mechanismu vláken.

Přečtěte si další informace o pojmu [nevláken zařízení](../iot-hub/iot-hub-devguide-device-twins.md) v Azure IoT Hub.

## <a name="defender-iot-micro-agent-twins"></a>Defender – IoT-mikro-agenti vlákna

Defender for IoT udržuje pro každé zařízení v této službě vlákna v programu IoT-IoT-Micro.
Program Defender-IoT-mikro-agent obsahuje všechny informace týkající se zabezpečení zařízení pro každé konkrétní zařízení ve vašem řešení.
Vlastnosti zabezpečení zařízení se udržují ve vyhrazeném programu Defender – IoT-mikro-Agent pro bezpečnější komunikaci a pro povolení aktualizací a údržby, které vyžadují méně prostředků.

Další informace o tom, jak vytvořit, přizpůsobit a nakonfigurovat [](how-to-agent-configuration.md) vlastníci, najdete v tématu [vytvoření programu Defender-IoT-Micro-The-IoT-agent s dvojitou](quickstart-create-security-twin.md) posloupností Další informace o pojmu nevláken modulu v IoT Hub najdete v tématu [Principy vláken modulů](../iot-hub/iot-hub-devguide-module-twins.md) .

## <a name="see-also"></a>Viz také

- [Přehled programu Defender for IoT](overview.md)
- [Nasazení agentů zabezpečení](how-to-deploy-agent.md)
- [Metody ověřování agenta zabezpečení](concept-security-agent-authentication-methods.md)