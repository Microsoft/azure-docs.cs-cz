---
title: Připojení k Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak se připojit ke službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/28/2018
ms.author: mabrigg
ms.openlocfilehash: 1cdf013325afe4b217f5f56043e06f60a4933419
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49993165"
---
# <a name="connect-to-azure-stack-development-kit"></a>Připojení k Azure Stack Development Kit

*Platí pro: Azure Stack Development Kit*

Ke správě prostředků, musíte se nejdřív připojit k Azure Stack Development Kit. V tomto článku jsme popisují kroky, které můžete provést pro připojení k sadě pro vývoj. Můžete použít jednu z následujících možností připojení:

* [Připojení ke vzdálené ploše](#connect-with-remote-desktop). Když se připojíte pomocí připojení ke vzdálené ploše, můžete rychle připojit jenom jednoho konkrétního uživatele development Kit.
* [Virtuální privátní sítě (VPN)](#connect-with-vpn). Když se připojíte pomocí sítě VPN, více uživatelů současně připojit z klientům mimo infrastruktura Azure stacku. Připojení k síti VPN vyžaduje některé nastavení.

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Připojení ke službě Azure Stack pomocí připojení ke vzdálené ploše

Jeden souběžný uživatel může spravovat prostředky v operátoru portál nebo portál user portal prostřednictvím připojení ke vzdálené ploše.

1. Otevřete připojení ke vzdálené ploše a připojte se k vývojové sadě. Uživatelské jméno zadejte **AzureStack\AzureStackAdmin**. Použijte operátor heslo, které jste zadali při nastavení služby Azure Stack.  

2. Ve vývojovém počítači kit otevřete Správce serveru. Vyberte **místní Server**, zrušte zaškrtnutí políčka **rozšířeného zabezpečení aplikace Internet Explorer** zaškrtněte políčko a potom zavřete Správce serveru.

3. Chcete-li otevřít [portálu user portal](azure-stack-key-features.md#portal), přejděte na stránku https://portal.local.azurestack.external/. Přihlaste se pomocí uživatelských přihlašovacích údajů. Otevřete Azure Stack [operátor portál](azure-stack-key-features.md#portal), přejděte na stránku https://adminportal.local.azurestack.external/. Přihlaste se pomocí přihlašovacích údajů Azure Active Directory (Azure AD), které jste zadali během instalace.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Připojení ke službě Azure Stack pomocí sítě VPN

Můžete vytvořit dělené tunelové propojení připojení VPN k Azure Stack Development Kit. Připojení k síti VPN můžete použít pro přístup k portálu Azure Stack operátor, portál user portal a lokálně nainstalovaných nástrojů, jako je Visual Studio a Powershellu ke správě prostředků Azure Stack. Připojení k síti VPN se podporuje ve službě Azure AD a nasazení služby Active Directory Federation Services (AD FS). Připojení k síti VPN umožňují více klientům připojit se ke službě Azure Stack ve stejnou dobu.

> [!NOTE]
> Připojení k síti VPN neposkytuje připojení k Azure Stack infrastruktuře virtuálních počítačů.

### <a name="prerequisites"></a>Požadavky

1. Nainstalujte [prostředí Azure PowerShell kompatibilní s Azure Stack](azure-stack-powershell-install.md) v místním počítači.  
2. Ve službě [Azure Stack development Kit by měl být blobEndpoint](azure-stack-powershell-download.md) .

### <a name="set-up-vpn-connectivity"></a>Nastavit připojení k síti VPN

Chcete-li vytvořit připojení VPN development Kit, otevřete prostředí Windows PowerShell jako správce na vaše místní počítače se systémem Windows. Potom spusťte následující skript (aktualizace IP adresy a hesla hodnoty pro vaše prostředí):

```PowerShell
# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit computer’s host IP address and certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

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

![Připojení k síti](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Připojení ke službě Azure Stack

Připojte se k instanci služby Azure Stack pomocí jedné z následujících metod:  

* Použití `Connect-AzsVpn ` příkaz:
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Po zobrazení výzvy důvěryhodnosti hostitele služby Azure Stack a nainstalovat certifikát z **AzureStackCertificateAuthority** v úložišti certifikátů místního počítače. (Do řádku může být skryty pomocí okna prostředí PowerShell.)

* V místním počítači, vybrat **nastavení sítě** > **VPN** > **azurestack** > **připojení**. Na řádku přihlásit, zadejte uživatelské jméno (**AzureStack\AzureStackAdmin**) a heslo.

### <a name="test-vpn-connectivity"></a>Test připojení k síti VPN

Pokud chcete otestovat připojení k portálu, otevřete webový prohlížeč a přejděte k portálu user portal (https://portal.local.azurestack.external/) nebo na portálu – operátor (https://adminportal.local.azurestack.external/). Přihlaste se a vytvářet prostředky.  

## <a name="next-steps"></a>Další postup

[Virtuální počítače zpřístupnit uživatelům Azure stacku](azure-stack-tutorial-tenant-vm.md)
