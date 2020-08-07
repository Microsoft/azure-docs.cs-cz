---
title: Nastavení diagnostiky
titleSuffix: Azure Digital Twins
description: Viz jak povolit protokolování s nastavením diagnostiky.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 0376a57e3f2c1158e9da97fb291a28c99ce2463c
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903951"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Řešení potíží se službou Azure Digital nevláken: protokolování diagnostiky

Digitální vlákna Azure shromažďuje [metriky](troubleshoot-metrics.md) pro vaši instanci služby, která poskytuje informace o stavu vašich prostředků. Tyto metriky můžete použít k vyhodnocení celkového stavu služby digitálních vláken Azure a prostředků, které jsou k ní připojené. Tyto údaje zaměřené na uživatele vám pomůžou zjistit, co se týká vašich digitálních vláken Azure, a pomáhat s analýzou příčin potíží, aniž byste museli kontaktovat podporu Azure.

V tomto článku se dozvíte, jak zapnout **protokolování diagnostiky** pro data metrik z instance digitálního vlákna Azure. Tyto protokoly můžete použít k usnadnění odstraňování potíží se službou a ke konfiguraci nastavení diagnostiky pro odesílání metriky digitálních vláken Azure do různých míst určení. Další informace o těchto nastaveních najdete v tématu [*Vytvoření nastavení diagnostiky pro odesílání protokolů a metrik platforem do různých umístění*](../azure-monitor/platform/diagnostic-settings.md).

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Zapnout nastavení diagnostiky pomocí Azure Portal

Tady je postup povolení nastavení diagnostiky instance digitálního vlákna Azure:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k instanci digitálních vláken Azure. Můžete ji najít zadáním jejího názvu do panelu hledání na portálu. 

2. V nabídce vyberte **nastavení diagnostiky** a pak **přidejte nastavení diagnostiky**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Snímek obrazovky zobrazující stránku nastavení diagnostiky a tlačítko, které se má přidat":::

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

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Snímek obrazovky zobrazující stránku nastavení diagnostiky a tlačítko, které se má přidat":::

Nové nastavení se projeví přibližně po dobu 10 minut. Po této konfiguraci se protokoly zobrazí na stránce **nastavení diagnostiky na stránce nastavení diagnostiky** pro vaši instanci. 

## <a name="next-steps"></a>Další kroky

* Další informace o konfiguraci diagnostiky najdete v tématu [*shromažďování a využívání dat protokolů z prostředků Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Informace o metrikách digitálních vláken Azure najdete v tématu [*řešení potíží: zobrazení metrik pomocí Azure monitor*](troubleshoot-metrics.md).
* Informace o tom, jak povolit výstrahy pro metriky, najdete v tématu [*řešení potíží: nastavení výstrah*](troubleshoot-alerts.md).
