---
title: Verze Preview – vytvoření bitové kopie s použitím vlastních klíčů
description: Vytvořte verzi image v galerii sdílených imagí pomocí šifrovacích klíčů spravovaných zákazníkem.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/11/2020
ms.author: cynthn
ms.openlocfilehash: 91f485d03717ab80bac26abd16da165d7b0dead7
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291921"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Verze Preview: použití klíčů spravovaných zákazníkem pro šifrování imagí

Image z Galerie se ukládají jako spravované disky, takže se automaticky šifrují pomocí šifrování na straně serveru. Šifrování na straně serveru používá 256 [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jedno z nejsilnějších šifrovacích šifr, které jsou kompatibilní se standardem FIPS 140-2. Další informace o kryptografických modulech založených na službě Azure Managed disks najdete v tématu [kryptografie API: Next Generation.](/windows/desktop/seccng/cng-portal)

Pro šifrování vašich imagí můžete spoléhat na klíče spravované platformou, používat vlastní klíče, nebo můžete použít obojí společně pro šifrování typu Double. Pokud se rozhodnete spravovat šifrování pomocí vlastních klíčů, můžete zadat *klíč spravovaný zákazníkem* , který se použije pro šifrování a dešifrování všech disků v imagí. 

Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem používá Azure Key Vault. Můžete buď importovat [klíče RSA](../key-vault/keys/hsm-protected-keys.md) do svého Key Vault, nebo vygenerovat nové klíče rsa v Azure Key Vault.

## <a name="prerequisites"></a>Požadavky

Tento článek vyžaduje, abyste už měli k dispozici sadu Encryption disk pro použití pro vaši image.

- Pokud chcete použít jenom klíč spravovaný zákazníkem, přečtěte si téma **Povolení klíčů spravovaných zákazníkem pomocí šifrování na straně serveru** pomocí [Azure Portal](./disks-enable-customer-managed-keys-portal.md) nebo [PowerShellu](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-your-azure-key-vault-and-diskencryptionset).

- Pokud chcete použít jak klíče spravované platformou, tak i zákaznickou správu (pro dvojité šifrování), přečtěte si téma **Povolení dvojitého šifrování v klidovém** provozu pomocí [Azure Portal](./disks-enable-double-encryption-at-rest-portal.md) nebo [PowerShellu](./windows/disks-enable-double-encryption-at-rest-powershell.md).
    > [!IMPORTANT]
    > Tento odkaz je nutné použít [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) pro přístup k Azure Portal. Dvojité šifrování v klidovém umístění není aktuálně viditelné ve veřejném Azure Portal bez použití odkazu.

## <a name="limitations"></a>Omezení

Pro šifrování imagí Galerie sdílených imagí je k dispozici několik omezení pro použití zákaznických klíčů:  

- Sady šifrovacích klíčů musí být ve stejném předplatném a oblasti jako vaše image.

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

Chcete-li určit šifrování disku nastavené pro verzi bitové kopie, použijte  [příkaz New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) s `-TargetRegion` parametrem. 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$targetRegion = @($region1)


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


## <a name="cli"></a>Rozhraní příkazového řádku 

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


Pokud chcete pro verzi image zadat šifrování disku, použijte parametr  [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create) `--target-region-encryption` . Formát pro `--target-region-encryption` je čárkami oddělený seznam klíčů pro šifrování operačních systémů a datových disků. Mělo by to vypadat takto: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>` . 

Pokud je zdroj pro disk s operačním systémem spravovaným diskem nebo virtuálním počítačem, použijte `--managed-image` k určení zdroje verze image. V tomto příkladu je zdrojem spravovaná bitová kopie, která má disk s operačním systémem a také datový disk na logické jednotce (LUN) 0. Disk s operačním systémem se zašifruje pomocí DiskEncryptionSet1 a datový disk se zašifruje pomocí DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
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
   --location westus\
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Virtuální počítač můžete vytvořit z Galerie sdílených imagí a pomocí klíčů spravovaných zákazníkem zašifrovat disky. Syntaxe je stejná jako vytvoření [zobecněného](vm-generalized-image-version-cli.md) nebo [specializovaného](vm-specialized-image-version-cli.md) virtuálního počítače z image, stačí přidat `--os-disk-encryption-set` parametr s ID sady šifrování. U datových disků přidejte k `--data-disk-encryption-sets` datovým diskům seznam diskových sad s oddělenými mezerami.


## <a name="portal"></a>Portál

Když vytvoříte verzi image na portálu, můžete použít kartu **šifrování** a zadat použít vaše šifrovací sady úložiště.

> [!IMPORTANT]
> Chcete-li použít dvojité šifrování, je nutné použít tento odkaz [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) pro přístup k Azure Portal. Dvojité šifrování v klidovém umístění není aktuálně viditelné ve veřejném Azure Portal bez použití odkazu.


1. Na stránce **vytvořit verzi image** vyberte kartu **šifrování** .
2. V části **typ šifrování**vyberte místně **zašifrované šifrování pomocí klíče spravovaného zákazníkem** nebo **dvojitým šifrováním pomocí klíčů spravovaných platformou a zákazníkem**. 
3. Pro každý disk v imagi vyberte v rozevíracím seznamu možnost použít **šifrování disku** . 

### <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Virtuální počítač můžete vytvořit z verze image a pomocí klíčů spravovaných zákazníkem zašifrovat disky. Když vytváříte virtuální počítač na portálu, na kartě **disky** vyberte možnost **šifrování v klidovém nastavení pomocí klíčů spravovaných zákazníkem** nebo **dvojitým šifrováním pro typ šifrování spravovaného platformou a zákazníkem spravovanými klíči** . **Encryption type** Pak můžete vybrat sadu šifrování z rozevíracího seznamu.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [šifrování disků na straně serveru](./windows/disk-encryption.md).

Informace o tom, jak dokoupit informace o plánu nákupu, najdete v tématu [zadání informací o plánu nákupu Azure Marketplace při vytváření imagí](marketplace-images.md).
