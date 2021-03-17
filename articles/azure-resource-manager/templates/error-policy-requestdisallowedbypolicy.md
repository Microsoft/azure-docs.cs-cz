---
title: Chyba RequestDisallowedByPolicy
description: Popisuje příčinu chyby RequestDisallowedByPolicy při nasazování prostředků pomocí Azure Resource Manager.
author: genlin
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 41581ba48da2f2e717c5abf2a749f8fd2b86ac06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75477665"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Chyba RequestDisallowedByPolicy se zásadami prostředků Azure

Tento článek popisuje příčinu chyby RequestDisallowedByPolicy, poskytuje také řešení pro tuto chybu.

## <a name="symptom"></a>Příznak

Během nasazení se může zobrazit chyba **RequestDisallowedByPolicy** , která vám brání v vytváření prostředků. Následující příklad ukazuje chybu:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Poradce při potížích

Chcete-li načíst podrobnosti o zásadách, které zablokovaly nasazení, použijte jednu z následujících metod:

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V PowerShellu zadejte jako parametr tento identifikátor zásady, `Id` aby se načetly podrobnosti o zásadách, které zablokovaly vaše nasazení.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

V Azure CLI zadejte název definice zásady:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Řešení

Pro zabezpečení nebo dodržování předpisů můžou správci předplatného přiřazovat zásady, které omezují způsob nasazení prostředků. Vaše předplatné může například mít zásadu, která zabraňuje vytváření veřejných IP adres, skupin zabezpečení sítě, User-Defined tras nebo směrovacích tabulek. Chybová zpráva v části **příznaky** zobrazuje název zásady.
Pokud chcete tento problém vyřešit, Projděte si zásady prostředků a určete, jak nasadit prostředky, které tyto zásady dodržují.

Další informace najdete v následujících článcích:

- [Co je Azure Policy?](../../governance/policy/overview.md)
- [Vytváření a Správa zásad pro vymáhání dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md)
