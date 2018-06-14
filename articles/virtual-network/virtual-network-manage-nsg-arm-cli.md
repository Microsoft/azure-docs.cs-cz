---
title: Správa skupin zabezpečení sítě - rozhraní příkazového řádku Azure | Microsoft Docs
description: Naučte se spravovat skupiny zabezpečení sítě pomocí rozhraní příkazového řádku Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ed17d314-07e6-4c7f-bcf1-a8a2535d7c14
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee43bfd85bdda16cb496ec938c266c72f0d5688b
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2018
ms.locfileid: "30959755"
---
# <a name="manage-network-security-groups-using-the-azure-cli"></a>Správa skupin zabezpečení sítě pomocí rozhraní příkazového řádku Azure

[!INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Resource Manager, které společnost Microsoft doporučuje pro většinu nových nasazení místo modelu nasazení classic.
> 

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="prerequisite"></a>Požadavek
Pokud nebyly dosud, nainstalovat a nakonfigurovat nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) a přihlaste se k Azure účet pomocí [az přihlášení](/cli/azure/reference-index#az_login). 

## <a name="view-existing-nsgs"></a>Zobrazit existující skupiny Nsg
Chcete-li zobrazit seznam skupin Nsg v určité skupiny zdrojů, spusťte [seznam nsg sítě az](/cli/azure/network/nsg#az_network_nsg_list) s `-o table` výstupní formát:

```azurecli
az network nsg list -g RG-NSG -o table
```

Očekávaný výstup:

    Location    Name          ProvisioningState    ResourceGroup    ResourceGuid
    ----------  ------------  -------------------  ---------------  ------------------------------------
    centralus   NSG-BackEnd   Succeeded            RG-NSG           <guid>
    centralus   NSG-FrontEnd  Succeeded            RG-NSG           <guid>

## <a name="list-all-rules-for-an-nsg"></a>Seznam všech pravidel pro skupiny NSG
Chcete-li zobrazit pravidla s názvem skupiny NSG **NSG front-endu**spusťte [az sítě nsg zobrazit](/cli/azure/network/nsg#az_network_nsg_show) příkaz pomocí [JMESPATH filtr dotazu](/cli/azure/query-az-cli2) a `-o table` výstupní formát:

```azurecli
    az network nsg show \
    --resource-group RG-NSG \
    --name NSG-FrontEnd \
    --query '[defaultSecurityRules[],securityRules[]][].{Name:name,Desc:description,Access:access,Direction:direction,DestPortRange:destinationPortRange,DestAddrPrefix:destinationAddressPrefix,SrcPortRange:sourcePortRange,SrcAddrPrefix:sourceAddressPrefix}' \
    -o table
```

Očekávaný výstup:

    Name                           Desc                                                    Access    Direction    DestPortRange    DestAddrPrefix    SrcPortRange    SrcAddrPrefix
    -----------------------------  ------------------------------------------------------  --------  -----------  ---------------  ----------------  --------------  -----------------
    AllowVnetInBound               Allow inbound traffic from all VMs in VNET              Allow     Inbound      *                VirtualNetwork    *               VirtualNetwork
    AllowAzureLoadBalancerInBound  Allow inbound traffic from azure load balancer          Allow     Inbound      *                *                 *               AzureLoadBalancer
    DenyAllInBound                 Deny all inbound traffic                                Deny      Inbound      *                *                 *               *
    AllowVnetOutBound              Allow outbound traffic from all VMs to all VMs in VNET  Allow     Outbound     *                VirtualNetwork    *               VirtualNetwork
    AllowInternetOutBound          Allow outbound traffic from all VMs to Internet         Allow     Outbound     *                Internet          *               *
    DenyAllOutBound                Deny all outbound traffic                               Deny      Outbound     *                *                 *               *
    rdp-rule                                                                               Allow     Inbound      3389             *                 *               Internet
    web-rule                                                                               Allow     Inbound      80               *                 *               Internet
> [!NOTE]
> Můžete také použít [seznam pravidel nsg sítě az](/cli/azure/network/nsg/rule#az_network_nsg_rule_list) k zobrazení seznamu pouze vlastní pravidla ze skupiny NSG.
>

## <a name="view-nsg-associations"></a>Zobrazit přidružení skupiny NSG

Chcete-li zobrazit prostředky **NSG front-endu** NSG je spojený s spustit `az network nsg show` příkaz: 

```azurecli
az network nsg show -g RG-NSG -n nsg-frontend --query '[subnets,networkInterfaces]'
```

Vyhledejte **networkInterfaces** a **podsítě** vlastnosti, jak ukazuje následující příklad výstupu:

```json
[
  [
    {
      "addressPrefix": null,
      "etag": null,
      "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/FrontEnd",
      "ipConfigurations": null,
      "name": null,
      "networkSecurityGroup": null,
      "provisioningState": null,
      "resourceGroup": "RG-NSG",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  null
]
```

V předchozím příkladu NSG není přidružen k žádné síťových rozhraní (NIC) a je přidružen k podsíti s názvem **front-endu**.

## <a name="add-a-rule"></a>Přidání pravidla
Chcete-li přidat pravidlo, které povoluje **příchozí** přenosy na portu **443** z libovolného počítače k **NSG front-endu** NSG, zadejte následující příkaz:

```azurecli
az network nsg rule create  \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd  \
--name allow-https \
--description "Allow access to port 443 for HTTPS" \
--access Allow \
--protocol Tcp  \
--direction Inbound \
--priority 102 \
--source-address-prefix "*"  \
--source-port-range "*"  \
--destination-address-prefix "*" \
--destination-port-range "443"
```

Očekávaný výstup:

```json
{
  "access": "Allow",
  "description": "Allow access to port 443 for HTTPS",
  "destinationAddressPrefix": "*",
  "destinationPortRange": "443",
  "direction": "Inbound",
  "etag": "W/\"<guid>\"",
  "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https",
  "name": "allow-https",
  "priority": 102,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "RG-NSG",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

## <a name="change-a-rule"></a>Změna pravidla
Změnit pravidlo předtím vytvořili, a povolit příchozí přenosy z **Internet** pouze, spusťte [aktualizace pravidla nsg sítě az](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) příkaz:

```azurecli
az network nsg rule update \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd \
--name allow-https \
--source-address-prefix Internet
```

Očekávaný výstup:

```json
{
"access": "Allow",
"description": "Allow access to port 443 for HTTPS",
"destinationAddressPrefix": "*",
"destinationPortRange": "443",
"direction": "Inbound",
"etag": "W/\"<guid>\"",
"id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https",
"name": "allow-https",
"priority": 102,
"protocol": "Tcp",
"provisioningState": "Succeeded",
"resourceGroup": "RG-NSG",
"sourceAddressPrefix": "Internet",
"sourcePortRange": "*"
}
```

## <a name="delete-a-rule"></a>Odstranění pravidla
Pokud chcete odstranit pravidlo vytvořili výše, spusťte následující příkaz:

```azurecli
az network nsg rule delete \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd \
--name allow-https
```


## <a name="associate-an-nsg-to-a-nic"></a>Přidružení skupiny NSG k síťové karty
Pro přidružení **NSG front-endu** NSG k **TestNICWeb1** síťového adaptéru, použijte [aktualizace seskupování sítě az](/cli/azure/network/nic#az_network_nic_update) příkaz:

```azurecli
az network nic update \
--resource-group RG-NSG \
--name TestNICWeb1 \
--network-security-group NSG-FrontEnd    
```

Očekávaný výstup:

```json
{
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": [],
    "internalDnsNameLabel": null,
    "internalDomainNameSuffix": "k0wkaguidnqrh0ud.gx.internal.cloudapp.net",
    "internalFqdn": null
  },
  "enableAcceleratedNetworking": false,
  "enableIpForwarding": false,
  "etag": "W/\"<guid>\"",
  "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1",
  "ipConfigurations": [
    {
      "applicationGatewayBackendAddressPools": null,
      "etag": "W/\"<guid>\"",
      "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1",
      "loadBalancerBackendAddressPools": null,
      "loadBalancerInboundNatRules": null,
      "name": "ipconfig1",
      "primary": true,
      "privateIpAddress": "192.168.1.6",
      "privateIpAddressVersion": "IPv4",
      "privateIpAllocationMethod": "Static",
      "provisioningState": "Succeeded",
      "publicIpAddress": null,
      "resourceGroup": "RG-NSG",
      "subnet": {
        "addressPrefix": null,
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
        "ipConfigurations": null,
        "name": null,
        "networkSecurityGroup": null,
        "provisioningState": null,
        "resourceGroup": "RG-NSG",
        "resourceNavigationLinks": null,
        "routeTable": null
      }
    }
  ],
  "location": "centralus",
  "macAddress": "00-0D-3A-91-A9-60",
  "name": "TestNICWeb1",
  "networkSecurityGroup": {
    "defaultSecurityRules": null,
    "etag": null,
    "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
    "location": null,
    "name": null,
    "networkInterfaces": null,
    "provisioningState": null,
    "resourceGroup": "RG-NSG",
    "resourceGuid": null,
    "securityRules": null,
    "subnets": null,
    "tags": null,
    "type": null
  },
  "primary": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "RG-NSG",
  "resourceGuid": "<guid>",
  "tags": {},
  "type": "Microsoft.Network/networkInterfaces",
  "virtualMachine": null
}
```

## <a name="dissociate-an-nsg-from-a-nic"></a>Zrušit přidružení skupiny NSG z síťový adaptér

Zrušení přidružení **NSG front-endu** NSG z **TestNICWeb1** síťového adaptéru, spusťte [aktualizace pravidla nsg sítě az](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) příkaz znovu, ale nahraďte `--network-security-group` argument prázdný řetězec (`""`).

```azurecli
az network nic update --resource-group RG-NSG --name TestNICWeb3 --network-security-group ""
```

Ve výstupu `networkSecurityGroup` klíč je nastaven na hodnotu null.

## <a name="dissociate-an-nsg-from-a-subnet"></a>Zrušit přidružení skupiny NSG z podsítě
Zrušení přidružení **NSG front-endu** NSG z **front-endu** podsíť, znovu spustit [aktualizace pravidla nsg sítě az](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) příkaz znovu, ale nahraďte `--network-security-group` argument prázdný řetězec (`""`).

```azurecli
az network vnet subnet update \
--resource-group RG-NSG \
--vnet-name testvnet \
--name FrontEnd \
--network-security-group ""
```

Ve výstupu `networkSecurityGroup` klíč je nastaven na hodnotu null.

## <a name="associate-an-nsg-to-a-subnet"></a>Přidružení skupiny NSG k podsíti
Pro přidružení **NSG front-endu** NSG k **front-endu** podsíť znovu, spusťte následující příkaz:

```azurecli
az network vnet subnet update \
--resource-group RG-NSG \
--vnet-name testvnet \
--name FrontEnd \
--network-security-group NSG-FrontEnd
```

Ve výstupu `networkSecurityGroup` klíč má podobný pro hodnotu:

```json
"networkSecurityGroup": {
    "defaultSecurityRules": null,
    "etag": null,
    "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
    "location": null,
    "name": null,
    "networkInterfaces": null,
    "provisioningState": null,
    "resourceGroup": "RG-NSG",
    "resourceGuid": null,
    "securityRules": null,
    "subnets": null,
    "tags": null,
    "type": null
  }
  ```

## <a name="delete-an-nsg"></a>Odstranit skupinu NSG
Skupinu NSG můžete odstranit, pouze pokud má není přidružen k žádnému prostředku. Pokud chcete odstranit skupinu NSG, proveďte následující kroky:

1. Chcete-li zkontrolovat prostředky přidružené k skupinu NSG, spusťte `azure network nsg show` jak je znázorněno v [přidružení skupiny Nsg zobrazení](#View-NSGs-associations).
2. Pokud skupina NSG je přidružen k žádné síťové adaptéry, spusťte `azure network nic set` jak je znázorněno v [zrušit přidružení skupiny NSG z síťový adaptér](#Dissociate-an-NSG-from-a-NIC) pro každý síťový adaptér. 
3. Pokud je přidružen k žádné podsíti NSG, spusťte `azure network vnet subnet set` jak je znázorněno v [zrušit přidružení skupiny NSG z podsítě](#Dissociate-an-NSG-from-a-subnet) pro každou podsíť.
4. Pokud chcete odstranit NSG, spusťte následující příkaz:

    ```azurecli
    az network nsg delete --resource-group RG-NSG --name NSG-FrontEnd
    ```
## <a name="next-steps"></a>Další postup
* [Povolit protokolování](virtual-network-nsg-manage-log.md) pro skupiny Nsg.

