---
title: Konfigurace privátních IP adres pro virtuální počítače – rozhraní příkazového příkazu Azure
description: Zjistěte, jak nakonfigurovat privátní IP adresy pro virtuální počítače pomocí rozhraní příkazového řádku Azure (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: kumud
ms.openlocfilehash: f4643aae0b28861f4ddb99d8dace749e62f930b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199474"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Konfigurace privátních IP adres pro virtuální počítač pomocí rozhraní příkazového příkazu Azure


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> Následující ukázkové příkazy Azure CLI očekávají existující jednoduché prostředí. Pokud chcete spustit příkazy tak, jak jsou zobrazeny v tomto dokumentu, nejprve vytvořte testovací prostředí popsané v [příkazu vytvořit virtuální síť](quick-create-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Určení statické privátní IP adresy při vytváření virtuálního počítačů

Chcete-li vytvořit virtuální hovirtuální ho dohledu *dns01* v podsíti *frontendu* virtuální sítě s názvem *TestVNet* se statickou privátní IP adresou *192.168.1.101*, postupujte takto:

1. Pokud jste tak ještě neučinili, nainstalujte a nakonfigurujte nejnovější [rozhraní příkazového příkazu Azure](/cli/azure/install-azure-cli) a přihlaste se k účtu Azure pomocí [přihlášení az](/cli/azure/reference-index).

2. Vytvořte veřejnou IP adresu pro virtuální hosto v příkazu [az network public-ip create](/cli/azure/network/public-ip) . Seznam uvedený za výstupem vysvětluje použité parametry.

    > [!NOTE]
    > Můžete chtít nebo potřebovat použít různé hodnoty pro vaše argumenty v tomto a následných krocích, v závislosti na vašem prostředí.

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

   * `--resource-group`: Název skupiny prostředků, ve které chcete vytvořit veřejnou IP adresu.
   * `--name`: Název veřejné ip adresy.
   * `--location`: Oblast Azure, ve které chcete vytvořit veřejnou IP adresu.

3. Spusťte příkaz [az network nic create](/cli/azure/network/nic) a vytvořte síťovou síť ovou síť se statickou privátní IP adresou. Seznam uvedený za výstupem vysvětluje použité parametry. 
   
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

    * `--private-ip-address`: Statická privátní IP adresa pro nic.
    * `--vnet-name`: Název virtuální sítě, ve které chcete vytvořit nic.
    * `--subnet`: Název podsítě, ve které chcete vytvořit síťovou síť.

4. Spusťte příkaz [azure vm create](/cli/azure/vm/nic) a vytvořte virtuální počítač pomocí veřejné IP a nic vytvořené dříve. Seznam uvedený za výstupem vysvětluje použité parametry.
   
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
   
   Parametry jiné než základní [az vm vytvořit](/cli/azure/vm) parametry.

   * `--nics`: Název nic, ke kterému je připojen virtuální hod.
   
Doporučujeme, abyste staticky nepřiřazovali privátní IP adresu přiřazenou virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače, pokud to není nutné, například při [přiřazování více IP adres virtuálnímu počítači s Windows](virtual-network-multiple-ip-addresses-cli.md). Pokud privátní IP adresu v operačním systému nastavíte ručně, ujistěte se, že se jedná o stejnou adresu jako privátní IP adresa přiřazená [síťovému rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings)Azure , jinak můžete ztratit připojení k virtuálnímu počítači. Přečtěte si další informace o nastavení [privátní IP adresy.](virtual-network-network-interface-addresses.md#private)

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Načtení informací o statické privátní IP adrese pro virtuální hosti

Spusťte následující příkaz příkazu Azure CLI, abyste sledovali hodnoty pro *metodu alloc privátní IP* a *privátní IP adresu*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Očekávaný výstup:

```json
"192.168.1.101"
```

Chcete-li zobrazit konkrétní ip informace o nic pro daný virtuální virtuální ms, dotaz na nic konkrétně:

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

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Odebrání statické privátní IP adresy z virtuálního počítačů

Statickou privátní IP adresu nelze odebrat z nic v azure cli pro nasazení Azure Resource Manager. Musíte:
- Vytvoření nové přenosné jizené mikiny, která používá dynamickou IP adresu
- Nastavte nic na virtuálním počítači dělat nově vytvořené nic. 

Pokud chcete změnit nic pro virtuální hod použitý v předchozích příkazech, proveďte následující kroky:

1. Spusťte příkaz **azure network nic create** a vytvořte novou síťovou síť pomocí dynamického přidělování IP adres s novou IP adresou. Protože není zadána žádná adresa IP, je metoda přidělení **Dynamic**.

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

2. Spusťte příkaz **azure vm set** a změňte nic používanou virtuálním počítačem.
   
    ```azurecli
   az vm nic set --resource-group TestRG --vm-name DNS01 --nics TestNIC2
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
    > Pokud je virtuální počítač dostatečně velký, aby měl více než jednu síťovou síťovou síťovou síťovou síť, **odstraňte** starou síťovou síťovou síťovou síťovou síťovou síťovou síťovou síťovou síťovou.

## <a name="next-steps"></a>Další kroky

Informace o správě [nastavení IP adres](virtual-network-network-interface-addresses.md).
