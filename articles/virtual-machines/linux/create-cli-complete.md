---
title: "Vytvořte prostředí Linux s 2.0 rozhraní příkazového řádku Azure | Microsoft Docs"
description: "Vytvoření úložiště, virtuální počítač s Linuxem, virtuální síť a podsíť, nástroj pro vyrovnávání zatížení, seskupování, veřejnou IP adresu a skupinu zabezpečení sítě, všechny od základů pomocí Azure CLI 2.0."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: iainfou
ms.openlocfilehash: f41bfec3c9f950893b69c90a86c2e4a254b72a8b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Vytvoření kompletní virtuální počítač Linux pomocí rozhraní příkazového řádku Azure
Rychle vytvořit virtuální počítač (VM) v Azure, můžete jeden příkaz rozhraní příkazového řádku Azure, kterou použije výchozí hodnoty pro vytvoření všechny požadované podpůrné prostředky. Prostředky, jako je například virtuální sítě, veřejnou IP adresu a pravidel skupiny zabezpečení sítě se vytvářejí automaticky. Pro další ovládací prvek v provozním prostředí použít, můžete vytvořit tyto prostředky předem a pak do nich přidat virtuální počítače. Tento článek vás provede postup vytvoření virtuálního počítače a každý z doprovodné materiály po jednom.

Ujistěte se, že jste nainstalovali nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) a přihlášení k účtu Azure s [az přihlášení](/cli/azure/reference-index#az_login).

V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Zahrnout názvy parametrů příklad *myResourceGroup*, *myVnet*, a *Můjvp*.

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Před virtuální počítač a doprovodné materiály virtuální sítě je třeba vytvořit skupinu prostředků. Vytvořte skupinu prostředků s [vytvořit skupinu az](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Ve výchozím nastavení výstup rozhraní příkazového řádku Azure je ve formátu JSON (JavaScript Object Notation). Chcete-li změnit výchozí výstup do seznamu nebo tabulka, například použít [konfigurace az--výstup](/cli/azure/reference-index#az_configure). Můžete také přidat `--output` na libovolný příkaz po dobu jednoho změnit ve výstupním formátu. Následující příklad ukazuje výstup JSON `az group create` příkaz:

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
Další, které vytvoření virtuální sítě v Azure a podsíť v do které můžete vytvořit virtuální počítače. Použití [vytvoření sítě vnet az](/cli/azure/network/vnet#az_network_vnet_create) vytvořit virtuální síť s názvem *myVnet* s *192.168.0.0/16* předponu adresy. Můžete také přidat podsíť s názvem *mySubnet* s předponou adresy z *192.168.1.0/24*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

Výstup se zobrazí logicky vytvořené podsíť ve virtuální síti:

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
Nyní vytvoříme veřejnou IP adresu s [vytvoření veřejné sítě az-ip](/cli/azure/network/public-ip#az_network_public_ip_create). Tato veřejná IP adresa umožňuje připojení k virtuální počítače z Internetu. Protože výchozí adresa je dynamická, vytvořit záznam DNS s názvem s `--domain-name-label` parametr. Následující příklad vytvoří veřejnou IP adresu s názvem *myPublicIP* s názvem DNS *mypublicdns*. Vzhledem k tomu, že název DNS musí být jedinečný, zadejte svůj vlastní jedinečný název DNS:

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
K řízení toku provozu do a z virtuálních počítačů, použijete skupinu zabezpečení sítě pro virtuální síťový adaptér nebo podsítě. Následující příklad používá [vytvořit az sítě nsg](/cli/azure/network/nsg#az_network_nsg_create) vytvoříte skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Můžete definovat pravidla, která povolují nebo odepírají konkrétní přenosy. Chcete-li povolit příchozí připojení na portu 22 (pro povolení přístupu SSH), vytvořte příchozí pravidlo s [vytvořit pravidla nsg sítě az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Následující příklad vytvoří pravidlo s názvem *myNetworkSecurityGroupRuleSSH*:

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

Chcete-li povolit příchozí připojení na portu 80 (pro webový provoz), přidejte další pravidla skupiny zabezpečení sítě. Následující příklad vytvoří pravidlo s názvem *myNetworkSecurityGroupRuleHTTP*:

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

Podívejte se na skupinu zabezpečení sítě a pravidla s [az sítě nsg zobrazit](/cli/azure/network/nsg#az_network_nsg_show):

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

## <a name="create-a-virtual-nic"></a>Vytvořit virtuální síťovou kartu
Virtuální síťové adaptéry (NIC) jsou prostřednictvím kódu programu k dispozici, protože pravidla můžete použít pro jejich použití. V závislosti na tom [velikost virtuálního počítače](sizes.md), víc virtuálních síťových karet můžete připojit k virtuálnímu počítači. V následujícím [vytvořit síťových adaptérů sítě az](/cli/azure/network/nic#az_network_nic_create) příkazu vytvoříte síťový adaptér s názvem *myNic* a přidružit ho ke skupině zabezpečení sítě. Veřejná IP adresa *myPublicIP* je taky přiřazený virtuální síťový adaptér.

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
Dostupnost nastaví nápovědu šíření virtuální počítače napříč domén selhání a aktualizace domény. I když můžete vytvořit pouze jeden virtuální počítač nyní, je osvědčeným postupem použít skupiny dostupnosti, aby bylo snazší v budoucnu rozšířit. 

Domén selhání definovat seskupování virtuálních počítačů, které sdílejí společné přepínač zdroje a sítě power. Ve výchozím nastavení jsou oddělené virtuální počítače, které jsou nakonfigurované v rámci vaší skupiny dostupnosti v rámci až tři domény selhání. Problémem hardwaru v jednom z těchto domén selhání nemá vliv na každý virtuální počítač, který běží vaše aplikace.

Aktualizace domén označují skupiny virtuálních počítačů a základní fyzický hardware, který může být restartován ve stejnou dobu. Během plánované údržby pořadí, ve které aktualizace se restartují domény nemusí být po sobě jdoucích, ale po restartu pouze jednu aktualizační doménu najednou.

Virtuální počítače Azure automaticky distribuuje mezi doménami selhání a aktualizace, když umístění do nastavení dostupnosti. Další informace najdete v tématu [Správa dostupnosti virtuálních počítačů](manage-availability.md).

Vytvořit sadu dostupnosti pro virtuální počítač s [az virtuálních počítačů sady dostupnosti. vytváření](/cli/azure/vm/availability-set#az_vm_availability_set_create). Následující příklad vytvoří skupinu dostupnosti s názvem *myAvailabilitySet*:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

Výstup domén selhání poznámky a aktualizace domény:

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
Jste vytvořili síťovým prostředkům pro podporu přístupné z Internetu virtuálních počítačů. Teď vytvořte virtuální počítač a zabezpečte ji pomocí klíče SSH. V tomto příkladu vytvoříme Ubuntu podle nejnovější LTS virtuálních počítačů. Můžete najít další Image s [seznamu obrázků virtuálních počítačů az](/cli/azure/vm/image#az_vm_image_list), jak je popsáno v [hledání Image virtuálního počítače Azure](cli-ps-findimage.md).

Zadejte klíč SSH pro ověřování. Pokud nemáte páru veřejného klíče SSH, můžete [jejich vytvoření](mac-create-ssh-keys.md) nebo použít `--generate-ssh-keys` parametr, aby pro vás vytvořil. Pokud již máte pár klíčů, tento parametr používá existující klíče v `~/.ssh`.

Vytvoření virtuálního počítače tak, že převedou všechny prostředky a informace o společně s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) příkaz. Následující příklad vytvoří virtuální počítač *myVM*:

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

SSH pro virtuální počítač s položkou DNS, kterou jste zadali při vytvoření veřejné IP adresy. To `fqdn` je zobrazené ve výstupu, jako je vytváření virtuálního počítače:

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

Můžete nainstalovat NGINX a sledovat provoz toku k virtuálnímu počítači. Nainstalujte NGINX následujícím způsobem:

```bash
sudo apt-get install -y nginx
```

Pokud chcete zobrazit výchozí web NGINX v akci, otevřete webový prohlížeč a zadejte vaše plně kvalifikovaný název domény:

![Výchozí web NGINX na vašem virtuálním počítači](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Exportovat jako šablonu
Co když teď chcete vytvořit další vývoj prostředí se stejnými parametry nebo produkčním prostředí odpovídající ho? Správce prostředků používá šablony JSON, které definují všech parametrů pro vaše prostředí. Můžete vytvořit na celé prostředí podle odkazující na tuto šablonu JSON. Můžete [vytvořit šablony JSON ručně](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo exportovat stávajícího prostředí pro vytvoření šablony JSON pro vás. Použití [export skupiny az](/cli/azure/group#az_group_export) export skupiny prostředků následujícím způsobem:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Tento příkaz vytvoří `myResourceGroup.json` souboru v aktuální pracovní adresář. Při vytváření prostředí z této šablony, zobrazí se výzva pro všechny názvy prostředků. Tyto názvy v souboru šablony můžete naplnit tak, že přidáte `--include-parameter-default-value` parametru `az group export` příkaz. Upravte svou šablonu JSON zadat názvy prostředků, nebo [vytvoření souboru parameters.JSON tímto kódem](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) určující názvy prostředků.

K vytvoření prostředí ze šablony, použijte [vytvořit nasazení skupiny az](/cli/azure/group/deployment#az_group_deployment_create) následujícím způsobem:

```azurecli
az group deployment create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

Můžete chtít číst [Další informace o tom, jak nasadit ze šablon](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Další informace o tom, jak přírůstkově aktualizovat prostředí, použijte soubor parametrů a přístup k šablony z jedno umístění úložiště.

## <a name="next-steps"></a>Další postup
Nyní jste připraveni začít pracovat s více síťovými součástmi a virtuálními počítači. Tato ukázka prostředí můžete vytváří vaše aplikace pomocí základních komponent zavedená sem.
