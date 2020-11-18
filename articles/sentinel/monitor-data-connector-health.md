---
title: Monitorování stavu datových konektorů pomocí tohoto sešitu Sentinel Azure | Microsoft Docs
description: Pomocí sešitu monitorování stavu můžete sledovat možnosti připojení a výkonu datových konektorů.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: yelevin
ms.openlocfilehash: 7d52b76601a617f62ae5b10fa38841ef2608bf49
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656986"
---
# <a name="monitor-the-health-of-your-data-connectors-with-this-azure-sentinel-workbook"></a>Monitorování stavu datových konektorů pomocí tohoto sešitu Sentinel v Azure

**Sešit monitorování stavu datových konektorů** umožňuje sledovat stav, konektivitu a výkon datových konektorů v rámci služby Azure Sentinel. Sešit poskytuje další monitory, detekuje anomálie a poskytuje přehled o stavu příjmu dat v pracovním prostoru. Pomocí logiky sešitu můžete monitorovat celkový stav přijatých dat a vytvářet vlastní zobrazení a výstrahy založené na pravidlech.

## <a name="use-the-health-monitoring-workbook"></a>Použití sešitu monitorování stavu

1. Na portálu Sentinel Azure v nabídce **Správa hrozeb** vyberte **sešity** .

1. V galerii **sešitů** zadejte do panelu hledání *stav* a z výsledků vyberte **monitorování stavu shromažďování dat** .

1. Vyberte možnost **Zobrazit šablonu** a použijte sešit jako je, nebo vyberte **Uložit** a vytvořte upravitelnou kopii sešitu. Po vytvoření kopie vyberte **Zobrazit uložený sešit**.

1. V sešitu nejdřív vyberte **předplatné** a **pracovní prostor** , který chcete zobrazit, a pak definujte **TimeRange** , aby se data vyfiltroval podle vašich potřeb. Chcete-li zobrazit místní vysvětlení sešitu, použijte přepínač **Zobrazit pomocníka** .

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-1.png" alt-text="Cílová stránka sešitu monitorování stavu datového konektoru" lightbox="media/monitor-data-connector-health/data-health-workbook-1.png":::

V tomto sešitu jsou tři oddíly s kartami:

1. Karta **Přehled** zobrazuje celkový stav příjmu dat ve vybraném pracovním prostoru: měření svazku, míry EPS a čas posledního přijetí protokolu.

1. Karta **anomálie kolekce dat** vám pomůže detekovat anomálie v procesu shromažďování dat podle tabulky a zdroje dat. Každá karta zobrazuje anomálie pro konkrétní tabulku (karta **Obecné** obsahuje kolekci tabulek). Anomálie se počítají pomocí funkce **series_decompose_anomalies ()** , která vrací **skóre anomálií**. [Další informace o této funkci](/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction?WT.mc_id=Portal-fx) Nastavte následující parametry pro funkci, která se má vyhodnotit:

    - **AnomaliesTimeRange:** Tento výběr času se vztahuje jenom na zobrazení anomálií shromažďování dat.
    - **SampleInterval:** Časový interval, ve kterém jsou data v zadaném časovém rozsahu vzorkovaná. Skóre anomálií se počítá jenom pro data z posledního intervalu.
    - **PositiveAlertThreshold:** Tato hodnota definuje kladnou prahovou hodnotu skóre anomálií. Přijímá desítkové hodnoty.
    - **NegativeAlertThreshold:** Tato hodnota definuje zápornou prahovou hodnotu skóre anomálií. Přijímá desítkové hodnoty.

        :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-2.png" alt-text="Stránka s monitorováním stavu datových konektorů v sešitu" lightbox="media/monitor-data-connector-health/data-health-workbook-2.png":::

1. Na kartě **informace o agentovi** se zobrazí informace o stavu agentů Log Analytics nainstalovaných na různých počítačích, ať už se jedná o virtuální počítač Azure, jiný CLOUDový virtuální počítač, místní virtuální počítač nebo fyzický. Můžete monitorovat následující:

   - Umístění systému

   - Stav prezenčního signálu a latence

   - Dostupná paměť a místo na disku

   - Operace agenta

    V této části musíte vybrat kartu, která popisuje prostředí počítačů: zvolte kartu **počítače spravované Azure** , pokud chcete zobrazit jenom počítače spravované službou Azure ARC. Kliknutím na kartu **všechny počítače** zobrazíte spravované i mimo Azure počítače s nainstalovaným agentem Log Analytics.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-3.png" alt-text="Stránka informace o agentovi monitorování stavu datových konektorů" lightbox="media/monitor-data-connector-health/data-health-workbook-3.png":::

## <a name="next-steps"></a>Další kroky
Naučte se, jak začlenit [data do služby Azure Sentinel](quickstart-onboard.md), [připojit zdroje dat](connect-data-sources.md)a [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).