---
title: Připojení k Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak se připojit k ASDK.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: knithinc
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 44f12c72716e98e2f28796359467685e0af579dd
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238242"
---
# <a name="connect-to-the-asdk"></a>Připojte se k ASDK

Ke správě prostředků, musíte nejdřív připojit k Azure Stack Development Kit (ASDK). V tomto článku jsme popisují kroky, které můžete provést připojení k ASDK s použitím následujících možností připojení:

* [Připojení ke vzdálené ploše (RDP)](#connect-with-rdp). Když se připojíte pomocí připojení ke vzdálené ploše, můžete rychle připojit jenom jednoho konkrétního uživatele development Kit.
* [Virtuální privátní síť (VPN)](#connect-with-vpn). Když se připojíte pomocí sítě VPN, více uživatelů současně připojit z klientům mimo infrastruktura Azure stacku. Připojení k síti VPN vyžaduje některé nastavení.

<a name="connect-with-rdp"></a>
## <a name="connect-to-azure-stack-using-rdp"></a>Připojení ke službě Azure Stack pomocí protokolu RDP

Jeden souběžný uživatel můžete přímo z hostitelského počítače ASDK spravovat prostředky v portálu pro správu služby Azure Stack nebo portálu user portal prostřednictvím připojení ke vzdálené ploše. 

> [!TIP]
> Tato možnost taky dovoluje RDP znovu použít při přihlášení ASDK hostitelský počítač k přihlášení k virtuálním počítačům vytvořeným v hostitelském počítači ASDK. 

1. Otevřete připojení ke vzdálené ploše (mstc.exe) a připojte se k development kit počítače IP adresa hostitele pomocí účtu oprávnění k přihlášení k hostitelskému počítači ASDK vzdáleně. Ve výchozím nastavení **AzureStack\AzureStackAdmin** má oprávnění k do vzdáleného úložiště v hostitelském počítači ASDK.  

2. Na hostitelském počítači development kit otevřete Správce serveru (ServerManager.exe). Vyberte **místní Server**, vypněte **konfigurace rozšířeného zabezpečení Internet Exploreru**, zavřete Správce serveru.

3. Přihlaste se k portálu pro správu jako **AzureStack\CloudAdmin** nebo použijte jiné přihlašovací údaje Azure Stack operátor. Je adresy portálu správy ASDK [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

4. Přihlaste se k portálu user portal jako **AzureStack\CloudAdmin** nebo použijte jiné přihlašovací údaje uživatele Azure stacku. Je ASDK uživatelského portálu adresy [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

> [!NOTE]
> Další informace o používání účtu, který najdete v tématu [základy správy ASDK](asdk-admin-basics.md#what-account-should-i-use).

<a name="connect-with-vpn"></a>
## <a name="connect-to-azure-stack-using-vpn"></a>Připojení ke službě Azure Stack pomocí sítě VPN

Rozdělit tunelové připojení sítě VPN do počítače hostitele ASDK pro přístup k Azure Stack portály a lokálně nainstalovaných nástrojů, jako je Visual Studio a prostředí PowerShell můžete vytvořit. Pomocí připojení VPN, můžete připojit několik uživatelů ve stejnou dobu pro prostředky Azure Stack, jejichž hostitelem ASDK.

Připojení k síti VPN se podporuje pro Azure AD a Active Directory Federation Services (AD FS) nasazení.

> [!NOTE]
> Připojení k síti VPN *nemá* poskytovat připojení k virtuálním počítačům Azure Stack. Nebudete moct pomocí RDP připojte virtuální počítače s Azure Stack během připojení přes síť VPN.

### <a name="prerequisites"></a>Požadavky
Před nastavením připojení VPN k ASDK, zajistěte, že splňujete následující předpoklady.

- Nainstalujte [prostředí Azure PowerShell kompatibilní s Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) v místním počítači.  
- Ve službě [Azure Stack development Kit by měl být blobEndpoint](asdk-post-deploy.md#download-the-azure-stack-tools) .

### <a name="set-up-vpn-connectivity"></a>Nastavit připojení k síti VPN

Chcete-li vytvořit připojení VPN k ASDK, otevřete PowerShell jako správce na místním počítači založené na Windows. Potom spusťte následující skript (aktualizace IP adresy a hesla hodnoty pro vaše prostředí):

```PowerShell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit host computer’s IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Pokud je instalace úspěšná, **azurestack** se zobrazí v seznamu připojení VPN.

![Připojení k síti](media/asdk-connect/vpn.png)  

### <a name="connect-to-azure-stack"></a>Připojení ke službě Azure Stack

  Připojte se k instanci služby Azure Stack pomocí jedné z následujících metod:  

  * Použití `Connect-AzsVpn ` příkaz:
      
    ```PowerShell
    Connect-AzsVpn `
      -Password $Password
    ```

  * V místním počítači, vybrat **nastavení sítě** > **VPN** > **azurestack** > **připojení**. Na řádku přihlásit, zadejte uživatelské jméno (**AzureStack\AzureStackAdmin**) a heslo.

Při prvním připojení se zobrazí výzva k instalaci kořenového certifikátu služby Azure Stack z **AzureStackCertificateAuthority** v úložišti certifikátů místního počítače. Tento krok přidává do seznamu důvěryhodných hostitelů ASDK certifikační autority (CA). Klikněte na tlačítko **Ano** k instalaci certifikátu.

![Kořenový certifikát](media/asdk-connect/cert.png)  
  
  > [!IMPORTANT]
  > Na řádku může být skrytá okna prostředí PowerShell nebo jiné aplikace.

### <a name="test-vpn-connectivity"></a>Test připojení k síti VPN

Pokud chcete otestovat připojení k portálu, otevřete webový prohlížeč a přejděte k portálu user portal (https://portal.local.azurestack.external/) nebo na portálu pro správu (https://adminportal.local.azurestack.external/). 

Přihlásit pomocí přihlašovacích údajů příslušné předplatné vytvořit a spravovat prostředky.  

## <a name="next-steps"></a>Další postup

[Řešení potíží](asdk-troubleshooting.md)
