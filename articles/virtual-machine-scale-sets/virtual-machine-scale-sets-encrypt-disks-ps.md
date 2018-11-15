---
title: Šifrování disků pro Azure škálovací sady pomocí Azure Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak pomocí prostředí Azure PowerShell k šifrování instancí virtuálních počítačů a připojených disků ve škálovací sadě virtuálního počítače Windows
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: f4bf1f35a39042fa38e5d7c7e52c4f8593ffd824
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51683521"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell-preview"></a>Šifrování operačního systému a připojené datové disky ve virtuálním počítači škálovací sady pomocí Azure Powershellu (Preview)

Pro ochranu dat v klidovém stavu pomocí standardní šifrovací technologie, podporují škálovací sady virtuálních počítačů Azure Disk Encryption (ADE). Šifrování je možné povolit pro Windows a Linux virtuálního počítače škálovací sady. Další informace najdete v tématu [Azure Disk Encryption pro Windows a Linuxem](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  Azure disk encryption pro škálovací sady virtuálních počítačů je aktuálně ve verzi public preview, k dispozici ve všech veřejných oblastech Azure.

Azure disk encryption je podporována:
- pro škálovací sady se spravovanými disky vytvořené a není podporováno pro nativní (nebo nespravované) disku škálovací sady.
- pro operační systém a datové svazky ve Windows škálovacích sadách. Zakázat šifrování je podporováno pro operační systém a datové svazky pro Windows škálovací sady.
- pro datové svazky v Linuxu škálovací sady. Šifrování disku operačního systému není podporován v aktuální verzi preview pro Linux škálovací sady.

Škálovací sady virtuálního počítače reimage a upgrade operace nejsou podporovány v aktuální verzi preview. Azure disk encryption pro verzi preview sady škálování virtuálních počítačů se doporučuje jenom v testovacích prostředích. Ve verzi preview Nepovolovat šifrování disku v produkčních prostředích, kde je třeba upgradovat image operačního systému v šifrované škálovací sady.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte v tomto kurzu použít modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="register-for-disk-encryption-preview"></a>Zaregistrovat se na disk encryption ve verzi preview

Azure disk encryption pro škálovací sady virtuálních počítačů ve verzi preview je potřeba registrovat předplatné s [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature). Stačí provést následující kroky při prvním použití funkce ve verzi preview šifrování disku:

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Může trvat až 10 minut žádost o registraci rozšíření. Vy můžete zkontrolovat stav registrace s [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Když `RegistrationState` sestavy *registrované*, přeregistrovat *Microsoft.Compute* zprostředkovatele s [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Vytvoření služby Azure Key Vault nepovoluje šifrování disku

Azure Key Vault umí ukládat klíče, tajné kódy nebo hesla, které umožňují bezpečný je implementovat ve svých aplikacích a službách. Kryptografické klíče jsou uložené ve službě Azure Key Vault software ochrany, nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovaných podle standardů FIPS 140-2 úrovně 2 standardů. Tyto klíče se používají k šifrování a dešifrování virtuální disky připojené k virtuálnímu počítači. Uchování kontroly nad těmito kryptografické klíče a můžete kontrolovat jejich použití.

Vytvoření služby Key Vault pomocí [nové AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Chcete-li povolit služby Key Vault má být použit pro šifrování disků, nastavte *EnabledForDiskEncryption* parametru. Následující příklad také definuje proměnné pro název skupiny prostředků, název trezoru klíčů a umístění. Zadejte vlastním jedinečným názvem služby Key Vault:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzureRmResourceGroup -Name $rgName -Location $location
New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Použít existující služby Key Vault

Tento krok je jenom nutné, pokud máte existující Key Vault, kterou chcete použít šifrování disku. Tento krok přeskočte, pokud jste vytvořili službu Key Vault v předchozí části.

Můžete povolit existující službu Key Vault ve stejném předplatném a oblasti jako škálovací sady pro šifrování disků s [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/AzureRM.KeyVault/Set-AzureRmKeyVaultAccessPolicy). Definovat název existující službu Key Vault v *$vaultName* proměnné následujícím způsobem:

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

K šifrování instancí virtuálních počítačů ve škálovací sadě, nejdřív získejte některé informace o ID identifikátor URI služby Key Vault a prostředek s [Get-AzureRmKeyVault](/powershell/module/AzureRM.KeyVault/Get-AzureRmKeyVault). Tyto proměnné se používají k spusťte proces šifrování s [Set-AzureRmVmssDiskEncryptionExtension](/powershell/module/AzureRM.Compute/Set-AzureRmVmssDiskEncryptionExtension):

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

Po zobrazení výzvy zadejte *y* pokračujte v procesu šifrování disku škálovací nastavení virtuálního počítače instance.

## <a name="check-encryption-progress"></a>Kontrola průběhu šifrování

Chcete-li zkontrolovat stav šifrování disku, použijte [Get-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Get-AzureRmVmssDiskEncryption):

```azurepowershell-interactive
Get-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Když instance virtuálních počítačů jsou zašifrované, *EncryptionSummary* kódu sestavy *bylo úspěšné a Stav zřizování* jak je znázorněno v následujícím příkladu výstupu:

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

Pokud již nechcete používat šifrovanými disky instancí virtuálních počítačů, můžete zakázat šifrování s [Disable-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Disable-AzureRmVmssDiskEncryption) následujícím způsobem:

```azurepowershell-interactive
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Další postup

V tomto článku se používá prostředí Azure PowerShell k šifrování škálovací sadu virtuálních počítačů. Můžete také použít [rozhraní příkazového řádku Azure](virtual-machine-scale-sets-encrypt-disks-cli.md) nebo šablony pro [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) nebo [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
