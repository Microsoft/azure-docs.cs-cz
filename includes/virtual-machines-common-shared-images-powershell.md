---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 82187b05a398c066f9da94c57cbe8a59a6ba3275
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145804"
---
## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.


## <a name="get-the-managed-image"></a>Získat spravované image

Zobrazí se seznam imagí, které jsou k dispozici ve skupině prostředků pomocí [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Jakmile budete znát název bitové kopie a jaký prostředek seskupit je v, můžete použít `Get-AzImage` znovu k získání objektu image a uložte ji do proměnné pro pozdější použití. Tento příklad načte obrázek s názvem *myImage* ze skupiny prostředků "myResourceGroup" a přiřadí ji do proměnné *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Vytvoření galerie obrázků 

Galerie obrázků je primární prostředek, který používá k povolení sdílení imagí. Povolené znaky pro název galerie jsou malá a velká písmena, číslice, tečky a tečky. Název galerie nesmí obsahovat pomlčky. Galerie názvy musí být jedinečné v rámci vašeho předplatného. 

Vytvoření galerie obrázků s využitím [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). Následující příklad vytvoří galerii s názvem *myGallery* v *myGalleryRG* skupinu prostředků.

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

Definice Image vytvořte logické seskupení pro bitové kopie. Používají se ke správě informace o verzích bitové kopie, které jsou vytvořeny v nich. Názvy imagí definice mohou být tvořené malá a velká písmena, číslice, tečky, pomlčky a tečky. Další informace o hodnotách, můžete použít definici image, najdete v části [obrázku definice](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Vytvoření image pomocí definice [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). V tomto příkladu je název image z Galerie *myGalleryImage*.

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


## <a name="create-an-image-version"></a>Vytvoření image verze

Vytvoření image verze ze spravované image pomocí [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Povolené znaky pro verze image jsou čísla a tečky. Čísla musí být v rozsahu 32bitového celého čísla. Formát: *Hlavní verze*. *Podverze*. *Oprava*.

V tomto příkladu je verze image *1.0.0* a se replikuje do obou *střed USA – západ* a *střed USA – jih* datových centrech. Při výběru cílových oblastí pro replikaci, mějte na paměti, že je také nutné zahrnout *zdroj* oblasti jako cíl pro replikaci.


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

> [!NOTE]
> Budete muset počkat na verzi image, aby zcela dokončit právě vytvořené a replikované před stejné spravované image můžete vytvořit jinou verzi image. 
>
> Můžete také ukládat vaše verze image v [Zónově redundantní úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) přidáním `-StorageAccountType Standard_ZRS` při vytváření verze image.
>
