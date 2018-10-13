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
ms.openlocfilehash: 91a4a9ae1d3d84f1396adad07d1cda73ee3747c9
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49312474"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Co se stane do mojí aplikace během nasazení?

Všechny metody oficiálně podporované nasazení mají jedno společné: jejich provedli změny v souborech `/site/home/wwwroot` složky vaší aplikace. Jedná se o stejné soubory, které jsou spouštěny v produkčním prostředí. Proto může nasazení selhat kvůli uzamčené soubory nebo aplikace v produkčním prostředí může mít nepředvídatelné chování během nasazení, protože ne všechny soubory, se aktualizují současně. Existuje několik různých způsobů, jak se těmto problémům:

- Zastavte aplikaci nebo povolit režim offline pro vaše aplikace během nasazení. Další informace najdete v tématu [zabývají uzamčené soubory během nasazování](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Nasazení [přípravných slotech](../articles/app-service/web-sites-staged-publishing.md) s [automatické prohození](../articles/app-service/web-sites-staged-publishing.md#configure-auto-swap) povolena. 
- Použití [spustit z balíčku](https://github.com/Azure/app-service-announcements/issues/84) místo.
