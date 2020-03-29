---
title: Vysoká dostupnost a spolehlivost
description: Informace o vysoké dostupnosti a spolehlivosti v Plánovači Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 20c2054e168a9b17d9b4ab159cfefbf607ab6d11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898567"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Vysoká dostupnost a spolehlivost pro Plánovač Azure

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje Azure Scheduler, který [je vyřazen .](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Pokud chcete pokračovat v práci s úlohami, které jste nastavili v Plánovači, [migrujte co](../scheduler/migrate-from-scheduler-to-logic-apps.md) nejdříve do Azure Logic Apps. 
>
> Plánovač už není k dispozici na webu Azure Portal, ale rutiny [ROZHRANÍ REST API](/rest/api/scheduler) a Azure [Scheduler PowerShell](scheduler-powershell-reference.md) zůstávají k dispozici v tuto chvíli, takže můžete spravovat své úlohy a kolekce úloh.

Azure Scheduler poskytuje [vysokou dostupnost](https://docs.microsoft.com/azure/architecture/framework/#resiliency) a spolehlivost pro vaše úlohy. Další informace naleznete v tématu [SLA for Scheduler](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Vysoká dostupnost

Plánovač Azure je [vysoce dostupný] a používá geograficky redundantní nasazení služby a geograficky regionální replikaci úloh.

### <a name="geo-redundant-service-deployment"></a>Geograficky redundantní nasazení služby

Azure Scheduler je dostupný téměř ve [všech geografických oblastech podporovaných Azure dnes](https://azure.microsoft.com/global-infrastructure/regions/#services). Takže pokud datové centrum Azure v hostované oblasti přestane být k dispozici, můžete stále používat Azure Scheduler, protože možnosti převzetí služeb převzetí služeb zpřístupňují Scheduler z jiného datového centra.

### <a name="geo-regional-job-replication"></a>Geograficky regionální replikace úloh

Vaše vlastní úlohy v Azure Scheduler se replikují napříč oblastmi Azure. Takže pokud jedna oblast má výpadek, Azure Scheduler převzetí selhání a zajišťuje, že vaše úloha běží z jiného datového centra ve spárované geografické oblasti.

Pokud například vytvoříte úlohu v usa – střed usa, Plánovač Azure tuto úlohu automaticky replikuje v severním centru USA. Pokud dojde k selhání v jižní centrální USA, Azure Scheduler spustí úlohu v severní centrální USA. 

![Geograficky regionální replikace úloh](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

Azure Scheduler také zajišťuje, že vaše data zůstanou ve stejné, ale širší geografické oblasti, jen pro případ, že dojde k selhání v Azure. Takže nemusíte duplikovat své úlohy, když chcete jen vysokou dostupnost. Azure Scheduler automaticky poskytuje vysokou dostupnost pro vaše úlohy.

## <a name="reliability"></a>Spolehlivost

Azure Scheduler zaručuje vlastní vysokou dostupnost, ale má jiný přístup k úlohám vytvořeným uživateli. Předpokládejme například, že vaše úloha vyvolá koncový bod HTTP, který není k dispozici. Azure Scheduler se stále pokouší úspěšně spustit vaši úlohu tím, že vám poskytne alternativní způsoby zpracování chyb: 

* Nastavte zásady opakování.
* Nastavte alternativní koncové body.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Zásady opakování

Azure Scheduler umožňuje nastavit zásady opakování. Pokud úloha selže, pak ve výchozím nastavení Plánovač opakuje úlohu ještě čtyřikrát v intervalech 30 sekund. Tuto zásadu opakování můžete provést agresivnější, například 10krát v 30sekundových intervalech nebo méně agresivní, například dvakrát v denních intervalech.

Předpokládejme například, že vytvoříte týdenní úlohu, která volá koncový bod HTTP. Pokud koncový bod HTTP přestane být k dispozici po dobu několika hodin při spuštění úlohy, pravděpodobně nebudete chtít čekat další týden na úlohu znovu spustit, což se stane, protože výchozí zásady opakování nebude fungovat v tomto případě. Takže můžete chtít změnit standardní zásady opakování tak, aby opakování dojít, například každé tři hodiny, nikoli každých 30 sekund. 

Informace o tom, jak nastavit zásadu opakování, naleznete [v tématu retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoints"></a>Alternativní koncové body

Pokud vaše úloha Plánovač Azure volá koncový bod, který je nedostupný, i po dodržování zásad opakování, Plánovač přejde zpět do alternativního koncového bodu, který může tyto chyby zpracovat. Takže pokud nastavíte tento koncový bod, Plánovač volá tento koncový bod, který umožňuje vlastní úlohy vysoce dostupné, když dojde k selhání.

Tento diagram například ukazuje, jak Plánovač dodržuje zásady opakování při volání webové služby v New Yorku. Pokud opakování nezdaří, Plánovač zkontroluje alternativní koncový bod. Pokud koncový bod existuje, Plánovač začne odesílat požadavky na alternativní koncový bod. Stejná zásada opakování platí pro původní i alternativní akci.

![Chování plánovače s zásadou opakování a alternativním koncovým bodem](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

Typ akce pro alternativní akci se může lišit od původní akce. Například i když původní akce volá koncový bod HTTP, alternativní akce může protokolovat chyby pomocí fronty úložiště, fronty service bus nebo akce tématu Service Bus.

Chcete-li se dozvědět, jak nastavit alternativní koncový bod, naleznete [v tématu errorAction](scheduler-concepts-terms.md#error-action).

## <a name="next-steps"></a>Další kroky

* [Koncepty, terminologie a hierarchie entit](scheduler-concepts-terms.md)
* [REST API Azure Scheduleru – referenční informace](/rest/api/scheduler)
* [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)
* [Omezení, kvóty, výchozí hodnoty a kódy chyb](scheduler-limits-defaults-errors.md)
