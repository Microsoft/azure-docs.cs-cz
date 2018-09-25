---
title: Zkontrolujte stav clusteru služby Průzkumník dat Azure
description: Tento článek popisuje kroky k určení, zda je váš cluster Průzkumník dat Azure v pořádku.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0746247d2c912ba66e81b95f45b168e32b522130
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46988423"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Zkontrolujte stav clusteru služby Průzkumník dat Azure

Existuje několik faktorů, které mají vliv na stav clusteru služby Průzkumník dat Azure, včetně CPU, paměť a diskový subsystém. Tento článek popisuje některé základní kroky, že které můžete provést ke zjišťování stavu clusteru.

1. Přihlaste se k [ https://dataexplorer.azure.com ](https://dataexplorer.azure.com).

1. V levém podokně vyberte svůj cluster a spusťte následující příkaz.

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    Výstup 1 je v pořádku; výstup 0 není v pořádku.

1. Přihlaste se [webu Azure portal](https://portal.azure.com)a přejděte ke svému clusteru.

1. V části **monitorování**vyberte **metriky**a pak vyberte **zachování**, jak je znázorněno na následujícím obrázku. Výstupní hodnota blížící se 1 znamená, že cluster v pořádku.

    ![Zachování metriky clusteru](media/check-cluster-health/portal-metrics.png)

1. Přidáte jiné metriky, jako je procesor a ukládání do mezipaměti měřidla využití prostředků pro cluster.

1. Pokud potřebujete pomoci diagnostikovat problémy s stav clusteru, otevřete prosím v žádosti o podporu [webu Azure portal](https://portal.azure.com).