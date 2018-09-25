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
ms.openlocfilehash: f2e252a000c5633b2cc1ef34683d7d2c7c133c03
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047445"
---
## <a name="using-rbac-to-share-images"></a>Použití RBAC pro sdílení imagí

Image můžete sdílet napříč předplatnými pomocí na základě řízení přístupu Role (RBAC). Každý uživatel, který má oprávnění ke čtení na verzi image, dokonce i mezi předplatnými, budou moct nasadit virtuální počítač pomocí image verze.

Další informace o tom, jak sdílet prostředky pomocí RBAC najdete v tématu [správě přístupu pomocí RBAC a rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Informace o seznamu

Získání umístění, stav a další informace o galeriích dostupné image pomocí [az sig seznamu](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Seznam definic image v galerii, včetně informací o typ operačního systému a stav, pomocí [az sig definici image seznamu](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list -g myGalleryRG -r myGallery -o table
```

Seznam verzí sdílené bitové kopie v galerii pomocí [az sig verze image seznamu](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list -g myGalleryRG -r myGallery -i myGalleryImage -o table
```

Získejte ID verze image pomocí [az sig verze image show](/cli/azure/sig/image-version#az-sig-image-version-show).

```
az sig image-version show \
-g myGalleryRG \     
-r myGallery \     
-i myGalleryImage \     
--gallery-image-version-name 1.0.0 \     
--query "id"
```