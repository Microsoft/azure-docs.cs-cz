---
title: Vytvoření linuxového prostředí pomocí Azure CLI 2.0 | Dokumentace Microsoftu
description: Vytvoření úložiště, virtuální počítač s Linuxem, virtuální sítě a podsítě, nástroj pro vyrovnávání zatížení, síťové rozhraní, veřejné IP adresy a skupinu zabezpečení sítě, vše od základů pomocí rozhraní příkazového řádku Azure CLI 2.0.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: c566c747d393dbfa3225faf6f8ad78fa8abfa3ac
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929701"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Vytvoření kompletní Linuxový virtuální počítač pomocí Azure CLI
Rychle vytvořit virtuální počítač (VM) v Azure, můžete jediným příkazem Azure CLI, který používá výchozí hodnoty pro vytvoření jakékoli požadované podpůrné prostředky. Prostředky, jako jsou virtuální síť, veřejnou IP adresu a pravidla skupiny zabezpečení sítě se vytvoří automaticky. Pro další kontrolu nad vaším prostředím v produkčním prostředí používat, můžete vytvořit tyto prostředky předem domluvili a pak do nich přidejte virtuální počítače. Tento článek vás provede postupy vytvoření virtuálního počítače a všechny podpůrné prostředky jeden po druhém.

Ujistěte se, že máte nainstalovanou nejnovější verzi [příkazového řádku Azure CLI 2.0](/cli/azure/install-az-cli2) a přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index#az_login).

V následujících příkladech nahraďte ukázkové názvy parametrů s vlastními hodnotami. Zahrnout názvy parametrů příklad *myResourceGroup*, *myVnet*, a *myVM*.

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená před virtuální počítač a podpůrné prostředky virtuální sítě. Vytvořte skupinu prostředků s [vytvořit skupiny az](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Ve výchozím nastavení je výstup příkazů Azure CLI ve formátu JSON (JavaScript Object Notation). Chcete-li změnit výchozí výstup na seznam nebo tabulku, například použít [konfigurace az--výstupu](/cli/azure/reference-index#az_configure). Můžete také přidat `--output` příslušnému příkazu pro jeden čas změnit ve výstupním formátu. Následující příklad ukazuje výstup JSON `az group create` příkaz:

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "eastus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-virtual-network-and-subnet"></a>Vytvoření virtuální sítě a podsítě
Další, které vytvoříte v Azure a podsíť ve virtuální sítě do kterého můžete vytvořit virtuální počítače. Použití [az network vnet vytvořit](/cli/azure/network/vnet#az_network_vnet_create) vytvořit virtuální síť s názvem *myVnet* s *192.168.0.0/16* předponu adresy. Můžete také přidat podsíť s názvem *mySubnet* s předponou adresy *192.168.1.0/24*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

Výstup ukazuje, že podsíť je logicky vytvořit uvnitř virtuální sítě:

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "eastus",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy
Nyní Pojďme vytvořit veřejnou IP adresu pomocí [az network public-ip vytvořit](/cli/azure/network/public-ip#az_network_public_ip_create). Tato veřejná IP adresa umožňuje připojení k vašim virtuálním počítačům z Internetu. Protože výchozí adresa je dynamická, vytvořit položku DNS s názvem s `--domain-name-label` parametru. Následující příklad vytvoří veřejnou IP adresu s názvem *myPublicIP* s názvem služby DNS *mypublicdns*. Protože název DNS musí být jedinečný, zadejte jedinečný název DNS:

```azurecli
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --dns-name mypublicdns
```

Výstup:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.eastus.cloudapp.azure.com",
      "reverseFqdn": null
    },
    "etag": "W/\"2632aa72-3d2d-4529-b38e-b622b4202925\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": null,
    "ipConfiguration": null,
    "location": "eastus",
    "name": "myPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Dynamic",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "4c65de38-71f5-4684-be10-75e605b3e41f",
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses"
  }
}
```


## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě
K řízení toku přenosů do a z vašich virtuálních počítačů, můžete použít skupinu zabezpečení sítě pro virtuální síťové karty nebo podsítě. Následující příklad používá [az network nsg vytvořit](/cli/azure/network/nsg#az_network_nsg_create) vytvoříte skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Můžete definovat pravidla, která povolují nebo odpírají konkrétní provoz. Pokud chcete povolit příchozí připojení na portu 22 (Chcete-li povolit přístup přes SSH), vytvořte příchozí pravidlo s [az network nsg pravidlo vytvořte](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Následující příklad vytvoří pravidlo *myNetworkSecurityGroupRuleSSH*:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow
```

Pokud chcete povolit příchozí připojení na portu 80 (pro webový provoz), přidáte jiné pravidlo skupiny zabezpečení sítě. Následující příklad vytvoří pravidlo *myNetworkSecurityGroupRuleHTTP*:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleWeb \
    --protocol tcp \
    --priority 1001 \
    --destination-port-range 80 \
    --access allow
```

Podívejte se na skupinu zabezpečení sítě a pravidla, jejichž [az network nsg show](/cli/azure/network/nsg#az_network_nsg_show):

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Výstup:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBou
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "eastus",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "47a9964e-23a3-438a-a726-8d60ebbb1c3c",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleWeb",
      "name": "myNetworkSecurityGroupRuleWeb",
      "priority": 1001,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": null,
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-a-virtual-nic"></a>Vytvoření virtuálních síťových Adaptérů
Virtuální síťové karty (síťové adaptéry) jsou prostřednictvím kódu programu k dispozici, protože pravidla lze použít k jejich použití. V závislosti na tom [velikost virtuálního počítače](sizes.md), víc virtuálních síťových karet můžete připojit k virtuálnímu počítači. V následujícím [az network nic vytvořit](/cli/azure/network/nic#az_network_nic_create) příkaz, vytvořte síťové rozhraní s názvem *myNic* a přidružte skupinu zabezpečení sítě. Veřejnou IP adresu *myPublicIP* je rovněž asociována s virtuální síťový adaptér

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIP \
    --network-security-group myNetworkSecurityGroup
```

Výstup:

```json
{
  "NewNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": [],
      "internalDnsNameLabel": null,
      "internalDomainNameSuffix": "brqlt10lvoxedgkeuomc4pm5tb.bx.internal.cloudapp.net",
      "internalFqdn": null
    },
    "enableAcceleratedNetworking": false,
    "enableIpForwarding": false,
    "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
    "ipConfigurations": [
      {
        "applicationGatewayBackendAddressPools": null,
        "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic/ipConfigurations/ipconfig1",
        "loadBalancerBackendAddressPools": null,
        "loadBalancerInboundNatRules": null,
        "name": "ipconfig1",
        "primary": true,
        "privateIpAddress": "192.168.1.4",
        "privateIpAddressVersion": "IPv4",
        "privateIpAllocationMethod": "Dynamic",
        "provisioningState": "Succeeded",
        "publicIpAddress": {
          "dnsSettings": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
          "idleTimeoutInMinutes": null,
          "ipAddress": null,
          "ipConfiguration": null,
          "location": null,
          "name": null,
          "provisioningState": null,
          "publicIpAddressVersion": null,
          "publicIpAllocationMethod": null,
          "resourceGroup": "myResourceGroup",
          "resourceGuid": null,
          "tags": null,
          "type": null
        },
        "resourceGroup": "myResourceGroup",
        "subnet": {
          "addressPrefix": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
          "ipConfigurations": null,
          "name": null,
          "networkSecurityGroup": null,
          "provisioningState": null,
          "resourceGroup": "myResourceGroup",
          "resourceNavigationLinks": null,
          "routeTable": null
        }
      }
    ],
    "location": "eastus",
    "macAddress": null,
    "name": "myNic",
    "networkSecurityGroup": {
      "defaultSecurityRules": null,
      "etag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "location": null,
      "name": null,
      "networkInterfaces": null,
      "provisioningState": null,
      "resourceGroup": "myResourceGroup",
      "resourceGuid": null,
      "securityRules": null,
      "subnets": null,
      "tags": null,
      "type": null
    },
    "primary": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "b3dbaa0e-2cf2-43be-a814-5cc49fea3304",
    "tags": null,
    "type": "Microsoft.Network/networkInterfaces",
    "virtualMachine": null
  }
}
```


## <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti
Skupiny dostupnosti nápovědy rozšíření virtuálních počítačů napříč doménami selhání a aktualizačních doménách. I když můžete vytvořit pouze jeden virtuální počítač nyní, je osvědčeným postupem je použití skupin dostupnosti, aby bylo snazší v budoucnu rozšířit. 

Domény selhání definují seskupení virtuálních počítačů, které sdílejí společný power přepínač zdroje a sítě. Ve výchozím nastavení virtuální počítače, které jsou nakonfigurované v rámci skupiny dostupnosti rozdělené až do tří domén selhání. Hardwarové potíže v jednom z těchto domén selhání nemá vliv na každý virtuální počítač, na kterém běží vaše aplikace.

Aktualizační domény označují skupiny virtuálních počítačů a podřízeného fyzického hardwaru, který lze restartovat současně. Během plánované údržby pořadí, ve které aktualizace se restartují, domény nemusí být sekvenční, ale vždy se restartuje jenom jedna aktualizační doména.

Virtuální počítače Azure automaticky distribuuje napříč doménami selhání a aktualizačními doménami, při vkládání je ve skupině dostupnosti. Další informace najdete v tématu [Správa dostupnosti virtuálních počítačů](manage-availability.md).

Vytvořit skupinu dostupnosti pro virtuální počítač s [az vm skupinu dostupnosti vytvořit](/cli/azure/vm/availability-set#az_vm_availability_set_create). Následující příklad vytvoří skupinu dostupnosti s názvem *myAvailabilitySet*:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

Poznámky k výstupu domén selhání a aktualizačními doménami:

```json
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet",
  "location": "eastus",
  "managed": null,
  "name": "myAvailabilitySet",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 5,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": null,
    "managed": true,
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```


## <a name="create-a-vm"></a>Vytvoření virtuálního počítače
Vytvořili jste síťové prostředky pro podporu přístupné z Internetu virtuálních počítačů. Teď vytvořte virtuální počítač a zabezpečte ji pomocí klíče SSH. V tomto příkladu vytvoříme Ubuntu virtuální počítač založený na nejnovější LTS. Můžete najít další Image s [az vm image seznamu](/cli/azure/vm/image#az_vm_image_list), jak je popsáno v [vyhledání imagí virtuálních počítačů Azure](cli-ps-findimage.md).

Zadejte klíč SSH pro účely ověření. Pokud nemáte páru veřejného klíče SSH, můžete si [je vytvořit](mac-create-ssh-keys.md) nebo použijte `--generate-ssh-keys` parametr vytvořit za vás. Pokud už máte pár klíčů, tento parametr používá existující klíče v `~/.ssh`.

Vytvoření virtuálního počítače tak, že všechny prostředky a informace o spolu s [az vm vytvořit](/cli/azure/vm#az_vm_create) příkazu. Následující příklad vytvoří virtuální počítač *myVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --availability-set myAvailabilitySet \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Připojte přes SSH k virtuálnímu počítači s položkou DNS, kterou jste zadali při vytvoření veřejné IP adresy. To `fqdn` se zobrazí ve výstupu při vytváření virtuálního počítače:

```json
{
  "fqdns": "mypublicdns.eastus.cloudapp.azure.com",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-13-71-C8",
  "powerState": "VM running",
  "privateIpAddress": "192.168.1.5",
  "publicIpAddress": "13.90.94.252",
  "resourceGroup": "myResourceGroup"
}
```

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Výstup:

```bash
The authenticity of host 'mypublicdns.eastus.cloudapp.azure.com (13.90.94.252)' can't be established.
ECDSA key fingerprint is SHA256:SylINP80Um6XRTvWiFaNz+H+1jcrKB1IiNgCDDJRj6A.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.eastus.cloudapp.azure.com,13.90.94.252' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@myVM:~$
```

Můžete nainstalovat server NGINX a sledovat provoz toku k virtuálnímu počítači. Instalaci serveru NGINX následujícím způsobem:

```bash
sudo apt-get install -y nginx
```

Pokud chcete zobrazit výchozí web NGINX v akci, otevřete webový prohlížeč a zadejte váš plně kvalifikovaný název domény:

![Výchozí web NGINX na virtuálním počítači](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Exportovat jako šablonu
Co dělat, pokud teď chcete vytvořit další vývojová prostředí se stejnými parametry nebo produkčním prostředí, který mu odpovídá? Správce prostředků pomocí šablon JSON, které definují parametry vašeho prostředí. Pomocí odkazu na tuto šablonu JSON sestavujete celé prostředí. Je možné [ručně vytvářet šablony JSON](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo exportovat stávající prostředí k vytvoření šablony JSON za vás. Použití [export skupiny az](/cli/azure/group#az_group_export) export skupiny prostředků následujícím způsobem:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Tento příkaz vytvoří `myResourceGroup.json` soubor v aktuálním pracovním adresáři. Při vytváření prostředí z této šablony se zobrazí výzva k zadání všechny názvy prostředků. Tyto názvy můžete naplnit v souboru šablony tak, že přidáte `--include-parameter-default-value` parametr `az group export` příkazu. Upravte svou šablonu JSON k určení názvy prostředků, nebo [vytvoření souboru parameters.json](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , který určuje názvy prostředků.

K vytvoření prostředí ze šablony, použijte [vytvořit nasazení skupiny pro az](/cli/azure/group/deployment#az_group_deployment_create) následujícím způsobem:

```azurecli
az group deployment create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

Můžete chtít číst [Další informace o tom, jak nasadit ze šablon](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Další informace o tom, jak přírůstkově aktualizovat prostředí, použijte soubor parametrů a přístup k šablony jedno umístění úložiště.

## <a name="next-steps"></a>Další postup
Nyní jste připraveni začít pracovat s více síťovými součástmi a virtuální počítače. Toto ukázkové prostředí můžete použít k sestavení aplikace s použitím základních komponent zavedené zde.
