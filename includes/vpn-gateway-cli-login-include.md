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
ms.openlocfilehash: bc7367ca2162dde9a266341f0d7b3dd1e8b0da81
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313689"
---
Přihlaste se k předplatnému Azure pomocí příkazu [az login](/cli/azure/#login) a postupujte podle pokynů na obrazovce. Další informace o přihlašování najdete v tématu [Začínáme s Azure CLI 2.0](/cli/azure/get-started-with-azure-cli).

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