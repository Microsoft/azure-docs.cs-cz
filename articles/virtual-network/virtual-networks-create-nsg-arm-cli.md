---
title: "Vytvoření skupin zabezpečení sítě - rozhraní příkazového řádku Azure | Microsoft Docs"
description: "Zjistěte, jak vytvořit a nasadit skupin zabezpečení sítě pomocí rozhraní příkazového řádku Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 9ea82c09-f4a6-4268-88bc-fc439db40c48
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/17/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4827fabf1d8cde366dda8b3a782a2fefe01db8d5
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="create-network-security-groups-using-the-azure-cli"></a>Vytvořit síť pomocí rozhraní příkazového řádku Azure skupin zabezpečení

[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Následující vzorové příkazy příkazového řádku Azure CLI očekávají, že existující jednoduché prostředí založené na předchozím scénáři. 

## <a name="create-the-nsg-for-the-frontend-subnet"></a>Skupina NSG pro vytvoření `FrontEnd` podsítě

Chcete-li vytvořit skupinu NSG s názvem *NSG front-endu* založená na předchozím scénáři, postupujte podle následujících kroků.

1. Pokud nebyly dosud, nainstalovat a nakonfigurovat nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) a přihlaste se k Azure účet pomocí [az přihlášení](/cli/azure/reference-index#az_login). 

2. Vytvoření pomocí NSG [vytvořit az sítě nsg](/cli/azure/network/nsg#az_network_nsg_create) příkaz. 

    ```azurecli
    az network nsg create \
    --resource-group testrg \
    --name NSG-FrontEnd \
    --location centralus 
    ```

    Parametry:
   
   * `--resource-group`: Název skupiny prostředků, kde se má vytvořit NSG. V našem scénáři je to *TestRG*.
   * `--location`: Oblast azure, kde se má vytvořit nová skupina NSG. Pro náš scénář *westus*.
   * `--name`: Název nové skupiny NSG. Pro náš scénář *NSG front-endu*.

    Očekávaný výstup je poměrně bit informací, včetně seznamu všechna výchozí pravidla. Následující příklad ukazuje, výchozí pravidla pomocí filtru dotazu JMESPATH s `table` výstupní formát:

    ```azurecli
    az network nsg show \
    -g testrg \
    -n nsg-frontend \
    --query 'defaultSecurityRules[].{Access:access,Desc:description,DestPortRange:destinationPortRange,Direction:direction,Priority:priority}' \
    -o table
    ```
   
   Výstup:

        Access    Desc                                                    DestPortRange    Direction      Priority
        
        Allow     Allow inbound traffic from all VMs in VNET              *                Inbound           65000
        Allow     Allow inbound traffic from azure load balancer          *                Inbound           65001
        Deny      Deny all inbound traffic                                *                Inbound           65500
        Allow     Allow outbound traffic from all VMs to all VMs in VNET  *                Outbound          65000
        Allow     Allow outbound traffic from all VMs to Internet         *                Outbound          65001
        Deny      Deny all outbound traffic                               *                Outbound          65500



3. Vytvořte pravidlo, které umožňuje přístup k portu 3389 (RDP) z Internetu se [vytvořit pravidla nsg sítě az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) příkaz.

    > [!NOTE]
    > V závislosti na prostředí, kterou používáte, možná budete muset změnit `*` znak v argumentech následující tak, aby rozbalte argument před spuštěním.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-FrontEnd \
    --name rdp-rule \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 100 \
    --source-address-prefix Internet \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 3389
    ```
   
    Očekávaný výstup:
   
    ```json
    {
        "access": "Allow",
        "description": null,
        "destinationAddressPrefix": "*",
        "destinationPortRange": "3389",
        "direction": "Inbound",
        "etag": "W/\"<guid>\"",
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule",
        "name": "rdp-rule",
        "priority": 100,
        "protocol": "Tcp",
        "provisioningState": "Succeeded",
        "resourceGroup": "testrg",
        "sourceAddressPrefix": "Internet",
        "sourcePortRange": "*"
    }
    ```

    Parametry:

    * `--resource-group testrg`Použití: skupinu prostředků. Všimněte si, že se jedná o velká a malá písmena.
    * `--nsg-name NSG-FrontEnd`: Název NSG, ve kterém se vytvoří pravidlo.
    * `--name rdp-rule`: Název pro nové pravidlo.
    * `--access Allow`: Úroveň přístupu pro pravidlo (zakázat nebo povolit).
    * `--protocol Tcp`: Protokol (Tcp, Udp nebo *).
    * `--direction Inbound`: Směr připojení (příchozí nebo odchozí).
    * `--priority 100`: Prioritu pro pravidlo.
    * `--source-address-prefix Internet`: Předpona zdrojové adresy v CIDR nebo pomocí výchozí značky.
    * `--source-port-range "*"`: Zdrojové portu nebo rozsah portů. Port, který otevírá připojení.
    * `--destination-address-prefix "*"`: Předpona cílové adresy v CIDR nebo pomocí výchozí značky.
    * `--destination-port-range 3389`: Cílový port nebo rozsah portů. Port, který obdrží požadavek na připojení.



4. Vytvoření pravidla, která umožňuje přístup k portu 80 (HTTP) z Internetu **vytvořit pravidla nsg sítě az** příkaz.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-FrontEnd \
    --name web-rule \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 200 \
    --source-address-prefix Internet \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 80
    ```
   
    Očekávaný putput:
   
    ```json
    {
        "access": "Allow",
        "description": null,
        "destinationAddressPrefix": "*",
        "destinationPortRange": "80",
        "direction": "Inbound",
        "etag": "W/\"<guid>\"",
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule",
        "name": "web-rule",
        "priority": 200,
        "protocol": "Tcp",
        "provisioningState": "Succeeded",
        "resourceGroup": "testrg",
        "sourceAddressPrefix": "Internet",
        "sourcePortRange": "*"
    }
    ```

5. Skupiny NSG k vytvoření vazby **front-endu** podsíť s [aktualizace az sítě vnet podsíť](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) příkaz.
        
    ```azurecli
    az network vnet subnet update \
    --vnet-name TestVNET \
    --name FrontEnd \
    --resource-group testrg \
    --network-security-group NSG-FrontEnd
    ```
   
    Očekávaný výstup:
   
    ```json
    {
        "addressPrefix": "192.168.1.0/24",
        "etag": "W/\"<guid>\"",
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/FrontEnd",
        "ipConfigurations": [
            {
            "etag": null,
            "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
            "name": null,
            "privateIpAddress": null,
            "privateIpAllocationMethod": null,
            "provisioningState": null,
            "publicIpAddress": null,
            "resourceGroup": "TestRG",
            "subnet": null
            }
        ],
        "name": "FrontEnd",
        "networkSecurityGroup": {
            "defaultSecurityRules": null,
            "etag": null,
            "id": "/subscriptions/<guid>f/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
            "location": null,
            "name": null,
            "networkInterfaces": null,
            "provisioningState": null,
            "resourceGroup": "testrg",
            "resourceGuid": null,
            "securityRules": null,
            "subnets": null,
            "tags": null,
            "type": null
        },
        "provisioningState": "Succeeded",
        "resourceGroup": "testrg",
        "resourceNavigationLinks": null,
        "routeTable": null
    }
    ```

## <a name="create-the-nsg-for-the-backend-subnet"></a>Skupina NSG pro vytvoření `BackEnd` podsítě
Chcete-li vytvořit skupinu NSG s názvem *NSG back-end* založená na předchozím scénáři, postupujte podle následujících kroků.

1. Vytvořte `NSG-BackEnd` NSG s **vytvořit az sítě nsg**.
   
    ```azurecli
    az network nsg create \
    --resource-group testrg \
    --name NSG-BackEnd \
    --location centralus
    ```
   
    Jako v kroku 2, předchozí je očekávaný výstup poměrně rozsáhlé, včetně výchozí pravidla.
   
2. Vytvoření pravidla, která umožňuje přístup k portu 1433 (SQL) z `FrontEnd` podsíť s **vytvořit pravidla nsg sítě az** příkaz.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-BackEnd \
    --name sql-rule \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 100 \
    --source-address-prefix 192.168.1.0/24 \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 1433
    ```
   
    Očekávaný výstup:

    ```json  
    {
    "access": "Allow",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "1433",
    "direction": "Inbound",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/sql-rule",
    "name": "sql-rule",
    "priority": 100,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "sourceAddressPrefix": "192.168.1.0/24",
    "sourcePortRange": "*"
    }
    ```

3. Vytvořte pravidlo, které odepře přístup k Internetu pomocí **vytvořit pravidla nsg sítě az** příkaz.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-BackEnd \
    --name web-rule \
    --access Deny \
    --protocol Tcp  \
    --direction Outbound  \
    --priority 200 \
    --source-address-prefix "*" \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range "*"
    ```
   
    Očekávaný putput:
   
    ```json
    {
    "access": "Deny",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "*",
    "direction": "Outbound",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/web-rule",
    "name": "web-rule",
    "priority": 200,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "sourceAddressPrefix": "*",
    "sourcePortRange": "*"
    }
    ```

4. Skupiny NSG k vytvoření vazby `BackEnd` podsíť pomocí **az sítě vnet podsíť sadu** příkaz.
   
    ```azurecli
    az network vnet subnet update \
    --vnet-name TestVNET \
    --name BackEnd \
    --resource-group testrg \
    --network-security-group NSG-BackEnd
    ```
   
    Očekávaný výstup:
   
    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/BackEnd",
    "ipConfigurations": null,
    "name": "BackEnd",
    "networkSecurityGroup": {
        "defaultSecurityRules": null,
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd",
        "location": null,
        "name": null,
        "networkInterfaces": null,
        "provisioningState": null,
        "resourceGroup": "testrg",
        "resourceGuid": null,
        "securityRules": null,
        "subnets": null,
        "tags": null,
        "type": null
    },
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "resourceNavigationLinks": null,
    "routeTable": null
    }
    ```
