---
title: Připojení k Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak připojit služby Azure Stack
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
ms.date: 1/14/2019
ms.author: mabrigg
ms.openlocfilehash: 2183467bab423fc3190bfe08f519ddc75dcd2874
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304444"
---
# <a name="connect-to-azure-stack"></a>Připojení ke službě Azure Stack

Ke správě prostředků, musíte se připojit k Azure Stack Development Kit. Tento článek podrobně popisuje kroky potřebné pro připojení k sadě pro vývoj. Můžete použít kteroukoli z následujících možností připojení:

* [Vzdálená plocha](#connect-with-remote-desktop): umožňuje jeden souběžný uživatel rychle připojit ze development kit.
* [Virtuální privátní sítě (VPN)](#connect-with-vpn): umožňuje připojení několika souběžných uživatelů z klientů mimo infrastruktury služby Azure Stack (vyžaduje konfiguraci).

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Připojení k Azure Stack pomocí vzdálené plochy
Připojení ke vzdálené ploše jeden souběžný uživatel umožňuje pracovat s portálem pro správu prostředků.

1. Otevřete připojení ke vzdálené ploše a připojte se k vývojové sadě. Zadejte **AzureStack\AzureStackAdmin** jako uživatelské jméno a heslo správce, který jste zadali při instalaci Azure Stack.  

2. Z vývojového počítače kit, otevřete Správce serveru, klikněte na tlačítko **místní Server**, vypněte rozšířené zabezpečení aplikace Internet Explorer a pak zavřete Správce serveru.

3. Chcete-li otevřít portál, přejděte na (https://portal.local.azurestack.external/) a přihlaste se pomocí uživatelských přihlašovacích údajů.


## <a name="connect-to-azure-stack-with-vpn"></a>Připojení k Azure Stack pomocí sítě VPN

Rozdělit tunelové připojení virtuální privátní sítě (VPN) připojení k Azure Stack Development Kit, můžete vytvořit. Prostřednictvím připojení VPN můžete přístup k portálu správce portálu user portal a místně nainstalovat nástroje, jako je Visual Studio a Powershellu ke správě prostředků Azure Stack. Připojení k síti VPN je podporován v obou prodloužil platnost aktivní Azure a nasazení na základě Active Directory Federation Services(AD FS). Připojení k síti VPN povolit více klientům připojit se ke službě Azure Stack ve stejnou dobu. 

> [!NOTE] 
> Toto připojení VPN neposkytuje připojení k Azure Stack infrastruktuře virtuálních počítačů. 

### <a name="prerequisites"></a>Požadavky

* Nainstalujte [Azure PowerShell kompatibilní služby Azure Stack](azure-stack-powershell-install.md) v místním počítači.  
* Ve službě [Azure Stack development Kit by měl být blobEndpoint](azure-stack-powershell-download.md) . 

### <a name="configure-vpn-connectivity"></a>Konfigurace připojení k síti VPN

Vytvořit připojení VPN typu development Kit, z vašeho místního počítače se systémem Windows otevřete relaci Powershellu se zvýšenými oprávněními a spusťte následující skript (ujistěte se, že k aktualizaci IP adresy a hesla, hodnoty pro vaše prostředí):

```PowerShell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the IP address and password values for your environment. 

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

Pokud je instalace úspěšná, měli byste vidět **azurestack** v seznamu připojení VPN.

![Připojení k síti](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Připojení ke službě Azure Stack

Připojte se k instanci služby Azure Stack pomocí některé z těchto dvou metod:  

* S použitím `Connect-AzsVpn ` příkaz: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Po zobrazení výzvy důvěryhodnosti hostitele služby Azure Stack a nainstalovat certifikát z **AzureStackCertificateAuthority** do úložiště certifikátů místního počítače. (řádku tento příkaz může nacházet za okno relace prostředí PowerShell). 

* Otevřete váš místní počítač **nastavení sítě** > **VPN** > klikněte na tlačítko **azurestack** > **připojit**. Na řádku přihlásit zadejte uživatelské jméno (AzureStack\AzureStackAdmin) a heslo.

### <a name="test-the-vpn-connectivity"></a>Test připojení k síti VPN

Pokud chcete otestovat připojení k portálu, otevřete internetový prohlížeč a přejděte na portál user portal (https://portal.local.azurestack.external/), přihlaste se a vytvářet prostředky.  

## <a name="next-steps"></a>Další postup



