---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5c1857372eb4aad2bf87e4078bae5e1fc41412a8
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100091837"
---
|Název |Description |Zásady |Verze |
|---|---|---|---|
|[Nasazení – konfigurace požadavků pro povolení Azure Monitor a agentů zabezpečení Azure na virtuálních počítačích](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Nakonfigurujte počítače, aby automaticky instalovaly Azure Monitor a agenty zabezpečení Azure. Security Center shromažďuje události od agentů a používá je k poskytování výstrah zabezpečení a přizpůsobených úloh posílení zabezpečení (doporučení). Vytvořte skupinu prostředků a Log Analytics pracovní prostor ve stejné oblasti jako počítač pro ukládání záznamů auditu. Tato zásada se vztahuje jenom na virtuální počítače v několika oblastech. |5 |1.0.0 – Preview |
|[Povolit Azure Monitor pro Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Povolí Azure Monitor pro Virtual Machine Scale Sets v zadaném oboru (skupina pro správu, předplatné nebo skupinu prostředků). Jako parametr převezme Log Analytics pracovní prostor. Poznámka: Pokud je vaše sada škálování upgradePolicy nastavena na ruční, je nutné použít rozšíření na všechny virtuální počítače v sadě tak, že na ně zavoláte upgrade. V rozhraní příkazového řádku by to bylo AZ VMSS Update-Instances. |6 |1.0.1 |
|[Povolit Azure Monitor pro virtuální počítače](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Povolte Azure Monitor pro virtuální počítače v zadaném oboru (skupina pro správu, předplatné nebo skupinu prostředků). Jako parametr převezme Log Analytics pracovní prostor. |10 |2.0.0 |
