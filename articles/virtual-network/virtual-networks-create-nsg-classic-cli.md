---
title: Vytvoření skupiny zabezpečení sítě (Classic) pomocí rozhraní příkazového řádku Azure Classic
description: Naučte se vytvořit a nasadit skupinu zabezpečení sítě (Classic) pomocí rozhraní příkazového řádku Azure Classic.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: c17e75849d1127c9c1001fd87af64e3ffe0eba14
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196718"
---
# <a name="create-a-network-security-group-classic-using-the-azure-classic-cli"></a>Vytvoření skupiny zabezpečení sítě (Classic) pomocí rozhraní příkazového řádku Azure Classic
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. Můžete také [vytvořit skupin zabezpečení sítě v modelu nasazení Správce prostředků](tutorial-filter-network-traffic-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Následující ukázkové příkazy Azure CLI očekávají v závislosti na scénáři jednoduché prostředí, které už je vytvořené. Pokud chcete spustit příkazy, které se zobrazují v tomto dokumentu, nejdříve Sestavte testovací prostředí [vytvořením virtuální](virtual-networks-create-vnet-classic-cli.md)sítě.

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Vytvoření NSG pro front-end podsíť

1. Pokud jste rozhraní příkazového řádku Azure nikdy nepoužívali, přečtěte si téma [instalace a konfigurace Azure CLI](/cli/azure/install-cli-version-1.0).
2. Přepnout do klasického režimu:

    ```azurecli
    azure config mode asm
    ```   

3. Vytvořit NSG::
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. Vytvořte pravidlo zabezpečení, které umožní přístup k portu 3389 (RDP) z Internetu:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. Vytvořte pravidlo, které umožní přístup k portu 80 (HTTP) z Internetu:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. Přidružte NSG k podsíti front-end:
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>Vytvoření NSG pro back-endové podsítě

1. Vytvořte NSG:
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. Vytvořte pravidlo, které umožní přístup k portu 1433 (SQL) z front-endové podsítě:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. Vytvoření pravidla zakazujícího přístup k Internetu:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. Přidružte NSG k back-endové podsíti:
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```