---
title: Verze Preview – vytvoření bitové kopie s použitím vlastních klíčů
description: Vytvořte verzi image v galerii sdílených imagí pomocí šifrovacích klíčů spravovaných zákazníkem.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/06/2020
ms.author: cynthn
ms.openlocfilehash: 469e225a1cc40dc2ecc45339d9355484e87c4af2
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223580"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Verze Preview: použití klíčů spravovaných zákazníkem pro šifrování imagí

Image z Galerie se ukládají jako spravované disky, takže se automaticky šifrují pomocí šifrování na straně serveru. Šifrování na straně serveru používá 256 [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jedno z nejsilnějších šifrovacích šifr, které jsou kompatibilní se standardem FIPS 140-2. Další informace o kryptografických modulech založených na službě Azure Managed disks najdete v tématu [kryptografie API: Next Generation.](/windows/desktop/seccng/cng-portal)

Pro šifrování imagí můžete spoléhat na klíče spravované platformou, nebo můžete šifrování spravovat pomocí vlastních klíčů. Pokud se rozhodnete spravovat šifrování pomocí vlastních klíčů, můžete zadat *klíč spravovaný zákazníkem* , který se použije pro šifrování a dešifrování všech disků v imagí. 

Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem používá Azure Key Vault. Můžete buď importovat [klíče RSA](../key-vault/keys/hsm-protected-keys.md) do svého Key Vault, nebo vygenerovat nové klíče rsa v Azure Key Vault.

Pokud chcete používat pro Image spravované klíče zákazníka, musíte nejdřív Azure Key Vault. Pak vytvoříte sadu šifrování disku. Sada šifrování disku se pak použije při vytváření verzí imagí.

Další informace o vytváření a používání sad Encryption disks najdete v tématu [spravované klíče zákazníka](./windows/disk-encryption.md#customer-managed-keys).

## <a name="limitations"></a>Omezení

Pro šifrování imagí Galerie sdílených imagí je k dispozici několik omezení pro použití zákaznických klíčů:  

- Sady šifrovacích klíčů musí být ve stejném předplatném a oblasti jako image.

- Nemůžete sdílet image, které používají spravované klíče zákazníka. 

- Image, které používají spravované klíče zákazníka, nejde replikovat do jiných oblastí.

- Po použití vlastních klíčů k šifrování disku nebo Image se nemůžete vrátit k šifrování disků nebo imagí pomocí klíčů spravovaných platformou.


> [!IMPORTANT]
> Šifrování pomocí klíčů spravovaných zákazníkem je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

Ve verzi Public Preview musíte nejprve zaregistrovat funkci.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Dokončení registrace trvá několik minut. Ke kontrole stavu registrace funkce použijte příkaz Get-AzProviderFeature.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Pokud RegistrationState vrátí registraci, můžete přejít k dalšímu kroku.

Ověřte registraci poskytovatele. Ujistěte se, že se vrátí `Registered` .

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Pokud se nevrátí `Registered` , k registraci zprostředkovatelů použijte následující:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Chcete-li určit šifrování disku nastavené pro verzi bitové kopie, použijte [příkaz New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) s `-TargetRegion` parametrem. 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$targetRegion = @{$region1}


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Virtuální počítač můžete vytvořit z Galerie sdílených imagí a pomocí klíčů spravovaných zákazníkem zašifrovat disky. Syntaxe je stejná jako vytvoření [zobecněného](vm-generalized-image-version-powershell.md) nebo [specializovaného](vm-specialized-image-version-powershell.md) virtuálního počítače z image, musíte použít rozšířenou sadu parametrů a přidat `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` ji do konfigurace virtuálního počítače.

V případě datových disků je nutné přidat `-DiskEncryptionSetId $setID` parametr při použití [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).


## <a name="cli"></a>CLI 

Ve verzi Public Preview musíte nejprve zaregistrovat funkci.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Ověřte stav registrace funkce.

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Když se tato funkce vrátí `"state": "Registered"` , můžete přejít k dalšímu kroku.

Ověřte vaši registraci.

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Pokud to není zaregistrováno, spusťte následující příkaz:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Pokud chcete pro verzi image zadat šifrování disku, použijte parametr [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create) `--target-region-encryption` . Formát pro `--target-region-encryption` je seznam klíčů oddělených mezerou pro šifrování operačního systému a datových disků. Mělo by to vypadat takto: `<encryption set for the OS disk>,<Lun number of the data disk>, <encryption set for the data disk>, <Lun number for the second data disk>, <encryption set for the second data disk>` . 

Pokud je zdroj pro disk s operačním systémem spravovaným diskem nebo virtuálním počítačem, použijte `--managed-image` k určení zdroje verze image. V tomto příkladu je zdrojem spravovaná bitová kopie, která má disk s operačním systémem a také datový disk na logické jednotce (LUN) 0. Disk s operačním systémem se zašifruje pomocí DiskEncryptionSet1 a datový disk se zašifruje pomocí DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Pokud je zdrojem disku s operačním systémem snímek, použijte `--os-snapshot` k určení disku s operačním systémem. Pokud jsou k dispozici snímky dat, které by měly být součástí verze image, přidejte je pomocí `--data-snapshot-luns` zadáním logické jednotky (LUN) a `--data-snapshots` zadáním snímků.

V tomto příkladu jsou zdrojem snímky disku. K dispozici je disk s operačním systémem a také datový disk na logické jednotce (LUN) 0. Disk s operačním systémem se zašifruje pomocí DiskEncryptionSet1 a datový disk se zašifruje pomocí DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot"
   --data-snapshot-luns 0
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot"
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Virtuální počítač můžete vytvořit z Galerie sdílených imagí a pomocí klíčů spravovaných zákazníkem zašifrovat disky. Syntaxe je stejná jako vytvoření [zobecněného](vm-generalized-image-version-cli.md) nebo [specializovaného](vm-specialized-image-version-cli.md) virtuálního počítače z image, stačí přidat `--os-disk-encryption-set` parametr s ID sady šifrování. U datových disků přidejte k `--data-disk-encryption-sets` datovým diskům seznam diskových sad s oddělenými mezerami.


## <a name="portal"></a>Portál

Když vytvoříte verzi image na portálu, můžete k zadání informací o sadách šifrování úložiště použít kartu **šifrování** .

1. Na stránce **vytvořit verzi image** vyberte kartu **šifrování** .
2. V části **typ šifrování**vyberte možnost místní **šifrování pomocí klíče spravovaného zákazníkem**. 
3. Pro každý disk v imagi vyberte v rozevíracím seznamu možnost použít **šifrování disku** . 

### <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Virtuální počítač můžete vytvořit z Galerie sdílených imagí a pomocí klíčů spravovaných zákazníkem zašifrovat disky. Když vytváříte virtuální počítač na portálu, na kartě **disky** vyberte možnost **šifrování v klidovém nastavení pomocí klíčů spravovaných zákazníkem** pro **typ šifrování**. Pak můžete vybrat sadu šifrování z rozevíracího seznamu.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [šifrování disků na straně serveru](./windows/disk-encryption.md).

Informace o tom, jak dokoupit informace o plánu nákupu, najdete v tématu [zadání informací o plánu nákupu Azure Marketplace při vytváření imagí](marketplace-images.md).
