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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "67175019"
---
## <a name="update-resources"></a>Aktualizace prostředků

Existují určitá omezení, která je možné aktualizovat. Následující položky lze aktualizovat: 

Galerie sdílených imagí:
- Description

Definice Image:
- Doporučené vCPU
- Doporučená paměť
- Description
- Datum konce životnosti

Verze Image:
- Počet místních replik
- Cílové oblasti
- Vyloučení z nejnovějšího
- Datum konce životnosti

Pokud plánujete přidání oblastí repliky, neodstraňujte zdrojovou spravovanou bitovou kopii. Zdrojová image spravovaná při replikaci verze image do dalších oblastí je nutná. 

Aktualizujte popis Galerie pomocí ([AZ SIG Update](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Aktualizujte popis definice obrázku pomocí [AZ SIG image-definition Update](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Aktualizací verze image přidejte oblast, do které chcete replikovat, pomocí [AZ SIG Image-Version Update](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Tato změna bude chvíli trvat, protože se image replikuje do nové oblasti.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>Odstranění prostředků

Prostředky je nutné odstranit v opačném pořadí, protože nejprve odstraňte verzi image. Po odstranění všech verzí imagí můžete definici image odstranit. Po odstranění všech definicí imagí můžete galerii odstranit. 

Odstraňte verzi Image pomocí [AZ SIG Image-Version Delete](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Odstraňte definici obrázku pomocí [AZ SIG image-definition Delete](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Odstraňte galerii obrázků pomocí [AZ SIG Delete](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
