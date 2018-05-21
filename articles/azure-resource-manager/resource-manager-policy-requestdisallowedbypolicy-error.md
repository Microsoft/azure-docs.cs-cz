---
title: Chyba RequestDisallowedByPolicy zásadám prostředků Azure | Microsoft Docs
description: Popisuje příčinou chyby RequestDisallowedByPolicy.
services: azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: genli
ms.openlocfilehash: 474400d92660b68fd7fef906216b8e37c6e8c94d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Chyba RequestDisallowedByPolicy zásadám prostředků Azure.

Tento článek popisuje příčinou chyby RequestDisallowedByPolicy, poskytuje také řešení této chyby.

## <a name="symptom"></a>Příznaky

Během nasazení, může dojít **RequestDisallowedByPolicy** chybu, která brání ve vytvoření prostředky. Následující příklad ukazuje, chyba:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Řešení potíží

Načíst podrobnosti o zásady, které blokované vaše nasazení, použijte následující metody:

### <a name="powershell"></a>PowerShell

V prostředí PowerShell, zadejte tento identifikátor zásady, jako `Id` parametr načíst podrobnosti o zásady, které blokované vaše nasazení.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

V Azure CLI 2.0 zadejte název definice zásady:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Řešení

Pro zabezpečení nebo dodržování předpisů může správce předplatného přiřadit zásady, které omezují, jak se prostředky nasadí. Například vaše předplatné může mít zásadu, která zabraňuje vytváření trasy definované uživatelem veřejných IP adres, skupiny zabezpečení sítě, nebo směrovacích tabulek. Chybovou zprávu ve **příznaky** část zobrazuje název zásad.
Chcete-li vyřešit tento problém, zkontrolujte zásady prostředků a určit, jak nasadit prostředky, které jsou v souladu s těmito zásadami.

Další informace najdete v následujících článcích:

- [Co je Azure zásad?](../azure-policy/azure-policy-introduction.md)
- [Vytvořit a spravovat zásady na vynucování dodržování shody](../azure-policy/create-manage-policy.md)
