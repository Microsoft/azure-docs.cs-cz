---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5aaf534e08b3184668c334c86b957dd872fd0dd9
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2020
ms.locfileid: "86509836"
---
|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Šablony pro tvůrce imagí virtuálních počítačů by měly používat privátní odkaz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Auditovat šablony tvůrce imagí virtuálních počítačů, které nemají nakonfigurovanou virtuální síť Pokud není virtuální síť nakonfigurovaná, vytvoří se a místo toho se použije veřejná IP adresa, která může vystavit prostředky přímo na internetu a zvýšit potenciální plochu pro útok. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
