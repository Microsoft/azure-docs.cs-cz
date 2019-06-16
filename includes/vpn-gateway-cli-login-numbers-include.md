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
ms.openlocfilehash: 21dbec52dded5a195cc764741ab9e8d79737c549
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66121193"
---
1. Přihlaste se k předplatnému Azure pomocí příkazu [az login](/cli/azure/) a postupujte podle pokynů na obrazovce. Další informace o přihlašování najdete v tématu [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli).

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
