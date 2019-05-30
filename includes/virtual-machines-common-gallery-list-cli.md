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
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226018"
---
## <a name="using-rbac-to-share-images"></a>Použití RBAC pro sdílení imagí

Image můžete sdílet napříč předplatnými pomocí řízení přístupu na základě Role (RBAC). Každý uživatel, který má oprávnění ke čtení na verzi image, dokonce i mezi předplatnými, budou moct nasadit virtuální počítač pomocí image verze.

Další informace o tom, jak sdílet prostředky pomocí RBAC najdete v tématu [správě přístupu pomocí RBAC a rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Informace o seznamu

Získání umístění, stav a další informace o galeriích dostupné image pomocí [az sig seznamu](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Seznam definic image v galerii, včetně informací o typ operačního systému a stav, pomocí [az sig definici image seznamu](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Seznam verzí sdílené bitové kopie v galerii pomocí [az sig verze image seznamu](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Získejte ID verze image pomocí [az sig verze image show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```