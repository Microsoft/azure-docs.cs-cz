---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/08/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8709956adee06e4e783ac5a7b317b2c4dec43e73
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53736859"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Co se stane do mojí aplikace během nasazení?

Všechny metody oficiálně podporované nasazení mají jedno společné: jejich provedli změny v souborech `/home/site/wwwroot` složky vaší aplikace. Jedná se o stejné soubory, které jsou spouštěny v produkčním prostředí. Proto může nasazení selhat kvůli uzamčené soubory nebo aplikace v produkčním prostředí může mít nepředvídatelné chování během nasazení, protože ne všechny soubory, se aktualizují současně. Existuje několik různých způsobů, jak se těmto problémům:

- Zastavte aplikaci nebo povolit režim offline pro vaše aplikace během nasazení. Další informace najdete v tématu [zabývají uzamčené soubory během nasazování](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Nasazení [přípravných slotech](../articles/app-service/deploy-staging-slots.md) s [automatické prohození](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) povolena. 
- Použití [spustit z balíčku](https://github.com/Azure/app-service-announcements/issues/84) místo.
