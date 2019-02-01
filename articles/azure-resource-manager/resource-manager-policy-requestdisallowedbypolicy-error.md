---
title: Chyba RequestDisallowedByPolicy zásadě prostředků Azure | Dokumentace Microsoftu
description: Popisuje příčinu chyby RequestDisallowedByPolicy.
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
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 2d55d5958568deef5ce350c9678e55e2e37a2ff8
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55496393"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Chyba RequestDisallowedByPolicy zásadě prostředků Azure

Tento článek popisuje příčinu chyby RequestDisallowedByPolicy, poskytuje také řešení této chyby.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Příznak

Během nasazení, se může zobrazit **RequestDisallowedByPolicy** chybu, která brání vytváření prostředků. Následující příklad ukazuje chybu:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Řešení potíží

K načtení podrobností o zásadu, která zablokuje vašeho nasazení, použijte následující jedné z metod:

### <a name="powershell"></a>PowerShell

V prostředí PowerShell, zadejte tento identifikátor zásad, jako `Id` parametr k načtení podrobností o zásadu, která zablokuje vašeho nasazení.

```PowerShell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

Rozhraní příkazového řádku Azure zadejte název definice zásady:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Řešení

Pro zabezpečení nebo dodržování předpisů může správce předplatného přiřadit zásady, které omezují, jak jsou nasazené prostředky. Vaše předplatné může například vytvořit zásadu, která brání vytváření trasy definované uživatelem veřejné IP adresy, skupiny zabezpečení sítě, nebo směrovací tabulky. Chybová zpráva portálu **příznaky** část ukazuje název zásady.
Chcete-li tento problém vyřešit, zkontrolujte zásady prostředků a zjistěte, jak nasadit prostředky, které dodržují tyto zásady.

Další informace najdete v následujících článcích:

- [Co je Azure Policy?](../azure-policy/azure-policy-introduction.md)
- [Vytvoření a Správa zásad pro vynucování dodržování předpisů](../azure-policy/create-manage-policy.md)
