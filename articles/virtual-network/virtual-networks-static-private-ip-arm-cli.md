---
title: Konfigurace privátních IP adres pro virtuální počítače – Azure CLI
description: Přečtěte si, jak nakonfigurovat privátní IP adresy pro virtuální počítače pomocí rozhraní příkazového řádku Azure (CLI).
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: allensu
ms.openlocfilehash: c34ab73422d8dd41feb9da542ed63fdba060fe3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84708157"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Konfigurace privátních IP adres pro virtuální počítač pomocí Azure CLI


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> Následující ukázkové příkazy Azure CLI očekávají existující jednoduché prostředí. Pokud chcete spustit příkazy, které se zobrazují v tomto dokumentu, nejdříve Sestavte testovací prostředí popsané v tématu [vytvoření virtuální](quick-create-cli.md)sítě.

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Při vytváření virtuálního počítače zadejte statickou privátní IP adresu.

Pokud chcete vytvořit virtuální počítač s názvem *DNS01* v podsíti *front-endu* virtuální sítě s názvem *TestVNet* se statickou privátní IP adresou *192.168.1.101*, proveďte následující kroky:

1. Pokud jste to ještě neudělali, nainstalujte a nakonfigurujte nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a přihlaste se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index).

2. Spuštěním příkazu [AZ Network nic Create](/cli/azure/network/nic) vytvořte síťovou kartu se statickou privátní IP adresou. Seznam uvedený za výstupem vysvětluje použité parametry. 
   
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

    * `--private-ip-address`: Statická privátní IP adresa pro síťovou kartu.
    * `--vnet-name`: Název virtuální sítě, ve které chcete vytvořit síťovou kartu.
    * `--subnet`: Název podsítě, ve které chcete vytvořit síťovou kartu.

3. Spuštěním příkazu [Azure VM Create](/cli/azure/vm/nic) vytvořte virtuální počítač pomocí veřejné IP adresy a síťového rozhraní, které jste vytvořili dříve. Seznam uvedený za výstupem vysvětluje použité parametry.
   
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
   
   Parametry jiné než základní [AZ VM Create](/cli/azure/vm) Parameters.

   * `--nics`: Název síťového rozhraní, ke kterému je připojený virtuální počítač.
   
Doporučuje se, abyste privátní IP adresu přiřazenou k virtuálnímu počítači Azure v operačním systému virtuálního počítače nepřiřadili staticky, pokud to není nutné, například při [přiřazování více IP adres k virtuálnímu počítači s Windows](virtual-network-multiple-ip-addresses-cli.md). Pokud ručně nastavíte privátní IP adresu v operačním systému, ujistěte se, že se jedná o stejnou adresu jako soukromá IP adresa přiřazená [síťovému rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings)Azure, nebo můžete ztratit připojení k virtuálnímu počítači. Přečtěte si další informace o nastavení [privátních IP adres](virtual-network-network-interface-addresses.md#private) .

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Načtení statických informací o privátních IP adresách pro virtuální počítač

Spuštěním následujícího příkazu rozhraní příkazového řádku Azure Sledujte hodnoty pro *IP adresu alokace* a privátní IP *adresy*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Očekávaný výstup:

```json
"192.168.1.101"
```

Pokud chcete zobrazit konkrétní informace o IP adrese síťového rozhraní pro daný virtuální počítač, Dotazujte se konkrétně na síťovou kartu:

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

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Odebrání statické privátní IP adresy z virtuálního počítače

Nemůžete odebrat statickou privátní IP adresu z síťové karty v Azure CLI pro Azure Resource Manager nasazení. Musíte:
- Vytvoření nové síťové karty, která používá dynamickou IP adresu
- Nastavte síťové rozhraní na virtuálním počítači nově vytvořenou síťovou kartu. 

Chcete-li změnit síťovou kartu pro virtuální počítač použitý v předchozích příkazech, proveďte následující kroky:

1. Spuštěním příkazu **Azure Network nic Create** vytvořte novou síťovou kartu pomocí dynamického přidělování IP adres s novou IP adresou. Vzhledem k tomu, že není zadána žádná IP adresa, je metoda přidělování **Dynamická**.

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

2. Spuštěním příkazu **Azure VM set** změňte síťové rozhraní používané virtuálním počítačem.
   
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
    > Pokud je virtuální počítač dostatečně velký, aby měl více než jednu síťovou kartu, odstraňte starou síťovou kartu spuštěním příkazu **Azure Network nic Delete** .

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o správě [nastavení IP adres](virtual-network-network-interface-addresses.md).
