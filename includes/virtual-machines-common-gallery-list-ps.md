---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/07/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f7539ed5083a386ef05134bea36426f4a360afad
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192173"
---
## <a name="shared-image-management"></a>Správa sdílené bitové kopie 

Tady je několik příkladů běžných úloh správy a postup pro jejich provedení pomocí Powershellu.

Seznam všech galeriích podle názvu.

```azurepowershell-interactive
$galleries = Get-AzureRMResource -ResourceType Microsoft.Compute/galleries
$galleries.Name
```

Vypsat všechny image definice podle názvu.

```azurepowershell-interactive
$imageDefinitions = Get-AzureRMResource -ResourceType Microsoft.Compute/galleries/images
$imageDefinitions.Name
```

Zobrazí seznam všech verzí image podle názvu.

```azurepowershell-interactive
$imageVersions = Get-AzureRMResource -ResourceType Microsoft.Compute/galleries/images/versions
$imageVersions.Name
```

Odstraňte image verze. Tento příklad odstraní verze image s názvem *1.0.0*.

```azurepowershell-interactive
Remove-AzureRmGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -Name 1.0.0 `
   -ResourceGroupName myGalleryRG
```





