---
title: Nainstalovat & nasazení agenta pro Linux C#
description: Přečtěte si, jak nainstalovat a nasadit agenta zabezpečení založeného na službě IoT C# na platformě Linux
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 48737831440a1402b6974955b4da61a4216b011f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936264"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Nasazení programu Defender pro IoT C# Based Security Agent pro Linux

Tato příručka vysvětluje, jak nainstalovat a nasadit agenta zabezpečení založeného na službě IoT C# na platformě Linux.

V této příručce se naučíte:

> [!div class="checklist"]
> * Instalace
> * Ověření nasazení
> * Odinstalace agenta
> * Řešení potíží

## <a name="prerequisites"></a>Požadavky

Další typy platforem a agentů najdete v tématu [Volba správného agenta zabezpečení](how-to-deploy-agent.md).

1. Chcete-li nasadit agenta zabezpečení, jsou na počítači, na kterém chcete nainstalovat, požadovány oprávnění místního správce.

1. [Vytvořte modul zabezpečení](quickstart-create-security-twin.md) pro zařízení.

## <a name="installation"></a>Instalace

Chcete-li nasadit agenta zabezpečení, použijte následující postup:

1. Stáhněte si nejnovější verzi do počítače z [GitHubu](https://aka.ms/iot-security-github-cs).

1. Extrahujte obsah balíčku a přejděte do složky _/install_ .

1. Do **skriptu InstallSecurityAgent** přidejte spuštěná oprávnění spuštěním `chmod +x InstallSecurityAgent.sh`

1. Dále spusťte následující příkaz s **kořenovými oprávněními**:

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   Další informace o parametrech ověřování najdete v tématu [Postup konfigurace ověřování](concept-security-agent-authentication-methods.md).

Tento skript provede následující akce:

- Nainstaluje požadavky.

- Přidá uživatele služby (s vypnutým interaktivním přihlášením).

- Nainstaluje agenta jako **démona** – předpokládá, že zařízení používá **systém** pro klasický model nasazení.

- Konfiguruje **sudoers** , aby mohl agentovi provádět určité úlohy jako kořen.

- Nakonfiguruje agenta pomocí zadaných parametrů ověřování.

Další nápovědu získáte spuštěním skriptu s parametrem – Help: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Odinstalace agenta

Chcete-li odinstalovat agenta, spusťte skript s parametrem-u: `./InstallSecurityAgent.sh -u` .

> [!NOTE]
> Odinstalace neodebere žádné chybějící součásti, které byly nainstalovány během instalace.

## <a name="troubleshooting"></a>Řešení potíží

1. Stav nasazení ověřte spuštěním:

    `systemctl status ASCIoTAgent.service`

1. Povolit protokolování.
   Pokud se agent nespustí, zapněte protokolování a získejte další informace.

   Zapnout protokolování podle:

   1. Otevřete konfigurační soubor pro úpravy v libovolném editoru Linux:

        `vi /var/ASCIoTAgent/General.config`

   1. Upravte následující hodnoty:

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" />
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```

       Hodnota **LogFilePath** se dá nakonfigurovat.

       > [!NOTE]
       > Doporučujeme, abyste po dokončení řešení potíží **vypnuli** protokolování. Zanechávání **protokolování zvyšuje velikost** souboru protokolu a využití dat.

   1. Restartujte agenta spuštěním:

       `systemctl restart ASCIoTAgent.service`

   1. Další informace o selhání najdete v souboru protokolu.

       Umístění souboru protokolu: `/var/ASCIoTAgent/IotAgentLog.log`

       Změňte cestu umístění souboru podle názvu, který jste zvolili pro **LogFilePath** v kroku 2.

## <a name="next-steps"></a>Další kroky

- Přečtěte si téma [Přehled](overview.md) služby Defender for IoT.
- Další informace o programu Defender pro [architekturu](architecture.md) IoT
- Povolení [služby](quickstart-onboard-iot-hub.md)
- Přečtěte si [Nejčastější dotazy](resources-frequently-asked-questions.md) .
- Vysvětlení [výstrah](concept-security-alerts.md)
