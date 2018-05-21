---
title: Chyby Azure kvóty | Microsoft Docs
description: Popisuje, jak vyřešit chyby qouta prostředků.
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
ms.openlocfilehash: 6d9048ae531abedb89b70989ce1c962357c514cd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="resolve-errors-for-resource-quotas"></a>Vyřešte chyby pro kvótou prostředků

Tento článek popisuje chyb kvóty, které může dojít, pokud nasazení prostředků.

## <a name="symptom"></a>Příznaky

Pokud nasadíte šablonu, která vytváří prostředky, které překračují vaší kvótách Azure, dojde k chybě nasazení, bude vypadat takto:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Nebo, mohou se zobrazit:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Příčina

Kvóty se použijí na skupinu prostředků, odběry, účty a další obory. Omezit počet jader pro oblast lze nakonfigurovat například vaše předplatné. Pokud se pokusíte nasazení virtuálního počítače s více jader, než povolené množství, obdržíte chybu oznamující, že byla překročena kvóta.
Dokončení kvóty informace najdete v tématu [předplatného Azure a omezení služby, kvóty a omezení](../azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="azure-cli"></a>Azure CLI

Pokud používáte Azure CLI, použijte `az vm list-usage` příkazu najděte kvóty virtuálního počítače.

```azurecli
az vm list-usage --location "South Central US"
```

Která vrací:

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

Pro prostředí PowerShell, použijte **Get-AzureRmVMUsage** příkazu najděte kvóty virtuálního počítače.

```powershell
Get-AzureRmVMUsage -Location "South Central US"
```

Která vrací:

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Řešení

Požádat o zvýšení kvóty, přejděte na portál a souborů podpory problém. V problém podporu požádejte o zvýšení v rámci svojí kvóty pro oblast, do které chcete nasadit.

> [!NOTE]
> Nezapomeňte, že pro skupiny prostředků, se kvóty pro každou oblast jednotlivých, ne pro celé předplatné. Pokud potřebujete nasadit 30 jader v západní USA, budete muset požádat o 30 Resource Manager jader v západní USA. Pokud potřebujete nasadit 30 jader v některém z oblasti, ke které máte přístup, je třeba požádat o 30 jader Resource Manager ve všech oblastech.
>
>

1. Vyberte **odběry**.

   ![Předplatná](./media/resource-manager-quota-errors/subscriptions.png)

2. Vyberte odběr, který potřebuje vyšší kvóty.

   ![Výběr předplatného](./media/resource-manager-quota-errors/select-subscription.png)

3. Vyberte **využití + kvóty**

   ![Vybrat využití a kvóty](./media/resource-manager-quota-errors/select-usage-quotas.png)

4. V pravém horním rohu vyberte **požádat o zvýšení**.

   ![Zvýšení počtu žádostí](./media/resource-manager-quota-errors/request-increase.png)

5. Vyplňte formuláře pro typ kvóty, které je potřeba zvýšit.

   ![Vyplňte formulář](./media/resource-manager-quota-errors/forms.png)