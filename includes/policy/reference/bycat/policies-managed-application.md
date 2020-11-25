---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a4f06313b577ffba8a10b4ecfff17e0117550d04
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998787"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Definice aplikace pro spravovanou aplikaci by měla používat účet úložiště poskytovaný zákazníkem.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |Pokud se jedná o regulativní nebo požadavek dodržování předpisů, použijte vlastní účet úložiště k řízení dat definice aplikace. Definici spravované aplikace si můžete uložit v rámci účtu úložiště, který jste zadali během vytváření, aby bylo možné její umístění a přístup plně spravovat, abyste splnili požadavky právních předpisů. |audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[Nasazení přidružení pro spravovanou aplikaci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |Nasadí prostředek přidružení, který přidruží vybrané typy prostředků k zadané spravované aplikaci.  Toto nasazení zásad nepodporuje vnořené typy prostředků. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
