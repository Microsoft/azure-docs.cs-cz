---
title: 'Azure Active Directory Domain Services: Konfigurace řešení potíží s skupinu zabezpečení sítě | Microsoft Docs'
description: Řešení potíží s konfigurací skupina NSG pro Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: ergreenl
ms.openlocfilehash: ca3292f1b89fc461950a47116126b6f5338fb381
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Řešení potíží s neplatná konfigurace sítí pro spravované doméně
Tento článek pomáhá vyřešit chyby konfigurace související se sítí, kterých se v následující upozornění:

## <a name="alert-aadds104-network-error"></a>Výstrahy AADDS104: Chyba sítě
**Upozornění:** *společnosti Microsoft je možné vás zastihnout na řadiče domény pro toto spravované domény. To může dojít, pokud skupina zabezpečení sítě (NSG) nakonfigurované na vaší virtuální sítě blokuje přístup k spravované doméně. Dalším důvodem je, pokud je trasy definované uživatelem, které blokuje příchozí provoz z Internetu.*

Neplatná konfigurace NSG jsou nejběžnější příčina chyby sítě Azure AD Domain Services. Skupina zabezpečení sítě (NSG), nakonfigurovaná pro virtuální síť musí umožňovat přístup k [určité porty](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services). Pokud jsou tyto porty zablokované, Microsoft nelze sledovat nebo aktualizovat vaší spravované domény. Synchronizace mezi adresáře Azure AD a vaší spravované domény je navíc dopad. Při vytváření vaše skupina NSG, nechte tyto porty otevřít, aby se zabránilo přerušení ve službě.


## <a name="sample-nsg"></a>Ukázka NSG
Následující tabulka znázorňuje ukázku NSG, který by zabezpečit vaší spravované domény současně Microsoft monitorovat, spravovat a aktualizovat informace.

![Ukázka NSG](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Služba Azure AD Domain Services vyžaduje neomezenou odchozí přístup z virtuální sítě. Doporučujeme Nevytvářet žádné další pravidla NSG, které omezuje odchozí přístup pro virtuální síť.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Přidat pravidlo pro skupinu zabezpečení sítě pomocí portálu Azure
Pokud nechcete, jak pomocí prostředí PowerShell, můžete ručně přidat jednoho pravidla do skupiny zabezpečení sítě pomocí portálu Azure. K vytvoření pravidel ve skupině zabezpečení sítě, proveďte následující kroky:

1. Přejděte na [skupin zabezpečení sítě](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) na portálu Azure
2. Z tabulky vyberte skupinu NSG přidružených k podsíti, ve kterém je povolená vaší spravované domény.
3. V části **nastavení** v levém panelu klikněte na možnost **příchozí pravidla zabezpečení** nebo **odchozí pravidla zabezpečení**.
4. Kliknutím na vytvořit pravidlo **přidat** a vyplněním informace. Klikněte na **OK**.
5. Ověřte, zda že byla vytvořena pravidla tím, že v tabulce pravidel.


## <a name="create-an-nsg-for-azure-ad-domain-services-using-powershell"></a>Vytvořit skupinu NSG pro Azure AD Domain Services pomocí prostředí PowerShell
Tato skupina NSG je nakonfigurovat pro povolení příchozí provoz pro porty vyžadované službou Azure AD Domain Services, při odepření další nežádoucí příchozí přístup.

**Předpoklad: Instalace a konfigurace prostředí Azure PowerShell** postupujte podle pokynů a [instalace modulu Azure PowerShell a připojte se k předplatnému Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

>[!TIP]
> Doporučujeme používat nejnovější verzi modulu Azure PowerShell. Pokud již máte starší verzi modulu Azure PowerShell nainstalovaný, aktualizujte na nejnovější verzi.
>

Pomocí následujících kroků můžete vytvořit nová skupina NSG pomocí prostředí PowerShell.
1. Přihlaste se k předplatnému Azure.

  ```PowerShell
  # Log in to your Azure subscription.
  Login-AzureRmAccount
  ```

2. Vytvořte skupinu NSG s tři pravidla. Následující skript definuje tři pravidla NSG, které umožní přístup k portům potřebné ke spuštění služby Azure AD Domain Services. Tento skript vytvoří nová skupina NSG, který obsahuje tato pravidla. Chcete-li přidat další pravidla, které umožňují další příchozí provoz, pokud to vyžaduje úlohy nasazené ve virtuální síti, použijte stejný formát.

  ```PowerShell
  # Allow inbound HTTPS traffic to enable synchronization to your managed domain.
  $SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD -Description "Allow synchronization with Azure AD" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443

  # Allow management of your domain over port 5986 (PowerShell Remoting)
  $PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting -Description "Allow management of domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986

  #The following two rules are optional and needed only in certain situations.

  # Allow management of your domain over port 3389 (remote desktop).
  $RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD -Description "Allow management of domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix 207.68.190.32/27, 13.106.78.32/27, 10.254.32.0/20, 10.97.136.0/22, 13.106.174.32/27, 13.106.4.96/27 -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389

  # Secure LDAP rule, it is recommended to change the source address prefix to include only the IP addresses
  $SecureLDAPRule = New-AzureRmNetworkSecurityRuleConfig -Name SecureLDAP -Description "Allow access through secure LDAP port" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 104 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 636

  # Create the NSG with the rules above (if you need the remote desktop rule and secure ldap rule, add it below)
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDomainServices-NSG" -SecurityRules $SyncRule, $PSRemotingRule
  ```

3. Nakonec přidružte NSG virtuální síť a podsíť výběru.

  ```PowerShell
  # Find vnet and subnet
  $Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
  $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

  # Set the nsg to the subnet and save the changes
  $Subnet.NetworkSecurityGroup = $Nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
  ```

## <a name="full-script-to-create-and-apply-an-nsg-for-azure-ad-domain-services"></a>Úplné skript pro vytvoření a použití skupinu NSG pro Azure AD Domain Services
>[!TIP]
> Doporučujeme používat nejnovější verzi modulu Azure PowerShell. Pokud již máte starší verzi modulu Azure PowerShell nainstalovaný, aktualizujte na nejnovější verzi.
>

```PowerShell
# Change the following values to match your deployment
$ResourceGroup = "ResourceGroupName"
$Location = "westus"
$VnetName = "exampleVnet"
$SubnetName = "exampleSubnet"

# Log in to your Azure subscription.
Login-AzureRmAccount

# Allow inbound HTTPS traffic to enable synchronization to your managed domain.
$SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD -Description "Allow synchronization with Azure AD" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

# Allow management of your domain over port 5986 (PowerShell Remoting)
$PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting -Description "Allow management of domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 5986

# The following two rules are optional and needed only in certain situations.

# Allow management of your domain over port 3389 (remote desktop).
$RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD -Description "Allow management of domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix 207.68.190.32/27, 13.106.78.32/27, 10.254.32.0/20, 10.97.136.0/22, 13.106.174.32/27, 13.106.4.96/27 -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

# Secure LDAP rule, it is recommended to change the source address prefix to include only the IP addresses
$SecureLDAPRule = New-AzureRmNetworkSecurityRuleConfig -Name SecureLDAP -Description "Allow access through secure LDAP port" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 104 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 636

# Create the NSG with the rules above (if you need the remote desktop rule and secure ldap rule, add it below)
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "AADDomainServices-NSG" -SecurityRules $SyncRule, $PSRemotingRule

# Find vnet and subnet
$Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

# Set the nsg to the subnet and save the changes
$Subnet.NetworkSecurityGroup = $Nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
```


## <a name="need-help"></a>Potřebujete pomoct?
Obraťte se na produktový tým Azure Active Directory Domain Services na [sdílet zpětnou vazbu nebo pro podporu](active-directory-ds-contact-us.md).
