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
ms.openlocfilehash: 0c1e704a3bdec239c87d879ae1ef95e6e76d27fc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966883"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limity, kvóty a omezení prahové hodnoty ve službě Azure Scheduler

> [!IMPORTANT]
> [Služba Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje Azure Scheduleru, která se vyřazuje. K plánování úloh, [místo toho vyzkoušet Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

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
