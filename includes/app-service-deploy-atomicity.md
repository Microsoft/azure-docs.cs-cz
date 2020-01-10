---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 3d9b0a7f08efcf9ea5892467b638e95d0404f35f
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752008"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Co se stane s aplikací během nasazení?

Všechny oficiálně podporované metody nasazení provedou změny souborů ve složce `/home/site/wwwroot` vaší aplikace. Tyto soubory se používají ke spuštění vaší aplikace. Proto může nasazení selhat z důvodu zamčených souborů. Aplikace se může v průběhu nasazování chovat nepředvídatelné, protože ne všechny soubory se současně aktualizovaly. To je nežádoucí pro zákaznickou aplikaci. Existuje několik různých způsobů, jak se těmto problémům vyhnout:

- Zastavte aplikaci nebo pro svou aplikaci v průběhu nasazení povolte offline režim. Další informace najdete v tématu práce [s uzamčenými soubory během nasazení](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Nasaďte do [přípravného slotu](../articles/app-service/deploy-staging-slots.md) s povoleným [automatickým zahozením](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) . 
- Místo průběžného nasazování použijte [Run z balíčku](https://github.com/Azure/app-service-announcements/issues/84) .
