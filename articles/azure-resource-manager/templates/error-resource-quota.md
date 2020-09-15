---
title: Chyby kvóty
description: Popisuje, jak vyřešit chyby kvót prostředků při nasazování prostředků pomocí Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 75e8abf31d035a1e3a106bc0c6561624762db5d5
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530416"
---
# <a name="resolve-errors-for-resource-quotas"></a>Řešení chyb týkajících se kvót prostředků

Tento článek popisuje problémy s kvótou, ke kterým může dojít při nasazování prostředků.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Příznak

Pokud nasadíte šablonu, která vytvoří prostředky, které překračují vaše kvóty Azure, zobrazí se chyba nasazení, která vypadá nějak takto:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Nebo se může zobrazit:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Příčina

Kvóty se uplatňují u jednotlivých skupin prostředků, předplatných, účtů a dalších oborů. Například v rámci předplatného může být nakonfigurované omezení počtu jader v jedné oblasti. Pokud se pokusíte nasadit virtuální počítač s více jádry, než je povolené množství, zobrazí se chyba s informací, že došlo k překročení kvóty.
Úplné informace o kvótě najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="azure-cli"></a>Azure CLI

V případě Azure CLI pomocí `az vm list-usage` příkazu Najděte kvóty virtuálních počítačů.

```azurecli
az vm list-usage --location "South Central US"
```

Který vrátí:

```output
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

Pro prostředí PowerShell použijte k nalezení kvót virtuálních počítačů příkaz **Get-AzVMUsage** .

```powershell
Get-AzVMUsage -Location "South Central US"
```

Který vrátí:

```output
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Řešení

Pokud chcete požádat o zvýšení kvóty, navštivte portál a zapište problém podpory. Ve svém problému podpory vyžádejte zvýšení kvóty pro oblast, do které chcete nasadit.

> [!NOTE]
> Pamatujte na to, že u skupin prostředků je kvóta pro každou jednotlivou oblast, ne pro celé předplatné. Pokud potřebujete nasadit 30 jader v Západní USA, musíte požádat o 30 Správce prostředků jader v Západní USA. Pokud potřebujete nasadit 30 jader v některé z oblastí, ke kterým máte přístup, měli byste požádat o 30 Správce prostředků jader ve všech oblastech.
>
>

1. Vyberte **Předplatná**.

   ![Snímek obrazovky zobrazuje nabídku portálu Azure s vybranými předplatnými.](./media/error-resource-quota/subscriptions.png)

2. Vyberte předplatné, které potřebuje navýšit kvótu.

   ![Výběr předplatného](./media/error-resource-quota/select-subscription.png)

3. Vybrat **využití a kvóty**

   ![Vybrat využití a kvóty](./media/error-resource-quota/select-usage-quotas.png)

4. V pravém horním rohu vyberte **požádat o zvýšení**.

   ![Zvýšení žádosti](./media/error-resource-quota/request-increase.png)

5. Vyplňte formuláře pro typ kvóty, který potřebujete navýšit.

   ![Vyplnit formulář](./media/error-resource-quota/forms.png)