---
title: Ladění ve Visual Studiu pomocí přehledů aplikací Azure
description: Analýza výkonu a diagnostika webové aplikace během ladění a v produkčním prostředí.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/07/2017
ms.reviewer: mbullwin
ms.custom: vs-azure
ms.openlocfilehash: 8905222214d58eeba24ecf50da768ffa1d65c39d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670878"
---
# <a name="debug-your-applications-with-azure-application-insights-in-visual-studio"></a>Ladění aplikací pomocí Azure Application Insights ve Visual Studiu
V sadě Visual Studio (2015 a novější) můžete analyzovat výkon a diagnostikovat problémy ve vaší webové aplikaci v ASP.NET během ladění i v produkčním prostředí pomocí telemetrie z [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

Pokud jste webovou aplikaci v ASP.NET vytvořili pomocí sady Visual Studio 2017 nebo novější, sada Application Insights SDK už v ní je. V opačném případě, pokud jste to ještě neudělali, [přidejte Application Insights do své aplikace](../../azure-monitor/app/asp-net.md).

Pokud chcete monitorovat aplikaci za provozu v produkčním prostředí, telemetrii Application Insights normálně zobrazíte na webu [Azure Portal](https://portal.azure.com), kde můžete nastavit upozornění a použít výkonné monitorovací nástroje. Pro účely ladění ale můžete vyhledávat a analyzovat telemetrii také v sadě Visual Studio. Visual Studio můžete použít k analýze telemetrie z vašeho produkčního webu a z ladění běží na vývojovém počítači. V druhém případě můžete spuštěné ladění analyzovat, i když jste ještě nenakonfigurovali sadu SDK k odesílání telemetrie na web Azure Portal. 

## <a name="debug-your-project"></a><a name="run"></a>Ladění projektu
Spusťte webovou aplikaci v režimu místního ladění pomocí klávesy F5. Otevřete různé stránky k vygenerování nějaké telemetrie.

V sadě Visual Studio uvidíte počet událostí, které byly zaznamenány modulem Application Insights v projektu.

![V sadě Visual Studio se zobrazí tlačítko Application Insights během ladění.](./media/visual-studio/appinsights-09eventcount.png)

Kliknutím na toto tlačítko můžete vyhledávat telemetrii. 

## <a name="application-insights-search"></a>Hledání Application Insights
V okně Hledání Application Insights se zobrazí události, které byly zaprotokolovány. (Pokud jste se při nastavování Application Insights přihlásili do Azure, můžete prohledávat stejné události na webu Azure Portal.)

![Klikněte pravým tlačítkem myši na projekt a vyberte Application Insights, Vyhledávání](./media/visual-studio/34.png)

> [!NOTE] 
> Jakmile vyberete nebo zrušíte výběr filtrů, klikněte na tlačítko Vyhledat na konci textového vyhledávacího pole.
>

Textové vyhledávání funguje na všechna pole v událostech. Například vyhledejte část adresy URL stránky nebo hodnotu vlastnosti, například města klienta; nebo určitá slova v protokolu trasování.

Kliknutím na libovolnou událost zobrazíte podrobné vlastnosti.

Pokud chcete zobrazit podrobné vlastnosti požadavků na webovou aplikaci, můžete se proklikat ke kódu.

![V části Podrobnosti o požadavku se proklikejte ke kódu](./media/visual-studio/31.png)

Můžete také otevřít související položky a pomocí nich diagnostikovat neúspěšné požadavky nebo výjimky.

![V části Podrobnosti o požadavku přejděte dolů k souvisejícím položkám](./media/visual-studio/41.png)

## <a name="view-exceptions-and-failed-requests"></a>Zobrazit výjimky a neúspěšné požadavky
Sestavy výjimek se zobrazí v okně Hledání. (V některých starších typech aplikací v ASP.NET je potřeba [nastavit monitorování výjimek](../../azure-monitor/app/asp-net-exceptions.md), abyste viděli výjimky, které rozhraní zpracovává.)

Klikněte na výjimku a získejte trasování zásobníku. Pokud je kód aplikace otevřen v sadě Visual Studio, můžete kliknutím z trasování zásobníku přejít na příslušný řádek kódu.

![Trasování zásobníku výjimky](./media/visual-studio/17.png)

## <a name="view-request-and-exception-summaries-in-the-code"></a>Zobrazit souhrny požadavků a výjimek v kódu
V řádku Objektiv kódu nad každou metodou obslužné rutiny se zobrazí počet požadavků a výjimek zaznamenaných application insights v posledních 24 h.

![Trasování zásobníku výjimky](./media/visual-studio/21.png)

> [!NOTE] 
> Code Lens zobrazí data Application Insights, pouze pokud jste [nakonfigurovali aplikaci k odesílání telemetrie na portál Application Insights](../../azure-monitor/app/asp-net.md).
>

[Další informace o Application Insights v Code Lens](../../azure-monitor/app/visual-studio-codelens.md)

## <a name="trends"></a>Trendy
Trendy představují nástroj pro vizualizaci chování aplikace v čase. 

Vybírejte z **Trendů zkoumání telemetrie** z tlačítka panelu nástrojů Application Insights nebo okna hledání Application Insights. Zvolte jeden z pěti běžných dotazů, abyste mohli začít. Na základě typů telemetrie, časových rozsahů a dalších vlastností můžete analyzovat různé datové sady. 

Pokud chcete vyhledat anomálie v datech, vyberte jednu z možností anomálií v rozevíracím seznamu „Typ zobrazení“. Možnosti filtrování v dolní části okna usnadňují zdokonalování v konkrétních podmnožinách vaší telemetrie.

![Trendy](./media/visual-studio/51.png)

[Další informace o trendech](../../azure-monitor/app/visual-studio-trends.md).

## <a name="local-monitoring"></a>Místní monitorování
(Z visual studia 2015 Update 2) Pokud jste nenakonfigurovali sadu SDK tak, aby odesílala telemetrická data na portál Application Insights (takže v applicationInsights.config není žádný klíč instrumentace), zobrazí diagnostické okno telemetrii z poslední relace ladění. 

Toto je žádoucí, pokud jste již publikovali předchozí verzi aplikace. Nechcete, aby se telemetrie z vaší relace ladění promíchala s telemetrií na portálu služby Application Insights z publikované aplikace.

Je také užitečné, pokud máte některou [vlastní telemetrii](../../azure-monitor/app/api-custom-events-metrics.md), kterou chcete ladit před odesláním telemetrie na portál.

* *Zpočátku jsem plně nakonfigurován Application Insights pro odesílání telemetrie na portálu. Ale teď bych chtěl vidět telemetrii pouze v sadě Visual Studio.*
  
  * V okně hledání nastavení je možnost vyhledávání místní diagnostiky i v případě, že vaše aplikace odesílá telemetrii na portál.
  * Chcete-li zastavit telemetrická data odesílaná na portál, zakomentujte řádek `<instrumentationkey>...` z ApplicationInsights.config. Až budete připraveni znovu odeslat telemetrii na portál, odkomentujte ji.


## <a name="next-steps"></a>Další kroky
|  |  |
| --- | --- |
| **[Přidání dalších dat](../../azure-monitor/app/asp-net-more.md)**<br/>Sledování využití, dostupnosti, závislostí, výjimek. Integrujte trasování z rozhraní protokolování. Zapisuje vlastní telemetrii. |![Visual Studio](./media/visual-studio/64.png) |
| **[Práce s portálem Application Insights](../../azure-monitor/app/overview-dashboard.md)**<br/>Zobrazení řídicích panelů, výkonných diagnostických a analytických nástrojů, výstrah, mapy živých závislostí vaší aplikace a exportovaných telemetrických dat. |![Visual Studio](./media/visual-studio/62.png) |

