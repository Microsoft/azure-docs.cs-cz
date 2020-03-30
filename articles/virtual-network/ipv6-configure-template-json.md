---
title: Nasazení aplikace iPv6 se dvěma zásobníky se základním nástrojem pro vyrovnávání zatížení ve virtuální síti Azure – šablona Resource Manger (preview)
titlesuffix: Azure Virtual Network
description: Tento článek ukazuje, jak nasadit aplikaci IPv6 dual stack ve virtuální síti Azure pomocí šablon virtuálních zařízení Azure Resource Manager.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/26/2019
ms.author: kumud
ms.openlocfilehash: b397c874045a89f5992aeadacfbbd4434a486977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70012839"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template-preview"></a>Nasazení aplikace iPv6 se dvěma zásobníky se základním vyrovnáváním zatížení v Azure – šablona (preview)

Tento článek obsahuje seznam úloh konfigurace IPv6 s částí šablony virtuálního počítače Azure Resource Manager, která se vztahuje na. Pomocí šablony popsané v tomto článku nasadit aplikaci duálního zásobníku (IPv4 + IPv6) se základním nástrojem pro vyrovnávání zatížení, která obsahuje virtuální síť se dvěma zásobníky s podsítěmi IPv4 a IPv6, základní nástroj pro vyrovnávání zatížení s duálními konfiguracemi front-endu (IPv4 + IPv6), virtuální počítače s virtuálními počítači s Síťové karty, které mají konfiguraci duální IP adresy, skupinu zabezpečení sítě a veřejné IP adresy.

Pokud chcete nasadit aplikaci s duálním zásobníkem (IPV4 + IPv6) pomocí standardního vykladače zatížení, přečtěte [si část Nasazení aplikace iPv6 se standardním vyvažovačem zatížení – šablonou](ipv6-configure-standard-load-balancer-template-json.md).

## <a name="required-configurations"></a>Požadované konfigurace

Vyhledejte oddíly šablony v šabloně a zjistěte, kde by k nim mělo dojít.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>Adresní prostor IPv6 pro virtuální síť

Oddíl Šablony, který chcete přidat:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>Podsíť IPv6 v adresním prostoru virtuální sítě IPv6

Oddíl Šablony, který chcete přidat:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>Konfigurace Protokolu IPv6 pro nic

Oddíl Šablony, který chcete přidat:
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

Pokud používáte síťové virtuální zařízení, přidejte trasy IPv6 do tabulky tras. V opačném případě je tato konfigurace volitelná.

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

### <a name="ipv6-internet-access-for-the-virtual-network"></a>IPv6 Přístup k internetu pro virtuální síť

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>Veřejné IP adresy IPv6

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

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6 Front-end pro balancer zatížení

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>Fond adres back-endu IPv6 pro balancer zatížení

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

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>Pravidla vyrovnávání zatížení IPv6 pro přidružení příchozích a odchozích portů

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

## <a name="sample-vm-template-json"></a>Ukázková šablona virtuálního počítače JSON
Pokud chcete nasadit dvouvrstvou aplikaci IPv6 se základním nástrojem pro vyrovnávání zatížení ve virtuální síti Azure pomocí šablony Azure Resource Manager, zobrazte ukázkovou šablonu [tady](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/).

## <a name="next-steps"></a>Další kroky

Můžete najít podrobnosti o cenách pro [veřejné IP adresy](https://azure.microsoft.com/pricing/details/ip-addresses/), [šířku pásma sítě](https://azure.microsoft.com/pricing/details/bandwidth/)nebo [Balancer zatížení](https://azure.microsoft.com/pricing/details/load-balancer/).
