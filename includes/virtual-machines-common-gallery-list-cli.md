---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/28/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1c06f5ab8995e7285365fa2d0ee77c327be2b1bd
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386866"
---
## <a name="list-information"></a>Informace o seznamu

Získejte informace o umístění, stavu a dalších informacích o dostupných galeriích imagí pomocí [AZ SIG list](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Seznamte se s definicemi obrázků v galerii, včetně informací o typu operačního systému a stavu, pomocí příkaz [AZ SIG image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Seznam verzí sdílených imagí v galerii získáte pomocí [seznamu AZ SIG Image-Version list](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Získejte ID verze Image pomocí [AZ SIG Image-Version show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```
