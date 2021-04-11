---
title: Zobrazit metriky pomocí Azure Monitor
description: Tento článek ukazuje, jak monitorovat metriky pomocí Azure Portalch grafů a Azure CLI.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/17/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: cedadf6b6026c74e25e9c81feb7f8291058b6f69
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961655"
---
# <a name="monitor-media-services-metrics"></a>Monitorování metrik služby Media Services

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

[Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview.md) vám umožní monitorovat metriky a diagnostické protokoly, které vám pomůžou pochopit, jak vaše aplikace provádí. Podrobný popis této funkce a informace o tom, proč byste měli použít Azure Media Services metriky a diagnostické protokoly, najdete v tématu [monitorování metrik Media Services a diagnostických protokolů](monitor-media-services-data-reference.md).

Azure Monitor poskytuje několik způsobů, jak pracovat s metrikami, včetně jejich grafu na portálu, přístupu k nim přes REST API nebo jejich dotazování pomocí rozhraní příkazového řádku Azure CLI. Tento článek ukazuje, jak monitorovat metriky pomocí Azure Portalch grafů a Azure CLI.

## <a name="prerequisites"></a>Požadavky

- [Vytvoření účtu Media Services](../account-create-how-to.md)
- Kontrola  [monitorování Media Services metriky a diagnostické protokoly](monitor-media-services-data-reference.md)

## <a name="view-metrics-in-azure-portal"></a>Zobrazit metriky v Azure Portal

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
1. Přejděte na účet Azure Media Services a vyberte **metriky**.
1. Klikněte na pole **obor** a vyberte prostředek, který chcete monitorovat.

    Na pravé straně se zobrazí okno **Vybrat obor** a seznam dostupných prostředků. V takovém případě se zobrazí:

    * &lt;Název účtu Media Services&gt;
    * &lt;&gt; / &lt; Název koncového bodu streamování názvu Media Services účtu&gt;
    * &lt;název účtu úložiště&gt;

    Filtr vyberte prostředek a stiskněte **použít**. Podrobnosti o podporovaných prostředcích a metrikách najdete v tématu [monitorování Media Services metriky](monitor-media-services-data-reference.md).

    > [!NOTE]
    > Pokud chcete přepínat mezi prostředky, které chcete monitorovat, klikněte znovu na **zdrojové** pole a opakujte tento krok.

1. Volitelné: zadejte název grafu (upravte název stisknutím tužky v horní části).
1. Přidejte metriky, které chcete zobrazit.
1. Svůj graf můžete připnout na řídicí panel.

## <a name="view-metrics-with-azure-cli"></a>Zobrazení metrik pomocí Azure CLI

Pokud chcete získat metriky "odchozích" pomocí Azure CLI, spusťte následující `az monitor metrics` příkaz:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Pokud chcete získat další metriky, nahraďte "výstup" pro název metriky, které vás zajímá.

## <a name="see-also"></a>Viz také

- [Metriky Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/data-platform.md)
- [Umožňuje vytvářet, zobrazovat a spravovat výstrahy metrik pomocí Azure monitor](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-metric.md).

## <a name="next-steps"></a>Další kroky

[Diagnostické protokoly](../media-services-diagnostic-logs-howto.md)
