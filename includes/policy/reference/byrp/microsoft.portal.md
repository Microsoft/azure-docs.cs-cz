---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8cfeb5f6f22156aacb03a0fd327c0918f182369f
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99215410"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Sdílené řídicí panely by neměly mít Markdownu dlaždice s vloženým obsahem.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Zakažte vytváření sdíleného řídicího panelu, který obsahuje vložený obsah v dlaždicích Markdownu, a vyjistěte, aby byl obsah uložen jako Markdownu soubor, který je hostovaný online. Pokud používáte vložený obsah na dlaždici Markdownu, nemůžete spravovat šifrování obsahu. Konfigurací vlastního úložiště můžete šifrovat, pošifrovat a dokonce i přenést vlastní klíče. Když se tyto zásady povolí, uživatelé budou moct používat 2020-09-01-Preview nebo vyšší verzi sdílených řídicích panelů REST API. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
