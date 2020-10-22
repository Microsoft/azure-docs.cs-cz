---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5b1e2f52366e800a3f12e304ed0e626b300e948c
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92347000"
---
|Name<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Registry kontejneru by měly být zašifrované pomocí klíče spravovaného zákazníkem (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Auditujte Registry kontejnerů, u kterých není šifrování povolené pomocí klíčů spravovaných zákazníkem (CMK). Azure automaticky šifruje obsah v registru v klidovém provozu pomocí klíčů spravovaných službou. Výchozí šifrování můžete doplnit další vrstvou šifrování pomocí klíče, který vytvoříte a spravujete v Azure Key Vault. Další informace o šifrování CMK najdete na adrese: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) . |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Registry kontejneru by neměly umožňovat neomezený přístup k síti.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Auditujte Registry kontejnerů, které nemají nakonfigurovaná žádná pravidla sítě nebo brány firewall (IP), a umožněte tak všem síťovým přístupům ve výchozím nastavení. Omezení přístupu k síti chrání Registry kontejnerů před potenciálními hrozbami. Registry kontejnerů s aspoň jedním pravidlem IP/firewallu nebo nakonfigurovanou virtuální sítí se považují za vyhovující. Další informace o Container Registry síťových pravidel najdete na stránce: [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) a [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) . |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Registry kontejneru by měly používat privátní odkazy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Auditujte Registry kontejnerů, které nemají alespoň jedno schválené připojení privátního koncového bodu. Klienti ve virtuální síti můžou bezpečně přistupovat k prostředkům, které mají připojení privátního koncového bodu prostřednictvím privátních odkazů. Veřejný přístup pak může být zakázán, aby bylo zajištěno, že k připojení k registru lze použít pouze privátní odkazy. Další informace najdete na adrese: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) . |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
