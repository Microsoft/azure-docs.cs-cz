---
title: Připojení cloudové služby k vlastnímu řadiči domény | Dokumenty společnosti Microsoft
description: Zjistěte, jak připojit role webu a pracovního procesu k vlastní doméně služby AD pomocí powershellu a rozšíření domény služby AD
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: d40e392984d2675c748bda00c61cdaeb1c0932da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75387016"
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Připojení rolí Cloudových služeb Azure k vlastnímu řadiči domény služby AD hostovanému v Azure
Nejdřív v Azure nastavíme virtuální síť (Virtuální síť). Potom přidáme řadič domény služby Active Directory (hostovaný na virtuálním počítači Azure) do virtuální sítě. Dále přidáme existující role cloudové služby do předem vytvořené virtuální sítě a pak je připojíme k řadiči domény.

Než začneme, je třeba mít na paměti několik věcí:

1. Tento kurz používá PowerShell, takže se ujistěte, že máte nainstalovaný a připravený Azure PowerShell. Pokud chcete získat pomoc s nastavením Azure PowerShellu, přečtěte [si, jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).
2. Instance řadiče domény služby AD a role webu/pracovního procesu musí být ve virtuální síti.

Postupujte podle tohoto průvodce krok za krokem a pokud narazíte na nějaké problémy, zanechte nám komentář na konci článku. Někdo se vám ozve (ano, čteme komentáře).

Síť, na kterou odkazuje cloudová služba, musí být **klasická virtuální síť**.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Virtuální síť v Azure můžete vytvořit pomocí portálu Azure nebo PowerShellu. Pro účely tohoto kurzu se používá PowerShell. Pokud chcete vytvořit virtuální síť pomocí portálu Azure, [přečtěte si, že najdete v tématu Vytvoření virtuální sítě](../virtual-network/quick-create-portal.md). Článek popisuje vytvoření virtuální sítě (Resource Manager), ale je nutné vytvořit virtuální síť (Classic) pro cloudové služby. Na portálu vyberte **Vytvořit prostředek**, do pole **Hledat** zadejte *virtuální síť* a stiskněte **Enter**. Ve výsledcích hledání vyberte v části **Vše** **možnost Virtuální síť**. V části **Select a deployment model**vyberte **Classic**a pak vyberte **Create**. Potom můžete postupovat podle kroků v článku.

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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
Po dokončení nastavení virtuální sítě bude nutné vytvořit řadič domény služby AD. V tomto kurzu nastavíme řadič domény služby AD ve virtuálním počítači Azure.

Chcete-li to provést, vytvořte virtuální počítač prostřednictvím prostředí PowerShell pomocí následujících příkazů:

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

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Povýšení virtuálního počítače na řadič domény
Chcete-li nakonfigurovat virtuální počítač jako řadič domény služby AD, budete se muset přihlásit k virtuálnímu počítači a nakonfigurovat ho.

Chcete-li se přihlásit k virtuálnímu virtuálnímu virtuálnímu serveru, můžete získat soubor RDP prostřednictvím prostředí PowerShell, použijte následující příkazy:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Jakmile jste přihlášeni k virtuálnímu počítači, nastavte virtuální počítač jako řadič domény služby AD podle podrobného průvodce o nastavení [řadiče domény služby AD zákazníka](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Přidání cloudové služby do virtuální sítě
Dále je potřeba přidat nasazení cloudové služby do nové virtuální sítě. Chcete-li to provést, upravte cloudovou službu cscfg přidáním příslušných oddílů do cscfg pomocí sady Visual Studio nebo editoru podle vašeho výběru.

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

Dále sestavte projekt cloudových služeb a nasaďte ho do Azure. Pokud chcete získat pomoc s nasazením balíčku cloudových služeb do Azure, přečtěte si informace o tom, [jak vytvořit a nasadit cloudovou službu.](cloud-services-how-to-create-deploy-portal.md)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Připojení webových/pracovních rolí k doméně
Po nasazení projektu cloudové služby v Azure připojte instance rolí k vlastní doméně služby AD pomocí rozšíření domény služby AD. Chcete-li přidat rozšíření domény služby AD do stávajícího nasazení cloudových služeb a připojit se k vlastní doméně, proveďte v prostředí PowerShell následující příkazy:

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

A je to hotovo.

Cloudové služby by měly být připojeny k vlastnímu řadiči domény. Pokud se chcete dozvědět více o různých možnostech, které jsou k dispozici pro konfiguraci rozšíření domény služby AD, použijte nápovědu k prostředí PowerShell. Několik příkladů následovat:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```



