---
title: Vytvoření prostředí Linux pomocí Azure CLI
description: Vytvoření úložiště, virtuálního počítače se systémem Linux, virtuální sítě a podsítě, nástroje pro vyrovnávání zatížení, síťového rozhraní, veřejné IP adresy a skupiny zabezpečení sítě, a to vše od základů pomocí Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: e8c21df3b783241a3a4bfdbe28b28b2bceb89b4d
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509111"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Vytvoření kompletního virtuálního počítače se systémem Linux pomocí Azure CLI
Pokud chcete rychle vytvořit virtuální počítač v Azure, můžete použít jeden příkaz Azure CLI, který pomocí výchozích hodnot vytvoří všechny požadované podpůrné prostředky. Automaticky se vytvoří prostředky, jako je virtuální síť, veřejná IP adresa a pravidla skupiny zabezpečení sítě. Pro lepší kontrolu vašeho prostředí při použití v produkčním prostředí můžete tyto prostředky vytvořit předem a potom do nich přidat své virtuální počítače. Tento článek vás provede postupem, jak vytvořit virtuální počítač a každý z podpůrných prostředků od jednoho.

Ujistěte se, že máte nainstalované nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a protokolujte ho do účtu Azure v rámci pomocí [AZ Login](/cli/azure/reference-index).

V následujících příkladech nahraďte příklady názvů parametrů vlastními hodnotami. Příklady názvů parametrů jsou *myResourceGroup*, *myVnet* a *myVM*.

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená před virtuálním počítačem a podporou prostředků virtuální sítě. Vytvořte skupinu prostředků pomocí [AZ Group Create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Ve výchozím nastavení je výstup příkazů Azure CLI ve formátu JSON (JavaScript Object Notation). Chcete-li změnit výchozí výstup na seznam nebo tabulku, použijte například [AZ Configure--Output](/cli/azure/reference-index). Můžete také přidat `--output` do libovolného příkazu pro jednorázovou změnu ve formátu výstupu. Následující příklad ukazuje výstup JSON z `az group create` příkazu:

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
V dalším kroku vytvoříte virtuální síť v Azure a podsíť, ve které můžete vytvářet virtuální počítače. Pomocí [AZ Network VNet Create](/cli/azure/network/vnet) vytvořte virtuální síť s názvem *myVnet* s předponou adresy *192.168.0.0/16* . Přidáte také podsíť s názvem *mySubnet* s předponou adresy *192.168.1.0/24*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

Výstup ukazuje logicky vytvořenou podsíť ve virtuální síti:

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
Teď vytvoříme veřejnou IP adresu pomocí [AZ Network Public-IP Create](/cli/azure/network/public-ip). Tato veřejná IP adresa vám umožní připojit se k virtuálním počítačům z Internetu. Vzhledem k tomu, že výchozí adresa je dynamická, vytvořte s parametrem pojmenovaný záznam DNS `--domain-name-label` . Následující příklad vytvoří veřejnou IP adresu s názvem *myPublicIP* s názvem DNS *mypublicdns*. Vzhledem k tomu, že název DNS musí být jedinečný, zadejte vlastní jedinečný název DNS:

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
K řízení toku provozu na virtuálních počítačích a z nich můžete použít skupinu zabezpečení sítě na virtuální síťové rozhraní nebo podsíť. Následující příklad používá [AZ Network NSG Create](/cli/azure/network/nsg) k vytvoření skupiny zabezpečení sítě s názvem *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Definujete pravidla, která povolují nebo zakazují konkrétní provoz. Pokud chcete povolit příchozí připojení na portu 22 (povolení přístupu přes SSH), vytvořte příchozí pravidlo pomocí [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule). Následující příklad vytvoří pravidlo s názvem *myNetworkSecurityGroupRuleSSH*:

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

Pokud chcete povolit příchozí připojení na portu 80 (pro webový provoz), přidejte další pravidlo skupiny zabezpečení sítě. Následující příklad vytvoří pravidlo s názvem *myNetworkSecurityGroupRuleHTTP*:

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

Projděte si skupinu zabezpečení sítě a pravidla pomocí [AZ Network NSG show](/cli/azure/network/nsg):

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
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBou",
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

## <a name="create-a-virtual-nic"></a>Vytvoření virtuální síťové karty
Karty virtuálních síťových rozhraní jsou k dispozici programově, protože můžete použít pravidla na jejich použití. V závislosti na [velikosti virtuálního počítače](../sizes.md)můžete k virtuálnímu počítači připojit několik virtuálních síťových karet. V následujících příkazech [AZ Network nic Create](/cli/azure/network/nic) vytvoříte síťové rozhraní s názvem *myNic* a přidružíte ho ke skupině zabezpečení vaší sítě. Veřejná IP adresa *myPublicIP* je také přidružená k virtuální síťové kartě.

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
Skupiny dostupnosti pomůžou rozšířit vaše virtuální počítače mezi doménami selhání a aktualizačními doménami. I když teď jenom jeden virtuální počítač vytváříte, doporučuje se používat skupiny dostupnosti, aby bylo možné ho v budoucnu snadněji rozbalovat. 

Domény selhání definují seskupení virtuálních počítačů, které sdílejí společný zdroj napájení a síťový přepínač. Ve výchozím nastavení jsou virtuální počítače, které jsou nakonfigurované v rámci skupiny dostupnosti, oddělené v rámci až tří domén selhání. Problém s hardwarem v jedné z těchto domén selhání nemá vliv na všechny virtuální počítače, na kterých je vaše aplikace spuštěná.

Aktualizace domén označují skupiny virtuálních počítačů a základní fyzický hardware, které lze restartovat současně. Během plánované údržby nemusí být pořadí, ve kterém se aktualizační domény restartují, sekvenční, ale v jednom okamžiku se restartuje jenom jedna aktualizační doména.

Azure automaticky distribuuje virtuální počítače napříč doménami selhání a aktualizačními doménami při jejich umísťování do skupiny dostupnosti. Další informace najdete v tématu [Správa dostupnosti virtuálních počítačů](../availability.md).

Vytvořte skupinu dostupnosti pro virtuální počítač pomocí [AZ VM Availability-set Create](/cli/azure/vm/availability-set). Následující příklad vytvoří skupinu dostupnosti *myAvailabilitySet*:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

Doména selhání výstupních poznámek a aktualizačních domén:

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
Vytvořili jste síťové prostředky pro podporu virtuálních počítačů s přístupem k Internetu. Nyní vytvořte virtuální počítač a zabezpečte ho pomocí klíče SSH. V tomto příkladu vytvoříme virtuální počítač Ubuntu založený na nejnovějším LTS. Další image najdete pomocí [seznamu AZ VM Image list](/cli/azure/vm/image), jak je popsáno v tématu [vyhledání imagí virtuálních počítačů Azure](cli-ps-findimage.md).

Zadejte klíč SSH, který se má použít pro ověřování. Pokud nemáte dvojici veřejného klíče SSH, můžete [je vytvořit](mac-create-ssh-keys.md) nebo použít `--generate-ssh-keys` parametr k jejich vytvoření. Pokud již máte dvojici klíčů, tento parametr používá existující klíče v `~/.ssh` .

Pomocí příkazu [AZ VM Create](/cli/azure/vm) vytvořte virtuální počítač tím, že spojíte všechny prostředky a informace. Následující příklad vytvoří virtuální počítač s názvem *myVM*:

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

Připojte se k VIRTUÁLNÍmu počítači přes SSH pomocí položky DNS, kterou jste zadali při vytváření veřejné IP adresy. Tento `fqdn` výstup se zobrazí ve výstupu při vytváření virtuálního počítače:

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
    https://www.ubuntu.com/business/services/cloud

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

Můžete nainstalovat NGINX a zobrazit tok provozu do virtuálního počítače. NGINX nainstalujte následujícím způsobem:

```bash
sudo apt-get install -y nginx
```

Pokud chcete zobrazit výchozí web NGINX v akci, otevřete webový prohlížeč a zadejte svůj plně kvalifikovaný název domény:

![Výchozí NGINX web na vašem VIRTUÁLNÍm počítači](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Exportovat jako šablonu
Co když teď chcete vytvořit další vývojové prostředí se stejnými parametry nebo produkčním prostředím, které odpovídá? Správce prostředků používá šablony JSON, které definují všechny parametry vašeho prostředí. Můžete sestavit celá prostředí odkazem na tuto šablonu JSON. Můžete vytvořit [šablony JSON ručně](../../azure-resource-manager/templates/template-syntax.md?toc=/azure/virtual-machines/linux/toc.json) nebo exportovat existující prostředí a vytvořit šablonu JSON. Pomocí příkazu [AZ Group export](/cli/azure/group) exportujte skupinu prostředků následujícím způsobem:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Tento příkaz vytvoří `myResourceGroup.json` soubor v aktuálním pracovním adresáři. Když z této šablony vytvoříte prostředí, zobrazí se výzva k zadání všech názvů prostředků. Tyto názvy můžete vyplnit v souboru šablony přidáním `--include-parameter-default-value` parametru do `az group export` příkazu. Upravte šablonu JSON tak, aby určovala názvy prostředků, nebo [vytvořte parameters.jsv souboru](../../azure-resource-manager/templates/template-syntax.md?toc=/azure/virtual-machines/linux/toc.json) , který určuje názvy prostředků.

Chcete-li vytvořit prostředí ze šablony, použijte příkaz [AZ Deployment Group Create](/cli/azure/deployment/group) následujícím způsobem:

```azurecli
az deployment group create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

Můžete si přečíst [Další informace o tom, jak nasadit z šablon](../../azure-resource-manager/templates/deploy-cli.md?toc=/azure/virtual-machines/linux/toc.json). Přečtěte si, jak postupně aktualizovat prostředí, použít soubor parametrů a získat přístup k šablonám z jediného umístění úložiště.

## <a name="next-steps"></a>Další kroky
Teď jste připraveni začít pracovat s několika síťovými součástmi a virtuálními počítači. Toto ukázkové prostředí můžete použít k sestavení aplikace pomocí základních součástí, které jsou zde zavedeny.