---
title: Spouštění úloh na pozadí pomocí WebJobs
description: Naučte se používat webové úlohy ke spouštění úloh na pozadí v Azure App Service. Vyberte si z nejrůznějších formátů skriptů a spouštějte je s CRON výrazy.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
zone_pivot_groups: app-service-webjob
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0d49323e2bc3c0522b1fb9ad49ffcc14f476e2dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102452794"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Spouštění úloh na pozadí pomocí WebJobs v Azure App Service

Koncept spouštění úloh na [pozadí](./webjobs-create-ieux-conceptual.md) v Azure je k dispozici s webovými úlohami Azure App Service. Postup nasazení <abbr title="Program nebo skript ve stejné instanci jako webová aplikace, aplikace API nebo mobilní aplikace.">WebJobs</abbr> pomocí [Azure Portal](https://portal.azure.com) nahrát spustitelný soubor nebo skript. 

Mezi tři podporované webové úlohy patří:

* **Continuous**: spouští se okamžitě, obvykle běží v nekonečné smyčce.
* **Plánováno**: spustí se z naplánované aktivační události.
* **Ruční**: spouští se z manuální aktivační události.

::: zone pivot="webjob-type-continuous"

[!INCLUDE [Continuous](./includes/webjobs-create-ieux-continuous.md)]

::: zone-end

::: zone pivot="webjob-type-scheduled"

[!INCLUDE [Scheduled](./includes/webjobs-create-ieux-scheduled.md)]

::: zone-end

::: zone pivot="webjob-type-manual"

[!INCLUDE [Manual](./includes/webjobs-create-ieux-manual.md)]

::: zone-end
   
## <a name="next-steps"></a><a name="NextSteps"></a> Další kroky

* [Další informace o úlohách na pozadí jako WebJobs](./webjobs-create-ieux-conceptual.md)
* [Zobrazit historii protokolu WebJobs](./webjobs-create-ieux-view-log.md)

* Zjednodušení mnoha programovacích úloh pomocí [sady WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)

* Naučte se [vyvíjet a nasazovat WebJobs pomocí sady Visual Studio](webjobs-dotnet-deploy-vs.md) .
