---
title: "Šifrování disku sadách škálování virtuálního počítače Azure | Microsoft Docs"
description: "Další informace o použití prostředí Azure PowerShell k šifrování instance virtuálních počítačů a připojené disky v sady škálování virtuálního počítače"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: iainfou
ms.openlocfilehash: 856d4bc7dd636b3a2f3d072a10989cafd7efd6a6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>Šifrování operačního systému a připojené datové disky v škálovací sadu virtuálních počítačů
K ochraně a ochrany dat v klidovém stavu pomocí technologie oborový standard šifrování, podporují sady škálování virtuálního počítače Azure Disk Encryption (ADE). Šifrování lze povolit pro Windows a Linux virtuálního počítače sady škálování. Další informace najdete v tématu [Azure Disk Encryption pro systém Windows a Linux](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  Azure Disk Encryption pro sady škálování virtuálního počítače je aktuálně ve verzi preview, k dispozici ve všech oblastech Azure veřejné. 
>
> Operace obnovení z Image a upgrade sady virtuálních počítačů škálování nejsou podporovány v aktuální verzi preview. Ve verzi preview šifrovací sada škálování se doporučuje jenom v testovacích prostředích. Ve verzi preview nepovolujte šifrování disku v produkčním prostředí, kde budete muset upgradovat image operačního systému.

Šifrování Azure disku je podporován:
- pro škálování sady vytvořené s spravovaných disků a nejsou podporovány pro sady škálování nativní (nebo nespravovaný) disku.
- pro svazky operačního systému a dat v systému Windows sady škálování. Zakázat šifrování je podporované pro svazky operačního systému a dat pro sady škálování systému Windows.
- pro datové svazky v sadách škálování Linux. Šifrování disku operačního systému není podporován v aktuální verzi preview pro Linux sady škálování.


## <a name="prerequisites"></a>Požadavky
Tento článek vyžaduje prostředí Azure PowerShell verze modulu 5.3.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

Registrace vaší Azure subsription pro verze preview šifrování disku pro virtuální počítač s více sadami s [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature): 

```powershell
Login-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Počkejte přibližně 10 minut, dokud *registrovaná* stavu je vrácený [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature), znovu registrovat `Microsoft.Compute` zprostředkovatele: 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```


## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Vytvoření Azure Key Vault povolené šifrování disku
Azure Key Vault můžete uložit klíčů, tajné klíče nebo hesla, které vám umožní bezpečně je implementovat v vašim aplikacím a službám. Kryptografické klíče ukládají v Azure Key Vault pomocí ochrany proti softwaru, nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovány pro FIPS 140-2 úroveň 2 standardů. Tyto klíče se používají k šifrování a dešifrování virtuálních disků připojených k virtuálnímu počítači. Uchování kontroly nad těchto kryptografické klíče a můžete auditovat jejich použití.

Vytvoření trezoru klíčů s [nové AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Chcete-li povolit Key Vault, který se má použít pro šifrování disku, nastavte *EnabledForDiskEncryption* parametr. V následujícím příkladu definuje také proměnných pro název skupiny prostředků, název pro Key Vault a umístění. Zadejte svůj vlastní jedinečný název pro Key Vault:

```powershell
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzureRmResourceGroup -Name $rgName -Location $location
New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```


### <a name="use-an-existing-key-vault"></a>Použít existující Key Vault
Tento krok jenom je potřeba, pokud máte existující Key Vault, kterou chcete použít s šifrování disku. Tento krok přeskočte, pokud jste vytvořili v předchozí části Key Vault.

Existující trezor klíč ve stejném předplatném, oblasti můžete povolit jako měřítka nastavit šifrování disku s [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/AzureRM.KeyVault/Set-AzureRmKeyVaultAccessPolicy). Zadejte název existující Key Vault ve *$vaultName* proměnná následujícím způsobem:

```powershell
$vaultName="myexistingkeyvault"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```


## <a name="create-a-scale-set"></a>Vytvoření škálovací sady
Nastavte nejprve, správce, uživatelské jméno a heslo pro instance virtuálních počítačů s [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Teď vytvořte škálovací sadu virtuálních počítačů pomocí rutiny [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). K distribuci přenosů k jednotlivým instancím virtuálních počítačů, se také vytvoří nástroj pro vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení obsahuje pravidla, která distribuovat přenosy na portu TCP 80, jakož i povolit vzdálené plochy přenosy na portu TCP 3389 a vzdálenou komunikaci prostředí PowerShell na portu TCP 5985:

```powershell
$vmssName="myScaleSet"

New-AzureRmVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```


## <a name="enable-encryption"></a>Povolit šifrování
K šifrování instancí virtuálního počítače ve škálovací sadě, nejdřív získejte některé informace o Identifikátor URI trezoru klíčů a prostředky s [Get-AzureRmKeyVault](/powershell/module/AzureRM.KeyVault/Get-AzureRmKeyVault). Tyto proměnné se používají k spusťte proces šifrování s [Set-AzureRmVmssDiskEncryptionExtension](/powershell/module/AzureRM.Compute/Set-AzureRmVmssDiskEncryptionExtension):

```powershell
$diskEncryptionKeyVaultUrl=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

Po zobrazení výzvy zadejte *y* Chcete-li pokračovat, proces šifrování disku na měřítka nastavit počítač instance.


## <a name="check-encryption-progress"></a>Kontrola průběhu šifrování
Chcete-li zkontrolovat stav šifrování disku, použijte [Get-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Get-AzureRmVmssDiskEncryption):

```powershell
Get-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Šifrovaná instance virtuálních počítačů *EncryptionSummary* code sestavy *byla úspěšná nebo ProvisioningState* jak ukazuje následující příklad výstupu:

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```


## <a name="disable-encryption"></a>Zakázat šifrování
Pokud již nechcete používat šifrované disky instancí virtuálních počítačů, můžete zakázat šifrování s [zakázat AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Disable-AzureRmVmssDiskEncryption) následujícím způsobem:

```powershell
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```


## <a name="next-steps"></a>Další kroky
V tomto článku jste použili prostředí Azure PowerShell k šifrování škálovací sadu virtuálních počítačů. Můžete také [Azure CLI 2.0](virtual-machine-scale-sets-encrypt-disks-cli.md) nebo šablon pro [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) nebo [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
