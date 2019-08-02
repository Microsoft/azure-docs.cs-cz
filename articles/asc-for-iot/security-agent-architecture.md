---
title: Princip Azure Security Center architektury zabezpečení služby IoT | Microsoft Docs
description: Pochopení architektury agenta zabezpečení pro agenty používané ve Azure Security Center pro službu IoT
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
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596910"
---
# <a name="security-agent-reference-architecture"></a>Referenční architektura agenta zabezpečení

Azure Security Center pro IoT poskytuje referenční architekturu pro agenty zabezpečení, které protokolují, zpracovávají, agreguje a odesílají data zabezpečení prostřednictvím IoT Hub.

Agenti zabezpečení jsou navrženi pro práci v prostředí s omezeným IoT a jsou vysoce přizpůsobitelné z hlediska hodnot, které poskytují ve srovnání s prostředky, které spotřebovávají.

Agenti zabezpečení podporují tyto funkce:

- Shromážděte nezpracované události zabezpečení ze základního operačního systému (Linux, Windows). Další informace o dostupných kolekcích dat zabezpečení najdete v tématu [Azure Security Center pro konfiguraci agenta IoT](how-to-agent-configuration.md).

- Agregovat nezpracované události zabezpečení do zpráv odesílaných pomocí IoT Hub.

- Proveďte ověření pomocí existující identity zařízení nebo identity vyhrazené modulu. Další informace najdete v tématu [metody ověřování agenta zabezpečení](concept-security-agent-authentication-methods.md) .

- Vzdálená konfigurace prostřednictvím použití modulu **azureiotsecurity** s dvojitou platností. Další informace najdete v tématu [konfigurace Azure Security Center pro agenta IoT](how-to-agent-configuration.md).

Azure Security Center pro agenty zabezpečení IoT se vypracovávají jako open source projekty a jsou dostupné z GitHubu: 

- [Azure Security Center pro agenta založeného na IoT C](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [Azure Security Center pro agenta C#založeného na IoT](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Podporované platformy agenta

Azure Security Center pro IoT nabízí různé instalační agenty pro 32bitová a 64bitová okna a jsou stejné pro 32bitové a 64bitové systémy Linux. Ujistěte se, že máte pro každé zařízení správný instalační program agentů podle následující tabulky:

| Architektura | Linux | Windows |    Podrobnosti|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32  | C  | C#  ||
| 64bitový  | C#nebo C           | C#      | Pro zařízení s více omezenými nebo minimálními prostředky zařízení doporučujeme použít agenta jazyka C.|
|

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o Azure Security Center architektuře agenta zabezpečení IoT a dostupných instalačních programů.

Pokud chcete pokračovat v seznámení s Azure Security Center pro nasazení IoT, postupujte podle následujících článků:

- Porozumění [metodám ověřování agenta zabezpečení](concept-security-agent-authentication-methods.md)
- Výběr a nasazení [agenta zabezpečení](how-to-deploy-agent.md)
- Přečtěte si téma Azure Security Center pro [požadavky služby](service-prerequisites.md) IoT
- Naučte se, jak [povolit Azure Security Center pro službu IoT v IoT Hub](quickstart-onboard-iot-hub.md)
- Další informace o službě najdete v tématu [Nejčastější dotazy k Azure Security Center pro IoT](resources-frequently-asked-questions.md)
