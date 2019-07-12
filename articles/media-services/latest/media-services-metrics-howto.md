---
title: Zobrazení metriky ve službě Azure Monitor
description: Tento článek ukazuje, jak monitorovat metriky ve službě Azure portal grafy a rozhraní příkazového řádku Azure.
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
ms.openlocfilehash: d331dc4eb0c6668426e1ab1a01a1dd1dcebe0c85
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795827"
---
# <a name="monitor-media-services-metrics"></a>Monitorování metrik služby Media Services 

[Azure Monitor](../../azure-monitor/overview.md) umožňuje monitorování metrik a diagnostické protokoly, které vám pomohou pochopit, jaký výkon vašich aplikací. Podrobný popis funkcí a pokud chcete zjistit, proč můžete chtít používat protokoly Diagnostika a metriky Azure Media Services, najdete v tématu [Media Services monitorování metrik a diagnostických protokolů](media-services-metrics-diagnostic-logs.md).

Platforma Azure Monitor poskytuje několik způsobů, jak pracovat s metriky, včetně grafů na portálu, přístup přes rozhraní REST API nebo jejich dotazování pomocí rozhraní příkazového řádku. Tento článek ukazuje, jak monitorovat metriky ve službě Azure portal grafy a rozhraní příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky

- [Vytvoření účtu Media Services](create-account-cli-how-to.md)
- Kontrola [Media Services monitorování metrik a diagnostických protokolů](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Zobrazení metrik na webu Azure portal

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
1. Přejděte do svého účtu Azure Media Services a vyberte **metriky**.
1. Klikněte na tlačítko **prostředků** pole a vyberte prostředek, pro kterou chcete monitorovat metriky. 

    **Vyberte prostředek** okna se zobrazí na pravé straně se seznamem prostředky dostupné pro vás. V tomto případě se zobrazí 

    * &lt;Název účtu Media Services&gt;
    * &lt;Název účtu Media Services&gt;/&lt;název koncového bodu streamování&gt;
    * &lt;Název účtu úložiště&gt;

    Vyberte prostředek a stiskněte klávesu **použít**. Podrobnosti o podporovaných zdrojích a metrik najdete v tématu [metriky monitorování Media Services](media-services-metrics-diagnostic-logs.md).
 
    ![Metriky](media/media-services-metrics/metrics02.png)
    
    > [!NOTE]
    > Chcete-li přepnout mezi prostředky, pro které chcete monitorovat metriky, klikněte na **prostředků** znovu pole a opakujte tento krok.
1. (Volitelně) pojmenujte graf (Upravit název stisknutím klávesy tužky v horní části).
1. Přidáte metriky, které chcete zobrazit.

    ![Metriky](media/media-services-metrics/metrics03.png)
1. Připnout graf na řídicí panel.

## <a name="view-metrics-with-azure-cli"></a>Zobrazení metrik pomocí Azure CLI

Získat metriky "Výchozí přenos dat" pomocí rozhraní příkazového řádku, spustili byste následující `az monitor metrics` příkazu rozhraní příkazového řádku:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Pokud chcete získat další metriky, nahraďte "Výstupní" název metriky, které vás zajímají.

## <a name="see-also"></a>Viz také:

* [Metriky Azure Monitor](../../azure-monitor/platform/data-platform.md)
* [Vytvořit, zobrazit a spravovat upozornění na metriku pomocí Azure monitoru](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Další postup

[Diagnostické protokoly](media-services-diagnostic-logs-howto.md)
