---
title: Kontrola stavu clusteru Průzkumníka dat Azure
description: Tento článek popisuje kroky ke sledování stavu clusteru Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a280d8869a3790444a97c38f792a3d9eeb6bde1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60861288"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Kontrola stavu clusteru Průzkumníka dat Azure

Existuje několik faktorů, které ovlivňují stav clusteru Azure Data Explorer, včetně procesoru, paměti a diskového subsystému. Tento článek ukazuje některé základní kroky, které můžete provést k měření stavu clusteru.

1. Přihlaste [https://dataexplorer.azure.com](https://dataexplorer.azure.com)se do .

1. V levém podokně vyberte cluster a spusťte následující příkaz.

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    Výstup 1 je v pořádku; výstup 0 není v pořádku.

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a přejděte do svého clusteru.

1. V části **Sledování**vyberte **Metriky**a pak vyberte **Keep Alive**, jak je znázorněno na následujícím obrázku. Výstup blízký 1 znamená cluster v pořádku.

    ![Metrika Zachovat naživu clusteru](media/check-cluster-health/portal-metrics.png)

1. Do grafu je možné přidat další metriky. Vyberte graf a pak **přidat metriku**. Vyberte jinou metriku - tento příklad ukazuje **PROCESOR**.

    ![Přidání metriky](media/check-cluster-health/add-metric.png)

1. Pokud potřebujete pomoc s diagnostikou problémů se stavem clusteru, otevřete žádost o podporu na [webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).