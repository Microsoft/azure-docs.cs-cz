---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 58f38f5fe5544f01636d5fd2b2393025e5cb01ab
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755884"
---
## <a name="create-an-image-gallery"></a>Vytvoření galerie imagí 

Galerie imagí je primární prostředek, který slouží k povolení sdílení obrázků. 

Povolenými znaky pro název galerie jsou velká a malá písmena, číslice, tečky a tečky. Název galerie nesmí obsahovat pomlčky.   Názvy galerií musí být v rámci vašeho předplatného jedinečné. 

Pomocí [AZ SIG Create](/cli/azure/sig#az-sig-create)vytvořte galerii imagí. Následující příklad vytvoří skupinu prostředků s názvem Gallery s názvem *myGalleryRG* v *východní USA* a galerii s názvem *myGallery* .

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>Sdílení galerie

Image můžete sdílet mezi předplatnými pomocí Role-Based Access Control (RBAC). Obrázky můžete sdílet na úrovni galerie, definice obrázku nebo verze image. Každý uživatel, který má oprávnění ke čtení verze image, dokonce i přes odběry, bude moci nasadit virtuální počítač pomocí verze image.

Doporučujeme sdílet s ostatními uživateli na úrovni galerie. Chcete-li získat ID objektu galerie, použijte příkaz [AZ SIG show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Pomocí ID objektu jako oboru společně s e-mailovou adresou a [AZ role Assignment vytvořit](/cli/azure/role/assignment#az-role-assignment-create) poskytněte uživateli přístup k galerii sdílených imagí. Nahraďte `<email-address>` a `<gallery iD>` vlastními informacemi.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Další informace o tom, jak sdílet prostředky pomocí RBAC, najdete v tématu [Správa přístupu pomocí RBAC a Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).
