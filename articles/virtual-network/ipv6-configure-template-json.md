---
title: Nasazení aplikace duálním zásobníkem IPv6 ve službě Azure virtual network – šablona Resource Manageru (preview)
titlesuffix: Azure Virtual Network
description: Tento článek ukazuje, jak nasadit aplikace duálním zásobníkem IPv6 ve virtuální síti Azure pomocí šablony Azure Resource Manageru virtuálních počítačů.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: ae90bc4a12763803f38224d917c4644a68ae7d6b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130875"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---template-preview"></a>Nasazení aplikace duálním zásobníkem protokolu IPv6 v Azure – šablony (Preview)

Tento článek obsahuje seznam úloh konfigurace IPv6 s částí, které platí pro šablony virtuálních počítačů Azure Resource Manageru. Šablonu je popsáno v tomto článku použít k nasazení aplikace duálním zásobníkem (IPv4 + IPv6) v Azure, která zahrnuje duálním zásobníkem virtuální síť s podsítí IPv4 a IPv6, nástroj pro vyrovnávání zatížení s front-endové konfigurace dvou (IPv4 + IPv6), virtuální počítače se síťovými kartami, které mají duální IP Konfigurace skupiny zabezpečení sítě a veřejné IP adresy. 

## <a name="required-configurations"></a>Požadované konfigurace

Hledat šablony oddíly v šabloně zobrazíte, kde by měl nastat.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>Adresní prostory IPv6 pro virtuální síť

Části šablony a přidejte:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>Podsíť protokolu IPv6 v rámci adresní prostor virtuální sítě IPv6

Části šablony a přidejte:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>Konfigurace síťového rozhraní IPv6

Části šablony a přidejte:
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

### <a name="ipv6-network-security-group-nsg-rules"></a>Pravidla skupinu zabezpečení sítě IPv6

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

## <a name="conditional-configuration"></a>Podmíněné konfigurace

Pokud používáte síťové virtuální zařízení, přidejte trasy IPv6 ve směrovací tabulce. V opačném případě tato konfigurace je volitelný.

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

### <a name="ipv6-internet-access-for-the-virtual-network"></a>Přístup k Internetu s protokolem IPv6 pro virtuální síť

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>Veřejná IP adresa protokolu IPv6 adresy

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

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6 front-endu nástroje pro vyrovnávání zatížení

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>Fond IPv6 adres Back-end pro nástroj pro vyrovnávání zatížení

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

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>IPv6 pravidla služby load balancer pro přidružení příchozí a odchozí porty

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

## <a name="sample-vm-template-json"></a>Ukázkový kód JSON šablony virtuálního počítače
Klikněte na tlačítko [tady](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/) k nasazení aplikace duálním zásobníkem IPv6 ve virtuální síti Azure pomocí šablony Azure Resource Manageru.

## <a name="next-steps"></a>Další postup

Můžete najít podrobnosti o cenách pro [veřejné IP adresy](https://azure.microsoft.com/pricing/details/ip-addresses/), [šířka pásma sítě](https://azure.microsoft.com/pricing/details/bandwidth/), nebo [nástroje pro vyrovnávání zatížení](https://azure.microsoft.com/pricing/details/load-balancer/).
