---
title: Principy architektury agenta zabezpečení Azure Security Center pro IoT| Dokumenty společnosti Microsoft
description: Seznamte se s architekturou agenta zabezpečení pro agenty používané ve službě Azure Security Center for IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 998aeab197931a75579fc39b28e3a248b85fc57b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596910"
---
# <a name="security-agent-reference-architecture"></a>Referenční architektura agenta zabezpečení

Azure Security Center pro IoT poskytuje referenční architekturu pro agenty zabezpečení, kteří protokolují, zpracovávají, agregují a odesílají data zabezpečení prostřednictvím služby IoT Hub.

Agenti zabezpečení jsou navrženy tak, aby fungovaly v omezeném prostředí IoT a jsou vysoce přizpůsobitelné z hlediska hodnot, které poskytují ve srovnání s prostředky, které spotřebovávají.

Agenti zabezpečení podporují následující funkce:

- Shromážděte nezpracované události zabezpečení ze základního operačního systému (Linux, Windows). Další informace o dostupných kolekcích dat zabezpečení najdete v [tématu Azure Security Center for IoT agent configuration](how-to-agent-configuration.md).

- Agregujte nezpracované události zabezpečení do zpráv odeslaných prostřednictvím centra IoT Hub.

- Ověřte pomocí existující identity zařízení nebo vyhrazené identity modulu. Další informace najdete v [tématu Metody ověřování agenta zabezpečení.](concept-security-agent-authentication-methods.md)

- Nakonfigurujte vzdáleně pomocí dvojčete modulu **azureiotsecurity.** Další informace najdete [v tématu Konfigurace Centra zabezpečení Azure pro agenta IoT](how-to-agent-configuration.md).

Azure Security Center pro agenty zabezpečení IoT se vyvíjí jako open source projekty a jsou dostupné na GitHubu: 

- [Azure Security Center pro agenta založeného na IoT C](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [Azure Security Center pro agenta založeného na IoT C#](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Platformy podporované agenty

Azure Security Center pro IoT nabízí různé instalační agenty pro 32bitový a 64bitový Systém Windows a totéž pro 32bitový a 64bitový Linux. Ujistěte se, že máte správný instalátor agenta pro každé z vašich zařízení podle následující tabulky:

| Architektura | Linux | Windows |    Podrobnosti|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32bitová  | C  | C#  ||
| 64bitová  | C# nebo C           | C#      | Doporučujeme používat agenta C pro zařízení s omezenějšími nebo minimálními prostředky zařízení.|
|

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o architektuře agenta zabezpečení Azure Security Center pro IoT a dostupných instalačních programech.

Pokud chcete pokračovat v začínáme s Azure Security Center pro nasazení IoT, použijte následující články:

- Principy [metod ověřování agenta zabezpečení](concept-security-agent-authentication-methods.md)
- Výběr a nasazení [agenta zabezpečení](how-to-deploy-agent.md)
- Kontrola [požadavků služby](service-prerequisites.md) Azure Security Center for IoT
- Zjistěte, jak [povolit službu Azure Security Center for IoT ve vašem Centru IoT Hub](quickstart-onboard-iot-hub.md)
- Další informace o službě v [centru zabezpečení Azure pro ioT nejčastější dotazy](resources-frequently-asked-questions.md)
