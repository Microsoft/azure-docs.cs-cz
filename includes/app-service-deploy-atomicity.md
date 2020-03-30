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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75945151"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Co se stane s mou aplikací během nasazení?

Všechny oficiálně podporované metody nasazení provádět změny `/home/site/wwwroot` souborů ve složce vaší aplikace. Tyto soubory se používají ke spuštění aplikace. Proto může selhání nasazení z důvodu uzamčených souborů. Aplikace může také chovat nepředvídatelně během nasazení, protože ne všechny soubory aktualizovány ve stejnou dobu. To je nežádoucí pro aplikaci orientovnou na zákazníka. Existuje několik různých způsobů, jak se těmto problémům vyhnout:

- [Spusťte aplikaci z balíčku ZIP přímo](../articles/app-service/deploy-run-package.md) bez rozbalení.
- Zastavte aplikaci nebo povolte offline režim pro aplikaci během nasazení. Další informace naleznete v tématu [Deal with locked files during deployment](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Nasazení do [pracovního slotu](../articles/app-service/deploy-staging-slots.md) s [povoleným automatickým prohozením.](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) 
