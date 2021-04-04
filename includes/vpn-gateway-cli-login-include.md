---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 74d7bd087df4b00c0bafb5ec33fbbdfa5c57b379
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92755821"
---
Přihlaste se k předplatnému Azure pomocí příkazu [az login](/cli/azure/) a postupujte podle pokynů na obrazovce. Další informace o přihlašování najdete v tématu [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli).

```azurecli
az login
```

Pokud máte více než jedno předplatné Azure, vypište předplatná pro daný účet.

```azurecli
az account list --all
```

Určete předplatné, které chcete použít.

```azurecli
az account set --subscription <replace_with_your_subscription_id>
```
