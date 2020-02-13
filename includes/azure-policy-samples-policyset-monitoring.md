---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 8fe3dca69974f1df09ffb9ca4e1ece5a614f61f5
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172965"
---
|Název |Popis |Zásady |Verze |
|---|---|---|---|
|[Povolit Azure Monitor pro VM Scale Sets (VMSS)](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Povolí Azure Monitor pro VM Scale Sets v zadaném oboru (skupina pro správu, předplatné nebo skupinu prostředků). Jako parametr převezme Log Analytics pracovní prostor. Poznámka: Pokud je vaše sada škálování upgradePolicy nastavena na ruční, je nutné použít rozšíření na všechny virtuální počítače v sadě tak, že na ně zavoláte upgrade. V rozhraní příkazového řádku by to bylo AZ VMSS Update-Instances. |6 |1.0.0 – Preview |
|[Povolit Azure Monitor pro virtuální počítače](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Povolí Azure Monitor pro Virtual Machines (virtuálních počítačů) v zadaném oboru (skupina pro správu, předplatné nebo skupinu prostředků). Jako parametr převezme Log Analytics pracovní prostor. |6 |1.0.0 – Preview |
