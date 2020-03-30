---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175469"
---
V závislosti na operačním systému klienta se postupy vzdáleného připojení k zařízení liší.

### <a name="remotely-connect-from-a-windows-client"></a>Vzdálené připojení z klienta systému Windows

Než začnete, ujistěte se, že váš klient systému Windows používá prostředí Windows PowerShell 5.0 nebo novější.

Chcete-li se vzdáleně připojit z klienta systému Windows, postupujte takto.

1. Spusťte relaci prostředí Windows PowerShell jako správce.
2. Zkontrolujte, zda je ve vašem klientovi spuštěna služba Vzdálená správa systému Windows. Na příkazovém řádku zadejte:

    `winrm quickconfig`

3. Přiřaďte k IP adrese zařízení proměnnou.

    $ip = "<device_ip>"

    Nahraďte `<device_ip>` ji IP adresou vašeho zařízení.

4. Chcete-li přidat IP adresu zařízení do seznamu důvěryhodných hostitelů klienta, zadejte následující příkaz:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Spuštění relace prostředí Windows PowerShell na zařízení:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Po zobrazení výzvy zadejte heslo. Použijte stejné heslo, které se používá k přihlášení do místního webového uživatelského prostředí. Výchozí heslo místního webového uživatelského uživatelského prostředí je *Password1*. Při úspěšném připojení k zařízení pomocí vzdáleného prostředí PowerShell se zobrazí následující ukázkový výstup:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Vzdálené připojení z klienta Linuxu

V klientovi Linuxu, který budete používat pro připojení:

- [Nainstalujte nejnovější PowerShell Core pro Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) z GitHubu, abyste získali funkci vzdáleného komunikace SSH. 
- [Nainstalujte `gss-ntlmssp` pouze balíček z modulu NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Pro klienty Ubuntu použijte následující příkaz:
    - `sudo apt-get install gss-ntlmssp`

Další informace naleznete v [prostředí PowerShell remoting over SSH](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6).

Chcete-li se vzdáleně připojit z klienta systému nfs, postupujte takto.

1. Chcete-li otevřít relaci Prostředí PowerShell, zadejte:

    `sudo pwsh`
 
2. Pro připojení pomocí vzdáleného klienta zadejte:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Po zobrazení výzvy zadejte heslo použité k přihlášení k zařízení.
 
> [!NOTE]
> Tento postup nefunguje na Mac OS.
