---
title: Šifrování disků pro sadách škálování Azure pomocí prostředí Azure PowerShell | Microsoft Docs
description: Další informace o použití prostředí Azure PowerShell k šifrování instance virtuálních počítačů a připojené disky v sadě škálování virtuálního počítače Windows
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: iainfou
ms.openlocfilehash: 91138ffad0fd906061e0b0ce5cdb251f402d3341
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell-preview"></a>Šifrování operačního systému a připojené datové disky v škálování virtuálních počítačů, nastavit pomocí prostředí Azure PowerShell (Preview)

K ochraně a ochrany dat v klidovém stavu pomocí technologie oborový standard šifrování, podporují sady škálování virtuálního počítače Azure Disk Encryption (ADE). Šifrování lze povolit pro Windows a Linux virtuálního počítače sady škálování. Další informace najdete v tématu [Azure Disk Encryption pro systém Windows a Linux](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  Azure disk encryption pro sady škálování virtuálního počítače je aktuálně ve verzi public preview, k dispozici ve všech oblastech Azure veřejné.

Šifrování Azure disku je podporován:
- pro škálování sady vytvořené s spravovaných disků a nejsou podporovány pro sady škálování nativní (nebo nespravovaný) disku.
- pro svazky operačního systému a dat v systému Windows sady škálování. Zakázat šifrování je podporované pro svazky operačního systému a dat pro sady škálování systému Windows.
- pro datové svazky v sadách škálování Linux. Šifrování disku operačního systému není podporován v aktuální verzi preview pro Linux sady škálování.

Operace obnovení z Image a upgrade sady virtuálních počítačů škálování nejsou podporovány v aktuální verzi preview. Azure disk šifrování pro náhled sady škálování virtuálního počítače se doporučuje jenom v testovacích prostředích. Ve verzi preview nepovolujte šifrování disku v produkčním prostředí, kde je třeba upgradovat image operačního systému v sadě škálování šifrované.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud si zvolíte instalaci a použití prostředí PowerShell místně, tento kurz vyžaduje prostředí Azure PowerShell verze modulu 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="register-for-disk-encryption-preview"></a>Registrace pro náhled šifrování disku

Azure disk encryption pro verzi preview sadách škálování virtuálních počítačů vyžaduje, abyste registrovat předplatné s [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature). Potřebujete jenom poprvé, že používáte funkci náhledu šifrování disku proveďte následující kroky:

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

To může trvat až 10 minut, než požadavek na rozšíření. Můžete zkontrolovat stav registrace s [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Když `RegistrationState` sestavy *registrovaná*, znovu zaregistrovat *Mirosoft.Compute* zprostředkovatele s [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Vytvoření Azure Key Vault povolené šifrování disku

Azure Key Vault můžete uložit klíčů, tajné klíče nebo hesla, které vám umožní bezpečně je implementovat v vašim aplikacím a službám. Kryptografické klíče ukládají v Azure Key Vault pomocí ochrany proti softwaru, nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovány pro FIPS 140-2 úroveň 2 standardů. Tyto klíče se používají k šifrování a dešifrování virtuálních disků připojených k virtuálnímu počítači. Uchování kontroly nad těchto kryptografické klíče a můžete auditovat jejich použití.

Vytvoření trezoru klíčů s [nové AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Chcete-li povolit Key Vault, který se má použít pro šifrování disku, nastavte *EnabledForDiskEncryption* parametr. V následujícím příkladu definuje také proměnných pro název skupiny prostředků, název pro Key Vault a umístění. Zadejte svůj vlastní jedinečný název pro Key Vault:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzureRmResourceGroup -Name $rgName -Location $location
New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Použít existující Key Vault

Tento krok jenom je potřeba, pokud máte existující Key Vault, kterou chcete použít s šifrování disku. Tento krok přeskočte, pokud jste vytvořili v předchozí části Key Vault.

Existující trezor klíč ve stejném předplatném, oblasti můžete povolit jako měřítka nastavit šifrování disku s [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/AzureRM.KeyVault/Set-AzureRmKeyVaultAccessPolicy). Zadejte název existující Key Vault ve *$vaultName* proměnná následujícím způsobem:

```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Vytvoření škálovací sady

Nejprve pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce instancí virtuálních počítačů:

```azurepowershell-interactive
$cred = Get-Credential
```

Teď vytvořte škálovací sadu virtuálních počítačů pomocí rutiny [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Za účelem distribuce provozu do jednotlivých instancí virtuálních počítačů se vytvoří také nástroj pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení obsahuje pravidla pro distribuci provozu na portu TCP 80, stejně jako provozu vzdálené plochy na portu TCP 3389 a vzdálené komunikace PowerShellu na portu TCP 5985:

```azurepowershell-interactive
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

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

Po zobrazení výzvy zadejte *y* Chcete-li pokračovat, proces šifrování disku na měřítka nastavit počítač instance.

## <a name="check-encryption-progress"></a>Kontrola průběhu šifrování

Chcete-li zkontrolovat stav šifrování disku, použijte [Get-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Get-AzureRmVmssDiskEncryption):

```azurepowershell-interactive
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

```azurepowershell-interactive
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Další postup

V tomto článku jste použili prostředí Azure PowerShell k šifrování škálovací sadu virtuálních počítačů. Můžete také [Azure CLI 2.0](virtual-machine-scale-sets-encrypt-disks-cli.md) nebo šablon pro [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) nebo [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
