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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "68424158"
---
> [!NOTE]
> Po 20 minutách nečinnosti může webová aplikace vytrvat déle. Časovač se Vynulovat jenom pro požadavky na skutečnou webovou aplikaci. Zobrazení konfigurace aplikace ve Azure Portal nebo provádění požadavků na web rozšířených nástrojů (`https://<app_name>.scm.azurewebsites.net`) neresetuje časovač. Pokud vaše aplikace spouští nepřetržité nebo plánované (aktivační událost časovače) webové úlohy, povolte možnost **vždy zapnuto** , aby bylo zajištěno, že budou webové úlohy spolehlivě fungovat. Tato funkce je dostupná jenom pro [cenové úrovně](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)Basic, Standard a Premium.
