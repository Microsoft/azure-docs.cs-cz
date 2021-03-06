---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 520a32ed44951a711dcb1d0975fb452829530c4f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244846"
---
[!INCLUDE [resource group intro text](resource-group.md)]

V Cloud Shell vytvořte skupinu prostředků pomocí [`az group create`](/cli/azure/group) příkazu. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *Západní Evropa*. Pokud chcete zobrazit všechna podporovaná umístění pro službu App Service na úrovni **Free**, spusťte příkaz [`az appservice list-locations --sku FREE`](/cli/azure/appservice).

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Obvykle budete svoji skupinu prostředků a prostředky vytvářet v oblasti, kterou máte blízko. 

Po dokončení příkazu se ve výstupu JSON zobrazí vlastnosti skupiny prostředků.