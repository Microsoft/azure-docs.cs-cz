---
title: Použití Azure Image Builder s galerií obrázků pro virtuální počítače s Windows (preview)
description: Vytvářejte verze image Azure Shared Gallery pomocí Azure Image Builder a Azure PowerShellu.
author: cynthn
ms.author: cynthn
ms.date: 01/14/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: 48eff11facf0f1432534d61f003f61e6755caf33
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869518"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Náhled: Vytvoření bitové kopie systému Windows a její distribuce do galerie sdílených obrázků 

Tento článek vám ukáže, jak můžete pomocí Azure Image Builder a Azure PowerShell uvytvořit verzi image v [galerii sdílených bitových obrázků](shared-image-galleries.md)a pak distribuovat image globálně. Můžete to provést také pomocí [příkazového příkazu k příkazu Azure](../linux/image-builder-gallery.md).

Ke konfiguraci bitové kopie použijeme šablonu json. Soubor .json, který používáme, je zde: [armTemplateWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). Budeme stahovat a upravovat místní verzi šablony, takže tento článek je napsán pomocí místní relace prostředí PowerShell.

Chcete-li distribuovat obraz do galerie sdílených obrázků, šablona `distribute` používá [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) jako hodnotu pro část šablony.

Azure Image Builder automaticky spustí sysprep pro generalizaci bitové kopie, jedná se o obecný příkaz sysprep, který můžete v případě potřeby [přepsat.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#vms-created-from-aib-images-do-not-create-successfully) 

Uvědomte si, kolikrát vrstvy přizpůsobení. Příkaz Sysprep můžete spustit až 8krát v jedné bitové kopii systému Windows. Po 8násobném spuštění programu Sysprep je nutné znovu vytvořit bitové kopii systému Windows. Další informace naleznete [v tématu Omezení počtu spuštění sysprepu](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep). 

> [!IMPORTANT]
> Azure Image Builder je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Registrace funkcí
Chcete-li během náhledu používat Azure Image Builder, musíte zaregistrovat novou funkci.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Zkontrolujte stav registrace funkce.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Počkejte, až `RegistrationState` je `Registered` před přechodem k dalšímu kroku.

Zkontrolujte registrace poskytovatele. Ujistěte se, že každý vrátí `Registered`.

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

Pokud se `Registered`nevrátí , použijte následující zaregistrovat zprostředkovatele:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>Vytvoření proměnných

Budeme používat některé informace opakovaně, takže vytvoříme některé proměnné pro ukládání těchto informací. Nahraďte hodnoty proměnných, `username` `vmpassword`jako a , vlastními informacemi.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"
```



## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků a udělit Azure Image Builder oprávnění k vytváření prostředků v této skupině prostředků.

```powershell
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
New-AzRoleAssignment `
   -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 `
   -Scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup `
   -RoleDefinitionName Contributor
```



## <a name="create-the-shared-image-gallery"></a>Vytvoření galerie sdílených obrázků

Chcete-li používat Tvůrce obrázků se sdílenou galerií obrázků, musíte mít existující galerii obrázků a definici obrázků. Image Builder nebude vytvářet galerii obrázků a definici obrazu pro vás.

Pokud ještě nemáte galerii a definici obrázku, začněte jejich vytvořením. Nejprve vytvořte galerii obrázků.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>Stažení a konfigurace šablony

Stáhněte si šablonu json a nakonfigurujte ji pomocí proměnných.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath

```


## <a name="create-the-image-version"></a>Vytvoření verze obrázku

Šablona musí být odeslána do služby, bude to stahovat všechny závislé artefakty, jako jsou skripty, a uložit je do pracovní skupiny prostředků, s předponou *IT_*.

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -api-version "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

Chcete-li vytvořit obrázek, musíte vyvolat 'Spustit' na šabloně.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

Vytvoření bitové kopie a její replikace do obou oblastí může chvíli trvat. Počkejte, až se tato část dokončí, než přejdeme k vytvoření virtuálního počítače.

Informace o možnostech automatizace získání stavu sestavení image najdete v tématu [Readme](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query) pro tuto šablonu na GitHubu.


## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Vytvořte virtuální počítač z verze image, kterou vytvořil Azure Image Builder.

Získejte verzi obrázku, kterou jste vytvořili.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Vytvořte virtuální ho v druhé oblasti, které byly image byla replikována.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>Ověření vlastního nastavení
Vytvořte připojení ke vzdálené ploše k virtuálnímu počítači pomocí uživatelského jména a hesla, které jste nastavili při vytváření virtuálního počítače. Uvnitř virtuálního provozu otevřete výzvu cmd a zadejte:

```console
dir c:\
```

Měli byste vidět `buildActions` adresář s názvem, který byl vytvořen během přizpůsobení bitové kopie.


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete nyní zkusit znovu přizpůsobit verzi bitové kopie a vytvořit novou verzi stejné bitové kopie, **přeskočte tento krok** a [pokračujte v použití Azure Image Builder k vytvoření jiné verze bitové kopie](image-builder-gallery-update-image-version.md).


Tím odstraníte obrázek, který byl vytvořen, spolu se všemi ostatními soubory prostředků. Před odstraněním prostředků se ujistěte, že jste s tímto nasazením hotová.

Nejprve odstraňte šablonu skupiny prostředků, jinak nebude pracovní skupina prostředků (*IT_*) používaná AIB vyčištěna.

Získejte ResourceID šablony obrázku. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

Odstranit šablonu obrázku.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

odstraňte skupinu prostředků.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

Informace o tom, jak aktualizovat vytvořenou verzi bitové kopie, najdete [v tématu Vytvoření image Azure k vytvoření jiné verze bitové kopie](image-builder-gallery-update-image-version.md).
