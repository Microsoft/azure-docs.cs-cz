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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175019"
---
## <a name="update-resources"></a>Aktualizace prostředků

Existují určitá omezení, co lze aktualizovat. Lze aktualizovat následující položky: 

Sdílená galerie obrázků:
- Popis

Definice obrázku:
- Doporučené virtuální procesory
- Doporučená paměť
- Popis
- Datum konce životnosti

Verze obrázku:
- Počet regionálních replik
- Cílové oblasti
- Vyloučení z nejnovějších
- Datum konce životnosti

Pokud plánujete přidat oblasti repliky, neodstraňujte zdrojovou spravovanou bitovou kopii. Zdrojová spravovaná bitová kopie je potřebná pro replikaci verze bitové kopie do dalších oblastí. 

Aktualizujte popis galerie pomocí[(az sig update](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Aktualizujte popis definice obrázku pomocí [aktualizace az sig image-definition update](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Aktualizujte verzi bitové kopie a přidejte oblast, do které chcete replikovat pomocí [aktualizace image az sig.](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update) Tato změna bude chvíli trvat, jak image získá replikována do nové oblasti.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>Odstranění prostředků

Musíte odstranit prostředky v opačném pořadí, odstraněním verze bitové kopie jako první. Po odstranění všech verzí obrázku můžete odstranit definici obrázku. Po odstranění všech definic obrázků můžete galerii odstranit. 

Odstranit verzi obrazu pomocí [az sig image-version delete](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Odstranit definici obrazu pomocí [az sig image-definition delete](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Odstranit galerii obrázků pomocí [az sig delete](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
