---
title: Verze Preview – vytvoření bitové kopie s použitím vlastních klíčů
description: Pomocí šifrovacích klíčů spravovaných zákazníkem vytvořte verzi image v galerii sdílených imagí.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/3/2020
ms.author: cynthn
ms.openlocfilehash: 258d8ab6ab23a95d73b8ed0c2549f373cf097674
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554084"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Verze Preview: použití klíčů spravovaných zákazníkem pro šifrování imagí

Image v galerii sdílených imagí se ukládají jako snímky, takže se automaticky šifrují pomocí šifrování na straně serveru. Šifrování na straně serveru používá 256 [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), což je jedno z nejúčinnějších šifr, které jsou k dispozici. Šifrování na straně serveru je také kompatibilní se standardem FIPS 140-2. Další informace o kryptografických modulech založených na službě Azure Managed disks najdete v tématu [kryptografické rozhraní API: další generace](/windows/desktop/seccng/cng-portal).

Pro šifrování imagí můžete spoléhat na klíče spravované platformou nebo použít vlastní klíče. Pro dvojité šifrování můžete použít i obojí současně. Pokud se rozhodnete spravovat šifrování pomocí vlastních klíčů, můžete zadat *klíč spravovaný zákazníkem* , který se použije pro šifrování a dešifrování všech disků v imagí. 

Šifrování na straně serveru prostřednictvím klíčů spravovaných zákazníkem používá Azure Key Vault. Můžete buď importovat [klíče RSA](../key-vault/keys/hsm-protected-keys.md) do trezoru klíčů, nebo vygenerovat nové klíče RSA v Azure Key Vault.

## <a name="prerequisites"></a>Požadavky

Tento článek vyžaduje, abyste již v každé oblasti, kam chcete replikovat bitovou kopii, nastavili šifrování disku:

- Pokud chcete použít jenom klíč spravovaný zákazníkem, přečtěte si články o povolování klíčů spravovaných zákazníkem pomocí šifrování na straně serveru pomocí [Azure Portal](./disks-enable-customer-managed-keys-portal.md) nebo [PowerShellu](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-without-automatic-key-rotation).

- Pokud chcete použít jak klíče spravované platformou, tak zákazníkem spravované klíče (pro dvojité šifrování), přečtěte si články o povolení dvojitého šifrování v klidovém prostředí pomocí [Azure Portal](./disks-enable-double-encryption-at-rest-portal.md) nebo [PowerShellu](./windows/disks-enable-double-encryption-at-rest-powershell.md).

   > [!IMPORTANT]
   > Pro přístup k Azure Portal je nutné použít odkaz [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) . Dvojité šifrování v klidovém umístění není aktuálně viditelné ve veřejném Azure Portal, pokud tento odkaz nepoužijete.

## <a name="limitations"></a>Omezení

Při použití klíčů spravovaných zákazníkem pro šifrování imagí v galerii sdílených imagí platí tato omezení:   

- Sady šifrovacích klíčů musí být ve stejném předplatném jako vaše image.

- Sady šifrovacích klíčů jsou regionální prostředky, takže každá oblast vyžaduje jinou sadu šifrovacích klíčů.

- Image, které používají klíče spravované zákazníkem, nemůžete kopírovat ani sdílet. 

- Po použití vlastních klíčů k šifrování disku nebo Image se nemůžete vrátit k šifrování disků nebo imagí pomocí klíčů spravovaných platformou.


> [!IMPORTANT]
> Šifrování prostřednictvím klíčů spravovaných zákazníkem je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

Ve verzi Public Preview musíte nejdřív zaregistrovat funkci:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Dokončení registrace trvá několik minut. Slouží `Get-AzProviderFeature` ke kontrole stavu registrace funkce:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Až se `RegistrationState` vrátí `Registered` , můžete přejít k dalšímu kroku.

Ověřte registraci poskytovatele. Ujistěte se, že se vrátí `Registered` .

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Pokud se nevrátí `Registered` , použijte následující kód k registraci zprostředkovatelů:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Pokud chcete zadat sadu šifrování disku pro verzi image, použijte  [příkaz New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) s `-TargetRegion` parametrem: 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$eastUS2osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet'}

$eastUS2dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet1';Lun=1}

$eastUS2dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet2';Lun=2}

$eastUS2DataDiskImageEncryptions = @($eastUS2dataDiskImageEncryption1,$eastUS2dataDiskImageEncryption2)

$encryption2 = @{OSDiskImage=$eastUS2osDiskImageEncryption;DataDiskImages=$eastUS2DataDiskImageEncryptions}

$region2 = @{Name='East US 2';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption2}

$targetRegion = @($region1, $region2)


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

Virtuální počítač můžete vytvořit z Galerie sdílených imagí a pomocí klíčů spravovaných zákazníkem zašifrovat disky. Syntaxe je stejná jako vytvoření [zobecněného](vm-generalized-image-version-powershell.md) nebo [specializovaného](vm-specialized-image-version-powershell.md) virtuálního počítače z image. Použijte sadu rozšířených parametrů a přidejte `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` ji do konfigurace virtuálního počítače.

V případě datových disků přidejte `-DiskEncryptionSetId $setID` parametr při použití [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).


## <a name="cli"></a>Rozhraní příkazového řádku 

V rámci verze Public Preview se nejdřív musíte zaregistrovat u této funkce. Registrace trvá přibližně 30 minut.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Ověřte stav registrace funkce:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Až se tento kód vrátí `"state": "Registered"` , můžete přejít k dalšímu kroku.

Ověřte svou registraci:

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Pokud to není zaregistrované, spusťte následující příkaz:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Pokud chcete pro verzi image zadat sadu šifrování disku, použijte parametr [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create) `--target-region-encryption` . Formát pro `--target-region-encryption` je čárkami oddělený seznam klíčů pro šifrování operačních systémů a datových disků. Mělo by to vypadat takto: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>` . 

Pokud je zdroj pro disk s operačním systémem spravovaným diskem nebo virtuálním počítačem, použijte `--managed-image` k určení zdroje verze image. V tomto příkladu je zdrojem spravovaná image, která má disk s operačním systémem a datový disk na logické jednotce (LUN) 0. Disk s operačním systémem se zašifruje pomocí DiskEncryptionSet1 a datový disk se zašifruje pomocí DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs eastus2 \
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Pokud je zdrojem disku s operačním systémem snímek, použijte `--os-snapshot` k určení disku s operačním systémem. Pokud jsou k dispozici snímky datových disků, které by měly být součástí verze bitové kopie, přidejte je. Použijte `--data-snapshot-luns` k zadání logické jednotky (LUN) a použijte `--data-snapshots` k určení snímků.

V tomto příkladu jsou zdrojem snímky disku. K dispozici je disk s operačním systémem a datový disk na logické jednotce (LUN) 0. Disk s operačním systémem se zašifruje pomocí DiskEncryptionSet1 a datový disk se zašifruje pomocí DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs eastus\
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Virtuální počítač můžete vytvořit z Galerie sdílených imagí a pomocí klíčů spravovaných zákazníkem zašifrovat disky. Syntaxe je stejná jako vytvoření [zobecněného](vm-generalized-image-version-cli.md) nebo [specializovaného](vm-specialized-image-version-cli.md) virtuálního počítače z image. Stačí přidat `--os-disk-encryption-set` parametr s ID sady šifrování. Pro datové disky přidejte `--data-disk-encryption-sets` seznam diskových sad pro datové disky oddělený mezerami.


## <a name="portal"></a>Portál

Když vytvoříte verzi image na portálu, můžete použít kartu **šifrování** k aplikování šifrovacích sad úložiště.

> [!IMPORTANT]
> Chcete-li použít dvojité šifrování, je nutné použít odkaz [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) pro přístup k Azure Portal. Dvojité šifrování v klidovém umístění není aktuálně viditelné ve veřejném Azure Portal, pokud tento odkaz nepoužijete.


1. Na stránce **vytvořit verzi image** vyberte kartu **šifrování** .
2. V části **typ šifrování** vyberte místně **zašifrované šifrování pomocí klíče spravovaného zákazníkem** nebo **dvojitým šifrováním pomocí klíčů spravovaných platformou a zákazníkem**. 
3. Pro každý disk v imagi vyberte sadu šifrování z rozevíracího seznamu **sada šifrování disku** . 

### <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Virtuální počítač můžete vytvořit z verze image a pomocí klíčů spravovaných zákazníkem zašifrovat disky. Když vytváříte virtuální počítač na portálu, na kartě **disky** vyberte možnost **šifrování v klidovém nastavení pomocí klíčů spravovaných zákazníkem** nebo **dvojitým šifrováním pomocí klíčů spravovaných platformou a zákazníkem spravovaných klíčů** pro **typ šifrování**. Pak můžete vybrat sadu šifrování z rozevíracího seznamu.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [šifrování disků na straně serveru](./disk-encryption.md).

Informace o tom, jak dokoupit informace o plánu nákupu, najdete v tématu [zadání informací o plánu nákupu Azure Marketplace při vytváření imagí](marketplace-images.md).