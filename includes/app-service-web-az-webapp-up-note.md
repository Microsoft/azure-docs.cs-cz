---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 02/27/2019
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 08458bd170707b28c69fdad1d8aa115a7ad245a5
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558477"
---
> [!NOTE]
> Příkaz `az webapp up` provádí tyto akce:
>
>- Vytvořte výchozí [skupinu prostředků](/cli/azure/group?view=azure-cli-latest#az-group-create).
>
>- Vytvořte výchozí [plán služby App Service](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create).
>
>- [Vytvoří aplikaci](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) se zadaným názvem.
>
>- Soubory [zip nasadí](../articles/app-service/deploy-zip.md) z aktuálního pracovního adresáře do aplikace.
>