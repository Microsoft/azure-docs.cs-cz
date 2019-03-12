---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554920"
---
Postupy pro vzdálené připojení k zařízení se liší podle operačního systému klienta.

### <a name="remotely-connect-from-a-windows-client"></a>Vzdálené připojení z klienta Windows

Než začnete, ujistěte se, že váš klient Windows běží prostředí Windows PowerShell 5.0 nebo novější.

Postupujte podle těchto kroků se vzdáleně připojíte z klienta Windows.

1. Spusťte relaci prostředí Windows PowerShell jako správce.
2. Ujistěte se, že služba Vzdálená správa Windows běží na klientovi. Na příkazovém řádku zadejte:

    `winrm quickconfig`

3. Přiřazení proměnné na IP adresu zařízení.

    $ip = "<device_ip>"

    Nahraďte `<device_ip>` s IP adresou vašeho zařízení.

4. Chcete-li přidat IP adresu vašeho zařízení do seznamu důvěryhodných hostitelů klienta, zadejte následující příkaz:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Spusťte relaci prostředí Windows PowerShell na zařízení:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Zadejte heslo po zobrazení výzvy. Použijte stejné heslo, který se používá k přihlašování do místního webového uživatelského rozhraní. Výchozí heslo místního webového uživatelského rozhraní je *Heslo1*. Po úspěšném připojení k zařízení pomocí vzdáleného prostředí PowerShell, se zobrazí následující ukázkový výstup:  

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

### <a name="remotely-connect-from-a-linux-client"></a>Vzdálené připojení z klienta systému Linux

V klientském počítači Linux, které budete používat pro připojení:

- [Nainstalujte nejnovější PowerShell Core pro Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) z Githubu, které chcete tuto funkci vzdálenou komunikaci SSH. 
- [Instalace pouze `gss-ntlmssp` balíček z modulu protokolu NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Pro klienty se systémem Ubuntu použijte následující příkaz:
    - `sudo apt-get install gss-ntlmssp`

Další informace najdete v části [Vzdálená komunikace Powershellu přes SSH](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6).

Postupujte podle těchto kroků se vzdáleně připojíte z klienta systému souborů NFS.

1. Chcete-li otevřít relaci prostředí PowerShell, zadejte:

    `sudo pwsh`
 
2. Pro připojení pomocí vzdáleného klienta, zadejte:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Po zobrazení výzvy zadejte heslo použité pro přihlášení ke svému zařízení.
 
> [!NOTE]
> Tento postup nefunguje v Mac OS.
