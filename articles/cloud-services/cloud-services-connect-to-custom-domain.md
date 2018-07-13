---
title: Připojte Cloudovou službu k vlastnímu řadiči domény | Dokumentace Microsoftu
description: Zjistěte, jak připojit své webové a pracovní role na vlastní doménu AD pomocí prostředí PowerShell a příponou domény služby AD
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 1e2d7c87-d254-4e7a-a832-67f84411ec95
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: b05e20b5c99c6f1b5b1bf93ca781ec97284fba79
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004911"
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Připojení k řadiči domény AD hostované v Azure vlastní role Azure Cloud Services
Nejprve nastavíme virtuální síť (VNet) v Azure. Potom přidáme Active Directory řadiče domény (hostované na virtuálním počítači Azure) do virtuální sítě. Dále jsme se přidat existující role cloudové služby na předem vytvořené virtuální síti a potom připojit k řadiči domény.

Než začneme, několik věcí, abyste mějte na paměti:

1. Tento kurz používá prostředí PowerShell, proto se ujistěte, že máte nainstalované a připravené k použití prostředí Azure PowerShell. Chcete-li získat nápovědu k nastavení prostředí Azure PowerShell, najdete v článku [instalace a konfigurace Azure Powershellu](/powershell/azure/overview).
2. Řadič domény AD a Web/Worker Role instance musí být ve virtuální síti.

Tento podrobný návod a pokud narazíte na nějaké problémy, napište nám komentář na konci tohoto článku. Někdo bude se vám ozvat (Ano, můžeme načíst komentáře).

Síť, na který odkazuje cloudové služby musí být **klasickou virtuální síť**.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Můžete vytvořit virtuální síť v Azure pomocí webu Azure portal nebo Powershellu. V tomto kurzu se používá PowerShell. Vytvoření virtuální sítě pomocí webu Azure portal najdete v tématu [vytvoření virtuální sítě](../virtual-network/quick-create-portal.md). Tento článek popisuje vytvoření virtuální sítě (Resource Manager), ale musíte vytvořit virtuální síť (Classic) pro cloudové služby. Provedete to tak, že na portálu vyberte **vytvořit prostředek**, typ *virtuální sítě* v **hledání** pole a potom stiskněte klávesu **Enter**. Ve výsledcích hledání podle **všechno, co**vyberte **virtuální síť**. V části **vybrat model nasazení**vyberte **Classic**a pak vyberte **vytvořit**. Můžete pak postupujte podle kroků v článku.

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
Po dokončení nastavení virtuální sítě, je potřeba vytvořit řadič domény AD. Pro účely tohoto kurzu jsme budou nastavovat řadič domény služby AD na virtuálním počítači Azure.

Vytvořte virtuální počítač prostřednictvím prostředí PowerShell pomocí následujících příkazů:

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

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Zvýšení úrovně virtuálního počítače s řadičem domény
Pokud chcete nakonfigurovat virtuální počítač jako řadič domény služby AD, musíte přihlásit k virtuálnímu počítači a nakonfigurujte ho.

Přihlásit se k virtuálnímu počítači, můžete získat soubor RDP pomocí Powershellu, pomocí následujících příkazů:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Po přihlášení k virtuálnímu počítači nastavit virtuální počítač jako řadič domény služby AD podle podrobného průvodce [jak nastavit váš zákazník řadič domény služby AD](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Přidání cloudové služby do virtuální sítě
Dále je třeba přidat vaše nasazení cloudové služby do nové sítě VNet. K tomuto účelu upravte přidáním v příslušných částech pro vaše cscfg pomocí sady Visual Studio nebo editoru podle vašeho výběru cscfg vaší cloudové služby.

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

Vedle sestavit projekt cloudové služby a nasadit ho do Azure. Chcete-li získat nápovědu k nasazování váš balíček cloudové služby na Azure, najdete v článku [jak vytvořit a nasadit Cloudovou službu](cloud-services-how-to-create-deploy-portal.md)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Připojení k doméně webové nebo pracovní role
Po nasazení se projekt cloudové služby v Azure, připojte vaše instance rolí k vlastní doméně AD pomocí rozšíření domény AD. Přidat rozšíření domény AD stávající nasazení cloud services a připojte se k vlastní doméně, spusťte následující příkazy v prostředí PowerShell:

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

A to je všechno.

Cloudové služby mají být připojené k vaší vlastní domény kontroleru. Pokud chcete získat další informace o tom, jak nakonfigurovat rozšíření domény AD možnosti různých, použijte nápovědu prostředí PowerShell. Pár příkladů, postupujte podle:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
