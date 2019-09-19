---
title: Chyba RequestDisallowedByPolicy se zásadami prostředků Azure | Microsoft Docs
description: Popisuje příčinu chyby RequestDisallowedByPolicy.
services: azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: e862637c688fd473b112fdfc0ee197da0444d02f
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121238"
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

## <a name="troubleshooting"></a>Řešení potíží

Chcete-li načíst podrobnosti o zásadách, které zablokovaly nasazení, použijte jednu z následujících metod:

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

V PowerShellu zadejte jako `Id` parametr tento identifikátor zásady, aby se načetly podrobnosti o zásadách, které zablokovaly vaše nasazení.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

V Azure CLI zadejte název definice zásady:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Řešení

Pro zabezpečení nebo dodržování předpisů můžou správci předplatného přiřazovat zásady, které omezují způsob nasazení prostředků. Vaše předplatné může mít například zásadu, která zabraňuje vytváření veřejných IP adres, skupin zabezpečení sítě, uživatelem definovaných tras nebo směrovacích tabulek. Chybová zpráva v části **příznaky** zobrazuje název zásady.
Pokud chcete tento problém vyřešit, Projděte si zásady prostředků a určete, jak nasadit prostředky, které tyto zásady dodržují.

Další informace najdete v následujících článcích:

- [Co je Azure Policy?](../governance/policy/overview.md)
- [Vytváření a správa zásad pro vynucování dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)
