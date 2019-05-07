---
title: Principy Azure Security Center pro architektury IoT zabezpečení agenta ve verzi Preview | Dokumentace Microsoftu
description: Vysvětlení architektury zabezpečení agenta pro agenty použité pro služby Azure IoT v Azure Security Center.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 3c05b7e9b1c6d1b9214da168f7abfcbb322f8f6d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192524"
---
# <a name="security-agent-reference-architecture"></a>Referenční Architektura zabezpečení agenta

> [!IMPORTANT]
> Azure Security Center pro IoT je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Azure Security Center (ASC) pro IoT poskytuje referenční architektury pro agenty zabezpečení, které protokolování, zpracování, agregovat a odesílat data zabezpečení prostřednictvím služby IoT hub.

Agenti zabezpečení jsou navrženy pro práci v prostředí omezené IoT a jsou vysoce přizpůsobitelné z hlediska hodnoty, které poskytují ve srovnání s prostředky, které se spotřebují.

Agenti zabezpečení podporují tyto funkce:

- Shromažďovat události zabezpečení nezpracovaná od základního operačního systému (Linux, Windows). Další informace o kolekcemi dostupný zabezpečení dat najdete v tématu [ASC pro konfiguraci agenta IoT](how-to-agent-configuration.md).

- Agregovat nezpracovaný bezpečnostních událostí do zprávy odeslané do služby IoT hub.

- Ověřování pomocí existující identitu zařízení nebo identitu vyhrazené modulu. Zobrazit [metody ověřování agenta zabezpečení](concept-security-agent-authentication-methods.md) Další informace.

- Konfigurace vzdálené používání **azureiotsecurity** dvojčete modulu. Další informace najdete v tématu [konfigurace ASC pro agenta IoT](how-to-agent-configuration.md).

ASC pro agenty zabezpečení IoT jsou vyvíjeny jako open source projekty a jsou k dispozici na Githubu: 

- [ASC pro agenta IoT C](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [ASC pro IoT C#– na základě agenta](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Agent podporovaný platformy

ASC pro IoT nabízí různé instalační program agenty pro 32bitovou a 64bitovou verzi Windows a pro 32bitové a 64bitové Linux stejný. Ujistěte se, že máte správný agenta instalačního programu pro každou z vašich zařízení podle následující tabulky:

| 32 nebo 64 bitů | Linux | Windows |    Podrobnosti|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32bit  | C  | C#  ||
| 64bit  | C#nebo C           | C#      | Agent použití jazyka C pro zařízení s minimálními zdroji|

## <a name="next-steps"></a>Další postup

V tomto článku jste se seznámili ASC Architektura agenta zabezpečení IoT a k dispozici instalační programy.

Pokud chcete pokračovat v seznamování s ASC pro nasazení IoT, použijte v následujících článcích:

- Vysvětlení [metody ověřování zabezpečení agenta](concept-security-agent-authentication-methods.md)
- Vybrat a nasadit [zabezpečení agenta](how-to-deploy-agent.md)
- Přečtěte si ASC pro IoT [požadované součásti služby](service-prerequisites.md)
- Zjistěte, jak [povolit ASC pro služby Azure IoT ve službě IoT Hub](quickstart-onboard-iot-hub.md)
- Další informace o službě z [ASC pro IoT – nejčastější dotazy](resources-frequently-asked-questions.md)
