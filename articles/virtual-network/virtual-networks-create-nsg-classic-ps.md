---
title: Vytvořte skupinu zabezpečení sítě (klasické) pomocí prostředí PowerShell | Microsoft Docs
description: Zjistěte, jak vytvořit a nasadit skupinu zabezpečení sítě (klasické) pomocí prostředí PowerShell
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 86810b0d-0240-46a2-8548-fca22daa56f3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.openlocfilehash: dece453e0ac6d4a8d31accaeb2403f1acdb1266f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-network-security-group-classic-using-powershell"></a>Vytvořit skupinu zabezpečení sítě (klasické) pomocí prostředí PowerShell
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. Můžete také [vytvářet skupiny Nsg v modelu nasazení Resource Manager](tutorial-filter-network-traffic.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Ukázka jednoduché prostředí už vytvořený očekávat níže uvedené příkazy prostředí PowerShell založené na výše uvedené scénáře. Pokud chcete ke spuštění příkazů, jak jsou zobrazeny v tomto dokumentu, nejprve vytvořit testovací prostředí podle [vytvoření virtuální sítě](virtual-networks-create-vnet-classic-netcfg-ps.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Vytvořit skupinu NSG pro podsíť front-endu

1. Pokud jste prostředí Azure PowerShell nikdy nepoužívali, projděte si téma [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

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

4. Vytvořte pravidlo zabezpečení povolení přístupu z Internetu na port 80:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name web-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 200 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '80'
    ```

## <a name="create-an-nsg-for-the-back-end-subnet"></a>Vytvořit skupinu NSG pro podsíť back-end

1. Vytvořte skupinu zabezpečení sítě s názvem *NSG back-end*:
   
    ```powershell
    New-AzureNetworkSecurityGroup -Name "NSG-BackEnd" -Location uswest `
      -Label "Back end subnet NSG"
    ```

2. Vytvořte pravidlo zabezpečení povolení přístupu z podsítě front-endu port 1433 (SQL Server používá výchozí port):
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix 192.168.1.0/24  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '1433'
    ```

3. Vytvořte pravidlo zabezpečení blokuje přístup z podsítě k Internetu:
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-BackEnd" `
      | Set-AzureNetworkSecurityRule -Name block-internet `
      -Action Deny -Protocol '*' -Type Outbound -Priority 200 `
      -SourceAddressPrefix '*'  -SourcePortRange '*' `
      -DestinationAddressPrefix Internet -DestinationPortRange '*'
   ```