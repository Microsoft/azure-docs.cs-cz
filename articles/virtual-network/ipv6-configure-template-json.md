---
title: Nasazení duální aplikace s protokolem IPv6 se základními Load Balancer v Azure Virtual Network – Šablona správce prostředků
titlesuffix: Azure Virtual Network
description: V tomto článku se dozvíte, jak nasadit aplikaci s duálním zásobníkem IPv6 ve službě Azure Virtual Network pomocí Azure Resource Manager šablon virtuálních počítačů.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: a02dc3b9fb1305aea9430aa1494cce2437ef6091
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016118"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template"></a>Nasazení aplikace s duálním zásobníkem IPv6 se základní Load Balancer v Azure – šablona

Tento článek poskytuje seznam úloh konfigurace protokolu IPv6 s částí Azure Resource Manager šablony virtuálního počítače, která se vztahuje na. Pomocí šablony popsané v tomto článku můžete nasadit aplikaci duálního zásobníku (IPv4 + IPv6) se základní Load Balancer, která zahrnuje virtuální síť s duálním zásobníkem s podsítěmi IPv4 a IPv6, základní Load Balancer s duálními konfiguracemi (IPv4 + IPv6), virtuální počítače se síťovými kartami, které mají konfiguraci s duálním protokolem IP, skupinu zabezpečení sítě a veřejné IP adresy.

Postup nasazení aplikace s duálním zásobníkem (IPV4 + IPv6) pomocí Standard Load Balancer najdete v tématu [nasazení duální aplikace s protokolem IPv6 pomocí Standard Load Balancer-Template](ipv6-configure-standard-load-balancer-template-json.md).

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
              "sourceAddressPrefix": "2001:db8:deca:deed::/64",
              "destinationAddressPrefix": "2001:db8:deca:deed::/64",
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
              "addressPrefix": "2001:db8:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "2001:db8:ace:f00d::1"
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
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
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
Postup nasazení aplikace s duálním zásobníkem IPv6 se základními Load Balancer ve službě Azure Virtual Network pomocí šablony Azure Resource Manager zobrazíte ukázkovou šablonu [zde](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/).

## <a name="next-steps"></a>Další kroky

Můžete najít podrobnosti o cenách pro [veřejné IP adresy](https://azure.microsoft.com/pricing/details/ip-addresses/), [šířku pásma sítě](https://azure.microsoft.com/pricing/details/bandwidth/)nebo [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
