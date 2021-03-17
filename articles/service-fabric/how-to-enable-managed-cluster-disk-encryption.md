---
title: Povolit šifrování disku pro Service Fabric uzly spravovaného clusteru (Preview)
description: Naučte se, jak povolit šifrování disku pro uzly spravovaného clusteru Azure Service Fabric v systému Windows pomocí šablony ARM.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: b7e56ff8db9f94b8c6681a1a7d69a4751b3f43a5
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642406"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-preview-nodes"></a>Povolit šifrování disku pro Service Fabric uzly spravovaného clusteru (Preview)

V této příručce se dozvíte, jak povolit šifrování disku na Service Fabric spravovaných uzlech clusteru ve Windows pomocí [Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md) funkce pro služby [Virtual Machine Scale sets](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md) prostřednictvím šablon Azure Resource Manager (ARM).

> [!IMPORTANT]
> Sada Virtual Machine Scale Encryption disk Preview zatím nepodporuje upgrade nebo obnovení bitové kopie. Nepoužívejte, pokud budete muset upgradovat bitovou kopii operačního systému.

## <a name="register-for-azure-disk-encryption"></a>Zaregistrujte se Azure Disk Encryption

Náhled šifrování disku pro sadu škálování virtuálního počítače vyžaduje samoobslužnou registraci. Spusťte následující příkaz:

```powershell
Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
```

Ověřte stav registrace spuštěním:

```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
```

Jakmile se stav změní na *registrováno*, budete připraveni pokračovat.

## <a name="provision-a-key-vault-for-disk-encryption"></a>Zřízení Key Vault pro šifrování disků

Azure Disk Encryption vyžaduje Azure Key Vault k řízení a správě šifrovacích klíčů a tajných klíčů disku. Váš Key Vault a Service Fabric spravovaného clusteru se musí nacházet ve stejné oblasti a předplatném Azure. Pokud jsou tyto požadavky splněny, můžete použít novou nebo existující Key Vault tím, že ji povolíte pro šifrování disku.

### <a name="create-key-vault-with-disk-encryption-enabled"></a>Vytvoření Key Vault se zapnutým šifrováním disku

Spuštěním následujících příkazů vytvořte novou Key Vault pro šifrování disku. Ujistěte se, že je oblast pro váš Key Vault [podporovaná pro Service Fabric spravované clustery](faq-managed-cluster.md#what-regions-are-supported-in-the-preview) a že je ve stejné oblasti jako váš cluster.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

New-AzResourceGroup -Name $resourceGroupName -Location eastus2 
New-AzKeyVault -ResourceGroupName $resourceGroupName -Name $keyvaultName -Location eastus2 -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

az keyvault create --resource-group $resourceGroupName --name $keyvaultName --enabled-for-disk-encryption
```

---

### <a name="update-existing-key-vault-to-enable-disk-encryption"></a>Pokud chcete povolit šifrování disku, aktualizujte existující Key Vault.

Spuštěním následujících příkazů Povolte šifrování disku pro existující Key Vault.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# ps 

Set-AzKeyVaultAccessPolicy -ResourceGroupName $resourceGroupName -VaultName $keyvaultName -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az keyvault update --name keyvaultName --enabled-for-disk-encryption 
```

---

## <a name="update-the-template-and-parameters-files-for-disk-encryption"></a>Aktualizace šablon a souborů parametrů pro šifrování disku

Následující krok vás provede změnou požadované šablony a povolí šifrování disku ve [stávajícím spravovaném clusteru](tutorial-managed-cluster-deploy.md). Alternativně můžete nasadit nový Service Fabric spravovaný cluster s povoleným šifrováním disku pomocí této šablony: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption

1. Do šablony přidejte následující parametry, kde nahradíte vlastní předplatné, název skupiny prostředků a název trezoru `keyVaultResourceId` .

```json
"parameters": { 
…
    "keyVaultResourceId": { 
        "type": "string", 
        "defaultValue": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>", 
        "metadata": { 
            "description": "Full resource id of the Key Vault used for disk encryption." 
        } 
    },
    "volumeType": { 
        "type": "string", 
        "defaultValue": "All", 
        "metadata": { 
            "description": "Type of the volume OS or Data to perform encryption operation" 
        }
    }
}, 
```

2. Dále přidejte `AzureDiskEncryption` rozšíření virtuálního počítače do typů uzlů spravovaného clusteru v šabloně:

```json
"properties": { 
    "vmExtensions": [ 
        { 
            "name": "AzureDiskEncryption", 
            "properties": { 
                "publisher": "Microsoft.Azure.Security", 
                "type": "AzureDiskEncryption", 
                "typeHandlerVersion": "2.1", 
                "autoUpgradeMinorVersion": true, 
                "settings": {                     
                    "EncryptionOperation": "EnableEncryption", 
                    "KeyVaultURL": "[reference(parameters('keyVaultResourceId'),'2016-10-01').vaultUri]", 
                    "KeyVaultResourceId": "[parameters('keyVaultResourceID')]",
                    "VolumeType": "[parameters('volumeType')]" 
                } 
            } 
        } 
    ] 
} 
```

3. Nakonec aktualizujte soubor parametrů, nahraďte své vlastní předplatné, skupinu prostředků a název trezoru klíčů v *keyVaultResourceId*:

```json
"parameters": { 
    … 
    "keyVaultResourceId": { 
        "value": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>" 
    },   
    "volumeType": { 
        "value": "All" 
    }    
} 
```

## <a name="deploy-and-verify-the-changes"></a>Nasazení a ověření změn

Až budete připraveni, nasaďte změny, aby se povolilo šifrování disku na spravovaném clusteru.

```powershell
$clusterName = "<clustername>" 

New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\template_diskEncryption.json -TemplateParameterFile \.parameters_diskEncryption.json -Debug -Verbose 
```

Můžete kontrolovat stav šifrování disku u základní sady škálování typu uzlu pomocí `Get-AzVmssDiskEncryption` příkazu. Nejdřív budete potřebovat najít název podpůrné skupiny prostředků spravovaného clusteru (obsahující základní virtuální síť, nástroj pro vyrovnávání zatížení, veřejnou IP adresu, NSG, sady škálování a účty úložiště). Nezapomeňte upravit `VmssName` na libovolný název typu uzlu clusteru, který chcete zkontrolovat (jak je uvedeno v šabloně nasazení).

```powershell
$VmssName = "NT1"
$supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName).ClusterId
Get-AzVmssDiskEncryption -ResourceGroupName $supportResourceGroupName -VMScaleSetName $VmssName
```

Výstup by měl vypadat přibližně takto:

```console
ResourceGroupName            : SFC_########-####-####-####-############
VmScaleSetName               : NT1
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="next-steps"></a>Další kroky

[Ukázka: SKU Standard Service Fabric Managed cluster, 1 typ uzlu s povoleným šifrováním disku](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[Azure Disk Encryption pro virtuální počítače s Windows](../virtual-machines/windows/disk-encryption-overview.md)

[Zašifrujte Virtual Machine Scale Sets pomocí Azure Resource Manager](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
