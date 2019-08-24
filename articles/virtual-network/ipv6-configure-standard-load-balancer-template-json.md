---
title: Nasazení aplikace s duálním zásobníkem IPv6 ve službě Azure Virtual Network – Šablona správce prostředků (Preview)
titlesuffix: Azure Virtual Network
description: V tomto článku se dozvíte, jak nasadit aplikaci s duálním zásobníkem IPv6 pomocí Standard Load Balancer ve službě Azure Virtual Network pomocí Azure Resource Manager šablon virtuálních počítačů.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 8f2c6bc7fb7ab0939da20932fd531c158549ce7a
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012853"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---template-preview"></a>Nasazení aplikace s duálním zásobníkem IPv6 ve službě Azure Virtual Network – šablona (Preview)

Tento článek poskytuje seznam úloh konfigurace protokolu IPv6 s částí Azure Resource Manager šablony virtuálního počítače, která se vztahuje na. Pomocí šablony popsané v tomto článku můžete nasadit aplikaci duálního zásobníku (IPv4 + IPv6) pomocí Standard Load Balancer v Azure, která zahrnuje virtuální síť s duálním zásobníkem s podsítěmi IPv4 a IPv6, Standard Load Balancer s front-end s duálním rozhraním (IPv4 + IPv6). konfigurace, virtuální počítače se síťovými kartami, které mají konfiguraci s duální IP adresou, skupinu zabezpečení sítě a veřejné IP adresy. 

## <a name="required-configurations"></a>Požadované konfigurace

V šabloně vyhledejte části šablon, kde se zobrazí tam, kde by se měly vyskytovat.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>AddressSpace IPv6 pro virtuální síť

Oddíl šablony, který se má přidat:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>Podsíť IPv6 v rámci virtuální sítě IPv6 addressSpace

Oddíl šablony, který se má přidat:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>Konfigurace protokolu IPv6 pro síťovou kartu

Oddíl šablony, který se má přidat:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>Pravidla skupiny zabezpečení sítě IPv6 (NSG)

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>Podmíněná konfigurace

Pokud používáte síťové virtuální zařízení, přidejte trasy IPv6 do směrovací tabulky. V opačném případě je tato konfigurace volitelná.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>Volitelná konfigurace

### <a name="ipv6-internet-access-for-the-virtual-network"></a>Internetový přístup k síti IPv6 pro virtuální síť

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>Veřejné IP adresy protokolu IPv6

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>Front-end protokolu IPv6 pro Load Balancer

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>Fond back-end adres protokolu IPv6 pro Load Balancer

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>Pravidla nástroje pro vyrovnávání zatížení IPv6 pro přidružení příchozích a odchozích portů

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>Ukázka JSON šablony virtuálního počítače
Pokud chcete nasadit aplikaci s duálním zásobníkem IPv6 ve službě Azure Virtual Network pomocí šablony Azure Resource Manager, [tady](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-stdlb/)si můžete zobrazit ukázkovou šablonu.

## <a name="next-steps"></a>Další postup

Můžete najít podrobnosti o cenách pro [veřejné IP adresy](https://azure.microsoft.com/pricing/details/ip-addresses/), [šířku pásma sítě](https://azure.microsoft.com/pricing/details/bandwidth/)nebo [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
