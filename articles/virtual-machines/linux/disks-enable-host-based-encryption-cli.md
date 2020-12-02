---
title: Povolení kompletního šifrování pomocí šifrování na hostiteli – Azure CLI – spravované disky
description: Pomocí šifrování na hostiteli můžete na Azure Managed disks povolit komplexní šifrování.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 5c0336b80bee1cd5eb76d0ce3d5f99c7296a8467
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499760"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Použití rozhraní příkazového řádku Azure k povolení kompletního šifrování pomocí šifrování na hostiteli

Pokud povolíte šifrování na hostiteli, data uložená na hostiteli virtuálního počítače se zašifrují v klidovém stavu a toky se zašifrují do služby úložiště. Koncepční informace o šifrování na hostiteli a také o dalších typech šifrování spravovaného disku najdete v tématu [šifrování v rámci hostitele – koncové šifrování pro data virtuálních počítačů](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Omezení

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Podporované oblasti

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Podporované velikosti virtuálních počítačů

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Velikosti virtuálních počítačů můžete zjistit také programově. Informace o tom, jak je načíst programově, najdete v části [hledání podporovaných velikostí virtuálních počítačů](#finding-supported-vm-sizes) .

## <a name="prerequisites"></a>Předpoklady

Aby bylo možné používat šifrování na hostiteli pro vaše virtuální počítače nebo služby Virtual Machine Scale Sets, musíte ve svém předplatném mít povolenou funkci. Odesláním e-mailu encryptionAtHost@microsoft.com s ID předplatného získáte funkci povolenou pro vaše předplatná.

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Vytvoření Azure Key Vault a DiskEncryptionSet

Jakmile je tato funkce povolená, budete muset nastavit Azure Key Vault a DiskEncryptionSet, pokud jste to ještě neudělali.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>Příklady

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Vytvořte virtuální počítač se šifrováním na hostiteli, který je povolený pomocí klíčů spravovaných zákazníkem. 

Vytvořte virtuální počítač se spravovanými disky pomocí identifikátoru URI prostředku DiskEncryptionSet vytvořeného dříve pro šifrování mezipaměti operačních systémů a datových disků pomocí klíčů spravovaných zákazníkem. Dočasné disky se šifrují pomocí klíčů spravovaných platformou. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 128 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Vytvořte virtuální počítač se šifrováním na hostiteli, který je povolený pomocí klíčů spravovaných platformou. 

Vytvořte virtuální počítač s povoleným šifrováním na hostiteli, aby se zašifroval mezipaměť s operačním systémem/datovými disky a dočasné disky pomocí klíčů spravovaných platformou. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 128 \
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>Aktualizujte virtuální počítač, aby se povolilo šifrování na hostiteli. 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>Ověření stavu šifrování na hostiteli pro virtuální počítač

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Vytvořte sadu škálování virtuálního počítače s šifrováním na hostiteli, který je povolený pomocí klíčů spravovaných zákazníkem. 

Pomocí identifikátoru URI prostředku DiskEncryptionSet vytvořeného dříve vytvořte v sadě prostředků škálování virtuálního počítače se službou Managed disks a Zašifrujte mezipaměť operačních systémů a datových disků pomocí klíčů spravovaných zákazníkem. Dočasné disky se šifrují pomocí klíčů spravovaných platformou. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 64 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Vytvořte sadu škálování virtuálního počítače s šifrováním na hostiteli, který je povolený pomocí klíčů spravovaných platformou. 

Vytvořte sadu škálování virtuálního počítače s šifrováním na hostiteli povoleno pro šifrování mezipaměti s operačním systémem/datovými disky a dočasné disky pomocí klíčů spravovaných platformou. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--data-disk-sizes-gb 64 128 \
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>Pokud chcete povolit šifrování na hostiteli, aktualizujte sadu škálování virtuálního počítače. 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>Ověřte stav šifrování na hostiteli pro sadu škálování virtuálního počítače.

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

## <a name="finding-supported-vm-sizes"></a>Hledání podporovaných velikostí virtuálních počítačů

Starší verze virtuálních počítačů se nepodporují. Seznam podporovaných velikostí virtuálních počítačů najdete v těchto verzích:

Volání [rozhraní API SKU prostředku](/rest/api/compute/resourceskus/list) a kontrola, zda `EncryptionAtHostSupported` je funkce nastavena na **hodnotu true**.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

Nebo zavolejte rutinu PowerShellu [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0) .

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili a nakonfigurovali tyto prostředky, je můžete použít k zabezpečení svých spravovaných disků. Následující odkaz obsahuje ukázkové skripty, z nichž každý má odpovídající scénář, který můžete použít k zabezpečení svých spravovaných disků.

[Ukázky šablon Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)