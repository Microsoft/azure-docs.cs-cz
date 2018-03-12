---
title: "Připojení ke cloudové službě k vlastní řadiče domény | Microsoft Docs"
description: "Zjistěte, jak připojit vaše role web nebo worker k vlastní doméně AD pomocí prostředí PowerShell a rozšíření AD domény"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 1e2d7c87-d254-4e7a-a832-67f84411ec95
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 4a50ae5e19ff9bf79b7f5361e5a274a2aba350f5
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Připojení k řadiči domény AD hostované v Azure vlastní role služeb v cloudu Azure
Nejprve nastavíme virtuální síť (VNet) v Azure. Potom přidáme Active Directory řadiče domény (hostované na virtuální počítač Azure) k virtuální síti. V dalším kroku jsme se přidat existující role cloudové služby na předem vytvořené virtuální síť a potom jejich připojení k řadiči domény.

Než začneme, několik věcí mějte na paměti, které:

1. Tento kurz používá prostředí PowerShell, proto se ujistěte, že máte Azure PowerShell nainstalované a připravené na vynucování. Chcete-li získat nápovědu pro nastavení prostředí Azure PowerShell, přečtěte si téma [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).
2. Vaše řadiče domény AD a Role Web nebo Worker instance musí být ve virtuální síti.

Postupujte podle podrobných pokynů a pokud narazíte na potíže, nechte nás komentář na konci tohoto článku. Někdo se vrátit k vám (Ano, jsme přečíst komentáře).

Musí být sítě, který je odkazován cloudové služby **klasickou virtuální síť**.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Můžete vytvořit virtuální síť v Azure pomocí portálu Azure nebo prostředí PowerShell. V tomto kurzu se používá prostředí PowerShell. Vytvoření virtuální sítě pomocí portálu Azure, naleznete v části [vytvořit virtuální síť](../virtual-network/quick-create-portal.md). Článek popisuje vytvoření virtuální sítě (Resource Manager), ale musíte vytvořit virtuální sítě (klasické) pro cloudové služby. Uděláte to tak, že na portálu vyberte **vytvořit prostředek**, typ *virtuální sítě* v **vyhledávání** pole a stiskněte klávesu **Enter**. Ve výsledcích hledání v části **všechno, co**, vyberte **virtuální síť**. V části **vybrat model nasazení**, vyberte **Classic**, pak vyberte **vytvořit**. Pak můžete podle kroků v článku.

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
Po dokončení nastavení virtuální sítě, musíte vytvořit řadič domény AD. V tomto kurzu budeme zadávat řadič domény služby AD na virtuální počítač Azure.

K tomu, vytvoření virtuálního počítače pomocí prostředí PowerShell pomocí následujících příkazů:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Povýšení na řadič domény virtuálního počítače
Konfigurace virtuálního počítače jako řadič domény AD, musíte se přihlaste k virtuálnímu počítači a jeho konfiguraci.

Přihlásit se k virtuálnímu počítači, můžete si stáhnout soubor RDP pomocí prostředí PowerShell, použijte následující příkazy:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Po přihlášení k virtuálnímu počítači nastavit virtuální počítač jako řadič domény služby AD podle podrobný průvodce [jak nastavit zákazníkovi řadič domény AD](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Přidat cloudové služby na virtuální síť
Dále musíte přidat do nové sítě VNet nasazením cloudové služby. K tomuto účelu upravte přidáním v příslušných částech k vaší cscfg pomocí sady Visual Studio nebo editoru podle své volby cscfg vaše cloudové služby.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

V dalším sestavení projektu cloudové služby a nasazení do Azure. Chcete-li získat nápovědu k nasazení vašeho balíčku cloudové služby Azure, přečtěte si téma [postup vytvoření a nasazení cloudové služby](cloud-services-how-to-create-deploy-portal.md)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Váš web/role pracovního procesu se připojte k doméně
Jakmile se projekt cloudové služby je nasazen na Azure, připojte k vlastní doméně AD pomocí rozšíření domény AD instance role. Pokud chcete přidat rozšíření AD domény do existující nasazení cloudové služby a připojit se k vlastní doméně, spusťte následující příkazy v prostředí PowerShell:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

A je to.

Cloudové služby by měl být připojený k řadiči domény vlastní. Pokud vás zajímají další informace o různých možnostech dostupných pro postup konfigurace rozšíření domény AD, použijte nápovědu prostředí PowerShell. Pár příkladů postupujte podle kroků:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
