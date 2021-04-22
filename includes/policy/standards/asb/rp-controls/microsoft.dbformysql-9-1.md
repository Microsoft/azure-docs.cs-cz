---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b40f49bd2bb7a2e39e77cf7c10352768a10f1506
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879802"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro Azure Database for MySQL by měla být povolená geograficky redundantní záloha.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL umožňuje zvolit možnost redundance pro váš databázový server. Dá se nastavit na geograficky redundantní úložiště záloh, ve kterém se data neukládají jenom v rámci oblasti, ve které je váš server hostovaný, ale taky se replikuje do spárované oblasti a poskytuje možnost obnovení v případě selhání oblasti. Konfigurace geograficky redundantního úložiště pro zálohování je povolená jenom během vytváření serveru. |Audit, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
