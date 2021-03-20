---
title: Limity, kvóty a prahové hodnoty v Azure Scheduleru
description: Přečtěte si o omezeních, kvótách, výchozích hodnotách a prahech omezení pro Azure Scheduler.
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "78898525"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limity, kvóty a prahové hodnoty omezení v Azure Scheduleru

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje [vyřazení](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)Azure Scheduleru. Pokud chcete pokračovat v práci s úlohami, které jste nastavili v plánovači, [migrujte prosím na Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) co nejdříve. 
>
> Plánovač již není v Azure Portal k dispozici, ale [rutiny prostředí PowerShell](scheduler-powershell-reference.md) [REST API](/rest/api/scheduler) a Azure Scheduler jsou v tuto chvíli dostupné, abyste mohli spravovat úlohy a kolekce úloh.

## <a name="limits-quotas-and-thresholds"></a>Limity, kvóty a prahové hodnoty

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>záhlaví x-MS-Request-ID

Každý požadavek vytvořený na službu Scheduleru vrátí hlavičku odpovědi s názvem **x-MS-Request-ID**. Tato hlavička obsahuje neprůhlednou hodnotu, která jednoznačně identifikuje požadavek. Takže pokud se požadavek konzistentně nezdařil a Vy jste potvrdili, že žádost je správně naformátovaná, můžete tuto chybu ohlásit společnosti Microsoft zadáním hodnoty hlavičky odpovědi **x-MS-Request-ID** , včetně těchto podrobností: 

* Hodnota **x-MS-Request-ID**
* Přibližná doba, kdy byla žádost provedena 
* Identifikátory pro předplatné Azure, kolekci úloh a úlohu 
* Typ operace, na kterou se žádost pokoušela

## <a name="next-steps"></a>Další kroky

* [Koncepty, terminologie a hierarchie entit služby Azure Scheduler](scheduler-concepts-terms.md)
* [Plány a fakturace pro Azure Scheduler](scheduler-plans-billing.md)
* [REST API Azure Scheduleru – referenční informace](/rest/api/scheduler)
* [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)