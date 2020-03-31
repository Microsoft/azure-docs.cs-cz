---
title: Limity, kvóty a prahové hodnoty v Plánovači Azure
description: Informace o omezeních, kvótách, výchozích hodnotách a prahových hodnotách omezení pro Plánovač Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898525"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limity, kvóty a prahové hodnoty omezení v Plánovači Azure

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje Azure Scheduler, který [je vyřazen .](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Pokud chcete pokračovat v práci s úlohami, které jste nastavili v Plánovači, [migrujte co](../scheduler/migrate-from-scheduler-to-logic-apps.md) nejdříve do Azure Logic Apps. 
>
> Plánovač už není k dispozici na webu Azure Portal, ale rutiny [ROZHRANÍ REST API](/rest/api/scheduler) a Azure [Scheduler PowerShell](scheduler-powershell-reference.md) zůstávají k dispozici v tuto chvíli, takže můžete spravovat své úlohy a kolekce úloh.

## <a name="limits-quotas-and-thresholds"></a>Limity, kvóty a prahové hodnoty

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>hlavička x-ms-request-id

Každý požadavek poslaný proti službě Plánovač vrátí hlavičku odpovědi s názvem **x-ms-request-id**. Tato hlavička obsahuje neprůhlednou hodnotu, která jednoznačně identifikuje požadavek. Pokud tedy požadavek konzistentně selže a potvrdíte, že je požadavek správně formátován, můžete chybu nahlásit společnosti Microsoft poskytnutím hodnoty hlavičky odpovědi **x-ms-request-id** a zahrnutím těchto podrobností: 

* Hodnota **x-ms-request-id**
* Přibližný čas, kdy byla žádost podána 
* Identifikátory předplatného Azure, kolekce úloh a úlohy 
* Typ operace, o kterou se požadavek pokusil

## <a name="next-steps"></a>Další kroky

* [Koncepty, terminologie a hierarchie entit služby Azure Scheduler](scheduler-concepts-terms.md)
* [Plány a fakturace pro Plánovač Azure](scheduler-plans-billing.md)
* [REST API Azure Scheduleru – referenční informace](/rest/api/scheduler)
* [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)