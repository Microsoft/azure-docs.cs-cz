---
title: "Nakonfigurovat privátní IP adresy pro virtuální počítače - rozhraní příkazového řádku Azure | Microsoft Docs"
description: "Zjistěte, jak nakonfigurovat privátní IP adresy pro virtuální počítače pomocí rozhraní příkazového řádku Azure (CLI)."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a9fe7020719079e11150c62068650aa6ca17b056
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Nakonfigurovat privátní IP adresy pro virtuální počítač pomocí rozhraní příkazového řádku Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Resource Manager. Můžete také [spravovat statickou privátní IP adresou v modelu nasazení classic](virtual-networks-static-private-ip-classic-cli.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> Následující vzorové příkazy příkazového řádku Azure CLI očekávat stávajícího jednoduché prostředí. Pokud chcete ke spuštění příkazů, jak jsou zobrazeny v tomto dokumentu, nejprve vytvořit testovací prostředí popsané v [vytvoření virtuální sítě](quick-create-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Při vytváření virtuálního počítače zadat statickou privátní IP adresu

Vytvoření virtuálního počítače s názvem *DNS01* v *front-endu* podsíť virtuální sítě s názvem *TestVNet* se statickou privátní IP z *192.168.1.101*, dokončení Následující kroky:

1. Pokud nebyly dosud, nainstalovat a nakonfigurovat nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) a přihlaste se k Azure účet pomocí [az přihlášení](/cli/azure/reference-index#az_login). 

2. Vytvoření veřejné IP adresy pro virtuální počítač s [vytvoření veřejné sítě az-ip](/cli/azure/network/public-ip#az_network_public_ip_create) příkaz. Seznam uvedený za výstupem vysvětluje použité parametry.

    > [!NOTE]
    > Vám může potřebujete nebo chcete použít různé hodnoty pro vaše argumenty v tomto a dalších krocích, v závislosti na vašem prostředí.
   
    ```azurecli
    az network public-ip create \
    --name TestPIP \
    --resource-group TestRG \
    --location centralus \
    --allocation-method Static
    ```

    Očekávaný výstup:
   
   ```json
   {
        "publicIp": {
            "idleTimeoutInMinutes": 4,
            "ipAddress": "52.176.43.167",
            "provisioningState": "Succeeded",
            "publicIPAllocationMethod": "Static",
            "resourceGuid": "79e8baa3-33ce-466a-846c-37af3c721ce1"
        }
    }
    ```

   * `--resource-group`: Název skupiny prostředků, ve kterém k vytvoření veřejné IP adresy.
   * `--name`: Název veřejné IP adresy.
   * `--location`: Oblast azure, ve kterém k vytvoření veřejné IP adresy.

3. Spustit [vytvořit síťových adaptérů sítě az](/cli/azure/network/nic#az_network_nic_create) příkaz pro vytvoření síťový adaptér se statickou privátní IP. Seznam uvedený za výstupem vysvětluje použité parametry. 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    Očekávaný výstup:
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    Parametry:

    * `--private-ip-address`: Statickou privátní IP adresou pro síťovou kartu.
    * `--vnet-name`: Název sítě VNet, ve které chcete vytvořit síťovou kartu.
    * `--subnet`: Název podsítě, ve které chcete vytvořit síťovou kartu.

4. Spustit [vytvoření virtuálního počítače azure](/cli/azure/vm/nic#az_vm_nic_create) příkaz pro vytvoření virtuálního počítače pomocí veřejné IP adresy a síťové karty, které jste vytvořili dříve. Seznam uvedený za výstupem vysvětluje použité parametry.
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    Očekávaný výstup:
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   Parametry než základní [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) parametry.

   * `--nics`: Název síťové karty, ke kterému je připojený virtuální počítač.
   

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Načíst statickou privátní IP adresu informace pro virtuální počítač

Pomocí následujícího příkazu příkazového řádku Azure CLI sledovat hodnoty *alokační metody privátní IP* a *privátní IP adresa*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Očekávaný výstup:

```json
"192.168.1.101"
```

Pokud chcete zobrazit konkrétní informace o IP síťového adaptéru pro tento virtuální počítač, konkrétně dotaz na síťový adaptér:

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

Výstup bude podobný tomuto:

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Odeberte statickou privátní IP adresu z virtuálního počítače

Statickou privátní IP adresu nelze odebrat z síťový adaptér v Azure CLI pro nasazení Azure Resource Manager. Postupujte takto:
- Vytvořte novou síťovou kartu, která používá dynamický IP
- Nastavení síťového adaptéru ve virtuálních počítačů na nově vytvořený síťový adaptér. 

Chcete-li změnit síťový adaptér pro virtuální počítač používá v předchozích příkazech, proveďte následující kroky:

1. Spustit **síťových adaptérů sítě azure vytvořit** příkaz pro vytvoření nové síťové karty pomocí dynamické přidělování IP adres s novou IP adresu. Vzhledem k tomu, že je zadána žádná IP adresa, je metoda přidělení **dynamické**.

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    Očekávaný výstup:

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. Spustit **sada virtuálních počítačů azure** příkazu změníte na síťový adaptér použit ve virtuálním počítači.
   
    ```azurecli
    azure vm set -g TestRG -n DNS01 -N TestNIC2
    ```

    Očekávaný výstup:
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > Pokud je dostatečně velký pro mít více než jeden síťový adaptér virtuálního počítače, spusťte **odstranit síťových adaptérů sítě azure** příkaz k odstranění původního síťový adaptér.
   
## <a name="next-steps"></a>Další postup
* Další informace o [vyhrazené veřejné IP adresy](virtual-networks-reserved-public-ip.md) adresy.
* Další informace o [veřejné IP (splnění) na úrovni instance](virtual-networks-instance-level-public-ip.md) adresy.
* Obrátit [vyhrazené IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx).

