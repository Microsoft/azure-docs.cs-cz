---
title: Přehled agentů zabezpečení
description: Pochopení architektury agenta zabezpečení pro agenty používané ve službě Azure Defender pro IoT Service.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: 929b8de8a6b70519bb44e41ba5cfede9ec12f110
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99808857"
---
# <a name="security-agent-reference-architecture"></a>Referenční architektura agenta zabezpečení

Azure Defender pro IoT poskytuje referenční architekturu pro agenty zabezpečení, které protokolují, zpracovávají, agreguje a odesílají data zabezpečení prostřednictvím IoT Hub.

Agenti zabezpečení jsou navrženi pro práci v prostředí s omezeným IoT a jsou vysoce přizpůsobitelné z hlediska hodnot, které poskytují ve srovnání s prostředky, které spotřebovávají.

Agenti zabezpečení podporují tyto funkce:

- Proveďte ověření pomocí existující identity zařízení nebo identity vyhrazené modulu. Další informace najdete v tématu [metody ověřování agenta zabezpečení](concept-security-agent-authentication-methods.md).

- Shromážděte nezpracované události zabezpečení ze základního operačního systému (Linux, Windows). Další informace o dostupných kolekcích dat zabezpečení najdete v tématu [Defender pro konfiguraci agenta IoT](how-to-agent-configuration.md).

- Agregovat nezpracované události zabezpečení do zpráv odesílaných pomocí IoT Hub.

- Vzdálená konfigurace prostřednictvím použití modulu **azureiotsecurity** s dvojitou platností. Další informace najdete v tématu [Konfigurace programu Defender pro agenta IoT](how-to-agent-configuration.md).

Agenti zabezpečení služby IoT pro IoT se vyvíjeli jako open source projekty a jsou k dispozici na GitHubu:

- [Defender pro agenta založeného na IoT C](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender pro agenta založeného na systému IoT C#](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Podporované platformy agenta

Defender for IoT nabízí různé agenty instalačního programu pro 32 bitových a 64 bitů a totéž jako pro 32 bitových 64 a 16bitových systémů Linux. Ujistěte se, že máte pro každé zařízení správný instalační program agentů podle následující tabulky:

| Architektura | Linux | Windows | Podrobnosti |
|--|--|--|--|
| bit 32 | C | C# |  |
| 64bitová verze | C# nebo C | C# | Pro zařízení s více omezenými nebo minimálními prostředky zařízení doporučujeme použít agenta jazyka C. |


## <a name="next-steps"></a>Další kroky

V tomto článku získáte základní přehled o programu Defender pro architekturu modulu zabezpečení IoT a dostupné instalační programy.

Pokud chcete pokračovat v seznámení s Defenderem pro nasazení IoT, postupujte podle následujících článků:

- Porozumění [metodám ověřování agenta zabezpečení](concept-security-agent-authentication-methods.md)
- Výběr a nasazení [agenta zabezpečení](how-to-deploy-agent.md)
- Přečtěte si téma Defender for IoT – [požadavky na systém](quickstart-system-prerequisites.md)
- Naučte se, jak [ve IoT Hub povolit Defender pro službu IoT](quickstart-onboard-iot-hub.md) .
- Další informace o službě najdete v tématu [Nejčastější dotazy k programu Defender pro IoT](resources-frequently-asked-questions.md) .
