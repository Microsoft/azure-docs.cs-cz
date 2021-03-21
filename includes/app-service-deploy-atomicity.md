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
ms.openlocfilehash: feed8b21833d4244d027d64d5e6547b94e4fa66f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004332"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Co se stane s aplikací během nasazení?

Všechny oficiálně podporované metody nasazení provedou změny souborů ve `/home/site/wwwroot` složce aplikace. Tyto soubory se používají ke spuštění vaší aplikace. Proto může nasazení selhat z důvodu zamčených souborů. Aplikace se může v průběhu nasazování chovat nepředvídatelné, protože ne všechny soubory se současně aktualizovaly. To je nežádoucí pro zákaznickou aplikaci. Existuje několik různých způsobů, jak se těmto problémům vyhnout:

- [Spusťte aplikaci z balíčku zip přímo](../articles/app-service/deploy-run-package.md) bez jejich dekomprimace.
- Zastavte aplikaci nebo pro svou aplikaci v průběhu nasazení povolte offline režim. Další informace najdete v tématu práce [s uzamčenými soubory během nasazení](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Nasaďte do [přípravného slotu](../articles/app-service/deploy-staging-slots.md) s povoleným [automatickým zahozením](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) . 
