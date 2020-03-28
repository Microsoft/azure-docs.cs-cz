---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: f06a78039217d7bc6b03c9196ac6824477ea977e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79381753"
---
|Name (Název) |Popis |Zásady |Version |
|---|---|---|---|
|[Povolení azure monitoru pro škálovací sady virtuálních strojů](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Povolte Azure Monitor pro škálovací sady virtuálních strojů v zadaném oboru (skupina pro správu, předplatné nebo skupina prostředků). Jako parametr převezme pracovní prostor Log Analytics. Poznámka: Pokud je upgrade škálovací sady zásada nastavena na ruční, je potřeba použít rozšíření na všechny virtuální počítače v sadě voláním upgrade na nich. V CLI by to bylo az vmss update-instance. |6 |1.0.1 |
|[Povolení Azure Monitoru pro virtuální počítače](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Povolte Azure Monitor pro virtuální počítače (VM) v zadaném oboru (skupina pro správu, předplatné nebo skupina prostředků). Jako parametr převezme pracovní prostor Log Analytics. |6 |1.0.1 |
