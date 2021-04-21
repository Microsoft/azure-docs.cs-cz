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
ms.openlocfilehash: 83d70a8d4c5806120ddb4ea776a8f4a6f7e63857
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800085"
---
## <a name="list-information"></a>Informace o seznamu

Získejte informace o umístění, stavu a dalších informacích o dostupných galeriích imagí pomocí [AZ SIG list](/cli/azure/sig#az_sig_list).

```azurecli-interactive 
az sig list -o table
```

Seznamte se s definicemi obrázků v galerii, včetně informací o typu operačního systému a stavu, pomocí příkaz [AZ SIG image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Seznam verzí sdílených imagí v galerii získáte pomocí [seznamu AZ SIG Image-Version list](/cli/azure/sig/image-version#az_sig_image_version_list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Získejte ID verze Image pomocí [AZ SIG Image-Version show](/cli/azure/sig/image-version#az_sig_image_version_show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```
