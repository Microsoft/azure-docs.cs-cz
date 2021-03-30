---
title: Přesunout prostředky nasazení Azure Classic
description: Pomocí Azure Resource Manager můžete přesunout prostředky nasazení Classic do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 78b9769a31fa0c96c12e18d05cb9c484aa52a1d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "75485283"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Pokyny k přesunutí pro prostředky modelu nasazení Classic

Postup přesunutí prostředků nasazených přes klasický model se liší v závislosti na tom, zda přesouváte prostředky v rámci předplatného nebo na nové předplatné.

## <a name="move-in-the-same-subscription"></a>Přesunout do stejného předplatného

Při přesouvání prostředků z jedné skupiny prostředků do jiné skupiny prostředků v rámci stejného předplatného platí následující omezení:

* Virtuální sítě (klasické) nejde přesunout.
* Virtuální počítače (Classic) se musí přesouvat s cloudovou službou.
* Cloudovou službu lze přesunout pouze v případě, že přesun zahrnuje všechny své virtuální počítače.
* V jednu chvíli se dá přesunout jenom jedna cloudová služba.
* Současně lze přesunout pouze jeden účet úložiště (Classic).
* Účet úložiště (Classic) nejde přesunout do stejné operace s virtuálním počítačem nebo cloudovou službou.

Pokud chcete přesunout klasické prostředky do nové skupiny prostředků v rámci stejného předplatného, použijte [operace přesunu Standard](../move-resource-group-and-subscription.md) na portálu, Azure PowerShell, Azure CLI nebo REST API. Stejné operace použijte při přesunu Správce prostředkůch prostředků.

## <a name="move-across-subscriptions"></a>Pohyb mezi předplatnými

Při přesouvání prostředků do nového předplatného platí následující omezení:

* Všechny klasické prostředky v předplatném je nutné přesunout do stejné operace.
* Cílové předplatné nesmí mít žádné jiné klasické prostředky.
* Přesunutí se dá požadovat jenom pomocí samostatného REST API pro klasická přesuny. Při přesunu klasických prostředků do nového předplatného nefungují standardní Správce prostředků přesun příkazů.

Pokud chcete přesunout klasické prostředky do nového předplatného, použijte operace REST, které jsou specifické pro klasické prostředky. Chcete-li použít REST, proveďte následující kroky:

1. Ověřte, jestli se zdrojové předplatné může účastnit přesunu mezi předplatnými. Použijte následující operaci:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     Do textu žádosti zadejte:

   ```json
   {
    "role": "source"
   }
   ```

     Odpověď na operaci ověřování je v následujícím formátu:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Ověřte, jestli se cílové předplatné může účastnit přesunu mezi předplatnými. Použijte následující operaci:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     Do textu žádosti zadejte:

   ```json
   {
    "role": "target"
   }
   ```

     Odpověď má ve stejném formátu jako ověření zdrojového předplatného.
1. Pokud oba odběry projdou ověřením, přesuňte všechny klasické prostředky z jednoho předplatného do jiného předplatného s následující operací:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    Do textu žádosti zadejte:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

Tato operace může běžet několik minut.

## <a name="next-steps"></a>Další kroky

Pokud máte potíže s přesunem klasických prostředků, obraťte se na [podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../move-resource-group-and-subscription.md).
