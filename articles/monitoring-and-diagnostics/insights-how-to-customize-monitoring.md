---
title: Přehled metrik ve službě Azure Monitor
description: Zjistěte, jak přizpůsobit grafy monitorování v Azure.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/06/2017
ms.author: robb
ms.component: metrics
ms.openlocfilehash: 44daf6461a062e75435ec6f70fbc3cf10327e799
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213040"
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Přehled metrik v Microsoft Azure
Všechny služby Azure sledování klíčových metrik, které umožňují monitorování stavu, výkonu, dostupnosti a využití vašich služeb. Tyto metriky lze zobrazit na webu Azure Portal a můžete také použít [rozhraní REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx) nebo [sady .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) pro přístup k úplné sadě metrik prostřednictvím kódu programu.

U některých služeb budete muset zapnout diagnostiku, chcete-li zobrazit všechny metriky. Pro ostatní uživatele, jako jsou virtuální počítače získáte základní sadu metriky, ale potřebujete povolit úplnou nastavit vysokou frekvencí metriky. Zobrazit [povolení monitorování a diagnostiky](insights-how-to-use-diagnostics.md) Další informace.

## <a name="using-monitoring-charts"></a>Použití grafů sledování
Můžete graf všechny metriky je za libovolné časové období, které zvolíte.

1. V [webu Azure Portal](https://portal.azure.com/), klikněte na tlačítko **Procházet**, a potom prostředek vás zajímá monitorování.
2. **Monitorování** oddíl obsahuje nejdůležitější metriky pro každý prostředek Azure. Například webové aplikace má **požadavky a chyby**, kde jako virtuální počítač bude mít **procento využití procesoru** a **pro čtení a zápisu disku**: ![přehledu monitorování](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)
3. Kliknutím na libovolný graf se seznámíte **metrika** okno. V okně, kromě grafu je tabulka, která zobrazuje agregace metrik (například average a minimální a maximální za časové období, které zvolíte). V následující tabulce, která jsou pravidla upozornění na zdroj.
    ![Okno metrika](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4. K přizpůsobení řádků, které se zobrazí, klikněte na tlačítko **upravit** tlačítko v grafu nebo **upravit graf** příkazu v okně metriky.
5. V okně Upravit dotaz můžete provést tři věci:
   
   * Změnit časový rozsah
   * Přepnout zobrazení mezi panelu a řádku
   * Zvolte jinou metics ![upravit dotaz](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)
6. Změnit časový rozsah je stejně jednoduché jako vyberete jiný rozsah (jako například **uplynulou hodinu**) a kliknutím na **Uložit** v dolní části okna. Můžete také zvolit **vlastní**, která umožňuje vybrat libovolné časové období za poslední 2 týdny. Například můžete zobrazit celé dva týdny nebo jenom 1 hodinu od včerejška. Zadejte do textového pole zadejte jiné hodinu.
    ![Vlastní časový rozsah](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
7. Pod časový rozsah chan můžete zvolit libovolný počet metrik zobrazit v grafu.
8. Po kliknutí na Uložit provedené změny se uloží pro tento konkrétní prostředek. Například pokud budete mít dva virtuální počítače a změnit v jednom grafu, je to neovlivní druhou.

## <a name="creating-side-by-side-charts"></a>Vytváření grafů vedle sebe
Pomocí výkonných vlastní nastavení na portálu můžete přidat tolik grafy, jak chcete.

1. V **...**  nabídce v horní části okna klikněte na tlačítko **přidat dlaždice**:  
    ![Přidat nabídku](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Pak můžete vybrat z grafu **Galerie** na pravé straně obrazovky: ![Galerie](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Pokud nevidíte metriku chcete, můžete kdykoli přidat jednu z přednastavených metrik a **upravit** grafu chcete zobrazit metriky, potřebujete.

## <a name="monitoring-usage-quotas"></a>Monitorování využití kvóty
Většina metriky ukazují, trendy v čase, ale určitá data, jako je využití kvóty se informace o bodu v čase s prahovou hodnotou.

Využití kvóty můžete zobrazit také v okně pro prostředky, které mají kvót:

![Využití](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Stejným způsobem jako s metrikami, můžete [rozhraní REST API](https://msdn.microsoft.com/library/azure/dn931963.aspx) nebo [sady .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) pro přístup k úplné sadě kvóty využívání prostřednictvím kódu programu.

## <a name="next-steps"></a>Další postup
* [Doručování oznámení o upozorněních](insights-receive-alert-notifications.md) kdykoliv metrika překročí mezní hodnotu.
* [Povolení monitorování a diagnostiky](insights-how-to-use-diagnostics.md) shromažďovat podrobné metriky vysokou frekvencí pro vaši službu.
* [Automatické škálování počtu instancí](insights-how-to-scale.md) k Ujistěte se, že vaše služba není k dispozici a reagují.
* [Monitorování výkonu aplikací](../application-insights/app-insights-azure-web-apps.md) Pokud chcete pochopit, přesně jak váš kód provádí v cloudu.
* Použití [Application Insights pro aplikace JavaScript a webové stránky](../application-insights/app-insights-web-track-usage.md) zobrazíte analytics klienta o prohlížečích, které webovou stránky navštíví.
* [Monitorujte dostupnost a odezvu libovolné webové stránce](../application-insights/app-insights-monitor-web-app-availability.md) pomocí Application Insights, můžete zjistit, pokud vaše stránka je mimo provoz.

