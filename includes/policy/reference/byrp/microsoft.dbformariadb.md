---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4879d071f53e54a96d74ca23e9bf507931c29313
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105035199"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro Azure Database for MariaDB by měla být povolená geograficky redundantní záloha.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB umožňuje zvolit možnost redundance pro váš databázový server. Dá se nastavit na geograficky redundantní úložiště záloh, ve kterém se data neukládají jenom v rámci oblasti, ve které je váš server hostovaný, ale taky se replikuje do spárované oblasti a poskytuje možnost obnovení v případě selhání oblasti. Konfigurace geograficky redundantního úložiště pro zálohování je povolená jenom během vytváření serveru. |Audit, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[Server MariaDB by měl používat koncový bod služby virtuální sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Pravidla brány firewall založená na virtuální síti slouží k povolení provozu z konkrétní podsítě až po Azure Database for MariaDB a zajištění, že provoz zůstane v rámci hranice Azure. Tato zásada poskytuje způsob, jak auditovat, jestli Azure Database for MariaDB používá koncový bod služby virtuální sítě. |AuditIfNotExists, zakázáno |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[Privátní koncový bod by měl být povolený pro MariaDB servery.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Připojení privátního koncového bodu vynutila zabezpečenou komunikaci tím, že umožňuje privátní připojení k Azure Database for MariaDB. Nakonfigurujte připojení privátního koncového bodu tak, aby umožňovalo přístup k provozu, který přichází jenom ze známých sítí, a neumožňuje přístup ze všech ostatních IP adres, včetně v Azure. |AuditIfNotExists, zakázáno |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[Přístup k veřejné síti by měl být pro MariaDB servery zakázaný.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Zakažte vlastnost přístup k veřejné síti pro zvýšení zabezpečení a zajistěte, aby k vašemu Azure Database for MariaDB mohl přistupovat jenom z privátního koncového bodu. Tato konfigurace přísně zakáže přístup ze všech veřejných adresních prostorů mimo rozsah IP adres Azure a odepře všechna přihlášení, která odpovídají pravidlům brány firewall na základě IP adresy nebo virtuální sítě. |Audit, zakázáno |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
