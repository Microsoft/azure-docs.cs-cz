---
title: Ukázkový – nastavení auditování serveru SQL Server Audit
description: Tato definice zásady ukázka audity nastavení auditování serveru SQL s auditIfNotExists.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
origin.date: 04/27/2018
ms.date: 03/11/2019
ms.author: v-biyu
ms.openlocfilehash: c4510a316760fa948aa39627c9a9c517437b1d77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926574"
---
# <a name="sample---audit-sql-server-audit-settings"></a>Ukázkový – Audit nastavení systému SQL server audit

Tyto integrované zásady auditují server SQL podle toho, jestli jsou povolená nastavení auditování.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Ukázková šablona

```json
{
  "if": {
    "field": "type",
    "equals": "Microsoft.SQL/servers"
  },
  "then": {
    "effect": "auditIfNotExists",
    "details": {
      "type": "Microsoft.SQL/servers/auditingSettings",
      "name": "default",
      "existenceCondition": {
        "allOf": [
          {
            "field": "Microsoft.Sql/auditingSettings.state",
            "equals": "[parameters('setting')]"
          }
        ]
      }
    }
  }
}
```

K nasazení této šablony můžete použít [Azure Portal](#deploy-with-the-portal) s [PowerShellem](#deploy-with-powershell) nebo s [Azure CLI](#deploy-with-azure-cli). K získání integrovaných zásad použijte ID `a6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9`.

## <a name="parameters"></a>Parametry

Předejte hodnotu parametru v následujícím formátu:

```json
{"setting": {"value":"enabled"}}
```

## <a name="deploy-with-the-portal"></a>Nasazení s využitím portálu

Při přiřazování zásad vyberte z dostupných integrovaných definic **Auditování nastavení auditování na úrovni SQL Serveru**.

## <a name="deploy-with-powershell"></a>Nasazení s využitím PowerShellu

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```powershell
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/a6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9

New-AzPolicyAssignment -name "SQL Audit audit" -PolicyDefinition $definition -PolicyParameter '{"setting": {"value":"enabled"}}' -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Vyčištění nasazení PowerShellu

Pokud chcete odebrat přiřazení zásad, spusťte následující příkaz.

```powershell
Remove-AzPolicyAssignment -Name "SQL Audit audit" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Nasazení s Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```cli
az policy assignment create --scope <scope> --name "SQL Audit audit" --policy a6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9 --params '{"setting": {"value":"enabled"}}'
```

### <a name="clean-up-azure-cli-deployment"></a>Vyčištění nasazení Azure CLI

Pokud chcete odebrat přiřazení zásad, spusťte následující příkaz.

```cli
az policy assignment delete --name "SQL Audit audit" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Další postup

- Další ukázky najdete v [ukázkách pro Azure Policy](index.md).