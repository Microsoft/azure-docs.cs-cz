---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0e5edcfad636c76107adc7e72b50ea61249aa00a
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807169"
---
|Název |Popis |Zásady |Verze |
|---|---|---|---|
|[Nasazení – konfigurace požadavků pro povolení Azure Monitor a agentů zabezpečení Azure na virtuálních počítačích](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Nakonfigurujte počítače, aby automaticky instalovaly Azure Monitor a agenty zabezpečení Azure. Security Center shromažďuje události od agentů a používá je k poskytování výstrah zabezpečení a přizpůsobených úloh posílení zabezpečení (doporučení). Vytvořte skupinu prostředků a Log Analytics pracovní prostor ve stejné oblasti jako počítač pro ukládání záznamů auditu. Tato zásada se vztahuje jenom na virtuální počítače v několika oblastech. |5 |1.0.0 – Preview |
|[Povolit Azure Monitor pro Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Povolí Azure Monitor pro Virtual Machine Scale Sets v zadaném oboru (skupina pro správu, předplatné nebo skupinu prostředků). Jako parametr převezme Log Analytics pracovní prostor. Poznámka: Pokud je vaše sada škálování upgradePolicy nastavena na ruční, je nutné použít rozšíření na všechny virtuální počítače v sadě tak, že na ně zavoláte upgrade. V rozhraní příkazového řádku by to bylo AZ VMSS Update-Instances. |6 |1.0.1 |
|[Povolit Azure Monitor pro virtuální počítače](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Povolte Azure Monitor pro virtuální počítače v zadaném oboru (skupina pro správu, předplatné nebo skupinu prostředků). Jako parametr převezme Log Analytics pracovní prostor. |10 |2.0.0 |
