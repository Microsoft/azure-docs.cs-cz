---
title: Konfigurace privátních IP adres pro virtuální počítače – rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat privátní IP adresy pro virtuální počítače pomocí rozhraní příkazového řádku Azure (CLI).
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
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
ms.openlocfilehash: a6e217194508feae3b227b5ef65b02d0305a22a7
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852894"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Konfigurace privátních IP adres pro virtuální počítač pomocí Azure CLI

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Resource Manager. Můžete také [spravovat statickou privátní IP adresou v modelu nasazení classic](virtual-networks-static-private-ip-classic-cli.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> Následující vzorové příkazy Azure CLI můžete očekávat existující jednoduché prostředí. Pokud chcete spustit příkazy, jak jsou zobrazeny v tomto dokumentu, nejprve vytvořit testovací prostředí popsané v [vytvořit síť vnet](quick-create-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Při vytváření virtuální počítač zadat statickou privátní IP adresou

Vytvoření virtuálního počítače s názvem *DNS01* v *front-endu* podsíti virtuální sítě s názvem *TestVNet* se statickou privátní IP adresou z *192.168.1.101*, dokončení Následující kroky:

1. Pokud nebyly dosud, nainstalovat a nakonfigurovat na nejnovější verzi [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) a přihlaste se k Azure pomocí účtu [az login](/cli/azure/reference-index#az_login).

2. Vytvoření veřejné IP adresy pro virtuální počítač s [az network public-ip vytvořit](/cli/azure/network/public-ip) příkazu. Seznam uvedený za výstupem vysvětluje použité parametry.

    > [!NOTE]
    > Možná chcete nebo muset použít jiné hodnoty pro vaše argumenty v tomto a dalších krocích, v závislosti na vašem prostředí.

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

   * `--resource-group`: Název skupiny prostředků, ve kterém chcete vytvořit veřejnou IP adresu.
   * `--name`: Název veřejné IP adresy.
   * `--location`: Oblast Azure, ve kterém chcete vytvořit veřejnou IP adresu.

3. Spustit [az network nic vytvořit](/cli/azure/network/nic#az_network_nic_create) příkazu vytvořte síťové rozhraní se statickou privátní IP adresou. Seznam uvedený za výstupem vysvětluje použité parametry. 
   
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

    * `--private-ip-address`: Statické privátní IP adresu pro síťové rozhraní
    * `--vnet-name`: Název sítě VNet, ve kterém chcete vytvořit síťové rozhraní
    * `--subnet`: Název podsítě, ve kterém chcete vytvořit síťové rozhraní

4. Spustit [vytvořit virtuální počítač azure](/cli/azure/vm/nic#az_vm_nic_create) příkaz pro vytvoření virtuálního počítače pomocí veřejné IP adresy a síťové rozhraní, které jste předtím vytvořili. Seznam uvedený za výstupem vysvětluje použité parametry.
   
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
   
   Parametry, než na úrovni basic [az vm vytvořit](/cli/azure/vm#az_vm_create) parametry.

   * `--nics`: Název síťového adaptéru, ke kterému je připojený virtuální počítač.
   
Doporučuje se, že nepřiřadíte staticky privátní IP adresa přiřazená virtuální počítač Azure v rámci operačního systému virtuálního počítače, není-li nezbytné, například kdy [přidělení více IP adres k virtuálnímu počítači s Windows](virtual-network-multiple-ip-addresses-cli.md). Pokud jste ručně nastavili privátní IP adresu v rámci operačního systému, ujistěte se, že se jedná o stejné adrese jako privátní IP adresy přiřazené k Azure [síťové rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings), nebo může ztratit připojení k virtuálnímu počítači. Další informace o [privátní IP adresu](virtual-network-network-interface-addresses.md#private) nastavení.

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Načíst statické privátní informace o IP adrese virtuálního počítače

Spusťte následující příkaz rozhraní příkazového řádku Azure sledovat hodnoty *alloc – metoda privátní IP* a *privátní IP adresa*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Očekávaný výstup:

```json
"192.168.1.101"
```

Chcete-li zobrazit konkrétní informace o IP síťové karty pro tento virtuální počítač, dotaz NIC konkrétně:

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

Statickou privátní IP adresu nelze odebrat ze síťové rozhraní v rozhraní příkazového řádku Azure pro nasazení Azure Resource Manageru. Musíte mít:
- Vytvořit nový síťový adaptér, který používá dynamickou IP
- Nastavení síťového rozhraní na virtuálním počítači nově vytvořené síťové rozhraní 

Chcete-li změnit síťového adaptéru pro virtuální počítač používaný v předchozích příkazech, proveďte následující kroky:

1. Spustit **vytvořit azure network nic** příkaz pro vytvoření nového síťového rozhraní pomocí dynamické přidělování IP adres s novou IP adresu. Protože není zadána žádná IP adresa, metoda přidělení je **dynamické**.

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

2. Spustit **sada virtuálních počítačů azure** příkaz, který změní síťové KARTĚ, virtuální počítač využívá.
   
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
    > Pokud virtuální počítač je dostatečně velký, aby více než jedním síťovým rozhraním, spusťte **azure network nic delete** příkaz Odstranit staré síťové rozhraní

## <a name="next-steps"></a>Další postup

Další informace o správě [nastavení IP adresy](virtual-network-network-interface-addresses.md).
