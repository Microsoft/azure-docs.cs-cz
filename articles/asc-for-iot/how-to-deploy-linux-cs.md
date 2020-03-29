---
title: Průvodce instalací a nasazením agenta Linuxu C# centra zabezpečení Azure pro IoT| Dokumenty společnosti Microsoft
description: Zjistěte, jak nainstalovat Azure Security Center pro ioT agenta na 32bitový i 64bitový Linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: b675198756ff7bc0791d49fee3649717e3e4da7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75367412"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Nasazení agenta zabezpečení Azure Security Center pro IoT založeného na C# pro Linux


Tato příručka vysvětluje, jak nainstalovat a nasadit Azure Security Center pro IoT C#agent zabezpečení na Linuxu.

V této příručce se naučíte: 
> [!div class="checklist"]
> * Instalace
> * Ověření nasazení
> * Odinstalace agenta
> * Řešení potíží 

## <a name="prerequisites"></a>Požadavky

Další platformy a varianty agentů najdete v [tématu Výběr správného agenta zabezpečení](how-to-deploy-agent.md).

1. K nasazení agenta zabezpečení jsou vyžadována práva místních správců v počítači, do kterýchcete nainstalovat. 

1. [Vytvořte bezpečnostní modul](quickstart-create-security-twin.md) pro zařízení.

## <a name="installation"></a>Instalace 

Chcete-li nasadit agenta zabezpečení, postupujte takto:

1. Stáhněte si nejnovější verzi do počítače z [GitHubu](https://aka.ms/iot-security-github-cs).

1. Extrahujte obsah balíčku a přejděte do složky _/Install._

1. Přidání spuštěná oprávnění do **skriptu InstallSecurityAgent** spuštěním`chmod +x InstallSecurityAgent.sh` 

1. Dále spusťte následující příkaz s **oprávněními root**: 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   Další informace o parametrech ověřování naleznete v tématu [Konfigurace ověřování](concept-security-agent-authentication-methods.md).

Tento skript provádí následující akce:

- Nainstaluje požadavky.

- Přidá uživatele služby (s interaktivním přihlášením zakázáno).

- Nainstaluje agenta jako **daemon** - předpokládá, že zařízení používá **systemd** pro klasický model nasazení.

- **Konfiguruje sudoers** umožnit agentovi provést určité úkoly jako root.

- Konfiguruje agenta s zadanými parametry ověřování.


Další nápovědu naleznete ve spuštění skriptu s parametrem –help:`./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Odinstalace agenta

Chcete-li odinstalovat agenta, spusťte `./InstallSecurityAgent.sh -u`skript s parametrem -u: . 

> [!NOTE]
> Odinstalace neodebere žádné chybějící požadavky, které byly nainstalovány během instalace.

## <a name="troubleshooting"></a>Řešení potíží  

1. Zkontrolujte stav nasazení spuštěním:

    `systemctl status ASCIoTAgent.service`

2. Povolte protokolování.  
   Pokud se agentnepodaří spustit, zapněte protokolování získat další informace.

   Zapněte protokolování:

   1. Otevřete konfigurační soubor pro úpravy v libovolném linuxovém editoru:

        `vi /var/ASCIoTAgent/General.config`

   1. Upravte následující hodnoty: 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       Hodnotu **logFilePath** lze konfigurovat. 

       > [!NOTE]
       > Po dokončení řešení potíží doporučujeme **vypnout** protokolování. Ponecháte-li **přihlašování,** zvětší se velikost souboru protokolu a využití dat.

   1. Restartujte agenta spuštěním:

       `systemctl restart ASCIoTAgent.service`

   1. Další informace o chybě naleznete v souboru protokolu.  

       Umístění souboru protokolu je:`/var/ASCIoTAgent/IotAgentLog.log`

       Změňte cestu k umístění souboru podle názvu, který jste zvolili pro **logFilePath** v kroku 2. 

## <a name="next-steps"></a>Další kroky

- Přečtěte si [přehled](overview.md) služby Azure Security Center for IoT
- Další informace o Azure Security Center pro [architekturu](architecture.md) IoT
- Povolení [služby](quickstart-onboard-iot-hub.md)
- Přečtěte si [nejčastější dotazy](resources-frequently-asked-questions.md)
- Principy [výstrah](concept-security-alerts.md)
