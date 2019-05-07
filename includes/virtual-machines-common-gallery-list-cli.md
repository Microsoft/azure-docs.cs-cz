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
ms.openlocfilehash: 1e78109472668c0f9a73af6430253a0d709979af
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149680"
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
az sig image-definition list -g myGalleryRG -r myGallery -o table
```

Seznam verzí sdílené bitové kopie v galerii pomocí [az sig verze image seznamu](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list -g myGalleryRG -r myGallery -i myImageDefinition -o table
```

Získejte ID verze image pomocí [az sig verze image show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   -g myGalleryRG \
   -r myGallery \
   -i myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```