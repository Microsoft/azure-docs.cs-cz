---
title: Vytvořte skupinu zabezpečení sítě (klasické) pomocí Azure CLI 1.0 | Microsoft Docs
description: Zjistěte, jak vytvořit a nasadit pomocí Azure CLI 1.0 skupina zabezpečení sítě (klasické).
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
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
ms.openlocfilehash: 5468801e56849498d712f51e71cfb31bf068398a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792473"
---
# <a name="create-a-network-security-group-classic-using-the-azure-cli-10"></a>Vytvořit skupinu zabezpečení sítě (klasické) pomocí Azure CLI 1.0
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. Můžete také [vytvářet skupiny Nsg v modelu nasazení Resource Manager](tutorial-filter-network-traffic-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Následující vzorové příkazy příkazového řádku Azure CLI očekávat jednoduché prostředí již vytvořili závislosti na scénáři. Pokud chcete ke spuštění příkazů, jak jsou zobrazeny v tomto dokumentu, nejprve vytvořit testovací prostředí podle [vytvoření virtuální sítě](virtual-networks-create-vnet-classic-cli.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Vytvořit skupinu NSG pro podsíť front-endu

1. Pokud jste rozhraní příkazového řádku Azure nikdy nepoužívali, projděte si téma [instalace a konfigurace rozhraní příkazového řádku Azure](../cli-install-nodejs.md).
2. Přepnout do klasického režimu:

    ```azurecli
    azure config mode asm
    ```   

3. Vytvořit skupinu NSG::
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. Vytvořte pravidlo zabezpečení, která umožňuje přístup k portu 3389 (RDP) z Internetu:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. Vytvoření pravidla, která umožňuje přístup k portu 80 (HTTP) z Internetu:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. Přidružení skupiny NSG k podsíti front-endu:
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>Vytvořit skupinu NSG pro podsíť back-end

1. Vytvoření této skupině:
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. Vytvoření pravidla, která umožňuje přístup k portu 1433 (SQL) z podsítě front-endu:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. Vytvořte pravidlo, které odepře přístup k Internetu:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. Přidružení skupiny NSG k podsíti back-end:
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```