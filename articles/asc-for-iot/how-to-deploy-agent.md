---
title: Výběr a nasazení Azure Security Center pro agenta IoT| Dokumenty společnosti Microsoft
description: Přečtěte si, jak výběr a nasazení Azure Security Center pro agenty zabezpečení IoT na zařízeních IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: d70f2f3ec87c8673013bcf7b6f70ebcbb8d06f08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75770012"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Výběr a nasazení agenta zabezpečení na zařízení IoT

Azure Security Center pro IoT poskytuje referenční architektury pro agenty zabezpečení, kteří monitorují a shromažďují data ze zařízení IoT.
Další informace naleznete v [tématu Referenční architektura agenta zabezpečení](security-agent-architecture.md).

Agenti jsou vyvíjeny jako open-source projekty a jsou k dispozici ve dvou variantách: <br> [C](https://aka.ms/iot-security-github-c)a [C#](https://aka.ms/iot-security-github-cs).

V tomto článku získáte informace o těchto tématech: 
> [!div class="checklist"]
> * Porovnat příchutě agenta zabezpečení
> * Objevte podporované platformy agentů
> * Vyberte si správnou variantu agenta pro vaše řešení

## <a name="understand-security-agent-options"></a>Principy možností agenta zabezpečení

Každý Azure Security Center pro zabezpečení IoT agent chuť nabízí stejnou sadu funkcí a podporuje podobné možnosti konfigurace. 

Agent zabezpečení založený na C má nižší nároky na paměť a je ideální volbou pro zařízení s menším počtem dostupných prostředků. 

|     | Agent zabezpečení na bázi C | Agent zabezpečení založený na c# |
| --- | ----------- | --------- |
| Open-source | K dispozici pod [licencí MIT](https://en.wikipedia.org/wiki/MIT_License) v [GitHubu](https://aka.ms/iot-security-github-cs) | K dispozici pod [licencí MIT](https://en.wikipedia.org/wiki/MIT_License) v [GitHubu](https://aka.ms/iot-security-github-c) |
| Vývojový jazyk    | C | C# |
| Podporované platformy Windows? | Ne | Ano |
| Požadavky systému Windows | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| Podporované platformy Linuxu? | Ano, x64 a x86 | Ano, pouze x64 |
| Požadované součásti systému Linux | libunwind8, libcurl3, uuid-runtime, auditováno, audispd-pluginy | libunwind8, libcurl3, uuid-runtime, auditováno, audispd-pluginy, sudo, netstat, iptables |
| Stopa disku | 10,5 MB | 90 MB |
| Paměťová stopa (v průměru) | 5,5 MB | 33 MB |
| [Ověřování](concept-security-agent-authentication-methods.md) pro službu IoT Hub | Ano | Ano |
| [Shromažďování](how-to-agent-configuration.md#supported-security-events) bezpečnostních dat | Ano | Ano |
| Agregace událostí | Ano | Ano |
| Vzdálená konfigurace prostřednictvím [dvojčete modulu zabezpečení](concept-security-module.md) | Ano | Ano |
|

## <a name="security-agent-installation-guidelines"></a>Pokyny pro instalaci agenta zabezpečení

Pro **systém Windows**: Skript Install SecurityAgent.ps1 musí být spuštěn z okna prostředí PowerShell správce. 

Pro **Linux**: InstallSecurityAgent.sh musí být spuštěn jako superuser. Doporučujeme předpojit instalační příkaz "sudo".


## <a name="choose-an-agent-flavor"></a>Vyberte si agent chuť 

Odpovězte na následující otázky týkající se zařízení IoT a vyberte správného agenta:

- Používáte _Windows Server_ nebo _Windows IoT Core_? 

    [Nasazení agenta zabezpečení založeného na c#pro systém Windows](how-to-deploy-windows-cs.md).

- Používáte linuxovou distribuci s architekturou x86? 

    [Nasazení agenta zabezpečení založeného na C pro Linux](how-to-deploy-linux-c.md).

- Používáte linuxovou distribuci s architekturou x64?

    Lze použít obě příchutě činidla. <br>
    [Nasazení agenta zabezpečení založeného na C pro Linux](how-to-deploy-linux-c.md) a/nebo nasazení [agenta zabezpečení založeného na C#pro Linux](how-to-deploy-linux-cs.md).

Obě varianty agenta nabízejí stejnou sadu funkcí a podporují podobné možnosti konfigurace.
Další informace najdete v [tématu Porovnání bezpečnostních agentů.](how-to-deploy-agent.md#understand-security-agent-options)

## <a name="supported-platforms"></a>Podporované platformy

Následující seznam obsahuje všechny aktuálně podporované platformy.

|Azure Security Center pro agenta IoT |Operační systém |Architektura |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64, ARMv7|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64, ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    x64|
|C#|Windows 10 IoT Core, sestavení 17763    |x64|
|

## <a name="next-steps"></a>Další kroky

Další informace o možnostech konfigurace najdete v návodu pro konfiguraci agenta. 
> [!div class="nextstepaction"]
> [Konfigurace agenta, jak vést](./how-to-agent-configuration.md)
