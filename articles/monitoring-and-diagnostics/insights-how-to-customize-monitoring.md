---
title: Přehled metriky v Azure monitorování
description: Zjistěte, jak přizpůsobit monitorování grafy v Azure.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/06/2017
ms.author: robb
ms.component: metrics
ms.openlocfilehash: 878ba004e7572ad78f574c15fd76c8868b281117
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262252"
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Přehled metriky v Microsoft Azure
Všech služeb Azure sledovat klíčové metriky, které umožňují monitorovat stav, výkon, dostupnost a využití vašich služeb. Tyto metriky lze zobrazit na webu Azure portal a můžete také [REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx) nebo [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) pro přístup k úplnou sadu metriky prostřednictvím kódu programu.

U některých služeb budete muset zapněte diagnostiku, chcete-li zobrazit všechny metriky. Pro jiné, jako jsou virtuální počítače zobrazí se základní sadu metriky, ale nutné povolit kompletní nastavení metriky vysoká frekvence. V tématu [zapínání monitorování a Diagnostika](insights-how-to-use-diagnostics.md) Další informace.

## <a name="using-monitoring-charts"></a>Používání monitorování grafů
Můžete všechny metriky grafu je přes vybraném časovém intervalu.

1. V [portálu Azure](https://portal.azure.com/), klikněte na tlačítko **Procházet**, a pak prostředek vás zajímá monitorování.
2. **Monitorování** část obsahuje nejdůležitější metriky pro každý prostředků Azure. Například webové aplikace se **požadavky a chyby**, kde jako virtuální počítač by mít **procento využití procesoru** a **pro čtení a zápisu disku**: ![přehledu monitorování](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)
3. Kliknutím na jakékoli grafu se zobrazí je **metrika** okno. V okně kromě grafu je tabulka zobrazující agregace metrik (například průměr, minimální a maximální přes časový rozsah, který jste zvolili). V následující tabulce, jsou pravidla výstrah pro prostředek.
    ![Okno metriky](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4. Chcete-li přizpůsobit řádky, které se zobrazí, klikněte na tlačítko **upravit** tlačítko v grafu, nebo **upravit graf** příkazu v okně metriky.
5. V okně Upravit dotaz můžete udělat tři věci:
   
   * Změnit časové rozmezí
   * Přepínač vzhled mezi panelu a řádku
   * Zvolte jiný metics ![upravit dotaz](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)
6. Změnit časový rozsah je stejně snadná jako výběr jiný rozsah (například **poslední hodinu**) a kliknutím na **Uložit** v dolní části okna. Můžete také **vlastní**, který vám umožňuje zvolit období, za poslední 2 týdny. Například se zobrazí pouze 1 hodinu od včerejška celou dva týdny, nebo. Zadejte do textového pole zadejte jiné hodinu.
    ![Vlastní časový rozsah](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
7. Časový rozsah můžete kanál zvolené libovolný počet metriky zobrazit v grafu.
8. Po kliknutí na tlačítko Uložit provedené změny se uloží pro tuto konkrétní prostředek. Například pokud máte dva virtuální počítače a změníte grafu na jednom, nebude ovlivňovat dalších.

## <a name="creating-side-by-side-charts"></a>Vytváření grafů vedle sebe
S výkonné přizpůsobení portálu můžete přidat libovolný počet grafy tak, jak chcete.

1. V **...**  nabídce v horní části okna klikněte na **přidat dlaždice**:  
    ![Nabídka Přidat](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Potom můžete vyberte graf můžete vybrat z **Galerie** na pravé straně obrazovky: ![Galerie](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Pokud nevidíte metriku chcete, můžete vždy přidat přednastavené metrik a **upravit** v grafu zobrazí metriku, je nutné.

## <a name="monitoring-usage-quotas"></a>Sledování využití kvóty
Většina metriky ukazují trendů v čase, ale některá data, jako je využití kvóty, jsou informace o bodu v čase s prahovou hodnotou.

V okně pro prostředky, které mají kvóty, můžete také zjistit kvóty využití:

![Využití](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Jako v metriky, můžete použít [REST API](https://msdn.microsoft.com/library/azure/dn931963.aspx) nebo [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) pro přístup k celé sady kvóty využití prostřednictvím kódu programu.

## <a name="next-steps"></a>Další postup
* [Příjem oznámení o výstrahách](insights-receive-alert-notifications.md) vždy, když metriky protne prahovou hodnotu.
* [Povolit monitorování a Diagnostika](insights-how-to-use-diagnostics.md) ke shromažďování metrik podrobné vysoká frekvence vaší služby.
* [Automatické škálování počtu instancí](insights-how-to-scale.md) zkontrolovat služby je k dispozici a dobře reagovaly.
* [Monitorování výkonu aplikací](../application-insights/app-insights-azure-web-apps.md) Pokud chcete zjistit, přesně jak kód provádí v cloudu.
* Použití [Application Insights pro aplikace JavaScript a webové stránky](../application-insights/app-insights-web-track-usage.md) získat analytics klienta o prohlížečů, které navštívit webovou stránku.
* [Sledování dostupnosti a odezvy žádné webové stránce](../application-insights/app-insights-monitor-web-app-availability.md) s Application Insights, můžete zjistit, pokud stránka je vypnutý.

