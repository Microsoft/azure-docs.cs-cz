---
title: Úlohy a aplikace replikace zpráv – Azure Service Bus | Microsoft Docs
description: Tento článek poskytuje přehled o vytváření úloh a aplikací replikace zpráv pomocí Azure Functions
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 4db151f54a2ad236ba937b005ba6a1fd3edd5967
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97657408"
---
# <a name="message-replication-tasks-and-applications"></a>Úlohy a aplikace replikace zpráv

Jak je vysvětleno v článku o [federaci replikace zpráv a mezi oblastmi](service-bus-federation-overview.md) , replikace sekvencí zpráv mezi dvojicemi Service Bus entit a mezi Service Bus a dalšími zdroji zpráv a cíli se obvykle zachází na Azure Functions.

[Azure Functions](../azure-functions/functions-overview.md) je škálovatelné a spolehlivé spouštěcí prostředí pro konfiguraci a spouštění aplikací bez serveru, včetně [replikace zpráv a úloh federace](service-bus-federation-overview.md) .

V tomto přehledu se dozvíte o Azure Functions "integrované možnosti pro tyto aplikace" o blocích kódu, které můžete přizpůsobit a upravit pro úlohy transformace, a o tom, jak nakonfigurovat Azure Functions aplikaci tak, aby se v ideálním případě mohla integrovat s Service Bus a dalšími službami zasílání zpráv Azure. V případě mnoha podrobností bude tento článek ukazovat na dokumentaci Azure Functions.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]
