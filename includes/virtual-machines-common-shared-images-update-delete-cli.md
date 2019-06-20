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
ms.openlocfilehash: 8d0f9866864ca4b02ca6238be2ac44537a586c2d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175019"
---
## <a name="update-resources"></a>Aktualizace prostředků

Existuje nějaké omezení toho, co se aktualizovalo. Je možné aktualizovat následující položky: 

Galerie sdílené bitové kopie:
- Popis

definice bitové kopie:
- Doporučené virtuálních procesorů
- Doporučená velikost paměti
- Popis
- Životnost datum ukončení

Verze Image:
- Počet replik místní
- Cílové oblasti
- Vyloučení z nejnovější
- Životnost datum ukončení

Pokud plánujete přidání oblasti repliky, neodstraňujte spravované image zdroje. Spravované image zdroje je potřeba se verze image se replikuje do dalších oblastí. 

Aktualizujte popis pomocí Galerie ([az sig aktualizace](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Aktualizovat popis definice image pomocí [az sig definici image aktualizace](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Aktualizovat image verze k přidání oblasti replikovat pomocí [aktualizace verze image sig az](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Tato změna bude nějakou dobu image budou replikována do nové oblasti.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>Odstranění prostředků

Budete muset v obráceném pořadí, odstraňte prostředky odstraněním verze image. Po odstranění všech verzí bitové kopie, můžete odstranit k definici image. Po odstranění všech definic image, můžete odstranit galerii. 

Odstranit verzi image pomocí [az sig-verze image odstranit](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Odstranit definici image pomocí [az sig-definici image odstranit](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Odstranit galerii image pomocí [odstranit az sig](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
