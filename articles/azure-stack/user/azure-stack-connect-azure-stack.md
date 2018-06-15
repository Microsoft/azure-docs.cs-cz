---
title: Připojení k Azure zásobníku | Microsoft Docs
description: Zjistěte, jak připojit Azure zásobníku
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
ms.date: 08/22/2017
ms.author: mabrigg
ms.openlocfilehash: 7479202a8afabf5a84560691a2bccf849206c077
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
ms.locfileid: "26636173"
---
# <a name="connect-to-azure-stack"></a>Připojení ke službě Azure Stack

Ke správě prostředků, musíte se připojit k Azure zásobníku Development Kit. Tento článek podrobnosti kroky potřebné k připojení k sadě pro vývoj. Můžete použít některý z následujících možností připojení:

* [Vzdálená plocha](#connect-with-remote-desktop): umožňuje, aby uživatel s jednotným souběžných rychle připojit z development kit.
* [Virtuální privátní sítě (VPN)](#connect-with-vpn): umožňuje více souběžným uživatelům připojení od klientů mimo infrastruktury Azure zásobníku (vyžaduje konfiguraci).

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Připojení k Azure zásobníku pomocí vzdálené plochy
Pomocí připojení vzdálené plochy může uživatel s jednotným souběžných práci s portálem ke správě prostředků.

1. Otevřete připojení ke vzdálené ploše a připojte development Kit. Zadejte **AzureStack\AzureStackAdmin** jako uživatelského jména a hesla pro správu, který jste zadali při instalaci Azure zásobníku.  

2. Z počítače development kit, otevřete Správce serveru, klikněte na tlačítko **místní Server**, vypněte rozšířené zabezpečení aplikace Internet Explorer a zavřete Správce serveru.

3. Chcete-li otevřít na portálu, přejděte do (https://portal.local.azurestack.external/) a přihlaste se pomocí uživatelských přihlašovacích údajů.


## <a name="connect-to-azure-stack-with-vpn"></a>Připojení k Azure zásobník sítě VPN

Můžete vytvořit děleného tunelového připojení virtuální privátní sítě (VPN) Azure zásobníku Development Kit. Prostřednictvím připojení VPN získat přístup k portálu správce portálu user portal a místně nainstalován nástroje, jako je Visual Studio a prostředí PowerShell ke správě prostředků Azure zásobníku. Připojení k síti VPN je podporován v obou Active Directory(AAD) Azure a Active Directory Federation Services(AD FS) na základě nasazení. Připojení k síti VPN povolit více klientům připojení k Azure zásobníku ve stejnou dobu. 

> [!NOTE] 
> Toto připojení VPN neposkytuje připojení k Azure zásobníku infrastruktury virtuálních počítačů. 

### <a name="prerequisites"></a>Požadavky

* Nainstalujte [zásobník Azure kompatibilní Azure PowerShell](azure-stack-powershell-install.md) v místním počítači.  
* Stažení [nástroje potřebné pro práci s Azure zásobníku](azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>Konfigurace připojení k síti VPN

Pokud chcete vytvořit připojení k síti VPN development Kit, otevřete relaci prostředí PowerShell zvýšenými z vašeho místního počítače se systémem Windows a spusťte následující skript (Nezapomeňte aktualizujte hodnoty IP adresu a heslo pro vaše prostředí):

```PowerShell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Pokud instalace proběhne úspěšně, měli byste vidět **azurestack** v seznamu připojení VPN.

![Připojení k síti](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Připojení ke službě Azure Stack

Připojte se k instanci Azure zásobníku pomocí některé z následujících dvou metod:  

* Pomocí `Connect-AzsVpn ` příkaz: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Po zobrazení výzvy důvěryhodnosti hostitele zásobník Azure a nainstalujte certifikát z **AzureStackCertificateAuthority** do úložiště certifikátů místního počítače. (řádku tento příkaz může vypadat za okno relace prostředí PowerShell). 

* Otevřete místního počítače **nastavení sítě** > **VPN** > klikněte na tlačítko **azurestack** > **připojit**. Do příkazového řádku přihlášení zadejte uživatelské jméno (AzureStack\AzureStackAdmin) a heslo.

### <a name="test-the-vpn-connectivity"></a>Test připojení VPN

K otestování připojení k portálu, otevřete internetový prohlížeč a přejděte na portál user Portal (https://portal.local.azurestack.external/), přihlaste se a vytvářet prostředky.  

## <a name="next-steps"></a>Další kroky



