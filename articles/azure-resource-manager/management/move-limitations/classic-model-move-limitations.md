---
title: Přesunutí prostředků nasazení Azure Classic
description: Pomocí Správce prostředků Azure přesuňte prostředky klasického nasazení do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 78b9769a31fa0c96c12e18d05cb9c484aa52a1d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485283"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Přesunout pokyny pro prostředky modelu klasického nasazení

Postup přesunutí prostředků nasazených prostřednictvím klasického modelu se liší v závislosti na tom, zda přesouváte prostředky v rámci předplatného nebo na nové předplatné.

## <a name="move-in-the-same-subscription"></a>Přechod ve stejném předplatném

Při přesunu prostředků z jedné skupiny prostředků do jiné skupiny prostředků v rámci stejného předplatného platí následující omezení:

* Virtuální sítě (klasické) nelze přesunout.
* Virtuální počítače (klasické) musí být přesunuty pomocí cloudové služby.
* Cloudovou službu lze přesunout pouze v případě, že přesun zahrnuje všechny své virtuální počítače.
* Současně lze přesunout pouze jednu cloudovou službu.
* Současně lze přesouvat pouze jeden účet úložiště (klasický).
* Účet úložiště (klasický) nelze přesunout ve stejné operaci s virtuálním počítačem nebo cloudové služby.

Chcete-li přesunout klasické prostředky do nové skupiny prostředků v rámci stejného předplatného, použijte [standardní operace přesunutí](../move-resource-group-and-subscription.md) prostřednictvím portálu, Azure PowerShell, Azure CLI nebo REST API. Stejné operace, které používáte k přesunutí prostředků Správce prostředků, se používají stejné operace.

## <a name="move-across-subscriptions"></a>Přechod mezi předplatnými

Při přesunu prostředků do nového předplatného platí následující omezení:

* Všechny klasické prostředky v předplatném musí být přesunuty ve stejné operaci.
* Cílové předplatné nesmí mít žádné jiné klasické prostředky.
* Přesun lze požadovat pouze prostřednictvím samostatného rozhraní REST API pro klasické pohyby. Standardní Správce prostředků přesunout příkazy nefungují při přesunu klasických prostředků do nového předplatného.

Chcete-li přesunout klasické prostředky do nového předplatného, použijte operace REST, které jsou specifické pro klasické prostředky. Chcete-li použít rest, postupujte takto:

1. Zkontrolujte, jestli se zdrojové předplatné může účastnit přesunu mezi předplatnými. Použijte následující operaci:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     V textu žádosti uveďte:

   ```json
   {
    "role": "source"
   }
   ```

     Odpověď pro operaci ověření je v následujícím formátu:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Zkontrolujte, jestli se cílové předplatné může zúčastnit přesunu mezi předplatnými. Použijte následující operaci:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     V textu žádosti uveďte:

   ```json
   {
    "role": "target"
   }
   ```

     Odpověď je ve stejném formátu jako ověření zdrojového předplatného.
1. Pokud obě předplatná projít ověření, přesuňte všechny klasické prostředky z jednoho předplatného do jiného předplatného s následující operací:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    V textu žádosti uveďte:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

Operace může být spuštěna několik minut.

## <a name="next-steps"></a>Další kroky

Pokud máte potíže s přesunem klasických zdrojů, obraťte se [na podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Příkazy k přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../move-resource-group-and-subscription.md).
