---
title: Vysoká dostupnost a spolehlivost
description: Informace o vysoké dostupnosti a spolehlivosti v Azure Scheduleru
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 98a6672af7e74fdd0732f3ba03264d2f674eb44f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368141"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Vysoká dostupnost a spolehlivost pro Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje [vyřazení](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)Azure Scheduleru. Pokud chcete pokračovat v práci s úlohami, které jste nastavili v plánovači, [migrujte prosím na Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) co nejdříve. 
>
> Plánovač již není v Azure Portal k dispozici, ale [rutiny prostředí PowerShell](scheduler-powershell-reference.md) [REST API](/rest/api/scheduler) a Azure Scheduler jsou v tuto chvíli dostupné, abyste mohli spravovat úlohy a kolekce úloh.

Azure Scheduler nabízí [vysokou dostupnost](/azure/architecture/framework/#resiliency) a spolehlivost pro vaše úlohy. Další informace najdete v tématu [SLA pro Scheduler](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Vysoká dostupnost

Služba Azure Scheduler je [vysoce dostupná] a používá replikaci geograficky redundantního nasazení služby i geograficky regionální úlohy.

### <a name="geo-redundant-service-deployment"></a>Geograficky redundantní nasazení služby

Služba Azure Scheduler je dostupná napříč skoro [všemi geografickými oblastmi, které Azure ještě dnes podporuje](https://azure.microsoft.com/global-infrastructure/regions/#services). Takže pokud se datacentrum Azure v hostované oblasti nebude k dispozici, můžete i nadále používat Azure Scheduler, protože možnosti převzetí služeb při selhání služby zpřístupňují Plánovač z jiného datového centra.

### <a name="geo-regional-job-replication"></a>Replikace geograficky regionálních úloh

Vaše vlastní úlohy v Azure Scheduleru se replikují napříč oblastmi Azure. Takže pokud dojde k výpadku jedné oblasti, služba Azure Scheduler převezme služby při selhání a zajistí, aby se vaše úloha spouštěla z jiného datového centra v spárované geografické oblasti.

Pokud například v Střed USA – jih vytvoříte úlohu, služba Azure Scheduler tuto úlohu automaticky replikuje v Střed USA – sever. Pokud v Střed USA – jih dojde k selhání, služba Azure Scheduler úlohu spustí v Střed USA – sever. 

![Replikace geograficky regionálních úloh](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

Azure Scheduler taky zajišťuje, aby vaše data zůstala v rámci stejné, ale širší geografické oblasti, a to jenom v případě, že v Azure dojde k selhání. Takže nemusíte duplikovat úlohy, pokud chcete jenom vysokou dostupnost. Azure Scheduler automaticky poskytuje vysokou dostupnost pro vaše úlohy.

## <a name="reliability"></a>Spolehlivost

Azure Scheduler garantuje vlastní vysokou dostupnost, ale pro úlohy vytvořené uživatelem potřebuje jiný přístup. Předpokládejme například, že vaše úloha vyvolá koncový bod HTTP, který není k dispozici. Služba Azure Scheduler se stále snaží úspěšně spustit úlohu tím, že vám poskytne alternativní možnosti pro zpracování selhání: 

* Nastavte zásady opakování.
* Nastavení alternativních koncových bodů.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Zásady opakování

Azure Scheduler umožňuje nastavit zásady opakování. Pokud se úloha nezdařila, bude ve výchozím nastavení služba Scheduler opakuje úlohu čtyřikrát dvakrát v intervalu 30 sekund. Tuto zásadu opakování můžete nastavit tak, aby se lépe agresivní, například 10 časů v intervalech 30 sekund, nebo méně agresivní, například dvakrát v denních intervalech.

Předpokládejme například, že vytvoříte týdenní úlohu, která volá koncový bod HTTP. Pokud se koncový bod HTTP po spuštění úlohy stane nedostupným, nebudete pravděpodobně chtít počkat jiný týden, než se úloha spustí znovu, což nastane, protože výchozí zásada opakování nebude v tomto případě fungovat. To znamená, že budete chtít změnit standardní zásady opakování, aby se opakování provádělo například každé tři hodiny, a ne každých 30 sekund. 

Informace o tom, jak nastavit zásady opakování, najdete v tématu [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoints"></a>Alternativní koncové body

Pokud vaše úloha Azure Scheduleru volá koncový bod, který je nedosažitelný, a to i po provedení zásady opakování, se Plánovač vrátí do alternativního koncového bodu, který může tyto chyby zpracovat. Takže pokud nastavíte tento koncový bod, Plánovač volání tohoto koncového bodu, který způsobí, že vaše vlastní úlohy budou vysoce dostupné, když dojde k selhání.

Tento diagram například ukazuje, jak Plánovač sleduje zásady opakování při volání webové služby v New Yorku. Pokud se opakované pokusy nezdaří, Scheduler vyhledá alternativní koncový bod. Pokud koncový bod existuje, Plánovač spustí odesílání požadavků do alternativního koncového bodu. Stejné zásady opakování platí i pro původní akci i pro alternativní akci.

![Chování Scheduleru se zásadami opakování a alternativním koncovým bodem](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

Typ akce alternativní akce se může lišit od původní akce. I když původní akce například volá koncový bod HTTP, může alternativní akce protokolovat chyby pomocí fronty úložiště, Service Bus fronty nebo akce v Service Bus tématu.

Další informace o nastavení alternativního koncového bodu najdete v tématu [errorAction](scheduler-concepts-terms.md#error-action).

## <a name="next-steps"></a>Další kroky

* [Koncepty, terminologie a hierarchie entit](scheduler-concepts-terms.md)
* [REST API Azure Scheduleru – referenční informace](/rest/api/scheduler)
* [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)
* [Omezení, kvóty, výchozí hodnoty a kódy chyb](scheduler-limits-defaults-errors.md)