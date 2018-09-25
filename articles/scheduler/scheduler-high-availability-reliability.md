---
title: Vysoká dostupnost a spolehlivost – Azure Scheduleru
description: Další informace o vysokou dostupnost a spolehlivost ve službě Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: d647de379972bac317a213e2f8925c0ff8c3372c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947920"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Vysoká dostupnost a spolehlivost Azure Scheduleru

> [!IMPORTANT]
> [Služba Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje Azure Scheduleru, která se vyřazuje. K plánování úloh, [místo toho vyzkoušet Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Azure Scheduler poskytuje [vysoké dostupnosti](https://docs.microsoft.com/azure/architecture/guide/pillars#availability) a spolehlivost vašich úloh. Další informace najdete v tématu [smlouva SLA pro Scheduler](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Vysoká dostupnost

Azure Scheduler je [s vysokou dostupností] a používá nasazení služby geograficky redundantní a geograficky místní úlohy replikace.

### <a name="geo-redundant-service-deployment"></a>Nasazení služby geograficky redundantní

Azure Scheduler je dostupný na webu Azure Portal napříč téměř [každé geografické oblasti nepodporuje v Azure ještě dnes](https://azure.microsoft.com/global-infrastructure/regions/#services). Aby, pokud datového centra Azure v prostředí oblast stane nedostupnou, můžete dál používat Azure Scheduleru, protože schopnosti převzetí služeb při selhání služby zpřístupnit plánovače z jiného datového centra.

### <a name="geo-regional-job-replication"></a>GEO místní úlohy replikace

Vlastní úlohy ve službě Azure Scheduler se replikují napříč oblastmi Azure. Takže pokud jedna oblast má k výpadku, převezme služby při selhání Azure Scheduleru a zajišťuje, že vaši úlohu spouští z jiného datového centra ve spárovaném zeměpisné oblasti.

Například pokud vytvoříte úlohu v střed USA – Jih, Azure Scheduleru automaticky replikuje tuto úlohu v střed USA – sever. Pokud dojde k selhání v střed USA – Jih, Azure Scheduleru spustí úlohu v střed USA – sever. 

![GEO místní úlohy replikace](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

Azure Scheduler také zajistí, že vaše data zůstanou v rámci stejné, ale nabízí širší geografické oblasti jen pro případ, dojde k selhání v Azure. Nemusíte tedy duplicitní úloh, chcete-li pouze vysokou dostupnost. Azure Scheduler automaticky poskytuje vysokou dostupnost pro vaše úlohy.

## <a name="reliability"></a>Spolehlivost

Azure Scheduler zaručuje vlastní vysokou dostupnost, ale používá jiný přístup pro úlohy vytvořené uživatelem. Předpokládejme například, že vaše úloha vyvolá koncový bod HTTP, který je k dispozici. Azure Scheduler se stále pokusí spustit úlohu úspěšně tím, že alternativní způsoby pro zpracování chyb: 

* Nastavte zásady opakování.
* Nastavte alternativní koncové body.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Zásady opakování

Azure Scheduler vám umožňuje nastavit zásady opakování. Pokud úloha selže, pak ve výchozím nastavení, Plánovač opakování úlohy čtyři víckrát v 30sekundovém intervalech. Můžete provádět takové zásady opakování agresivnější, jako například 10krát v 30sekundovém intervalech nebo méně agresivní, jako například dvakrát na denních intervalech.

Předpokládejme například, že vytvořte týdenní úlohu, která volá koncový bod HTTP. Pokud koncový bod HTTP k dispozici několik hodin, kdy se spouští vaše úloha, nebudete chtít čekat ještě jeden týden pro úlohu spustit znovu, který se stane, protože výchozí zásady opakování nebude fungovat v tomto případě. Ano můžete chtít změnit zásady opakovaných pokusů standard tak, aby opakované pokusy dojít například každé tři hodiny, spíše než každých 30 sekund. 

Zjistěte, jak nastavit zásady opakování, najdete v článku [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoints"></a>Alternativní koncové body

Pokud vaše úloha Azure Scheduleru zavolá koncový bod, který nedostupný, dokonce i po provedení zásady opakování, Plánovač spadne zpět na alternativní koncový bod, který dokáže zpracovat takové chyby. Proto pokud nastavíte tento koncový bod, Plánovač volání tohoto koncového bodu, který provede vlastní úlohy s vysokou dostupností, když dochází k chybám.

Například tento diagram znázorňuje, jak Scheduler následuje zásady opakovaných pokusů při volání webové služby v New Yorku. Pokud selžou i opakované pokusy, Plánovač zkontroluje alternativní koncový bod. Pokud koncový bod existuje, Plánovač spustí odesílání požadavků na alternativní koncový bod. Stejné zásady opakování platí pro původní akce a alternativní akci.

![Plánovač chování pomocí zásady opakovaných pokusů a alternativní koncový bod](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

Typ akce pro alternativní akci se může lišit od původního akce. Protokolujte například, i když původní akce zavolá koncový bod HTTP, alternativní akce může být chyby pomocí fronty úložiště, frontu služby Service Bus nebo akce témata služby Service Bus.

Zjistěte, jak nastavit alternativní koncový bod, najdete v článku [errorAction](scheduler-concepts-terms.md#error-action).

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je Azure Scheduler?](scheduler-intro.md)
* [Koncepty, terminologie a hierarchie entit](scheduler-concepts-terms.md)
* [Sestavení komplexních plánů a pokročilé opakování](scheduler-advanced-complexity.md)
* [Limity, kvóty, výchozí hodnoty a kódy chyb](scheduler-limits-defaults-errors.md)
