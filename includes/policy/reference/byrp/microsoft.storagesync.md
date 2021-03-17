---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ce13484aa085cef89ba34151824ee843dd3bd90e
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "103419883"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Synchronizace souborů Azure by měl používat privátní odkaz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |Vytvoření privátního koncového bodu pro uvedený prostředek služby synchronizace úložiště vám umožní adresovat prostředek služby synchronizace úložiště v rámci privátního adresního prostoru IP v síti vaší organizace, nikoli prostřednictvím veřejného koncového bodu přístupného z Internetu. Vytvoření privátního koncového bodu sám o sobě nezakáže veřejný koncový bod. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Konfigurace Synchronizace souborů Azure s privátními koncovými body](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |Pro zadaný prostředek služby synchronizace úložiště se nasadí privátní koncový bod. To vám umožní vyřešit prostředek služby synchronizace úložiště z privátního adresního prostoru IP adres v síti vaší organizace, nikoli prostřednictvím veřejného koncového bodu přístupného z Internetu. Existence jednoho nebo více privátních koncových bodů samotného nezakáže veřejný koncový bod. |DeployIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[Úprava – konfigurace Synchronizace souborů Azure zakázání přístupu k veřejné síti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |Veřejné koncové body, které jsou přístupné z Internetu Synchronizace souborů Azure, jsou zásadami vaší organizace zakázané. Ke službě synchronizace úložiště můžete přistupovat prostřednictvím svých privátních koncových bodů. |Upravit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[Přístup k veřejné síti by měl být pro Synchronizace souborů Azure zakázaný.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |Zakázání veřejného koncového bodu umožňuje omezit přístup k vašemu prostředku služby synchronizace úložiště na požadavky určené ke schváleným privátním koncovým bodům v síti vaší organizace. Neexistují žádné nezabezpečené informace o povolování požadavků do veřejného koncového bodu, ale možná je budete chtít zakázat, aby splňovaly požadavky na zákonné, právní nebo podnikové zásady. Veřejný koncový bod pro službu synchronizace úložiště můžete zakázat nastavením incomingTrafficPolicy prostředku na AllowVirtualNetworksOnly. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
