---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: f9707ab48d64ede2e796675eb3a0a6e7820c7073
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603374"
---
V závislosti na operačním systému klienta se postupy pro vzdálené připojení k zařízení liší.

### <a name="remotely-connect-from-a-windows-client"></a>Vzdálené připojení z klienta Windows

Než začnete, ujistěte se, že na vašem klientovi Windows běží Windows PowerShell 5,0 nebo novější.

Pomocí těchto kroků se můžete vzdáleně připojit z klienta Windows.

1. Spusťte relaci Windows PowerShellu jako správce.
2. Ujistěte se, že je ve vašem klientovi spuštěná služba Vzdálená správa systému Windows. Na příkazovém řádku zadejte:

    `winrm quickconfig`

3. Přiřaďte proměnnou k IP adrese zařízení.

    $ip = "<device_ip>"

    Nahraďte `<device_ip>` IP adresou vašeho zařízení.

4. Pokud chcete přidat IP adresu vašeho zařízení do seznamu důvěryhodných hostitelů klienta, zadejte následující příkaz:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Spusťte relaci prostředí Windows PowerShell na zařízení:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Po zobrazení výzvy zadejte heslo. Použijte stejné heslo, které se použije k přihlášení k místnímu webovému uživatelskému rozhraní. Výchozí heslo místního webového uživatelského rozhraní je *Heslo1*. Po úspěšném připojení k zařízení pomocí vzdáleného prostředí PowerShell se zobrazí následující vzorový výstup:  

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

### <a name="remotely-connect-from-a-linux-client"></a>Vzdálené připojení z klienta se systémem Linux

V klientovi se systémem Linux, který budete používat pro připojení:

- [Nainstalujte nejnovější prostředí PowerShell Core pro Linux](/powershell/scripting/install/installing-powershell-core-on-linux) z GitHubu, abyste získali funkci Vzdálená komunikace SSH. 
- [Nainstalujte jenom `gss-ntlmssp` balíček z modulu NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). U klientů Ubuntu použijte následující příkaz:
    - `sudo apt-get install gss-ntlmssp`

Pokud potřebujete další informace, přečtěte si [v protokolu SSH Vzdálená komunikace PowerShellu](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Pomocí těchto kroků se můžete vzdáleně připojit z klienta NFS.

1. K otevření relace PowerShellu zadejte:

    `pwsh`
 
2. Pro připojení pomocí vzdáleného klienta zadejte:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Po zobrazení výzvy zadejte heslo, které jste použili k přihlášení do zařízení.
 
> [!NOTE]
> Tento postup nefunguje na macOS.