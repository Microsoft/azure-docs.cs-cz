---
title: Limity, kvóty a limity ve službě Azure Scheduler
description: Další informace o limity, kvóty, výchozí hodnoty a omezení prahové hodnoty pro Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 478afb20f3dabec74d66d00bec325408ce6604fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64713739"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limity, kvóty a omezení prahové hodnoty ve službě Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje službu Azure Scheduler, která se vyřazuje z provozu. K plánování úloh [místo ní zkuste použít Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="limits-quotas-and-thresholds"></a>Limity, kvóty a prahové hodnoty

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>Hlavička x-ms-request-id

Každý požadavek směřovaný služba Plánovač vrátí hlavičku odpovědi s názvem **x-ms-request-id**. Této hlavičky obsahuje neprůhledná hodnota, která jednoznačně identifikuje požadavek. Proto, pokud bude žádost neúspěšná konzistentně a potvrzení žádosti je ve správném formátu, můžete nahlásit chybu Microsoft tím, že poskytuje **x-ms-request-id** hodnota hlavičky odpovědi a tyto podrobnosti, včetně: 

* **X-ms-request-id** hodnota
* Přibližný čas, kdy byl požadavek 
* Identifikátory pro předplatné Azure, kolekce úloh a úlohy 
* Typ operace, která k pokusu o žádosti

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je Azure Scheduler?](scheduler-intro.md)
* [Koncepty, terminologie a hierarchie entit Azure Scheduleru](scheduler-concepts-terms.md)
