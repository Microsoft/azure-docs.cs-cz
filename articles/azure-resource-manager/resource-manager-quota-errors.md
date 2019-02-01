---
title: Kvóta Azure chyby | Dokumentace Microsoftu
description: Popisuje, jak vyřešit chyby kvóty prostředků.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: 7938f2c47e4af8d8804191fbb9e55b379f9554ef
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488608"
---
# <a name="resolve-errors-for-resource-quotas"></a>Řešení chyb u kvóty prostředků

Tento článek popisuje kvóty chyby, které můžete narazit při nasazování prostředků.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Příznak

Pokud provádíte nasazení šablony, která vytváří prostředky, které překračují Azure kvóty, získáte chyba nasazení, který bude vypadat takto:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Nebo, může se zobrazit:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Příčina

Kvóty se vztahují na skupinu prostředků, předplatná, účty a další obory. Vaše předplatné může například nakonfigurovat k omezení počtu jader pro oblast. Pokud budete chtít nasadit virtuální počítač s více jader, než je povolené, zobrazí se chybová zpráva s informacemi o tom, že došlo k překročení kvóty.
Kompletní kvóty informace najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="azure-cli"></a>Azure CLI

Používáte Azure CLI, použijte `az vm list-usage` příkazu najděte kvóty virtuálního počítače.

```azurecli
az vm list-usage --location "South Central US"
```

Který vrátí:

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

Pokud používáte PowerShell, použijte **Get-AzVMUsage** příkazu najděte kvóty virtuálního počítače.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Který vrátí:

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Řešení

Chcete-li požádat o zvýšení kvóty, přejděte na portál a souborů problém podpory. V problém podpory požádat o zvýšení v rámci svojí kvóty pro oblast, do které chcete nasadit.

> [!NOTE]
> Mějte na paměti, že pro skupiny prostředků, kvóta se pro každé jednotlivé oblasti, ne pro celé předplatné. Pokud je potřeba nasadit 30 jader v oblasti západní USA, budete muset požádat o 30 jader Resource Manageru v oblasti západní USA. Pokud je potřeba nasadit 30 jader v některém z oblasti, ke kterému máte přístup, je třeba požádat o 30 jader Resource Manageru ve všech oblastech.
>
>

1. Vyberte **Předplatná**.

   ![Předplatná](./media/resource-manager-quota-errors/subscriptions.png)

2. Vyberte předplatné, které potřebuje navýšit kvótu.

   ![Výběr předplatného](./media/resource-manager-quota-errors/select-subscription.png)

3. Vyberte **využití a kvóty**

   ![Vyberte využití a kvóty](./media/resource-manager-quota-errors/select-usage-quotas.png)

4. V pravém horním rohu vyberte **požádat o zvýšení**.

   ![Zvýšení počtu žádostí](./media/resource-manager-quota-errors/request-increase.png)

5. Vyplňte formuláře pro typ kvóty, který potřebujete navýšit.

   ![Vyplňte formulář](./media/resource-manager-quota-errors/forms.png)