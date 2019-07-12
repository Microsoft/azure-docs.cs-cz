---
title: Vyberte a nasazení Azure Security Center pro IoT agenta ve verzi Preview | Dokumentace Microsoftu
description: Další informace o tom, jak vybrat a nasazení Azure Security Center pro agenty zabezpečení IoT na zařízeních IoT.
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
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: c549e5ccbda9b364b3e7d20c9572eb777c32299e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616832"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Vyberte a nasaďte agenta zabezpečení do zařízení IoT

> [!IMPORTANT]
> Azure Security Center pro IoT je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Security Center (ASC) pro IoT poskytuje referenční architektury pro agenty zabezpečení, které monitorování a shromažďování dat ze zařízení IoT.
Zobrazit [zabezpečení agenta referenční architektura](security-agent-architecture.md) Další informace.

Agenti jsou vyvíjeny jako open source projekty a jsou k dispozici mají dvě varianty: <br> [C](https://aka.ms/iot-security-github-c), a [ C# ](https://aka.ms/iot-security-github-cs).

V tomto článku získáte informace o těchto tématech: 
> [!div class="checklist"]
> * Porovnání typů zabezpečení agenta
> * Zjistit podporovaném agentu platformy
> * Zvolte správný agenta charakter pro vaše řešení

## <a name="understand-security-agent-options"></a>Vysvětlení možností zabezpečení agenta

Každý ASC pro IoT zabezpečení agenta flavor nabízí stejnou sadu funkcí a podporuje podobné možnosti konfigurace. 

Zabezpečení na základě C agenta má nižší paměťové nároky a je ideální volbou pro zařízení s menším počtem dostupných prostředků. 

|     | Zabezpečení na základě C agenta | C#– na základě zabezpečení agenta |
| --- | ----------- | --------- |
| Open source | K dispozici v rámci [licencí MIT](https://en.wikipedia.org/wiki/MIT_License) v [Githubu](https://aka.ms/iot-security-github-cs) | K dispozici v rámci [licencí MIT](https://en.wikipedia.org/wiki/MIT_License) v [Githubu](https://aka.ms/iot-security-github-c) |
| Vývojový jazyk    | C | C# |
| Podporované platformy Windows? | Ne | Ano |
| Požadavky na Windows | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| Podporované platformy Linux? | Ano, x64 a x86 | Ano, x64 pouze |
| Požadavky pro Linux | libunwind8 libcurl3, uuid modulu runtime, démona auditd, audispd modulů plug-in | libunwind8 libcurl3, uuid modulu runtime, démona auditd, audispd moduly plug-in, sudo, netstat, iptables |
| Nároky na disku | 10.5 MB | 90MB |
| Nároky na paměť (v průměru) | 5.5 MB | 33MB |
| [Ověřování](concept-security-agent-authentication-methods.md) do služby IoT Hub | Ano | Ano |
| Zabezpečení dat [kolekce](how-to-agent-configuration.md#supported-security-events) | Ano | Ano |
| Agregace událostí | Ano | Ano |
| Konfigurace vzdáleného prostřednictvím [dvojče zařízení zabezpečení](concept-security-module.md) | Ano | Ano |


## <a name="choose-an-agent-flavor"></a>Zvolte flavor agenta 

Odpovězte na následující otázky o zařízení IoT k výběru správné agenta:

- Používáte _systému Windows Server_ nebo _Windows IoT Core_? 

    [Nasazení C#– na základě zabezpečení agenta pro Windows](how-to-deploy-windows-cs.md).

- Používáte Linuxová distribuce s x86 architektura? 

    [Nasazení agenta pro Linux zabezpečení na základě C](how-to-deploy-linux-c.md).

- Používáte Linuxová distribuce s x64 architektura?

    Můžete použít buď flavor agenta. <br>
    [Nasazení agenta pro Linux zabezpečení na základě C](how-to-deploy-linux-c.md) a/nebo [nasadit C#– na základě zabezpečení agenta pro Linux](how-to-deploy-linux-cs.md).

Agent mají obě varianty, které představují stejnou sadu funkcí a podporují podobné možnosti konfigurace.
Zobrazit [zabezpečení agenta porovnání](how-to-deploy-agent.md#understand-security-agent-options) Další informace.

## <a name="supported-platforms"></a>Podporované platformy

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

## <a name="next-steps"></a>Další kroky

Další informace o možnostech konfigurace, pokračujte v Průvodci s postupy pro konfiguraci agenta. 
> [!div class="nextstepaction"]
> [Konfigurace agenta postup Průvodce](./how-to-agent-configuration.md)
