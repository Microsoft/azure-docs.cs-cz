---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93332002cd2ac513d125e0f9eb75dff4a2d8760c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836847"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Co se stane do mojí aplikace během nasazení?

Všechny metody oficiálně podporované nasazení provedli změny v souborech `/home/site/wwwroot` složky vaší aplikace. Tyto soubory jsou stejné jako ty, které jsou spouštěny v produkčním prostředí. Proto může nasazení selhat z důvodu uzamčené soubory. Aplikace v produkčním prostředí může také nepředvídatelné chování během nasazení, protože ne všechny soubory aktualizovány ve stejnou dobu. Existuje několik různých způsobů, jak se těmto problémům:

- Zastavte aplikaci nebo povolit režim offline pro vaše aplikace během nasazení. Další informace najdete v tématu [řešit uzamčené soubory během nasazování](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Nasazení [přípravných slotech](../articles/app-service/deploy-staging-slots.md) s [automatické prohození](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) povolena. 
- Použití [spustit z balíčku](https://github.com/Azure/app-service-announcements/issues/84) místo průběžné nasazování.
