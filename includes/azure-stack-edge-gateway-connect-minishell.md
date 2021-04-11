---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 89e648099a5ac6d905f475319cc108dd0d05a6e9
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081153"
---
V závislosti na operačním systému klienta se postupy pro vzdálené připojení k zařízení liší.

### <a name="remotely-connect-from-a-windows-client"></a>Vzdálené připojení z klienta Windows


#### <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

- Na vašem klientovi Windows běží Windows PowerShell 5,0 nebo novější.
- Váš klient Windows má podpisový řetěz (kořenový certifikát) odpovídající certifikátu uzlu nainstalovanému v zařízení. Podrobné pokyny najdete v tématu [instalace certifikátu na klienta Windows](../articles/databox-online/azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).
- `hosts`Soubor umístěný v `C:\Windows\System32\drivers\etc` pro klienta systému Windows obsahuje položku odpovídající certifikátu uzlu v následujícím formátu:

    `<Device IP>    <Node serial number>.<DNS domain of the device>`

    Tady je příklad položky `hosts` souboru:
 
    `10.100.10.10    1HXQG13.wdshcsso.com`
  

#### <a name="detailed-steps"></a>Podrobné kroky

Pomocí těchto kroků se můžete vzdáleně připojit z klienta Windows.

1. Spusťte relaci Windows PowerShellu jako správce.
2. Ujistěte se, že je ve vašem klientovi spuštěná služba Vzdálená správa systému Windows. Na příkazovém řádku zadejte:

    `winrm quickconfig`

    Další informace najdete v tématu [instalace a konfigurace pro vzdálená správa systému Windows](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management#quick-default-configuration).

3. Přiřaďte proměnnou k IP adrese zařízení.

    $ip = "<device_ip>"

    Nahraďte `<device_ip>` IP adresou vašeho zařízení.

4. Pokud chcete přidat IP adresu vašeho zařízení do seznamu důvěryhodných hostitelů klienta, zadejte následující příkaz:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Spusťte relaci prostředí Windows PowerShell na zařízení:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL`

    Pokud se zobrazí chyba související se vztahem důvěryhodnosti, zkontrolujte, jestli je na klientovi, který přistupuje k vašemu zařízení, nainstalovaný podpisový řetězec uzlu, který jste nahráli do vašeho zařízení.

    Pokud certifikáty nepoužíváte (doporučujeme použít certifikáty!), můžete tuto kontrolu přeskočit pomocí možností relace: `-SkipCACheck -SkipCNCheck -SkipRevocationCheck` .

    ```powershell
    $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck 
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions    
    ```

    > [!NOTE] 
    > Když použijete tuto `-UseSSL` možnost, bude Vzdálená komunikace prostřednictvím prostředí PowerShell prostřednictvím *protokolu HTTPS*. Pro vzdálené připojení prostřednictvím PowerShellu doporučujeme vždycky používat *protokol HTTPS* . 

6. Po zobrazení výzvy zadejte heslo. Použijte stejné heslo, které se použije k přihlášení k místnímu webovému uživatelskému rozhraní. Výchozí heslo místního webového uživatelského rozhraní je *Heslo1*. Po úspěšném připojení k zařízení pomocí vzdáleného prostředí PowerShell se zobrazí následující vzorový výstup:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

> [!IMPORTANT]
> V aktuální verzi se můžete připojit k rozhraní PowerShell zařízení jenom přes klienta Windows. `-UseSSL`Možnost nefunguje u klientů se systémem Linux.

<!--### Remotely connect from a Linux client-->

<!--On the Linux client that you'll use to connect:

- [Install the latest PowerShell Core for Linux](/powershell/scripting/install/installing-powershell-core-on-linux) from GitHub to get the SSH remoting feature. 
- [Install only the `gss-ntlmssp` package from the NTLM module](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). For Ubuntu clients, use the following command:
    - `sudo apt-get install gss-ntlmssp`

For more information, go to [PowerShell remoting over SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Follow these steps to remotely connect from an NFS client.

1. To open PowerShell session, type:

    `pwsh`
 
2. For connecting using the remote client, type:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    When prompted, provide the password used to sign into your device.
 
> [!NOTE]
> This procedure does not work on Mac OS.-->