---
title: Přesunout prostředky nasazení Azure Classic
description: Pomocí Azure Resource Manager můžete přesunout prostředky nasazení Classic do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 78b9769a31fa0c96c12e18d05cb9c484aa52a1d5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485283"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Pokyny k přesunutí pro prostředky modelu nasazení Classic

Postup přesunutí prostředků nasazených přes klasický model se liší v závislosti na tom, zda přesouváte prostředky v rámci předplatného nebo na nové předplatné.

## <a name="move-in-the-same-subscription"></a>Přesunout do stejného předplatného

Při přesouvání prostředků z jedné skupiny prostředků do jiné skupiny prostředků v rámci stejného předplatného, platí následující omezení:

* Nelze přesunout virtuální sítě (classic).
* Virtuální počítače (classic) musí přesunout s cloudovou službou.
* Cloudová služba se dá přesunout jedině po přesunutí zahrnuje všechny virtuální počítače.
* Pouze jednu cloudovou službu se dají přesunout najednou.
* Pouze jeden účet úložiště (classic) je možné přesunout najednou.
* Účet úložiště (classic) se nedají přesouvat v rámci jedné operace se virtuální počítač nebo cloudovou službu.

Pokud chcete přesunout klasické prostředky do nové skupiny prostředků v rámci stejného předplatného, použijte [operace přesunu Standard](../move-resource-group-and-subscription.md) na portálu, Azure PowerShell, Azure CLI nebo REST API. Můžete použít stejné operace jako při přesouvání prostředků Resource Manageru.

## <a name="move-across-subscriptions"></a>Pohyb mezi předplatnými

Při přesouvání prostředků do nového předplatného, platí následující omezení:

* V rámci jedné operace musí přesunout všechny klasické prostředky v předplatném.
* Cílové předplatné nesmí mít žádné jiné klasické prostředky.
* Přesunutí je možné jenom požádat prostřednictvím samostatných rozhraní REST API pro klasické přesuny. Standardní příkazy Resource Manager přesunout nefungují při přesouvání klasických prostředků do nového předplatného.

Klasické prostředky přesunout do nového předplatného, pomocí operace REST, které jsou specifické pro klasické prostředky. Chcete-li použít REST, proveďte následující kroky:

1. Zaškrtněte, pokud zdrojové předplatné mohl podílet na přesun mezi předplatnými. Použijte následující operace:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     V textu požadavku patří:

   ```json
   {
    "role": "source"
   }
   ```

     Odpověď pro operace ověření je v následujícím formátu:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Zaškrtněte, pokud cílové předplatné se mohou účastnit přesun mezi předplatnými. Použijte následující operace:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     V textu požadavku patří:

   ```json
   {
    "role": "target"
   }
   ```

     Odpověď je ve stejném formátu jako zdroj ověření předplatného.
1. Pokud obě předplatná projít ověřením, přesune všechny klasické prostředky z jednoho předplatného do jiného předplatného pomocí následující operace:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    V textu požadavku patří:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

Operace může běžet několik minut.

## <a name="next-steps"></a>Další kroky

Pokud máte potíže s přesunem klasických prostředků, obraťte se na [podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../move-resource-group-and-subscription.md).
