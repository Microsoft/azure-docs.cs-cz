---
title: Úlohy a aplikace replikace událostí – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje přehled o sestavování úloh a aplikací replikace událostí pomocí Azure Functions
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: a65815c7da400af8b5b6d46358e6bca6edbd7543
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97663599"
---
# <a name="event-replication-tasks-and-applications-with-azure-functions"></a>Úlohy a aplikace replikace událostí s Azure Functions

> [!TIP]
> Pro všechny úlohy stavové replikace, kde potřebujete vzít v úvahu datové části vašich událostí a transformovat, agregovat, rozšiřovat nebo snižovat, použijte [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) místo Azure Functions.

Jak je vysvětleno v článku o [federaci replikace událostí a mezi různými oblastmi](event-hubs-federation-overview.md) , nestavová replikace datových proudů událostí mezi páry Event Hubs a mezi Event Hubs a dalšími zdroji datových proudů událostí a cílí na Azure Functions.

[Azure Functions](../azure-functions/functions-overview.md) je škálovatelné a spolehlivé spouštěcí prostředí pro konfiguraci a spouštění aplikací bez serveru, včetně replikace událostí a federačních úloh.

V tomto přehledu se dozvíte o Azure Functions "integrované možnosti pro tyto aplikace" o blocích kódu, které můžete přizpůsobit a upravit pro úlohy transformace, a o tom, jak nakonfigurovat Azure Functions aplikaci tak, aby se v ideálním případě mohla integrovat s Event Hubs a dalšími službami zasílání zpráv Azure. V případě mnoha podrobností bude tento článek ukazovat na dokumentaci Azure Functions.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]









