---
title: Instalace Azure Security Center Windows pro agenta IoT | Microsoft Docs
description: Přečtěte si, jak nainstalovat Azure Security Center pro agenta IoT na 32 nebo 64 zařízeních s Windows.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: acc99f260931de7fd8c7566a3ff6daf43f34c5ef
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597208"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Nasazení Azure Security Center pro agenta zabezpečení C#na bázi IoT pro Windows

Tato příručka vysvětluje, jak nainstalovat Azure Security Center pro agenta zabezpečení C#na bázi IoT ve Windows.

V této příručce se naučíte: 
> [!div class="checklist"]
> * Instalace
> * Ověření nasazení
> * Odinstalace agenta
> * Řešení potíží 

## <a name="prerequisites"></a>Požadavky

Další typy platforem a agentů najdete v tématu [Volba správného agenta zabezpečení](how-to-deploy-agent.md).

1. Oprávnění místního správce k počítači, na kterém chcete nainstalovat. 

1. [Vytvořte modul zabezpečení](quickstart-create-security-twin.md) pro zařízení.

## <a name="installation"></a>Instalace 

Chcete-li nainstalovat agenta zabezpečení, použijte následující pracovní postup:

1. Na zařízení nainstalujte Azure Security Center pro IoT C# Windows Agent. Stáhněte si nejnovější verzi do počítače z Azure Security Center pro [úložiště GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS)IoT.

1. Rozbalte obsah balíčku a přejděte do složky/Install.

1. Otevřete Windows PowerShell jako správce. 
1. Do skriptu InstallSecurityAgent přidejte spuštěná oprávnění spuštěním:<br>
    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```
    
    pak spusťte:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Příklad:
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Další informace o parametrech ověřování najdete v tématu [Postup konfigurace ověřování](concept-security-agent-authentication-methods.md).

Tento skript provede následující akce:

- Nainstaluje požadavky.

- Přidá uživatele služby (s vypnutým interaktivním přihlášením).

- Nainstaluje agenta jako systémovou **službu**.

- Nakonfiguruje agenta pomocí zadaných parametrů ověřování.


Další nápovědu získáte pomocí příkazu Get-Help v prostředí PowerShell. <br>Příklad Get-Help:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Ověřit stav nasazení

- Ověřte stav nasazení agenta spuštěním:<br>
    ```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Odinstalace agenta

Postup při odinstalaci agenta:

1. Spusťte následující skript PowerShellu s parametrem **-Mode** nastaveným na **odinstalovat**.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Řešení potíží

Pokud se agent nespustí, zapněte protokolování (protokolování je ve výchozím nastavení vypnuté) a získejte další informace.

Zapnutí protokolování:

1. Otevřete konfigurační soubor (General. config) pro úpravy pomocí standardního editoru souborů.

1. Upravte následující hodnoty:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Doporučujeme, abyste po dokončení řešení potíží vypnuli protokolování. Zanechávání **protokolování zvyšuje velikost** souboru protokolu a využití dat. 

1. Restartujte agenta spuštěním následujícího PowerShellu nebo příkazového řádku:

    **Powershell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   or

    **CMD**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Další informace o selhání najdete v souboru protokolu.

   Umístění souboru protokolu:`%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>Další postup
- Přečtěte si [Přehled](overview.md) služby Azure Security Center for IoT.
- Další informace o [architektuře](architecture.md) Azure Security Center pro IoT
- Povolení [služby](quickstart-onboard-iot-hub.md)
- Přečtěte si [Nejčastější dotazy](resources-frequently-asked-questions.md) .
- Vysvětlení [výstrah](concept-security-alerts.md)