---
title: Instalace Windows ASC pro IoT agenta ve verzi Preview | Dokumentace Microsoftu
description: Další informace o tom, jak nainstalovat agenta IoT na zařízeních s Windows 32bitové nebo 64bitové ASC.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 5c3293746fcc52570e708fd4bfab446981d49c24
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621826"
---
# <a name="deploy-an-asc-for-iot-c-based-security-agent-for-windows"></a>Nasazení ASC pro IoT C#– na základě zabezpečení agenta pro Windows

> [!IMPORTANT]
> ASC pro IoT je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tato příručka vysvětluje, jak nainstalovat ASC pro IoT C#– na základě zabezpečení agenta na Windows.

V této příručce se naučíte: 
> [!div class="checklist"]
> * Instalace
> * Ověření nasazení
> * Odinstalace agenta
> * Řešení potíží 

## <a name="prerequisites"></a>Požadavky

Další platformy a chuti agenta, najdete v části [vyberte správné zabezpečení agenta](how-to-deploy-agent.md).

1. Práva místního správce na počítači, který chcete nainstalovat. 

1. [Vytvoření modulu zabezpečení](quickstart-create-security-twin.md) zařízení.

## <a name="installation"></a>Instalace 

Pokud chcete nainstalovat agenta zabezpečení, postupujte takto:

1. Instalace pro Windows IoT ASC C# agenta na zařízení, stáhnout nejnovější verzi na svůj počítač z ASC pro IoT [úložiště GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

2. Extrahujte obsah balíčku a přejděte do složky/Install.

3. Otevřete Windows PowerShell jako správce. 
    1. Přidat spuštěné oprávnění do skriptu InstallSecurityAgent spuštěním ```Unblock-File .\InstallSecurityAgent.ps1```
    
        a spusťte:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Příklad:
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Zobrazit [ke konfiguraci ověřování](concept-security-agent-authentication-methods.md) Další informace o ověření parametrů.

Skript provede následující akce:

- Nainstaluje požadavky.

- Přidá uživatele služby (pomocí interaktivního přihlášení, zakázáno).

- Nainstaluje agenta jako **systémová služba**.

- Nakonfiguruje agenta pomocí zadaného ověřovacího parametry.


Potřebujete další pomoc použijte příkaz Get-Help v prostředí PowerShell <br>Get-Help příklad:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Zkontrolujte stav nasazení

- Zkontrolujte stav nasazení agenta spuštěním:<br>
    ```sc.exe query "ASC IoT Agent" ```

### <a name="uninstall-the-agent"></a>Odinstalace agenta

Odinstalace agenta:

1. Spusťte následující skript prostředí PowerShell s **-režim** parametr nastaven na **odinstalovat**.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Řešení potíží

Pokud agenta nepodaří spustit, zapněte protokolování (protokolování je *vypnout* ve výchozím nastavení) Chcete-li získat další informace.

Zapnutí protokolování:

1. Otevřete konfigurační soubor (General.config) s možností úprav pomocí standardních souborových editoru.

1. Upravte následující hodnoty:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Doporučujeme vám zapnout protokolování **vypnout** po dokončení odstraňování potíží. Opuštění protokolování **na** zvýšení protokolování využití velikost a data souborů. 

1. Restartujte agenta spuštěním následujícího prostředí PowerShell nebo příkazového řádku:

    **Powershell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   nebo

    **CMD**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Projděte si soubor protokolu pro další informace o selhání.

   Umístění souboru protokolu: `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>Další postup
- Přečtěte si ASC pro služby Azure IoT [– přehled](overview.md)
- Další informace o ASC pro IoT [architektury](architecture.md)
- Povolit [služby](quickstart-onboard-iot-hub.md)
- Přečtěte si [– nejčastější dotazy](resources-frequently-asked-questions.md)
- Vysvětlení [výstrahy](concept-security-alerts.md)