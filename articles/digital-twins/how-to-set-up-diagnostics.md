---
title: Nastavení diagnostiky
titleSuffix: Azure Digital Twins
description: Viz jak povolit protokolování s nastavením diagnostiky.
author: baanders
ms.author: baanders
ms.date: 7/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 60ee2102a2b725c68e29afec9a44de03e52e9467
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374515"
---
# <a name="enable-logging-with-diagnostics-settings"></a>Povolit protokolování s nastavením diagnostiky

Data metriky můžete odeslat [Log Analytics](../azure-monitor/log-query/get-started-portal.md), koncovému bodu [Event Hubs](../event-hubs/event-hubs-about.md) nebo [Azure Storage](../storage/blobs/storage-blobs-overview.md) tím, že povolíte protokolování s nastavením diagnostiky pro vaši instanci.

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Zapnout nastavení diagnostiky pomocí Azure Portal

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k instanci digitálních vláken Azure. Můžete ji najít zadáním jejího názvu do panelu hledání na portálu. 

2. V nabídce vyberte **nastavení diagnostiky** a pak **přidejte nastavení diagnostiky**.

    :::image type="content" source="media/how-to-view-metrics/diagnostic-settings.png" alt-text="Snímek obrazovky zobrazující stránku nastavení diagnostiky a tlačítko, které se má přidat":::

3. Na následující stránce zadejte následující hodnoty:
 * **Název nastavení diagnostiky**: zadejte název diagnostického nastavení.
 * **Podrobnosti kategorie**: vyberte operace, které chcete monitorovat, a zaškrtněte políčka pro povolení diagnostiky pro tyto operace. Operace, které nastavení diagnostiky mohou hlásit:
    - DigitalTwinsOperation
    - EventRoutesOperation
    - ModelsOperation
    - QueryOperation
    - AllMetrics
 * **Podrobnosti cíle**: vyberte, kam chcete protokoly odesílat. Můžete vybrat libovolnou kombinaci tří možností:
    - Odeslání do Log Analytics
    - Archivovat v účtu úložiště
    - Streamovat do centra událostí

    Pokud jsou nutné pro výběr cíle, bude vám pravděpodobně požádáno, abyste doplnili další podrobnosti.  
    
4. Uložte nová nastavení. 

    :::image type="content" source="media/how-to-set-up-diagnostics/diagnostic-settings-details.png" alt-text="Snímek obrazovky zobrazující stránku nastavení diagnostiky a tlačítko, které se má přidat":::

Nové nastavení se projeví přibližně po dobu 10 minut. Po této konfiguraci se protokoly zobrazí na stránce **nastavení diagnostiky na stránce nastavení diagnostiky** pro vaši instanci. 

## <a name="next-steps"></a>Další kroky

* Další informace o konfiguraci diagnostiky najdete v tématu [*shromažďování a využívání dat protokolů z prostředků Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Informace o metrikách digitálních vláken Azure najdete v tématu [ *Postup: zobrazení metrik pomocí Azure monitor*](how-to-view-metrics.md)