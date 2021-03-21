---
title: Nainstalovat & nasazení agenta pro Linux C
description: Přečtěte si, jak nainstalovat a nasadit agenta zabezpečení založeného na službě IoT C v systému Linux
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
ms.openlocfilehash: f7f6b28070600ae53242181934ea903a486c25b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103494559"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Nasazení programu Defender pro agenta zabezpečení založeného na IoT C pro Linux

V této příručce se dozvíte, jak nainstalovat a nasadit agenta zabezpečení založeného na službě IoT C v systému Linux.

- Instalace
- Ověření nasazení
- Odinstalace agenta
- Řešení potíží

## <a name="prerequisites"></a>Předpoklady

Další typy platforem a agentů najdete v tématu [Volba správného agenta zabezpečení](how-to-deploy-agent.md).

1. Chcete-li nasadit agenta zabezpečení, jsou na počítači, na kterém chcete nainstalovat nástroj, požadovány oprávnění místního správce (sudo).

1. [Vytvořte pro zařízení Defender-IoT-Micro-agent](quickstart-create-security-twin.md) .

## <a name="installation"></a>Instalace

Chcete-li nainstalovat a nasadit agenta zabezpečení, použijte následující pracovní postup:

1. Stáhněte si nejnovější verzi do počítače z [GitHubu](https://aka.ms/iot-security-github-c).

1. Extrahujte obsah balíčku a přejděte do složky _/Src/Installation_ .

1. Spuštěním následujícího příkazu přidejte do **skriptu InstallSecurityAgent** oprávnění ke spuštění:

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Dále spusťte:

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   Další informace o parametrech ověřování najdete v tématu [Postup konfigurace ověřování](concept-security-agent-authentication-methods.md) .

Tento skript provede následující funkci:

1. Nainstaluje požadavky.

1. Přidá uživatele služby (s vypnutým interaktivním přihlášením).

1. Nainstaluje agenta jako **démona** – předpokládá, že zařízení používá **systém** pro správu služeb.

1. Nakonfiguruje agenta pomocí zadaných parametrů ověřování.

Další nápovědu získáte spuštěním skriptu s parametrem – Help:

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>Odinstalace agenta

Chcete-li odinstalovat agenta, spusťte skript s parametrem –-Uninstall:

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>Poradce při potížích

Stav nasazení ověřte spuštěním:

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>Další kroky

- Přečtěte si téma [Přehled](overview.md) služby Defender for IoT.
- Další informace o programu Defender pro [architekturu](architecture.md) IoT
- Povolení [služby](quickstart-onboard-iot-hub.md)
- Přečtěte si [Nejčastější dotazy](resources-frequently-asked-questions.md) .
- Vysvětlení [výstrah zabezpečení](concept-security-alerts.md)
