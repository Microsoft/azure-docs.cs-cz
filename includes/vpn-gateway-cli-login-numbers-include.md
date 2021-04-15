---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devex-tag-azurecli
ms.openlocfilehash: 1a22c63eb01abc3775b2bf299d8464323e69c372
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386792"
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
