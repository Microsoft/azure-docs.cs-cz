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
ms.openlocfilehash: ac2cf4d688b1bdc54ed2d7341f0e195d3b2fe42d
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236463"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Co se stane do mojí aplikace během nasazení?

Všechny metody oficiálně podporované nasazení mají společnou jednou z věcí: budou provádět změny v souborech `/site/home/wwwroot` složky vaší aplikace. Jedná se o stejné soubory, které jsou spouštěny v produkčním prostředí. Proto nasazení může selhat z důvodu uzamčené soubory, nebo aplikaci v produkčním prostředí může mít nepředvídatelné chování během nasazení, protože ne všechny soubory, jsou aktualizovány současně. Existuje několik různých způsobů, abyste se těmto problémům:

- Zastavení aplikace nebo povolit offline režimu pro vaši aplikaci při nasazení. Další informace najdete v tématu [týkajících se uzamčeném soubory během nasazení](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Nasazení do [pracovní pozici](../articles/app-service/web-sites-staged-publishing.md) s [automatické prohození](../articles/app-service/web-sites-staged-publishing.md#configure-auto-swap) povolena. 
- Použití [spustit z Zip](https://github.com/Azure/app-service-announcements/issues/84) místo.
