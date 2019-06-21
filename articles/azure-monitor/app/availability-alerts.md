---
title: Nastavení upozornění na dostupnost Azure Application insights | Dokumentace Microsoftu
description: Nastavení testů webu ve službě Application Insights. Zasílání upozornění, pokud web přestane být k dispozici nebo reaguje pomalu.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: cc022f91d4b4fec42929769df8c979320548a1f9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304905"
---
# <a name="availability-alerts"></a>Upozornění na dostupnost

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) odesílá do vaší aplikace webové požadavky v pravidelných intervalech z bodů po celém světě. To vás může upozornit, pokud vaše aplikace nereaguje nebo zda stránka reaguje příliš pomalu.

## <a name="enable-alerts"></a>Povolení upozornění

Výstrahy jsou nyní automaticky povolené ve výchozím nastavení, ale aby bylo možné plně konfigurovat výstrahy, je nejprve nutné nejprve vytvořit test dostupnosti.

![Vytvoření prostředí](./media/availability-alerts/create-test.png)

> [!NOTE]
>  S [oznámení pro nové sjednocené](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), pravidlo upozornění předvolby závažnosti a oznámení s [skupiny akcí](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **musí být** nakonfigurované v prostředí upozornění. Bez následujících kroků budou dostávat jen oznámení na portálu.

1. Po uložení test dostupnosti, v podrobnostech kartu klikněte na symbol tří teček testu, který jste právě vytvořili. Klikněte na "Upravit upozornění".

   ![Upravit po uložení](./media/availability-alerts/edit-alert.png)

2. Nastavte úroveň závažnosti požadované, popis pravidla a co je nejdůležitější – skupina akcí, která má předvolby oznámení, které chcete použít pro toto pravidlo upozornění.

   ![Upravit po uložení](./media/availability-alerts/set-action-group.png)

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Upozornění na X z Y míst hlášení chyb

X z Y umístění, pravidlo upozornění je povolená ve výchozím nastavení [oznámení pro nové sjednocené prostředí](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), když vytvoříte nový test dostupnosti. Výběrem možnosti "klasickém" nebo zakázání pravidla upozornění, můžete se odhlásit.

> [!NOTE]
> Konfigurace skupin akcí pro příjem oznámení při aktivaci upozornění podle výše uvedených kroků. Bez tohoto kroku budou dostávat jen na portálu oznámení, když se pravidlo aktivuje.
>

### <a name="alert-on-availability-metrics"></a>Upozornění na metriky dostupnosti

Použití [oznámení pro nové sjednocené](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), můžete upozornění na segmentovaným agregační dostupnosti a otestovat také doba trvání metriky:

1. Vyberte prostředek Application Insights v prostředí metrik a vyberte metriku dostupnosti:

    ![Výběr metriky dostupnosti](./media/availability-alerts/select-metric.png)

2. Konfigurace výstrah, které možnosti z nabídky se dostanete na nové prostředí kde můžete vybrat konkrétní testy nebo nastavit pravidlo upozornění na umístění. Můžete také nakonfigurovat akce skupiny pro toto pravidlo upozornění tady.

### <a name="alert-on-custom-analytics-queries"></a>Upozornění na vlastních analytických dotazů

Použití [oznámení pro nové sjednocené](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), může upozornit na [vlastní protokol dotazů](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Pomocí vlastních dotazů můžete upozornění na jakékoli libovolného podmínku, která vám pomůže využít vaše nejspolehlivější signál problémy s dostupností. To platí také zejména, pokud odesíláte pomocí sady SDK TrackAvailability výsledky vlastní dostupnosti. 

> [!Tip]
> Metriky na údaje o dostupnosti zahrnují vlastní dostupnost výsledky, které vám mohou odesílání voláním naší sady SDK TrackAvailability. Můžete použít upozornění na metriky podporu, aby upozornění na výsledky vlastní dostupnosti.
>

## <a name="troubleshooting"></a>Řešení potíží

Vyhrazené [článek pro řešení potíží](troubleshoot-availability.md).

## <a name="next-steps"></a>Další postup

* [Vícekrokové webové testy](availability-multistep.md)
* [Adresa URL příkazem ping webové testy](monitor-web-app-availability.md)

