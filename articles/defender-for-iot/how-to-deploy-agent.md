---
title: Výběr a nasazení agentů zabezpečení
description: Přečtěte si, jak vybrat a nasadit Defender pro agenty zabezpečení IoT na zařízeních IoT.
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 8e18b79cc14fe98879ec97361f6e275d8fd918bb
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936870"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Výběr a nasazení agenta zabezpečení na zařízení IoT

Defender pro IoT poskytuje referenční architektury pro agenty zabezpečení, kteří monitorují a shromažďují data ze zařízení IoT.
Další informace najdete v tématu [Referenční architektura agenta zabezpečení](security-agent-architecture.md).

Agenti se vyvíjí jako open source projekty a jsou k dispozici ve dvou charakterech: <br> [C](https://aka.ms/iot-security-github-c)a [C#](https://aka.ms/iot-security-github-cs).

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Porovnání charakteru agenta zabezpečení
> * Zjistit podporované platformy agenta
> * Volba správného charakteru agenta pro vaše řešení

## <a name="understand-security-agent-options"></a>Pochopení možností agenta zabezpečení

Každý Defender pro charakter agenta zabezpečení IoT nabízí stejnou sadu funkcí a podporuje podobné možnosti konfigurace.

Agent zabezpečení založený na jazyce C má nižší nároky na paměť a je ideální volbou pro zařízení s menšími dostupnými prostředky.

|     | Agent zabezpečení založený na jazyce C | Agent zabezpečení založený na jazyce C# |
| --- | ----------- | --------- |
| **Open source** | K dispozici v rámci [licence MIT](https://en.wikipedia.org/wiki/MIT_License) na [GitHubu](https://aka.ms/iot-security-github-c) | K dispozici v rámci [licence MIT](https://en.wikipedia.org/wiki/MIT_License) na [GitHubu](https://aka.ms/iot-security-github-cs) |
| **Vývojový jazyk**    | C | C# |
| **Podporované platformy Windows?** | No | Yes |
| **Požadavky Windows** | --- | [Rozhraní WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| **Podporované platformy Linux?** | Ano, x64 a x86 | Ano, pouze x64 |
| **Předpoklady pro Linux** | libunwind8, libcurl3, UUID – modul runtime, audit, audispd – moduly plug-in | libunwind8, libcurl3, UUID-runtime, audited, audispd-plugins, sudo, netstat, softwaru iptables |
| **Nároky na disk** | 10,5 MB | 90 MB |
| **Nároky na paměť (v průměru)** | 5,5 MB | 33 MB |
| **[Ověřování](concept-security-agent-authentication-methods.md) pro IoT Hub** | Yes | Yes |
| **[Shromažďování](how-to-agent-configuration.md#supported-security-events) dat zabezpečení** | Yes | Yes |
| **Agregace událostí** | Yes | Yes |
| **Vzdálená konfigurace prostřednictvím [modulu zabezpečení je dvojitá](concept-security-module.md) .** | Yes | Yes |

## <a name="security-agent-installation-guidelines"></a>Pokyny k instalaci agenta zabezpečení

Pro **Windows**: skript Install SecurityAgent.ps1 se musí spustit z okna PowerShellu pro správu.

Pro **Linux**: InstallSecurityAgent.sh musí být spuštěný jako uživatel. Doporučujeme použít předponu instalačního příkazu s názvem "sudo".

## <a name="choose-an-agent-flavor"></a>Volba charakteru agenta

Odpovězte na následující otázky týkající se zařízení IoT a vyberte správného agenta:

- Používáte _Windows Server_ nebo _Windows IoT Core_?

    [Nasazení agenta zabezpečení založeného na jazyce C# pro systém Windows](how-to-deploy-windows-cs.md).

- Používáte distribuci Linux s architekturou x86?

    [Nasazení agenta zabezpečení založeného na jazyce C pro Linux](how-to-deploy-linux-c.md).

- Používáte distribuci systému Linux s architekturou x64?

    Lze použít jak charakter agenta. <br>
    [Nasazení agenta zabezpečení založeného na jazyce C pro Linux](how-to-deploy-linux-c.md) nebo [nasazení agenta zabezpečení založeného na jazyce C# pro Linux](how-to-deploy-linux-cs.md).

Charakter obou agentů nabízí stejnou sadu funkcí a podporují podobné možnosti konfigurace.
Další informace najdete v tématu [porovnání agenta zabezpečení](how-to-deploy-agent.md#understand-security-agent-options) .

## <a name="supported-platforms"></a>Podporované platformy

Následující seznam obsahuje všechny aktuálně podporované platformy.

|Defender pro agenta IoT |Operační systém |Architektura |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |    x64|
|C|Ubuntu 18.04 |    x64, ARMv7|
|C|Debian 9 |    x64, x86|
|C#|Ubuntu 16.04     |x64|
|C#|Ubuntu 18.04    |x64, ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    x64|
|C#|Windows 10 IoT Core, Build 17763    |x64|
|

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o možnostech konfigurace, přejděte k příručce Průvodce konfigurací agenta.
> [!div class="nextstepaction"]
> [Průvodce konfigurací agenta](./how-to-agent-configuration.md)
