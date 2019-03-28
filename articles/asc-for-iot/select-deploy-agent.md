---
title: Vybrat a nasadit ASC pro IoT agenta ve verzi Preview | Dokumentace Microsoftu
description: Další informace o tom, jak vybrat a nasadit ASC pro agenty zabezpečení IoT na zařízeních IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 5ce583fe98acb7e0a4aaeb720cb2d5ed5eebb9e3
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541959"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Vyberte a nasaďte agenta zabezpečení do zařízení IoT

> [!IMPORTANT]
> ASC pro IoT je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


ASC pro IoT poskytuje referenční architektury pro agenty zabezpečení, které monitorování a shromažďování dat ze zařízení IoT.

Zabezpečení agentů jsou vyvíjeny jako open source projekty a jsou k dispozici mají dvě varianty: <br> [C](https://aka.ms/iot-security-github-c), a [ C# ](https://aka.ms/iot-security-github-cs).

## <a name="supported-platforms-for-asc-for-iot-agents"></a>Podporované platformy pro ASC pro agenty IoT

Následující seznam obsahuje všechny aktuálně podporovaných platformách.  

|ASC pro agenta IoT |Operační systém |Architektura |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core build 17763 |x64|


## <a name="which-flavor-should-i-use"></a>Které flavor mám použít?

Vezměte v úvahu následující dotazy s ohledem na zařízení IoT:

- Používáte _systému Windows Server_ nebo _Windows IoT Core_? 

    Použití [ASC pro IoT instalaci pro Windows s C#-agent zabezpečení založený na](tutorial-deploy-windows-cs.md).

- Používáte Linuxová distribuce s x86 architektura? 

    Použití [ASC pro IoT instalace pro Linux s agentem zabezpečení na základě C](tutorial-deploy-linux-c.md).
- Používáte Linuxová distribuce s x64 architektura?

    Můžete použít oba typy. <br>
    [ASC pro IoT instalace pro Linux s agentem zabezpečení na základě C](tutorial-deploy-linux-c.md) a/nebo [ASC pro IoT instalace pro Linux s C#-agent zabezpečení založený na](tutorial-deploy-linux-cs.md).

Mají obě varianty, které mají stejnou sadu funkcí a podporují podobné možnosti konfigurace. Zabezpečení na základě C agent má nižší paměťové nároky.


## <a name="next-steps"></a>Další postup
- [Přehled](overview.md)
- [Zabezpečení agentů](security-agent-architecture.md)
- [Metody ověřování zabezpečení agenta](concept-security-agent-authentication-methods.md)
- [ASC pro IoT – nejčastější dotazy](resources-frequently-asked-questions.md)