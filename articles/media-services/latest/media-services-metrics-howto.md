---
title: Zobrazení metrik pomocí Azure Monitoru
description: Tento článek ukazuje, jak sledovat metriky pomocí grafů portálu Azure a Azure CLI.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: c230e1e950bb924631032940642a6202acf4ade8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382932"
---
# <a name="monitor-media-services-metrics"></a>Monitorování metrik služby Media Services

[Azure Monitor](../../azure-monitor/overview.md) umožňuje monitorovat metriky a diagnostické protokoly, které vám pomohou pochopit, jak si vaše aplikace vedou. Podrobný popis této funkce a zjistěte, proč byste chtěli používat protokoly metrik a diagnostiky Azure Media Services, najdete [v tématu Monitor Media Services metriky a diagnostické protokoly](media-services-metrics-diagnostic-logs.md).

Azure Monitor poskytuje několik způsobů, jak pracovat s metrikami, včetně jejich vytváření grafů na portálu, přístupu k nim prostřednictvím rozhraní REST API nebo dotazování pomocí rozhraní příkazového příkazového příkazu Azure. Tento článek ukazuje, jak sledovat metriky pomocí grafů portálu Azure a Azure CLI.

## <a name="prerequisites"></a>Požadavky

- [Vytvoření účtu Media Services](create-account-cli-how-to.md)
- Zkontrolovat [metriky a diagnostické protokoly služby Monitor Media Services](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Zobrazení metrik na Webu Azure Portal

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
1. Přejděte na svůj účet Mediální služby Azure a vyberte **metriky**.
1. Klikněte na pole **ZDROJ** a vyberte zdroj, pro který chcete sledovat metriky.

    Vpravo se zobrazí okno **Vybrat zdroj** se seznamem zdrojů, které máte k dispozici. V tomto případě vidíte:

    * &lt;Název účtu služby Media Services&gt;
    * &lt;Název koncového&gt;/&lt;bodu datového proudu účtu služby Media Services&gt;
    * &lt;název účtu úložiště&gt;

    Vyberte zdroj a stiskněte **klávesu Použít**. Podrobnosti o podporovaných prostředcích a metrikách naleznete [v tématu Metriky Monitor Media Services](media-services-metrics-diagnostic-logs.md).

    ![Metriky](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > Chcete-li přepínat mezi prostředky, u kterých chcete sledovat metriky, klikněte znovu na pole **ZDROJ** a opakujte tento krok.
1. (Volitelně) pojmenujte graf (upravte název stisknutím tužky v horní části).
1. Přidejte metriky, které chcete zobrazit.

    ![Metriky](media/media-services-metrics/metrics03.png)
1. Graf můžete připnout na řídicí panel.

## <a name="view-metrics-with-azure-cli"></a>Zobrazení metrik pomocí azure CLI

Chcete-li získat metriky "Odchozí" s Azure CLI, spustíte následující `az monitor metrics` příkaz:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Chcete-li získat další metriky, nahraďte název metriky, která vás zajímá, "Odchozí přenos".

## <a name="see-also"></a>Viz také

* [Metriky azure monitoru](../../azure-monitor/platform/data-platform.md)
* [Vytvářejte, zobrazujte a spravujte upozornění na metriky pomocí Azure Monitoru](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Další kroky

[Diagnostické protokoly](media-services-diagnostic-logs-howto.md)
