---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 308c94332ac71f873b32178fe6da418c9079010f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866559"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Definice aplikace pro spravovanou aplikaci by měla používat účet úložiště poskytovaný zákazníkem.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |Pokud se jedná o regulativní nebo požadavek dodržování předpisů, použijte vlastní účet úložiště k řízení dat definice aplikace. Definici spravované aplikace si můžete uložit v rámci účtu úložiště, který jste zadali během vytváření, aby bylo možné její umístění a přístup plně spravovat, abyste splnili požadavky právních předpisů. |audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[Nasazení přidružení pro spravovanou aplikaci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |Nasadí prostředek přidružení, který přidruží vybrané typy prostředků k zadané spravované aplikaci.  Toto nasazení zásad nepodporuje vnořené typy prostředků. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
