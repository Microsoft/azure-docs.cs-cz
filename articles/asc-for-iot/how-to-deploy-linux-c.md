---
title: Instalace & nasazení agenta Linuxu C
description: Zjistěte, jak nainstalovat Azure Security Center pro ioT agenta na 32bitový i 64bitový Linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: d9f9602a19a266c70b17422e90566f72de2978f6
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311191"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Nasazení agenta zabezpečení Azure Security Center pro IoT založeného na C pro Linux

Tato příručka vysvětluje, jak nainstalovat a nasadit Azure Security Center pro agenta zabezpečení založeného na IoT C na Linuxu.

V této příručce se naučíte:

> [!div class="checklist"]
> * Instalace
> * Ověření nasazení
> * Odinstalace agenta
> * Řešení potíží

## <a name="prerequisites"></a>Požadavky

Další platformy a varianty agentů najdete v [tématu Výběr správného agenta zabezpečení](how-to-deploy-agent.md).

1. K nasazení agenta zabezpečení jsou vyžadována práva místních správců v počítači, do který chcete nainstalovat (sudo).

1. [Vytvořte bezpečnostní modul](quickstart-create-security-twin.md) pro zařízení.

## <a name="installation"></a>Instalace

Chcete-li nainstalovat a nasadit agenta zabezpečení, použijte následující pracovní postup:

1. Stáhněte si nejnovější verzi do počítače z [GitHubu](https://aka.ms/iot-security-github-c).

1. Extrahujte obsah balíčku a přejděte do složky _/src/installation._

1. Spuštění oprávnění do **skriptu InstallSecurityAgent** spuštěním následujícího příkazu:

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Dále spusťte:

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   Další informace o parametrech ověřování naleznete v [tématu Konfigurace ověřování.](concept-security-agent-authentication-methods.md)

Tento skript provádí následující funkci:

1. Nainstaluje požadavky.

1. Přidá uživatele služby (s interaktivním přihlášením zakázáno).

1. Nainstaluje agenta jako **daemon** - předpokládá, že zařízení používá **systemd** pro správu služeb.

1. Konfiguruje agenta s poskytnutými parametry ověřování.

Další nápovědu naleznete ve spuštění skriptu s parametrem –help:

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>Odinstalace agenta

Chcete-li odinstalovat agenta, spusťte skript s parametrem –-uninstall:

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>Řešení potíží

Zkontrolujte stav nasazení spuštěním:

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>Další kroky

- Přečtěte si [přehled](overview.md) služby Azure Security Center for IoT
- Další informace o Azure Security Center pro [architekturu](architecture.md) IoT
- Povolení [služby](quickstart-onboard-iot-hub.md)
- Přečtěte si [nejčastější dotazy](resources-frequently-asked-questions.md)
- Principy [výstrah zabezpečení](concept-security-alerts.md)
