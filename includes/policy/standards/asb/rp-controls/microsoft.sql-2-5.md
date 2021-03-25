---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0e6aaab50b549f0bf5c971c0e05c83b84a8b379b
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104913"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[SQL servery s auditováním pro cíl účtu úložiště by měly být nakonfigurované s 90 dní uchovávání nebo vyšší úrovně.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Pro účely vyšetřování incidentů doporučujeme nastavit uchovávání dat pro auditování SQL Server k cílovému účtu úložiště aspoň na 90 dní. Potvrďte, že splňujete nezbytná pravidla uchovávání pro oblasti, ve kterých pracujete. V některých případech se vyžaduje dodržování regulativních standardů. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
