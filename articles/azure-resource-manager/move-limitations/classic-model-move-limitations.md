---
title: Přesunutí prostředků Azure Classic nasazení do nové předplatné nebo skupinu prostředků
description: Pomocí Azure Resource Manageru pro přesun prostředků nasazení Classic do nové skupiny prostředků nebo předplatného.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 4770f957b6b9eea75b50776a7491b1ca479e50e2
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723503"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Pokyny k přesunutí prostředků modelu nasazení Classic

Kroky pro přesun prostředky nasazené prostřednictvím klasického modelu liší v závislosti na tom, jestli přesouváte prostředky v rámci předplatného nebo do nového předplatného.

## <a name="move-in-the-same-subscription"></a>Přesunout v rámci stejného předplatného

Při přesouvání prostředků z jedné skupiny prostředků do jiné skupiny prostředků v rámci stejného předplatného, platí následující omezení:

* Nelze přesunout virtuální sítě (classic).
* Virtuální počítače (classic) musí přesunout s cloudovou službou.
* Cloudová služba se dá přesunout jedině po přesunutí zahrnuje všechny virtuální počítače.
* Pouze jednu cloudovou službu se dají přesunout najednou.
* Pouze jeden účet úložiště (classic) je možné přesunout najednou.
* Účet úložiště (classic) se nedají přesouvat v rámci jedné operace se virtuální počítač nebo cloudovou službu.

Chcete-li přesunout klasické prostředky do nové skupiny prostředků v rámci stejného předplatného, použijte [operací přesunu standardní](../resource-group-move-resources.md) na portálu, Azure Powershellu, rozhraní příkazového řádku Azure nebo rozhraní REST API. Můžete použít stejné operace jako při přesouvání prostředků Resource Manageru.

## <a name="move-across-subscriptions"></a>Přesun mezi předplatnými

Při přesouvání prostředků do nového předplatného, platí následující omezení:

* V rámci jedné operace musí přesunout všechny klasické prostředky v předplatném.
* Cílové předplatné nesmí mít ostatní klasické prostředky.
* Přesunutí je možné jenom požádat prostřednictvím samostatných rozhraní REST API pro klasické přesuny. Standardní příkazy Resource Manager přesunout nefungují při přesouvání klasických prostředků do nového předplatného.

Klasické prostředky přesunout do nového předplatného, pomocí operace REST, které jsou specifické pro klasické prostředky. Pokud chcete použít REST, proveďte následující kroky:

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

## <a name="next-steps"></a>Další postup

Pokud máte potíže při přesouvání klasických prostředků, obraťte se na [podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Příkazy pro přesun prostředky, najdete v části [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../resource-group-move-resources.md).
