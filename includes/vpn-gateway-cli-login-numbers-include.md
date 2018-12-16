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
ms.openlocfilehash: cf3bbb6c45061d6f4885839fbfb7f069264fe986
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444040"
---
1. Přihlaste se k předplatnému Azure pomocí příkazu [az login](/cli/azure/#login) a postupujte podle pokynů na obrazovce. Další informace o přihlašování najdete v tématu [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli).

   ```azurecli
   az login
   ```
2. Pokud máte více než jedno předplatné Azure, vypište předplatná pro daný účet.

   ```azurecli
   az account list --all
   ```
3. Určete předplatné, které chcete použít.

   ```azurecli
   az account set --subscription <replace_with_your_subscription_id>
   ```
