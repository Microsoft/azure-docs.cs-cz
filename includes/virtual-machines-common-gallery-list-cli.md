---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1ec3ecdafb8e475f5f13372789528612ccd7b8b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66226018"
---
## <a name="using-rbac-to-share-images"></a>Sdílení obrázků pomocí RBAC

Můžete sdílet obrázky mezi odběry pomocí řízení přístupu na základě rolí (RBAC). Každý uživatel, který má oprávnění ke čtení verze bitové kopie, a to i v rámci předplatných, bude moci nasadit virtuální počítač pomocí verze bitové kopie.

Další informace o sdílení prostředků pomocí RBAC najdete v [tématu Správa přístupu pomocí RBAC a Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Informace o seznamu

Získejte umístění, stav a další informace o dostupných galeriích obrázků pomocí [seznamu az sig](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Seznam definic obrázků v galerii, včetně informací o typu a stavu operačního systému, pomocí [seznamu az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Seznam sdílených verzí obrázků v galerii, pomocí [az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Získejte ID verze obrázku pomocí [az sig image-version show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```