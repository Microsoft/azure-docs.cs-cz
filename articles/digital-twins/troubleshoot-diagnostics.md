---
title: Nastavení diagnostiky
titleSuffix: Azure Digital Twins
description: Viz jak povolit protokolování s nastavením diagnostiky.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: d47bb7cc868c5733c6e36290f097fec783764cd3
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "88003584"
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
        
        Další podrobnosti o těchto možnostech najdete v části [*Podrobnosti o kategorii*](#category-details) níže.
     * **Podrobnosti cíle**: vyberte, kam chcete protokoly odesílat. Můžete vybrat libovolnou kombinaci tří možností:
        - Odeslání do Log Analytics
        - Archivovat v účtu úložiště
        - Streamovat do centra událostí

        Pokud jsou nutné pro výběr cíle, bude vám pravděpodobně požádáno, abyste doplnili další podrobnosti.  
    
4. Uložte nová nastavení. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Snímek obrazovky zobrazující stránku nastavení diagnostiky a tlačítko, které se má přidat":::

Nové nastavení se projeví přibližně po dobu 10 minut. Po této konfiguraci se protokoly zobrazí na stránce **nastavení diagnostiky na stránce nastavení diagnostiky** pro vaši instanci. 

## <a name="category-details"></a>Podrobnosti kategorie

Tady jsou další podrobnosti o kategoriích protokolů, které se dají vybrat v části **Podrobnosti o kategorii** při nastavování nastavení diagnostiky.

| Kategorie protokolu | Popis |
| --- | --- |
| ADTModelsOperation | Protokolovat všechna volání rozhraní API týkající se modelů |
| ADTQueryOperation | Protokolování všech volání rozhraní API vztahujících se k dotazům |
| ADTEventRoutesOperation | Protokolujte všechna volání rozhraní API, která souvisí s trasami událostí, a také výstup událostí z digitálních vláken Azure do služby koncového bodu, jako je Event Grid, Event Hubs a Service Bus |
| ADTDigitalTwinsOperation | Protokolovat všechna volání rozhraní API týkající se digitálních vláken Azure |

Každá kategorie protokolu se skládá z operací zápisu, čtení, odstranění a akce.  Tato mapa pro REST API volání následujícím způsobem:

| Typ události | REST API operace |
| --- | --- |
| Zápis | Vložit a opravit |
| Číst | GET |
| Odstranit | DELETE |
| Akce | POST |

Tady je vyčerpávající seznam operací a odpovídajících [digitálních vláken Azure REST API volání](https://docs.microsoft.com/rest/api/azure-digitaltwins/) , která se protokolují v jednotlivých kategoriích. 

>[!NOTE]
> Každá kategorie protokolu obsahuje několik operací/REST API volání. V následující tabulce jsou všechny kategorie protokolů namapovány na všechny operace/REST API volání pod ní, dokud není uvedena další kategorie protokolu. 

| Kategorie protokolu | Operace | REST API volání a další události |
| --- | --- | --- |
| ADTModelsOperation | Microsoft. DigitalTwins/modely/Write | Digitální nazdvojené modely – rozhraní API pro aktualizaci |
|  | Microsoft. DigitalTwins/modely/číst | Digitální zdvojené modely, které získají podle ID a rozhraní API seznamů |
|  | Microsoft. DigitalTwins/modely/DELETE | Digitální vyzdvojené modely – odstranění rozhraní API |
|  | Microsoft. DigitalTwins/modely/akce | Digitální nazdvojené modely – přidání rozhraní API |
| ADTQueryOperation | Microsoft. DigitalTwins/dotaz/akce | Rozhraní API pro vyzdvojené dotazy |
| ADTEventRoutesOperation | Microsoft. DigitalTwins/eventroutes/Write | Směrování událostí – přidat rozhraní API |
|  | Microsoft. DigitalTwins/eventroutes/Read | Rozhraní API pro směrování událostí Get podle ID a seznamu |
|  | Microsoft. DigitalTwins/eventroutes/DELETE | Rozhraní API pro odstranění tras událostí |
|  | Microsoft. DigitalTwins/eventroutes/Action | Výstup události do služby koncového bodu (nikoli volání rozhraní API) |
| ADTDigitalTwinsOperation | Microsoft. DigitalTwins/DigitalTwins/Write | Digitální vlákna přidat, přidat vztah, aktualizovat, aktualizovat komponentu |
|  | Microsoft. DigitalTwins/DigitalTwins/Read | Digitální vlákna získá podle ID, získá komponentu, získá relaci podle ID, vypíše příchozí vztahy, vypíše relace. |
|  | Microsoft. DigitalTwins/DigitalTwins/DELETE | Digitální vlákna odstranit, odstranit relaci |
|  | Microsoft. DigitalTwins/DigitalTwins/Action | Digitální vlákna odesílají telemetrii komponent, odesílají telemetrii. |

## <a name="next-steps"></a>Další kroky

* Další informace o konfiguraci diagnostiky najdete v tématu [*shromažďování a využívání dat protokolů z prostředků Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Informace o metrikách digitálních vláken Azure najdete v tématu [*řešení potíží: zobrazení metrik pomocí Azure monitor*](troubleshoot-metrics.md).
* Informace o tom, jak povolit výstrahy pro metriky, najdete v tématu [*řešení potíží: nastavení výstrah*](troubleshoot-alerts.md).
