---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5ad3af71227ae1f6d6701102bf29c042454a965c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090825"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Sdílené řídicí panely by neměly mít Markdownu dlaždice s vloženým obsahem.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Zakažte vytváření sdíleného řídicího panelu, který obsahuje vložený obsah v dlaždicích Markdownu, a vyjistěte, aby byl obsah uložen jako Markdownu soubor, který je hostovaný online. Pokud používáte vložený obsah na dlaždici Markdownu, nemůžete spravovat šifrování obsahu. Konfigurací vlastního úložiště můžete šifrovat, pošifrovat a dokonce i přenést vlastní klíče. Když se tyto zásady povolí, uživatelé budou moct používat 2020-09-01-Preview nebo vyšší verzi sdílených řídicích panelů REST API. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
