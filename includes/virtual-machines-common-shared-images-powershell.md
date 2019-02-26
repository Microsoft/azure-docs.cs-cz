---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 12/10/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: c74a96e3dcce1394e0af5447c07ad38c54b960fa
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825730"
---
## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.


## <a name="preview-register-the-feature"></a>Verze Preview: Zaregistrovat funkci

Sdílené Galerie obrázků je ve verzi preview, ale budete muset zaregistrovat funkci, než budete moct použít. Postup pro registraci Galerie obrázků sdílené funkce:

```azurepowershell-interactive
Register-AzProviderFeature `
   -FeatureName GalleryPreview `
   -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="get-the-managed-image"></a>Získat spravované image

Zobrazí se seznam imagí, které jsou k dispozici ve skupině prostředků pomocí [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Jakmile budete znát název bitové kopie a jaký prostředek seskupit je v, můžete použít `Get-AzImage` znovu k získání objektu image a uložte ji do proměnné pro pozdější použití. Tento příklad načte obrázek s názvem *myImage* ze skupiny prostředků "myResourceGroup" a přiřadí ji do proměnné *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Vytvoření galerie obrázků 

Galerie obrázků je primární prostředek, který používá k povolení sdílení imagí. Galerie názvy musí být jedinečné v rámci vašeho předplatného. Vytvoření galerie obrázků s využitím [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). Následující příklad vytvoří galerii s názvem *myGallery* v *myGalleryRG* skupinu prostředků.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   
## <a name="create-an-image-definition"></a>Vytvoření definice bitové kopie 

Vytvoření s použitím definice image Galerie [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). V tomto příkladu je název image z Galerie *myGalleryImage*.

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```
### <a name="using-publisher-offer-and-sku"></a>Pomocí vydavatele, nabídky a skladové položky 
Pro zákazníky, kteří plánují na implementaci sdílené bitové kopie **v nadcházející verzi**, budete moct používat vaše osobní definované **-vydavatel**, **-nabízejí** a **- Sku** hodnoty najít a zadejte definici image a pak vytvořte virtuální počítač pomocí nejnovější verze image z odpovídající image definice. Tady jsou například tři definice, které image a jejich hodnoty:

|Definice image|Vydavatel|Nabídka|Skladová jednotka (SKU)|
|---|---|---|---|
|myImage1|myPublisher|myOffer|mySku|
|myImage2|myPublisher|standardOffer|mySku|
|myImage3|Testování|standardOffer|testSku|

Všechny tyto tři mají jedinečné sady hodnot. Verze bitové kopie, které sdílejí jeden nebo dva, ale ne všechny tři hodnoty může mít. **V příští verzi**, bude moct tyto hodnoty kombinovat, aby bylo možné požádat o nejnovější verzi konkrétní image. **To nebude fungovat v aktuální verzi**, ale bude k dispozici v budoucnu. Po vydání, pomocí následující syntaxe by měla sloužit pro nastavení zdrojového obrazu jako *myImage1* z výše uvedené tabulky.

```powershell
$vmConfig = Set-AzVMSourceImage `
   -VM $vmConfig `
   -PublisherName myPublisher `
   -Offer myOffer `
   -Skus mySku 
```

Podobá se to jak aktuálně určíte pomocí vydavatele, nabídky a skladové položky pro [Image Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) získat nejnovější verzi Marketplace image. Každá definice image s myslete na to, musí mít jedinečnou sadu tyto hodnoty.  

##<a name="create-an-image-version"></a>Vytvoření image verze

Vytvoření image verze ze spravované image pomocí [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) . V tomto příkladu je verze image *1.0.0* a se replikuje do obou *střed USA – západ* a *střed USA – jih* datových centrech.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-01-01' `
   -asJob 
```

Může trvat nějakou replikovat bitovou kopii na všechny cílové oblasti, takže jsme vytvořili úlohu, můžete průběh sledovat. Chcete-li zobrazit průběh úlohy, zadejte `$job.State`.

```azurepowershell-interactive
$job.State
```

