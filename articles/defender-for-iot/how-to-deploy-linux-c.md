---
title: Nainstalovat & nasazení agenta pro Linux C
description: Přečtěte si, jak nainstalovat a nasadit agenta zabezpečení založeného na službě IoT C v systému Linux
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 6f59db7ff24412c66a6a4898b14272ea9540fdd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778810"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Nasazení programu Defender pro agenta zabezpečení založeného na IoT C pro Linux

V této příručce se dozvíte, jak nainstalovat a nasadit agenta zabezpečení založeného na službě IoT C v systému Linux.

- Instalace
- Ověření nasazení
- Odinstalace agenta
- Řešení potíží

## <a name="prerequisites"></a>Požadavky

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

## <a name="troubleshooting"></a>Řešení potíží

Stav nasazení ověřte spuštěním:

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>Další kroky

- Přečtěte si téma [Přehled](overview.md) služby Defender for IoT.
- Další informace o programu Defender pro [architekturu](architecture.md) IoT
- Povolení [služby](quickstart-onboard-iot-hub.md)
- Přečtěte si [Nejčastější dotazy](resources-frequently-asked-questions.md) .
- Vysvětlení [výstrah zabezpečení](concept-security-alerts.md)
