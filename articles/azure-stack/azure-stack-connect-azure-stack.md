---
title: Připojení k Azure zásobníku | Microsoft Docs
description: Zjistěte, jak se připojit k Azure zásobníku.
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
ms.date: 06/06/2018
ms.author: mabrigg
ms.openlocfilehash: c1932f2ed0486fb56e467466c0fed53702e8f9b0
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248682"
---
# <a name="connect-to-azure-stack-development-kit"></a>Připojení k Azure zásobníku Development Kit

*Platí pro: Azure zásobníku Development Kit*

Ke správě prostředků, musíte nejdřív připojit k Azure zásobníku Development Kit. V tomto článku jsme popisují kroky, které můžete provést připojení k sadě pro vývoj. Můžete použít jednu z následujících možností připojení:

* [Připojení ke vzdálené ploše](#connect-with-remote-desktop). Při připojení pomocí připojení ke vzdálené ploše jednoho uživatele můžete rychle připojit k sadě pro vývoj.
* [Virtuální privátní sítě (VPN)](#connect-with-vpn). Jakmile se připojíte pomocí sítě VPN, můžou souběžně více uživatelé připojovat ze klientům mimo infrastruktury Azure zásobníku. Připojení k síti VPN vyžaduje některé instalační program.

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Připojení k Azure zásobníku pomocí připojení ke vzdálené ploše

Uživatel s jednotným souběžných můžete spravovat prostředky v portálu operátor nebo portálu user portal prostřednictvím připojení ke vzdálené ploše.

1. Otevřete připojení ke vzdálené ploše a připojte development Kit. Uživatelské jméno, zadejte **AzureStack\AzureStackAdmin**. Použijte operátor heslo, které jste zadali při nastavení Azure zásobníku.  

2. Na kit vývojovém počítači otevřete Správce serveru. Vyberte **místní Server**, zrušte **rozšířeného zabezpečení aplikace Internet Explorer** zaškrtněte políčko a pak zavřete Správce serveru.

3. Chcete-li otevřít [portálu user portal](azure-stack-key-features.md#portal), přejděte na https://portal.local.azurestack.external/. Přihlaste se pomocí přihlašovacích údajů uživatele. Chcete-li otevřít zásobník Azure [operátor portál](azure-stack-key-features.md#portal), přejděte na https://adminportal.local.azurestack.external/. Přihlaste se pomocí přihlašovacích údajů Azure Active Directory (Azure AD), které zadáte během instalace.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Připojení k Azure zásobníku pomocí sítě VPN

Můžete vytvořit dělené tunelové propojení VPN připojení Azure zásobníku Development Kit. Připojení k síti VPN můžete použít pro přístup k portálu operátor Azure zásobníku, portál user portal a lokálně nainstalované nástroje, například Visual Studio a prostředí PowerShell ke správě prostředků Azure zásobníku. Připojení k síti VPN se podporuje ve službě Azure AD a nasazení služby Active Directory Federation Services (AD FS). Připojení k síti VPN umožňují více klientům připojení k Azure zásobníku ve stejnou dobu.

> [!NOTE]
> Připojení k síti VPN neposkytuje připojení k Azure zásobníku infrastruktury virtuálních počítačů.

### <a name="prerequisites"></a>Požadavky

1. Nainstalujte [kompatibilní s Azure zásobník Azure PowerShell](azure-stack-powershell-install.md) v místním počítači.  
2. Stažení [nástroje potřebné pro práci s Azure zásobníku](azure-stack-powershell-download.md).

### <a name="set-up-vpn-connectivity"></a>Nastavit připojení k síti VPN

Pokud chcete vytvořit připojení k síti VPN development Kit, otevřete prostředí Windows PowerShell jako správce v místním počítači systému Windows. Potom spusťte následující skript (aktualizace IP adresy a hesla hodnoty pro vaše prostředí):

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

Pokud instalace proběhne úspěšně, **azurestack** se zobrazí v seznamu připojení VPN.

![Připojení k síti](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Připojení ke službě Azure Stack

Připojte se k instanci Azure zásobníku pomocí jedné z následujících metod:  

* Použití `Connect-AzsVpn ` příkaz:
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Po zobrazení výzvy důvěryhodnosti hostitele zásobník Azure a nainstalujte certifikát z **AzureStackCertificateAuthority** v úložišti certifikátů místního počítače. (Na řádku může být skrytý na základě okno prostředí PowerShell.)

* V místním počítači, vyberte **nastavení sítě** > **VPN** > **azurestack** > **připojit**. Do příkazového řádku přihlášení zadejte uživatelské jméno (**AzureStack\AzureStackAdmin**) a heslo.

### <a name="test-vpn-connectivity"></a>Test připojení VPN

Chcete-li otestovat připojení k portálu, otevřete webový prohlížeč a pak přejděte k portálu user portal (https://portal.local.azurestack.external/) nebo portálu – operátor (https://adminportal.local.azurestack.external/). Přihlaste se a vytvářet prostředky.  

## <a name="next-steps"></a>Další postup

[Virtuální počítače zpřístupnit uživatelům Azure zásobníku](azure-stack-tutorial-tenant-vm.md)
