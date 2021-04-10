---
title: Instalace agenta C# na zařízení s Windows
description: Přečtěte si, jak nainstalovat Defender pro agenta IoT na 32 nebo 64 zařízeních s Windows.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: b5efc9ce675fa068f378cdc2bdd5a077d3437a93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778759"
---
# <a name="deploy-a-defender-for-iot-c-based-security-agent-for-windows"></a>Nasazení programu Defender pro agenta zabezpečení na bázi IoT C# pro Windows

Tato příručka vysvětluje, jak nainstalovat Defender pro agenta zabezpečení založeného na službě IoT C# do systému Windows.

V této příručce se naučíte:

- Instalace
- Ověření nasazení
- Odinstalace agenta
- Řešení potíží

## <a name="prerequisites"></a>Požadavky

Další typy platforem a agentů najdete v tématu [Volba správného agenta zabezpečení](how-to-deploy-agent.md).

1. Oprávnění místního správce k počítači, na kterém chcete nainstalovat.

1. [Vytvořte pro zařízení Defender-IoT-Micro-agent](quickstart-create-security-twin.md) .

## <a name="installation"></a>Instalace

Chcete-li nainstalovat agenta zabezpečení, použijte následující pracovní postup:

1. Na zařízení nainstalujte agenta Defender pro IoT Windows C#. Stáhněte si nejnovější verzi počítače z programu Defender pro [úložiště GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS)IoT.

1. Rozbalte obsah balíčku a přejděte do složky/Install.

1. Otevřete Windows PowerShell jako správce.
1. Do skriptu InstallSecurityAgent přidejte spuštěná oprávnění spuštěním:

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    pak spusťte:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    Například:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    Další informace o parametrech ověřování najdete v tématu [Postup konfigurace ověřování](concept-security-agent-authentication-methods.md).

Tento skript provede následující akce:

* Nainstaluje požadavky.
* Přidá uživatele služby (s zakázáním interaktivního přihlášení).
* Nainstaluje agenta jako **systémovou službu**.
* Nakonfiguruje agenta pomocí zadaných parametrů ověřování.

Další nápovědu získáte pomocí příkazu Get-Help v prostředí PowerShell.

Get-Help příklad:    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Ověřit stav nasazení

Ověřte stav nasazení agenta spuštěním:

```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Odinstalace agenta

Postup při odinstalaci agenta:

1. Spusťte následující skript PowerShellu s parametrem **-Mode** nastaveným na **odinstalovat**.

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ```

## <a name="troubleshooting"></a>Řešení potíží

Pokud se agent nespustí, zapněte protokolování (protokolování je ve výchozím nastavení *vypnuté* ) a získejte další informace.

Zapnutí protokolování:

1. Otevřete konfigurační soubor (General.config) pro úpravy pomocí standardního editoru souborů.

1. Upravte následující hodnoty:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/>
   <add key="diagnosticVerbosityLevel" value="Some" />
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Doporučujeme, abyste po dokončení řešení potíží **vypnuli** protokolování. Zanechávání **protokolování zvyšuje velikost** souboru protokolu a využití dat.

1. Restartujte agenta spuštěním následujícího PowerShellu nebo příkazového řádku:

    **PowerShell**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   nebo

    **PŘEPSAT**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. Další informace o selhání najdete v souboru protokolu. Soubor protokolu by byl přítomen v pracovním adresáři, ve kterém se skript spouští. 

   Umístění souboru protokolu: `.\IoTAgentLog.log`

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [Přehled](overview.md) služby Defender for IoT.
* Další informace o programu Defender pro [architekturu](architecture.md) IoT
* Povolení [služby](quickstart-onboard-iot-hub.md)
* Přečtěte si [Nejčastější dotazy](resources-frequently-asked-questions.md) .
* Vysvětlení [výstrah](concept-security-alerts.md)
