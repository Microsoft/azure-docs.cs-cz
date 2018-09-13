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
ms.date: 03/09/2018
ms.author: genli
ms.openlocfilehash: a9993942c20f2c33d944b74fb124a363d0663ced
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35642914"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Chyba RequestDisallowedByPolicy zásadě prostředků Azure

Tento článek popisuje příčinu chyby RequestDisallowedByPolicy, poskytuje také řešení této chyby.

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
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
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
