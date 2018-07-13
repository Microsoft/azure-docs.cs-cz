---
title: Vytvořte skupinu zabezpečení sítě (classic) pomocí Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit a nasadit skupinu zabezpečení sítě (classic) pomocí Powershellu
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 86810b0d-0240-46a2-8548-fca22daa56f3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: ecb977660ed99a3cea2a71a867f50822b23e568c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38634192"
---
# <a name="create-a-network-security-group-classic-using-powershell"></a>Vytvořte skupinu zabezpečení sítě (classic) pomocí Powershellu
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. Můžete také [vytvořit skupiny Nsg v modelu nasazení Resource Manager](tutorial-filter-network-traffic.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Ukázka jednoduché prostředí už vytvořili očekávat následující příkazy prostředí PowerShell založený na výše uvedeném scénáři. Pokud chcete spustit příkazy, jak jsou zobrazeny v tomto dokumentu, nejprve vytvořit testovací prostředí podle [vytvoření virtuální sítě](virtual-networks-create-vnet-classic-netcfg-ps.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Vytvořte skupinu zabezpečení sítě pro front-endové podsítě

1. Pokud jste prostředí Azure PowerShell nikdy nepoužívali, přečtěte si téma [instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

2. Vytvořte skupinu zabezpečení sítě s názvem *NSG front-endu*:

    ```powershell   
    New-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" -Location uswest `
      -Label "Front end subnet NSG"
   ```

3. Vytvořte pravidlo zabezpečení umožňuje přístup k portu 3389 z Internetu:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '3389'
   ```

4. Vytvořte pravidlo zabezpečení umožňuje přístup na port 80 z Internetu:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name web-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 200 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '80'
    ```

## <a name="create-an-nsg-for-the-back-end-subnet"></a>Vytvořte skupinu zabezpečení sítě pro podsíť back-end

1. Vytvořte skupinu zabezpečení sítě s názvem *NSG back-endu*:
   
    ```powershell
    New-AzureNetworkSecurityGroup -Name "NSG-BackEnd" -Location uswest `
      -Label "Back end subnet NSG"
    ```

2. Vytvořte pravidlo zabezpečení povolení přístupu z podsítě front-endový port 1433 (výchozí port používaný systémem SQL Server):
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix 192.168.1.0/24  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '1433'
    ```

3. Vytvořte pravidlo zabezpečení, blokovat přístup k Internetu z podsítě:
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-BackEnd" `
      | Set-AzureNetworkSecurityRule -Name block-internet `
      -Action Deny -Protocol '*' -Type Outbound -Priority 200 `
      -SourceAddressPrefix '*'  -SourcePortRange '*' `
      -DestinationAddressPrefix Internet -DestinationPortRange '*'
   ```