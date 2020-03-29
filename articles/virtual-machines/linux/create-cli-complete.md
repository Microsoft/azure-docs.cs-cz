---
title: Vytvoření linuxového prostředí pomocí azure cli
description: Vytvořte úložiště, virtuální počítač s Linuxem, virtuální síť a podsíť, nástroj pro vyrovnávání zatížení, síťovou síťovou karty, veřejnou IP adresu a skupinu zabezpečení sítě, to vše od základu pomocí rozhraní příkazového příkazu k síti Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 7ee4674f5e7c04709256459c3417a1379a65aedc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969565"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Vytvoření kompletního virtuálního počítače s Linuxem pomocí azure cli
Chcete-li rychle vytvořit virtuální počítač (VM) v Azure, můžete použít jeden příkaz Azure CLI, který používá výchozí hodnoty k vytvoření všech požadovaných podpůrných prostředků. Automaticky se vytvoří prostředky, jako je virtuální síť, veřejná IP adresa a pravidla skupiny zabezpečení sítě. Chcete-li získat větší kontrolu nad prostředím při používání produkčního prostředí, můžete tyto prostředky vytvořit předem a potom do nich přidat virtuální počítače. Tento článek vás provede, jak vytvořit virtuální hod a každý z podpůrných prostředků jeden po druhém.

Ujistěte se, že jste nainstalovali nejnovější [azure cli](/cli/azure/install-az-cli2) a přihlášeni k účtu Azure s [az přihlášení](/cli/azure/reference-index).

V následujících příkladech nahraďte názvy příkladů parametrů vlastními hodnotami. Příklady názvů parametrů zahrnují *myResourceGroup*, *myVnet*a *myVM*.

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořena před virtuálním počítačem a podporou prostředků virtuální sítě. Vytvořte skupinu prostředků pomocí [vytvoření skupiny az](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Ve výchozím nastavení je výstup příkazů Azure CLI v JSON (JavaScript Object Notation). Chcete-li změnit výchozí výstup na seznam nebo tabulku, použijte například [az configure --output](/cli/azure/reference-index). Můžete také `--output` přidat do libovolného příkazu pro jednorázovou změnu ve výstupním formátu. Následující příklad ukazuje výstup JSON `az group create` z příkazu:

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
Dále vytvoříte virtuální síť v Azure a podsíť, do které můžete vytvořit virtuální počítače. Pomocí [vytvoření virtuální sítě az](/cli/azure/network/vnet) vytvořte virtuální síť s názvem *myVnet* s předponou adresy *192.168.0.0/16.* Přidáte také podsíť s názvem *mySubnet* s předponou adresy *192.168.1.0/24*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

Výstup ukazuje, že podsíť je logicky vytvořena uvnitř virtuální sítě:

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
Nyní pojďme vytvořit veřejnou IP adresu s [az sítě public-ip vytvořit](/cli/azure/network/public-ip). Tato veřejná IP adresa umožňuje připojení k virtuálním počítačům z Internetu. Vzhledem k tomu, že výchozí adresa `--domain-name-label` je dynamická, vytvořte s parametrem pojmenovanou položku DNS. Následující příklad vytvoří veřejnou IP adresu s názvem *myPublicIP* s názvem DNS *mypublicdns*. Vzhledem k tomu, že název DNS musí být jedinečný, zadejte svůj vlastní jedinečný název DNS:

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
Chcete-li řídit tok provozu do a z virtuálních počítačů, použijte skupinu zabezpečení sítě na virtuální síťovou síť nebo podsíť. Následující příklad používá [az network nsg create](/cli/azure/network/nsg) k vytvoření skupiny zabezpečení sítě s názvem *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Definujete pravidla, která povolují nebo popírají určitý provoz. Chcete-li povolit příchozí připojení na portu 22 (chcete-li povolit přístup SSH), vytvořte příchozí pravidlo s [vytvořením pravidla az network nsg](/cli/azure/network/nsg/rule). Následující příklad vytvoří pravidlo s názvem *myNetworkSecurityGroupRuleSSH*:

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

Chcete-li povolit příchozí připojení na portu 80 (pro webový provoz), přidejte další pravidlo skupiny zabezpečení sítě. Následující příklad vytvoří pravidlo s názvem *myNetworkSecurityGroupRuleHTTP*:

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

Zkontrolujte skupinu zabezpečení sítě a pravidla pomocí [az network nsg show](/cli/azure/network/nsg):

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

## <a name="create-a-virtual-nic"></a>Vytvoření virtuální nic
Karty rozhraní virtuální sítě (NIC) jsou programově dostupné, protože můžete použít pravidla pro jejich použití. V závislosti na [velikosti virtuálního počítače](sizes.md)můžete k virtuálnímu počítači připojit více virtuálních síťové karty. V následujícím příkazu [az network nic create](/cli/azure/network/nic) vytvoříte síťovou síť s názvem *myNic* a přidružíte ji ke skupině zabezpečení sítě. Veřejná IP adresa *myPublicIP* je také spojena s virtuální nic.

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
Sady dostupnosti pomáhají rozložit virtuální počítače mezi doménami selhání a aktualizovat domény. I když právě teď vytvoříte jenom jeden virtuální virtuální ms, je vhodné použít skupiny dostupnosti, aby se v budoucnu usnadnilo rozšiřování. 

Domény selhání definují seskupení virtuálních počítačů, které sdílejí společný zdroj napájení a síťový přepínač. Ve výchozím nastavení jsou virtuální počítače, které jsou nakonfigurované v rámci vaší skupiny dostupnosti, odděleny až ve třech doménách selhání. Problém s hardwarem v jedné z těchto domén selhání nemá vliv na každý virtuální počítač, který je spuštěn vaší aplikace.

Aktualizační domény označují skupiny virtuálních počítačů a základního fyzického hardwaru, které lze restartovat současně. Během plánované údržby nemusí být pořadí restartování aktualizačních domén sekvenční, ale současně je restartována pouze jedna aktualizační doména.

Azure automaticky distribuuje virtuální počítače přes chyby a aktualizovat domény při jejich umístění do skupiny dostupnosti. Další informace najdete [v tématu správa dostupnosti virtuálních aplikací](manage-availability.md).

Vytvořte sadu dostupnosti pro váš virtuální počítač s [vytvořením sady dostupnosti virtuálního počítače az](/cli/azure/vm/availability-set). Následující příklad vytvoří skupinu dostupnosti *myAvailabilitySet*:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

Výstup obsahuje domény selhání a aktualizační domény:

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
Vytvořili jste síťové prostředky pro podporu virtuálních her bez připojení k Internetu. Teď vytvořte virtuální hoapískový virtuální ms a zabezpečte ho klíčem SSH. V tomto příkladu vytvoříme virtuální počítač Ubuntu založený na nejnovějším LTS. Další image s [seznamem bitových kopií virtuálních vzbáče](/cli/azure/vm/image), jak je popsáno v [hledání ibi virtuálních počítačů Azure](cli-ps-findimage.md).

Zadejte klíč SSH, který se má použít pro ověřování. Pokud nemáte dvojici veřejného klíče SSH, můžete je `--generate-ssh-keys` [vytvořit](mac-create-ssh-keys.md) nebo použít parametr k jejich vytvoření za vás. Pokud již máte dvojici klíčů, tento `~/.ssh`parametr používá existující klíče v aplikaci .

Vytvořte virtuální hosti tím, že všechny prostředky a informace společně s [az vm vytvořit](/cli/azure/vm) příkaz. Následující příklad vytvoří virtuální hod s názvem *myVM*:

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

SSH do virtuálního počítače s položkou DNS, kterou jste zadali při vytváření veřejné IP adresy. To `fqdn` se zobrazí ve výstupu při vytváření virtuálního počítače:

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

Můžete nainstalovat NGINX a zobrazit tok provozu do virtuálního provozu. Nainstalujte NGINX takto:

```bash
sudo apt-get install -y nginx
```

Chcete-li zobrazit výchozí web NGINX v akci, otevřete webový prohlížeč a zadejte svůj hlavní přístupný obsah:

![Výchozí web NGINX na vašem virtuálním počítači](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Exportovat jako šablonu
Co když nyní chcete vytvořit další vývojové prostředí se stejnými parametry nebo produkční prostředí, které odpovídá? Správce prostředků používá šablony JSON, které definují všechny parametry pro vaše prostředí. Vytvoření celé prostředí odkazem na tuto šablonu JSON. Šablony [JSON](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) můžete vytvořit ručně nebo exportovat existující prostředí a vytvořit šablonu JSON za vás. Pomocí [exportu skupiny az](/cli/azure/group) exportujte skupinu prostředků následujícím způsobem:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Tento příkaz `myResourceGroup.json` vytvoří soubor v aktuálním pracovním adresáři. Při vytváření prostředí z této šablony budete vyzváni ke všem názvům prostředků. Tyto názvy můžete naplnit v `--include-parameter-default-value` souboru `az group export` šablony přidáním parametru do příkazu. Upravte šablonu JSON a určete názvy prostředků nebo [vytvořte soubor parameters.json,](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) který určuje názvy prostředků.

Chcete-li vytvořit prostředí ze šablony, použijte [vytvoření nasazení skupiny az](/cli/azure/group/deployment) následujícím způsobem:

```azurecli
az group deployment create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

Můžete si přečíst [další informace o tom, jak nasadit ze šablon](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Přečtěte si, jak postupně aktualizovat prostředí, používat soubor parametrů a přistupovat k šablonám z jednoho umístění úložiště.

## <a name="next-steps"></a>Další kroky
Teď jste připraveni začít pracovat s více síťovými součástmi a virtuálními počítači. Toto ukázkové prostředí můžete použít k vytvoření aplikace pomocí základních součástí zavedených zde.
