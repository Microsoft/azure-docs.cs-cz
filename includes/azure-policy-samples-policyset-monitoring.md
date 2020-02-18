---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/13/2020
ms.author: dacoulte
ms.openlocfilehash: 315d0f54d1f8fb875576f685cd0197003975c8eb
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77372287"
---
|Název |Popis |Zásady |Version |
|---|---|---|---|
|[Povolit Azure Monitor pro VM Scale Sets (VMSS)](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Povolí Azure Monitor pro VM Scale Sets v zadaném oboru (skupina pro správu, předplatné nebo skupinu prostředků). Jako parametr převezme Log Analytics pracovní prostor. Poznámka: Pokud je vaše sada škálování upgradePolicy nastavena na ruční, je nutné použít rozšíření na všechny virtuální počítače v sadě tak, že na ně zavoláte upgrade. V rozhraní příkazového řádku by to bylo AZ VMSS Update-Instances. |6 |1.0.0 – Preview |
|[Povolit Azure Monitor pro virtuální počítače](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Povolí Azure Monitor pro Virtual Machines (virtuálních počítačů) v zadaném oboru (skupina pro správu, předplatné nebo skupinu prostředků). Jako parametr převezme Log Analytics pracovní prostor. |6 |1.0.0 – Preview |
