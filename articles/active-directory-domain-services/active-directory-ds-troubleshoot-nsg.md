---
title: 'Azure Active Directory Domain Services: Konfigurace řešení potíží s skupina zabezpečení sítě | Dokumentace Microsoftu'
description: Řešení potíží s konfigurací NSG pro službu Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: ergreenl
ms.openlocfilehash: 67f4f0850d0600fc7ca0f1323e7c7801187089f5
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37950730"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Řešení potíží s neplatnou konfiguraci sítě pro vaši spravovanou doménu
Tento článek pomáhá odstraňovat potíže a řešit chyby související se sítí konfigurace, které za následek následující upozornění:

## <a name="alert-aadds104-network-error"></a>Upozornění AADDS104: Chyba sítě
**Zpráva s výstrahou:** *společnost Microsoft se nám kontaktovat řadiče této spravované doméně. To může stát v případě skupinu zabezpečení sítě (NSG) nakonfigurovaná ve vaší virtuální síti blokuje přístup ke spravované doméně. Dalším důvodem je, pokud trasy definované uživatelem, která blokuje příchozí provoz z Internetu.*

Neplatná konfigurace skupiny zabezpečení sítě jsou nejčastější příčina chyby sítě pro službu Azure AD Domain Services. Skupiny zabezpečení sítě (NSG) nakonfigurovaná pro virtuální síť musí umožňovat přístup k [určité porty](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services). Pokud tyto porty jsou blokované, Microsoft nemůže monitorovat nebo aktualizovat vaší spravované domény. Kromě toho má vliv synchronizace adresáře služby Azure AD a vaší spravované domény. Při vytváření vaší skupiny zabezpečení sítě, nechte tyto porty otevřené zabránili přerušení služby.

### <a name="checking-your-nsg-for-compliance"></a>Kontroluje se skupina NSG pro dodržování předpisů

1. Přejděte [skupiny zabezpečení sítě](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) stránky na webu Azure Portal
2. Z tabulky zvolte skupinu zabezpečení sítě přidružená k podsíti, ve kterém je povolena vaší spravované domény.
3. V části **nastavení** na levém panelu klikněte na tlačítko **příchozí pravidla zabezpečení**
4. Zkontrolujte pravidla na místě a určit, která pravidla blokují přístup k [tyto porty](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)
5. Upravte skupiny NSG k zajištění dodržování předpisů buď odstraňuje se pravidlo, přidání pravidla nebo zcela vytvořením nové skupiny zabezpečení sítě. Kroky pro [přidat pravidlo](#add-a-rule-to-a-network-security-group-using-the-azure-portal) nebo [vytvořit nové, který vyhovuje NSG](#create-a-nsg-for-azure-ad-domain-services-using-powershell) jsou níže

## <a name="sample-nsg"></a>Ukázková skupina zabezpečení sítě
Následující tabulka znázorňuje ukázku NSG, která by zabezpečení vaší spravované domény zároveň umožní Microsoftu monitorovat, spravovat a aktualizovat informace.

![Ukázková skupina zabezpečení sítě](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Azure AD Domain Services vyžaduje neomezený odchozího přístupu z virtuální sítě. Doporučujeme nevytvářet jakékoli další pravidlo NSG, která omezuje přístup pro odchozí připojení pro virtuální síť.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Přidejte pravidlo skupiny zabezpečení sítě pomocí webu Azure portal
Pokud nechcete, jak pomocí prostředí PowerShell, můžete ručně přidat jednoho pravidla do skupin zabezpečení sítě pomocí webu Azure portal. Vytváření pravidel ve skupině zabezpečení sítě, proveďte následující kroky:

1. Přejděte [skupiny zabezpečení sítě](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) stránky na webu Azure Portal.
2. Z tabulky zvolte skupinu zabezpečení sítě přidružená k podsíti, ve kterém je povolena vaší spravované domény.
3. V části **nastavení** na levém panelu klikněte na možnost **příchozí pravidla zabezpečení** nebo **odchozí pravidla zabezpečení**.
4. Vytvořit pravidlo kliknutím **přidat** a vyplníte informace. Klikněte na **OK**.
5. Ověřte, že vaše pravidlo bylo vytvořené ve vyhledávání v tabulce pravidel.


## <a name="create-a-nsg-for-azure-ad-domain-services-using-powershell"></a>Vytvoření NSG pro Azure AD Domain Services pomocí Powershellu
Tato skupina zabezpečení sítě nakonfigurována tak, aby umožňovala příchozí provoz na porty vyžadované službou Azure AD Domain Services, při zamítnutí jiné nežádoucí příchozí přístup.

**Předpoklad: Instalace a konfigurace Azure Powershellu** postupujte podle pokynů a [instalace modulu Azure PowerShell a připojte se ke svému předplatnému Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

>[!TIP]
> Doporučujeme používat nejnovější verzi modulu Azure PowerShell. Pokud už máte starší verzi modulu Azure PowerShell nainstalovaný, aktualizujte na nejnovější verzi.
>

Použijte následující postup k vytvoření nové skupiny zabezpečení sítě pomocí Powershellu.
1. Přihlaste se ke svému předplatnému Azure.

  ```PowerShell
  # Log in to your Azure subscription.
  Connect-AzureRmAccount
  ```

2. Vytvořte skupinu zabezpečení sítě s tři pravidla. Následující skript definuje tři pravidla NSG, které umožňují přístup k portům potřebných ke spuštění služby Azure AD Domain Services. Tento skript vytvoří nová skupina NSG, která obsahuje tato pravidla. Chcete-li přidat další pravidla, která umožňují další příchozí provoz, pokud to vyžaduje úloh nasazených ve virtuální síti používají stejný formát.

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

3. A konečně přidružení NSG virtuální síť a podsíť podle výběru.

  ```PowerShell
  # Find vnet and subnet
  $Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
  $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

  # Set the nsg to the subnet and save the changes
  $Subnet.NetworkSecurityGroup = $Nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
  ```

## <a name="full-script-to-create-and-apply-an-nsg-for-azure-ad-domain-services"></a>Úplná skript k vytvoření a skupinu zabezpečení sítě pro službu Azure AD Domain Services
>[!TIP]
> Doporučujeme používat nejnovější verzi modulu Azure PowerShell. Pokud už máte starší verzi modulu Azure PowerShell nainstalovaný, aktualizujte na nejnovější verzi.
>

```PowerShell
# Change the following values to match your deployment
$ResourceGroup = "ResourceGroupName"
$Location = "westus"
$VnetName = "exampleVnet"
$SubnetName = "exampleSubnet"

# Log in to your Azure subscription.
Connect-AzureRmAccount

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
Obraťte se na Azure Active Directory Domain Services produktovému týmu a [podělit se o názory, nebo pro podporu](active-directory-ds-contact-us.md).
