---
title: Škálování Azure Cosmos DB podle plánu pomocí časovače Funkce Azure
description: Zjistěte, jak škálovat změny propustnosti v Azure Cosmos DB pomocí PowerShellu a Azure Functions.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 68ba40ea212c061fa5c8bbddc47ea0dfc6d8caa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75935165"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Škálování propustnost Azure Cosmos DB pomocí aktivační události Časovač funkcí Azure

Výkon účtu Azure Cosmos je založen na množství zřízené propustnosti vyjádřené v jednotkách požadavků za sekundu (RU/s). Zřizování je na druhé rozlišovací schopnost a fakturuje se na základě nejvyšší Ru/s za hodinu. Tento model zřízené kapacity umožňuje službě poskytovat předvídatelnou a konzistentní propustnost, zaručenou nízkou latenci a vysokou dostupnost. Většina produkčních úloh tyto funkce. Však ve vývoji a testování prostředí, kde Azure Cosmos DB se používá pouze v pracovní době, můžete vertikálně navýšit propustnost v dopoledních hodinách a škálování zpět ve večerních hodinách po pracovní době.

Propustnost můžete nastavit prostřednictvím [šablon Azure Resource Manager](resource-manager-samples.md), Azure [CLI](cli-samples.md)a [PowerShellu](powershell-samples-sql.md), pro účty rozhraní API core (SQL) nebo pomocí sad Azure Cosmos DB SDKs pro daný jazyk. Výhodou použití šablon Správce prostředků, Azure CLI nebo PowerShellu je, že podporují všechna pravidla api modelu Azure Cosmos DB.

## <a name="throughput-scheduler-sample-project"></a>Ukázkový projekt plánovače propustností

Abychom zjednodušili proces škálování Azure Cosmos DB podle plánu, vytvořili jsme ukázkový projekt s názvem [Plánovač propustností Azure Cosmos](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler). Tento projekt je aplikace Azure Functions se dvěma aktivačními událostmi časovače – "ScaleUpTrigger" a "ScaleDownTrigger". Aktivační události spustit skript prostředí PowerShell, který nastaví propustnost pro každý prostředek, jak je definováno v souboru `resources.json` v každé aktivační události. ScaleUpTrigger je nakonfigurován tak, aby běžel na 8 AM UTC a ScaleDownTrigger je nakonfigurován tak, aby běžel na 6 PM UTC a tyto časy lze snadno aktualizovat v rámci souboru `function.json` pro každou aktivační událost.

Můžete klonovat tento projekt místně, upravit jej určit prostředky Azure Cosmos DB pro škálování nahoru a dolů a plán ke spuštění. Později ho můžete nasadit v předplatném Azure a zabezpečit ho pomocí identity spravované služby pomocí oprávnění [RBAC (Řízení přístupu na základě rolí)](role-based-access-control.md) s rolí "Operátor Azure Cosmos DB" pro nastavení propustnosti na vašich účtech Azure Cosmos.

## <a name="next-steps"></a>Další kroky

- Další informace a stáhněte si ukázku z [plánovače propustností Služby Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler).
