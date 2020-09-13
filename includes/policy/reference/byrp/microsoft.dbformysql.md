---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4a055c5fe6aaa31324eb91c57debda8ee18e7bfd
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90022588"
---
|Name<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Zajistěte, aby byla pro servery MySQL povolená vlastní ochrana dat klíčů.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83cef61d-dbd1-4b20-a4fc-5fbc7da10833) |Tato zásada Audituje servery MySQL ve vašem prostředí, aniž by byla povolena ochrana dat klíčů pro vlastní klíč. Další podrobnosti najdete na adrese [https://aka.ms/mysqlbyok](https://aka.ms/mysqlbyok) . |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableByok_Audit.json) |
|[Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Tato zásada Audituje všechny servery MySQL, které nevynucují připojení SSL. Azure Database for MySQL podporuje připojení Azure Database for MySQLho serveru k klientským aplikacím pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž" v prostředních prostředcích, a to šifrováním datového proudu mezi serverem a vaší aplikací. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Pro Azure Database for MySQL by měla být povolená geograficky redundantní záloha.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Tato zásada Audituje jakékoli Azure Database for MySQL s geograficky redundantní zálohou, která není povolená. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[Server MySQL by měl používat koncový bod služby virtuální sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3375856c-3824-4e0e-ae6a-79e011dd4c47) |Tato zásada Audituje servery MySQL, které nejsou nakonfigurované na používání koncového bodu služby virtuální sítě. Další podrobnosti najdete na adrese [https://aka.ms/mysqlvnet](https://aka.ms/mysqlvnet) . |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_VirtualNetworkServiceEndpoint_Audit.json) |
|[Pro servery MySQL by měl být povolen soukromý koncový bod.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |Tato zásada Audituje servery MySQL, které nejsou nakonfigurované na používání privátního koncového bodu. Další podrobnosti najdete na adrese [https://aka.ms/mysqlprivatelink](https://aka.ms/mysqlprivatelink) . |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[Přístup k veřejné síti by měl být pro servery MySQL zakázaný.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |Tato zásada provádí audit serverů MySQL ve vašem prostředí s povoleným přístupem k veřejné síti. Další podrobnosti najdete na adrese [https://go.microsoft.com/fwlink/?linkid=2120014](https://go.microsoft.com/fwlink/?linkid=2120014) . |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |
