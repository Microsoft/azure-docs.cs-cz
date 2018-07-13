---
title: Vytvořte skupinu zabezpečení sítě (classic) pomocí rozhraní příkazového řádku Azure CLI 1.0 | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit a nasadit skupinu zabezpečení sítě (classic) pomocí rozhraní příkazového řádku Azure CLI 1.0.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38696614"
---
# <a name="create-a-network-security-group-classic-using-the-azure-cli-10"></a>Vytvořte skupinu zabezpečení sítě (classic) pomocí rozhraní příkazového řádku Azure CLI 1.0
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. Můžete také [vytvořit skupiny Nsg v modelu nasazení Resource Manager](tutorial-filter-network-traffic-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Následující vzorové příkazy Azure CLI můžete očekávat jednoduché prostředí už vytvořili závislosti na scénáři. Pokud chcete spustit příkazy, jak jsou zobrazeny v tomto dokumentu, nejprve vytvořit testovací prostředí podle [vytvoření virtuální sítě](virtual-networks-create-vnet-classic-cli.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Vytvořte skupinu zabezpečení sítě pro front-endové podsítě

1. Pokud jste rozhraní příkazového řádku Azure nikdy nepoužívali, přečtěte si téma [instalace a konfigurace rozhraní příkazového řádku Azure](../cli-install-nodejs.md).
2. Přejděte do režimu classic:

    ```azurecli
    azure config mode asm
    ```   

3. Vytvořte skupinu zabezpečení sítě::
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. Vytvořte pravidlo zabezpečení, které umožňuje přístup na port 3389 (RDP) z Internetu:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. Vytvoření pravidla, která umožňuje přístup na port 80 (HTTP) z Internetu:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. Přidružení NSG k front-endové podsítě:
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>Vytvoření skupiny zabezpečení sítě pro podsíť back-end

1. Vytvoření skupiny zabezpečení sítě:
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. Vytvoření pravidla, která umožňuje přístup k portu 1433 (SQL) z front-endové podsítě:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. Vytvořte pravidlo pro odepření přístupu k Internetu:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. Přidružení NSG k back endové podsítě:
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```