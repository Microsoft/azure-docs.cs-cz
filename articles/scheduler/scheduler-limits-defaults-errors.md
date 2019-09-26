---
title: Limity, kvóty a prahové hodnoty v Azure Scheduleru
description: Přečtěte si o omezeních, kvótách, výchozích hodnotách a prahech omezení pro Azure Scheduler.
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 293cd956f8270a4863fcc657f58c970096cec1e3
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300923"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limity, kvóty a prahové hodnoty omezení v Azure Scheduleru

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje [vyřazení](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)Azure Scheduleru. Pokud chcete pokračovat v práci s úlohami, které jste nastavili v plánovači, [migrujte prosím na Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) co nejdříve.

## <a name="limits-quotas-and-thresholds"></a>Limity, kvóty a prahové hodnoty

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>záhlaví x-MS-Request-ID

Každý požadavek vytvořený na službu Scheduleru vrátí hlavičku odpovědi s názvem **x-MS-Request-ID**. Tato hlavička obsahuje neprůhlednou hodnotu, která jednoznačně identifikuje požadavek. Takže pokud se požadavek konzistentně nezdařil a Vy jste potvrdili, že žádost je správně naformátovaná, můžete tuto chybu ohlásit společnosti Microsoft zadáním hodnoty hlavičky odpovědi **x-MS-Request-ID** , včetně těchto podrobností: 

* Hodnota **x-MS-Request-ID**
* Přibližná doba, kdy byla žádost provedena 
* Identifikátory pro předplatné Azure, kolekci úloh a úlohu 
* Typ operace, na kterou se žádost pokoušela

## <a name="see-also"></a>Viz také:

* [Co je Azure Scheduler?](scheduler-intro.md)
* [Koncepty, terminologie a hierarchie entit Azure Scheduleru](scheduler-concepts-terms.md)
