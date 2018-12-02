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
ms.openlocfilehash: b8e3d32f0e230673ecbc043597afe8e7b5f25e06
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742236"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Co se stane do mojí aplikace během nasazení?

Všechny metody oficiálně podporované nasazení mají jedno společné: jejich provedli změny v souborech `/home/site/wwwroot` složky vaší aplikace. Jedná se o stejné soubory, které jsou spouštěny v produkčním prostředí. Proto může nasazení selhat kvůli uzamčené soubory nebo aplikace v produkčním prostředí může mít nepředvídatelné chování během nasazení, protože ne všechny soubory, se aktualizují současně. Existuje několik různých způsobů, jak se těmto problémům:

- Zastavte aplikaci nebo povolit režim offline pro vaše aplikace během nasazení. Další informace najdete v tématu [zabývají uzamčené soubory během nasazování](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Nasazení [přípravných slotech](../articles/app-service/web-sites-staged-publishing.md) s [automatické prohození](../articles/app-service/web-sites-staged-publishing.md#configure-auto-swap) povolena. 
- Použití [spustit z balíčku](https://github.com/Azure/app-service-announcements/issues/84) místo.
