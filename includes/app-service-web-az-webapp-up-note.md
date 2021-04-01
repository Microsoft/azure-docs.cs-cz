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
ms.openlocfilehash: d285b48606485fbd7f53511a15be1e2a31791829
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244882"
---
> [!NOTE]
> Příkaz `az webapp up` provádí tyto akce:
>
>- Vytvořte výchozí [skupinu prostředků](/cli/azure/group#az-group-create).
>
>- Vytvořte výchozí [plán služby App Service](/cli/azure/appservice/plan#az-appservice-plan-create).
>
>- [Vytvoří aplikaci](/cli/azure/webapp#az-webapp-create) se zadaným názvem.
>
>- Soubory [zip nasadí](../articles/app-service/deploy-zip.md) z aktuálního pracovního adresáře do aplikace.
>