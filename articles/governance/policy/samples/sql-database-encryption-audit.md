---
title: Ukázka – audit transparentního šifrování dat v SQL
description: Tato ukázková definice zásad Audituje, jestli v SQL Database není povolené transparentní šifrování dat.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 4027ad28b6589872dd2f52961710c4db95a257e7
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254791"
---
# <a name="sample---audit-sql-database-encryption"></a>Ukázka – auditovat šifrování databáze SQL

Toto integrované audity zásad, pokud SQL Database nemá povolené transparentní šifrování dat.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Ukázková šablona

[!code-json[main](../../../../policy-templates/samples/SQL/audit-sql-db-tde-status/azurepolicy.json "Audit TDE for SQL Database")]

Tuto šablonu můžete nasadit pomocí [Azure Portal](#deploy-with-the-portal), pomocí [PowerShellu](#deploy-with-powershell) nebo pomocí [Azure CLI](#deploy-with-azure-cli). K získání předdefinované zásady použijte ID `17k78e20-9358-41c9-923c-fb736d382a12`.

## <a name="deploy-with-the-portal"></a>Nasazení s využitím portálu

Při přiřazování zásady vyberte možnost **Auditovat stav transparentního šifrování dat** z dostupných integrovaných definic.

## <a name="deploy-with-powershell"></a>Nasazení s využitím PowerShellu

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/17k78e20-9358-41c9-923c-fb736d382a12

New-AzPolicyAssignment -name "SQL TDE Audit" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Vyčištění nasazení prostředí PowerShell

Spuštěním následujícího příkazu odeberte přiřazení zásady.

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name "SQL TDE Audit" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Nasazení pomocí Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "SQL TDE Audit" --policy 17k78e20-9358-41c9-923c-fb736d382a12
```

### <a name="clean-up-azure-cli-deployment"></a>Vyčištění nasazení Azure CLI

Spuštěním následujícího příkazu odeberte přiřazení zásady.

```azurecli-interactive
az policy assignment delete --name "SQL TDE Audit" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další ukázky na [Azure Policy Samples](index.md)