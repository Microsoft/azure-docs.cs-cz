---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 48ed3ca91d672775dcbd0c36513ef948ef0f8a93
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100124"
---
|Název<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Sdílené řídicí panely by neměly mít Markdownu dlaždice s vloženým obsahem.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Zakažte vytváření sdíleného řídicího panelu, který obsahuje vložený obsah v dlaždicích Markdownu, a vyjistěte, aby byl obsah uložen jako Markdownu soubor, který je hostovaný online. Pokud používáte vložený obsah na dlaždici Markdownu, nemůžete spravovat šifrování obsahu. Konfigurací vlastního úložiště můžete šifrovat, pošifrovat a dokonce i přenést vlastní klíče. Když se tyto zásady povolí, uživatelé budou moct používat 2020-09-01-Preview nebo vyšší verzi sdílených řídicích panelů REST API. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
