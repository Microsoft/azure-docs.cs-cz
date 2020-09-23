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
ms.openlocfilehash: a5382313c837482f116f498f3a05c36447062b0a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936877"
---
# <a name="security-module"></a>Modul zabezpečení

Tento článek vysvětluje, jak Defender for IoT používá vlákna a moduly zařízení.

## <a name="device-twins"></a>Vlákna zařízení

V případě řešení IoT, která jsou integrovaná v Azure, jsou vlákna zařízení v automatizaci správy zařízení a v automatizaci procesů klíčovou rolí.

Defender for IoT nabízí úplnou integraci s vaší stávající platformou správy zařízení IoT a umožňuje vám spravovat stav zabezpečení zařízení a využívat stávající možnosti řízení zařízení. Integrace se dosahuje pomocí IoT Hubho mechanismu vláken.

Přečtěte si další informace o pojmu [nevláken zařízení](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) v Azure IoT Hub.

## <a name="security-module-twins"></a>Modul zabezpečení je nevlákenný.

Defender for IoT udržuje pro každé zařízení v této službě dvojitou bezpečnostní modul.
Modul zabezpečení obsahuje všechny informace týkající se zabezpečení zařízení pro každé konkrétní zařízení ve vašem řešení.
Vlastnosti zabezpečení zařízení se udržují ve vyhrazeném modulu zabezpečení, který je v bezpečí pro bezpečnější komunikaci a povolování aktualizací a údržby, které vyžadují méně prostředků.

Informace o tom, jak vytvořit, přizpůsobit a nakonfigurovat vlákna, najdete v tématu [vytvoření vlákna zabezpečení](quickstart-create-security-twin.md) a [Konfigurace agentů zabezpečení](how-to-agent-configuration.md) . Další informace o pojmu nevláken modulu v IoT Hub najdete v tématu [Principy vláken modulů](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) .

## <a name="see-also"></a>Viz také

- [Přehled programu Defender for IoT](overview.md)
- [Nasazení agentů zabezpečení](how-to-deploy-agent.md)
- [Metody ověřování agenta zabezpečení](concept-security-agent-authentication-methods.md)
