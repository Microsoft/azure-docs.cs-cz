---
title: Připojení cloudové služby (klasické) k vlastnímu řadiči domény | Microsoft Docs
description: Naučte se připojit své webové a pracovní role k vlastní doméně služby AD pomocí prostředí PowerShell a rozšíření domény AD.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 8c2c8377944caa7ad28f6b379531e6d5bf44c9e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742501"
---
# <a name="connecting-azure-cloud-services-classic-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Připojení rolí Azure Cloud Services (Classic) k vlastnímu řadiči domény služby AD hostovanému v Azure

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

Napřed nastavíme Virtual Network (VNet) v Azure. Pak do virtuální sítě přidáte řadič Doména služby Active Directory (hostovaný na virtuálním počítači Azure). V dalším kroku přidáme existující role cloudové služby do předem vytvořené virtuální sítě a pak je připojíte k řadiči domény.

Než začneme, mějte na paměti několik věcí, které je potřeba vzít v úvahu:

1. V tomto kurzu se používá PowerShell, takže se ujistěte, že máte Azure PowerShell nainstalované a připravené k použití. Pokud potřebujete pomoc s nastavením Azure PowerShell, přečtěte si téma [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/).
2. Váš řadič domény služby AD a instance webové služby nebo role pracovního procesu musí být ve virtuální síti.

Postupujte podle tohoto podrobného průvodce a pokud narazíte na nějaké problémy, ponechte nám na konci tohoto článku komentář. Někdo se vrátí zpět (Ano, poznámky pro čtení).

Síť, na kterou se odkazuje přes cloudovou službu, musí být **klasický virtuální síť**.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Virtual Network můžete v Azure vytvořit pomocí Azure Portal nebo PowerShellu. V tomto kurzu se používá PowerShell. Pokud chcete vytvořit virtuální síť pomocí Azure Portal, přečtěte si téma [vytvoření virtuální sítě](../virtual-network/quick-create-portal.md). Tento článek se zabývá vytvořením virtuální sítě (Správce prostředků), ale musíte pro Cloud Services vytvořit virtuální síť (Classic). Provedete to tak, že na portálu vyberete **vytvořit prostředek**, do **vyhledávacího** pole zadáte *virtuální síť* a pak stisknete **ENTER**. Ve výsledcích hledání vyberte v části **vše** možnost **virtuální síť**. V části **Vybrat model nasazení** vyberte **Classic** a pak vyberte **vytvořit**. Pak můžete postupovat podle kroků v článku.

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

## <a name="create-a-virtual-machine"></a>Vytvořit virtuální počítač
Po dokončení nastavení Virtual Network budete muset vytvořit řadič domény služby AD. V tomto kurzu budeme na virtuálním počítači Azure nastavovat řadič domény služby AD.

Provedete to tak, že vytvoříte virtuální počítač přes PowerShell pomocí následujících příkazů:

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

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Zvýšení úrovně virtuálního počítače na řadič domény
Pokud chcete virtuální počítač nakonfigurovat jako řadič domény služby AD, musíte se přihlásit k VIRTUÁLNÍmu počítači a nakonfigurovat ho.

Pokud se chcete přihlásit k virtuálnímu počítači, můžete získat soubor RDP prostřednictvím PowerShellu, a to pomocí následujících příkazů:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Jakmile se přihlásíte k virtuálnímu počítači, nastavte svůj virtuální počítač jako řadič domény služby AD pomocí podrobného průvodce pro [nastavení řadiče domény služby AD DS](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Přidání cloudové služby do Virtual Network
Dál je potřeba přidat nasazení cloudové služby do nové virtuální sítě. Provedete to tak, že v aplikaci Visual Studio přidáte relevantní oddíly s použitím sady Visual Studio nebo editoru podle vašeho výběru.

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

Dále Sestavte projekt cloudových služeb a nasaďte ho do Azure. Pokud chcete získat pomoc s nasazením balíčku Cloud Services do Azure, přečtěte si téma [Vytvoření a nasazení cloudové služby](cloud-services-how-to-create-deploy-portal.md) .

## <a name="connect-your-webworker-roles-to-the-domain"></a>Připojte své webové a pracovní role k doméně.
Po nasazení projektu cloudové služby v Azure připojte instance rolí k vlastní doméně služby AD pomocí rozšíření domény služby AD. Pokud chcete přidat rozšíření domény AD do stávajícího nasazení Cloud Services a připojit se k vlastní doméně, spusťte v PowerShellu následující příkazy:

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

Vaše cloudové služby by měly být připojené k vlastnímu řadiči domény. Pokud chcete získat další informace o různých možnostech, které jsou k dispozici pro konfiguraci rozšíření domény služby AD, použijte nápovědu prostředí PowerShell. Následuje několik příkladů:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```



