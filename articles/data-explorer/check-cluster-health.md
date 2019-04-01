---
title: Zkontrolujte stav clusteru služby Průzkumník dat Azure
description: Tento článek popisuje kroky k určení, zda je váš cluster Průzkumník dat Azure v pořádku.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 8930c2a7538ca33622de68c9a888349b3301cd98
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755861"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Zkontrolujte stav clusteru služby Průzkumník dat Azure

Existuje několik faktorů, které mají vliv na stav clusteru služby Průzkumník dat Azure, včetně CPU, paměť a diskový subsystém. Tento článek popisuje některé základní kroky, že které můžete provést ke zjišťování stavu clusteru.

1. Přihlaste se k webu [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. V levém podokně vyberte svůj cluster a spusťte následující příkaz.

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    Výstup 1 je v pořádku; výstup 0 není v pořádku.

1. Přihlaste se [webu Azure portal](https://portal.azure.com)a přejděte ke svému clusteru.

1. V části **monitorování**vyberte **metriky**a pak vyberte **zachování**, jak je znázorněno na následujícím obrázku. Výstupní hodnota blížící se 1 znamená, že cluster v pořádku.

    ![Zachování metriky clusteru](media/check-cluster-health/portal-metrics.png)

1. Je možné přidat další metriky do grafu. Vyberte graf, pak **přidat metriku**. Vybrat jinou metriku – tento příklad ukazuje **procesoru**.

    ![Přidání metriky](media/check-cluster-health/add-metric.png)

1. Pokud potřebujete pomoci diagnostikovat problémy s stav clusteru, otevřete prosím v žádosti o podporu [webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).