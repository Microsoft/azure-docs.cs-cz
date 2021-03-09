---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 5c1ac3f79ab5db2622dafd3229b39bbe19bce41e
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473695"
---
V závislosti na operačním systému klienta se postupy pro vzdálené připojení k zařízení liší.

### <a name="remotely-connect-from-a-windows-client"></a>Vzdálené připojení z klienta Windows


#### <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

- Na vašem klientovi Windows běží Windows PowerShell 5,0 nebo novější.
- Váš klient Windows má podpisový řetěz (kořenový certifikát) odpovídající certifikátu uzlu nainstalovanému v zařízení. Podrobné pokyny najdete v tématu [instalace certifikátu na klienta Windows](../articles/databox-online/azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).
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

    > [!NOTE] 
    > Když použijete tuto `-UseSSL` možnost, bude Vzdálená komunikace prostřednictvím prostředí PowerShell prostřednictvím *protokolu HTTPS*. Pro vzdálené připojení prostřednictvím PowerShellu doporučujeme vždycky používat *protokol HTTPS* . I když relace *http* není nejbezpečnější metodou připojení, je přijatelná v důvěryhodných sítích.

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

### <a name="remotely-connect-from-a-linux-client"></a>Vzdálené připojení z klienta se systémem Linux

V klientovi se systémem Linux, který budete používat pro připojení:

- [Nainstalujte nejnovější prostředí PowerShell Core pro Linux](/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6&preserve-view=true) z GitHubu, abyste získali funkci Vzdálená komunikace SSH. 
- [Nainstalujte jenom `gss-ntlmssp` balíček z modulu NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). U klientů Ubuntu použijte následující příkaz:
    - `sudo apt-get install gss-ntlmssp`

Pokud potřebujete další informace, přečtěte si [v protokolu SSH Vzdálená komunikace PowerShellu](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6&preserve-view=true).

Pomocí těchto kroků se můžete vzdáleně připojit z klienta NFS.

1. K otevření relace PowerShellu zadejte:

    `sudo pwsh`
 
2. Pro připojení pomocí vzdáleného klienta zadejte:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser -UseSSL`

    Po zobrazení výzvy zadejte heslo, které jste použili k přihlášení do zařízení.
 
> [!NOTE]
> Tento postup nefunguje na Mac OS.