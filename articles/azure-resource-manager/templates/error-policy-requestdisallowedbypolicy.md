---
title: Chyba RequestDisallowedByPolicy
description: Popisuje příčinu chyby RequestDisallowedByPolicy při nasazování prostředků pomocí Správce prostředků Azure.
author: genlin
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 41581ba48da2f2e717c5abf2a749f8fd2b86ac06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75477665"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Chyba RequestDisallowedByPolicy se zásadami prostředků Azure

Tento článek popisuje příčinu chyby RequestDisallowedByPolicy, poskytuje také řešení této chyby.

## <a name="symptom"></a>Příznak

Během nasazení se může zobrazit chyba **RequestDisallowedByPolicy,** která zabraňuje vytváření prostředků. Následující příklad ukazuje chybu:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Řešení potíží

Chcete-li načíst podrobnosti o zásadách, které zablokovaly vaše nasazení, použijte následující jednu z metod:

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V PowerShellu zadejte tento `Id` identifikátor zásadjako parametr pro načtení podrobností o zásadách, které zablokovaly vaše nasazení.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

V azure cli zadejte název definice zásady:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Řešení

Z důvodu zabezpečení nebo dodržování předpisů mohou správci předplatného přiřazovat zásady, které omezují způsob nasazení prostředků. Vaše předplatné může mít například zásadu, která brání vytváření veřejných IP adres, skupin zabezpečení sítě, uživatelem definovaných tras nebo tabulek tras. Chybová zpráva v části **Příznaky** zobrazuje název zásady.
Chcete-li tento problém vyřešit, zkontrolujte zásady prostředků a zjistěte, jak nasadit prostředky, které jsou v souladu s těmito zásadami.

Další informace najdete v těchto článcích:

- [Co je Azure Policy?](../../governance/policy/overview.md)
- [Vytváření a správa zásad pro vynucení dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md)
