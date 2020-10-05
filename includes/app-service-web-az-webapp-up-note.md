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
ms.openlocfilehash: 8b5be0a438d9c5bb1fd0596368327c53a2d6c31f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "67175683"
---
> [!NOTE]
> Příkaz `az webapp up` provádí tyto akce:
>
>- Vytvořte výchozí [skupinu prostředků](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create).
>
>- Vytvořte výchozí [plán služby App Service](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create).
>
>- [Vytvoří aplikaci](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) se zadaným názvem.
>
>- Soubory [zip nasadí](https://docs.microsoft.com/azure/app-service/deploy-zip) z aktuálního pracovního adresáře do aplikace.
>
