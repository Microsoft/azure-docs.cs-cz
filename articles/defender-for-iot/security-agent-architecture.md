---
title: 'Rychlý Start: Přehled agentů zabezpečení'
description: V tomto rychlém startu se naučíte pochopit architekturu agenta zabezpečení pro agenty používané ve službě Azure Defender pro službu IoT.
ms.topic: quickstart
ms.date: 01/24/2021
ms.openlocfilehash: 2e7d7d1e6770667b1ce966724611cc003116409d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778504"
---
# <a name="quickstart-security-agent-reference-architecture"></a>Rychlý Start: Referenční architektura agenta zabezpečení

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

## <a name="prerequisites"></a>Požadavky

Žádné

## <a name="agent-supported-platforms"></a>Podporované platformy agenta

Defender for IoT nabízí různé agenty instalačního programu pro 32 bitových a 64 bitů a totéž jako pro 32 bitových 64 a 16bitových systémů Linux. Ujistěte se, že máte pro každé zařízení správný instalační program agentů podle následující tabulky:

| Architektura | Linux | Windows | Podrobnosti |
|--|--|--|--|
| bit 32 | C | C# |  |
| 64bitová verze | C# nebo C | C# | Pro zařízení s více omezenými nebo minimálními prostředky zařízení doporučujeme použít agenta jazyka C. |


## <a name="next-steps"></a>Další kroky

V tomto článku máte přehled o programu Defender pro IoT Defender – IoT-Micro-agent a k dostupným instalačním programům.

Pokud chcete pokračovat v seznámení s Defenderem pro nasazení IoT, postupujte podle následujících článků:

> [!div class="nextstepaction"]
> [metody ověřování agenta zabezpečení](concept-security-agent-authentication-methods.md)
