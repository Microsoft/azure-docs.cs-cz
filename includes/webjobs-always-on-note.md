---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 82e5221daefaecb687ad9feb79305e546d4ec17e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68424158"
---
> [!NOTE]
> Po 20 minutách nečinnosti může být časový rozsah webové aplikace. Časovač resetují pouze požadavky na skutečnou webovou aplikaci. Zobrazení konfigurace aplikace na webu Azure portal nebo vytváření požadavků na`https://<app_name>.scm.azurewebsites.net`web pokročilých nástrojů ( ) časovač neresetujte. Pokud vaše aplikace běží souvislé nebo naplánované (Časovač aktivační události) WebJobs, povolit **vždy zapnuto,** aby zajistily, že WebJobs spustit spolehlivě. Tato funkce je k dispozici pouze v [cenových úrovních](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)Basic, Standard a Premium .
