---
title: Vytvoření skupiny zabezpečení sítě (klasické) pomocí prostředí PowerShell | Microsoft Docs
description: Naučte se vytvořit a nasadit skupinu zabezpečení sítě (Classic) pomocí prostředí PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 86810b0d-0240-46a2-8548-fca22daa56f3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 2/02/2019
ms.author: genli
ms.openlocfilehash: 0f957c5d59dec06057841a95ec48a54462778a69
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672482"
---
# <a name="create-a-network-security-group-classic-using-powershell"></a>Vytvoření skupiny zabezpečení sítě (Classic) pomocí prostředí PowerShell
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. Můžete také [vytvořit skupin zabezpečení sítě v modelu nasazení Správce prostředků](tutorial-filter-network-traffic.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Následující ukázkové příkazy prostředí PowerShell očekávají jednoduché prostředí již vytvořené v závislosti na výše uvedeném scénáři. Pokud chcete spustit příkazy, které se zobrazují v tomto dokumentu, nejdříve Sestavte testovací prostředí [vytvořením virtuální](virtual-networks-create-vnet-classic-netcfg-ps.md)sítě.

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Vytvoření NSG pro front-end podsíť

1. Pokud jste Azure PowerShell nikdy nepoužili, přečtěte si téma [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).

2. Vytvořte skupinu zabezpečení sítě s názvem *NSG-front-end*:

    ```powershell   
    New-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" -Location uswest `
      -Label "Front end subnet NSG"
   ```

3. Vytvořte pravidlo zabezpečení umožňující přístup z Internetu k portu 3389:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '3389'
   ```

4. Vytvořte pravidlo zabezpečení umožňující přístup z Internetu k portu 80:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name web-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 200 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '80'
    ```
5. Přidružte skupinu zabezpečení sítě k podsíti:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-Frontend" `
    | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName "TestVNet" `
    -Subnet "FrontEnd"
    ```
## <a name="create-an-nsg-for-the-back-end-subnet"></a>Vytvoření NSG pro back-endové podsítě

1. Vytvořte skupinu zabezpečení sítě s názvem *NSG-back-end*:
   
    ```powershell
    New-AzureNetworkSecurityGroup -Name "NSG-BackEnd" -Location uswest `
      -Label "Back end subnet NSG"
    ```

2. Vytvořit pravidlo zabezpečení umožňující přístup z front-endové podsítě na port 1433 (výchozí port používaný SQL Server):
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix 192.168.1.0/24  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '1433'
    ```

3. Vytvořte pravidlo zabezpečení blokující přístup z podsítě k Internetu:
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-BackEnd" `
      | Set-AzureNetworkSecurityRule -Name block-internet `
      -Action Deny -Protocol '*' -Type Outbound -Priority 200 `
      -SourceAddressPrefix '*'  -SourcePortRange '*' `
      -DestinationAddressPrefix Internet -DestinationPortRange '*'
   ```
4. Přidružte skupinu zabezpečení sítě k podsíti:
    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-Backend" `
    | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName "TestVNet" `
    -Subnet "BackEnd"
    ```