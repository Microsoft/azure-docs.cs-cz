---
title: Ukázka kódu JSON pro Azure Policy – Vyžadování šifrování pro Data Lake Store | Microsoft Docs
description: Tyto ukázkové zásady JSON vyžadují šifrování pro Data Lake Store.
services: azure-policy
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-policy
ms.devlang: ''
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 04/27/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 470adb5f27c8a5318197c100a8f973319d9c5001
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34602427"
---
# <a name="require-data-lake-store-encryption"></a>Vyžadování šifrování Data Lake Store

Tyto integrované zásady odmítají účty Data Lake Store, které nemají povolené šifrování.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Ukázková šablona

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DataLakeStore/accounts"
      },
      {
        "field": "Microsoft.DataLakeStore/accounts/encryptionState",
        "equals": "Disabled"
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

K nasazení této šablony můžete použít [Azure Portal](#deploy-with-the-portal) s [PowerShellem](#deploy-with-powershell) nebo s [Azure CLI](#deploy-with-azure-cli). K získání integrovaných zásad použijte ID `a7ff3161-0087-490a-9ad9-ad6217f4f43a`.

## <a name="deploy-with-the-portal"></a>Nasazení s využitím portálu

Při přiřazování zásad vyberte z dostupných integrovaných definic **Vynucení šifrování v účtech DataLakeStore**.

## <a name="deploy-with-powershell"></a>Nasazení s využitím PowerShellu

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/a7ff3161-0087-490a-9ad9-ad6217f4f43a

New-AzureRmPolicyAssignment -name "Data Lake Store encryption" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Vyčištění nasazení PowerShellu

Pokud chcete odebrat přiřazení zásad, spusťte následující příkaz.

```powershell
Remove-AzureRmPolicyAssignment -Name "Data Lake Store encryption" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Nasazení s Azure CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "Data Lake Store encryption" --policy a7ff3161-0087-490a-9ad9-ad6217f4f43a
```

### <a name="clean-up-azure-cli-deployment"></a>Vyčištění nasazení Azure CLI

Pokud chcete odebrat přiřazení zásad, spusťte následující příkaz.

```azurecli-interactive
az policy assignment delete --name "Data Lake Store encryption" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Další kroky

- Další příklady najdete v [Ukázkách Azure Policy](../json-samples.md).