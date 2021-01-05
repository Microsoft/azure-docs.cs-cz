---
title: Architektura agenta zabezpečení
description: Pochopení architektury agenta zabezpečení pro agenty používané ve službě Azure Defender pro IoT Service.
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
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 5773f7af3bb065976e8f05d7b54c58b90da2d3d2
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835121"
---
# <a name="security-agent-reference-architecture"></a>Referenční architektura agenta zabezpečení

Azure Defender pro IoT poskytuje referenční architekturu pro agenty zabezpečení, které protokolují, zpracovávají, agreguje a odesílají data zabezpečení prostřednictvím IoT Hub.

Agenti zabezpečení jsou navrženi pro práci v prostředí s omezeným IoT a jsou vysoce přizpůsobitelné z hlediska hodnot, které poskytují ve srovnání s prostředky, které spotřebovávají.

Agenti zabezpečení podporují tyto funkce:

- Shromážděte nezpracované události zabezpečení ze základního operačního systému (Linux, Windows). Další informace o dostupných kolekcích dat zabezpečení najdete v tématu [Defender pro konfiguraci agenta IoT](how-to-agent-configuration.md).

- Agregovat nezpracované události zabezpečení do zpráv odesílaných pomocí IoT Hub.

- Proveďte ověření pomocí existující identity zařízení nebo identity vyhrazené modulu. Další informace najdete v tématu [metody ověřování agenta zabezpečení](concept-security-agent-authentication-methods.md) .

- Vzdálená konfigurace prostřednictvím použití modulu **azureiotsecurity** s dvojitou platností. Další informace najdete v tématu [Konfigurace programu Defender pro agenta IoT](how-to-agent-configuration.md).

Defender pro agenty zabezpečení IoT se vyvíjí jako open source projekty a jsou k dispozici z GitHubu:

- [Defender pro agenta založeného na IoT C](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender pro agenta založeného na systému IoT C#](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Podporované platformy agenta

Defender for IoT nabízí různé instalační agenty pro 32bitová a 64bitová okna a totéž pro 32bitové a 64bitové systémy Linux. Ujistěte se, že máte pro každé zařízení správný instalační program agentů podle následující tabulky:

| Architektura | Linux | Windows | Podrobnosti |
|--|--|--|--|
| 32 | C | C# |  |
| 64bitový | C# nebo C | C# | Pro zařízení s více omezenými nebo minimálními prostředky zařízení doporučujeme použít agenta jazyka C. |


## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o programu Defender pro architekturu agenta zabezpečení IoT a dostupných instalačních programech.

Pokud chcete pokračovat v seznámení s Defenderem pro nasazení IoT, postupujte podle následujících článků:

- Porozumění [metodám ověřování agenta zabezpečení](concept-security-agent-authentication-methods.md)
- Výběr a nasazení [agenta zabezpečení](how-to-deploy-agent.md)
- Přečtěte si téma Defender for IoT – [požadavky na systém](quickstart-system-prerequisites.md)
- Naučte se, jak [ve IoT Hub povolit Defender pro službu IoT](quickstart-onboard-iot-hub.md) .
- Další informace o službě najdete v tématu [Nejčastější dotazy k programu Defender pro IoT](resources-frequently-asked-questions.md) .
