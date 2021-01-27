---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4cb4425c32d049a6706424adbdd4b5602ed20fdf
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807279"
---
|Name<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Registry kontejneru by měly být zašifrované pomocí klíče spravovaného zákazníkem (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Pomocí klíčů spravovaných zákazníkem můžete spravovat šifrování v klidovém obsahu registrů. Ve výchozím nastavení jsou data v klidovém stavu zašifrovaná pomocí klíčů spravovaných službou, ale klíče spravované zákazníkem (CMK) se běžně vyžadují ke splnění standardů dodržování legislativních předpisů. CMKs povolí šifrování dat pomocí Azure Key Vaultho klíče vytvořeného a vlastněného vámi. Máte plnou kontrolu a zodpovědnost za životní cyklus klíčů, včetně rotace a správy. Další informace o šifrování CMK najdete na adrese [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) . |Audit, zamítnutí, zakázáno |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Registry kontejneru by neměly umožňovat neomezený přístup k síti.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Služba Azure Container Registry ve výchozím nastavení přijímá připojení přes Internet z hostitelů v jakékoli síti. Chcete-li chránit své Registry před potenciálními hrozbami, povolte přístup pouze z určitých veřejných IP adres nebo rozsahů adres. Pokud váš registr nemá pravidlo IP/firewall nebo nakonfigurovanou virtuální síť, zobrazí se ve špatných prostředcích. Další informace o Container Registry síťových pravidel najdete tady: [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) a tady [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) . |Audit, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Registry kontejneru by měly používat privátní odkaz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Privátní odkaz Azure umožňuje připojit virtuální síť ke službám Azure bez veřejné IP adresy ve zdroji nebo cíli. Platforma privátních odkazů zpracovává připojení mezi klientem a službami přes páteřní síť Azure. Mapováním privátních koncových bodů na Registry kontejnerů místo celé služby budete chráněni také před riziky úniku dat. Další informace najdete tady: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) . |Audit, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
