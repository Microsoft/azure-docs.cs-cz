---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4c54b7f2bcad81b8113e35b115dd66e395c333da
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "107284624"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Virtuální počítače s přístupem k Internetu by měly být chráněné pomocí skupin zabezpečení sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Chraňte své virtuální počítače před potenciálními hrozbami tím, že omezíte přístup k nim pomocí skupin zabezpečení sítě (NSG). Další informace o řízení provozu pomocí skupin zabezpečení sítě [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Předávání IP na virtuálním počítači by mělo být zakázané.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |Povolení předávání IP na síťové kartě virtuálního počítače umožní počítači přijímat přenosy adresované do jiných cílů. Předávání IP adres se vyžaduje jen zřídka (například při použití virtuálního počítače jako síťového virtuálního zařízení), a proto by ho měl zkontrolovat tým zabezpečení sítě. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[Porty pro správu by měly být uzavřeny na virtuálních počítačích](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Otevřené porty pro vzdálenou správu zveřejňují váš virtuální počítač na vysokou úroveň rizika vyplývající z internetových útoků. Tyto útoky se pokoušejí získat přístup správce k počítači prostřednictvím přihlašovacích údajů hrubou silou. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
