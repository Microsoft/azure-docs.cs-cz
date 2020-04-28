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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "66226018"
---
## <a name="using-rbac-to-share-images"></a>Použití RBAC ke sdílení imagí

Image můžete sdílet mezi předplatnými pomocí Access Control na základě rolí (RBAC). Každý uživatel, který má oprávnění ke čtení verze bitové kopie, dokonce i v rámci předplatných, bude moci nasadit virtuální počítač pomocí verze image.

Další informace o tom, jak sdílet prostředky pomocí RBAC, najdete v tématu [Správa přístupu pomocí RBAC a Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


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