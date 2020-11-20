---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 80a91092cd4ede390f79b16e586a26eda46c9f9f
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94986905"
---
|Název |Popis |Zásady |Verze |
|---|---|---|---|
|[Povolit Azure Monitor pro Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Povolí Azure Monitor pro Virtual Machine Scale Sets v zadaném oboru (skupina pro správu, předplatné nebo skupinu prostředků). Jako parametr převezme Log Analytics pracovní prostor. Poznámka: Pokud je vaše sada škálování upgradePolicy nastavena na ruční, je nutné použít rozšíření na všechny virtuální počítače v sadě tak, že na ně zavoláte upgrade. V rozhraní příkazového řádku by to bylo AZ VMSS Update-Instances. |6 |1.0.1 |
|[Povolit Azure Monitor pro virtuální počítače](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Povolte Azure Monitor pro virtuální počítače v zadaném oboru (skupina pro správu, předplatné nebo skupinu prostředků). Jako parametr převezme Log Analytics pracovní prostor. |10 |2.0.0 |
