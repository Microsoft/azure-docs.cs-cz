---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 074d28f8144245247944f9c3409507d331c81166
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758631"
---
|Name (Název) |Popis |Zásady |Version |
|---|---|---|---|
|[Povolení azure monitoru pro škálovací sady virtuálních strojů](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Povolte Azure Monitor pro škálovací sady virtuálních strojů v zadaném oboru (skupina pro správu, předplatné nebo skupina prostředků). Jako parametr převezme pracovní prostor Log Analytics. Poznámka: Pokud je upgrade škálovací sady zásada nastavena na ruční, je potřeba použít rozšíření na všechny virtuální počítače v sadě voláním upgrade na nich. V CLI by to bylo az vmss update-instance. |6 |1.0.1 |
|[Povolení Azure Monitoru pro virtuální počítače](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Povolte Azure Monitor pro virtuální počítače (VM) v zadaném oboru (skupina pro správu, předplatné nebo skupina prostředků). Jako parametr převezme pracovní prostor Log Analytics. |6 |1.0.1 |
