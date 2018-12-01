---
title: Ladění aplikací pomocí Azure Application Insights v sadě Visual Studio | Dokumentace Microsoftu
description: Analýza výkonu a diagnostika webové aplikace během ladění a v produkčním prostředí.
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: carmonm
ms.assetid: 2059802b-1131-477e-a7b4-5f70fb53f974
ms.service: application-insights
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/07/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: 2d035883534430e76d251b22dfc95d34e4eb03cd
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52723563"
---
# <a name="debug-your-applications-with-azure-application-insights-in-visual-studio"></a>Ladění aplikací pomocí Azure Application Insights v sadě Visual Studio
V sadě Visual Studio (2015 a novější) můžete analyzovat výkon a diagnostikovat problémy ve vaší webové aplikaci v ASP.NET během ladění i v produkčním prostředí pomocí telemetrie z [Azure Application Insights](app-insights-overview.md).

Pokud jste webovou aplikaci v ASP.NET vytvořili pomocí sady Visual Studio 2017 nebo novější, sada Application Insights SDK už v ní je. V opačném případě, pokud jste to ještě neudělali, [přidejte Application Insights do své aplikace](app-insights-asp-net.md).

Pokud chcete monitorovat aplikaci za provozu v produkčním prostředí, telemetrii Application Insights normálně zobrazíte na webu [Azure Portal](https://portal.azure.com), kde můžete nastavit upozornění a použít výkonné monitorovací nástroje. Pro účely ladění ale můžete vyhledávat a analyzovat telemetrii také v sadě Visual Studio. Visual Studio můžete použít k analýze telemetrie jak z produkčního tak z ladění se spustí na vývojovém počítači. V druhém případě můžete spuštěné ladění analyzovat, i když jste ještě nenakonfigurovali sadu SDK k odesílání telemetrie na web Azure Portal. 

## <a name="run"></a> Ladění projektu
Spusťte webovou aplikaci v režimu místního ladění pomocí klávesy F5. Otevřete různé stránky k vygenerování nějaké telemetrie.

V sadě Visual Studio zobrazí počet událostí, které byly zaprotokolovány modulem Application Insights ve vašem projektu.

![V sadě Visual Studio se zobrazí tlačítko Application Insights během ladění.](./media/app-insights-visual-studio/appinsights-09eventcount.png)

Kliknutím na toto tlačítko můžete vyhledávat telemetrii. 

## <a name="application-insights-search"></a>Hledání Application Insights
V okně Hledání Application Insights se zobrazí události, které byly zaprotokolovány. (Pokud jste přihlášení do Azure při nastavování Application Insights, můžete vyhledávat stejné události na webu Azure Portal.)

![Klikněte pravým tlačítkem myši na projekt a vyberte Application Insights, Vyhledávání](./media/app-insights-visual-studio/34.png)

> [!NOTE] 
> Jakmile vyberete nebo zrušíte výběr filtrů, klikněte na tlačítko Vyhledat na konci textového vyhledávacího pole.
>

Textové vyhledávání funguje na všechna pole v událostech. Například vyhledejte část adresy URL stránky nebo hodnotu vlastnosti, například města klienta; nebo určitá slova v protokolu trasování.

Kliknutím na libovolnou událost zobrazíte podrobné vlastnosti.

Pokud chcete zobrazit podrobné vlastnosti požadavků na webovou aplikaci, můžete se proklikat ke kódu.

![V části Podrobnosti o požadavku se proklikejte ke kódu](./media/app-insights-visual-studio/31.png)

Můžete také otevřít související položky a pomocí nich diagnostikovat neúspěšné požadavky nebo výjimky.

![V části Podrobnosti o požadavku přejděte dolů k souvisejícím položkám](./media/app-insights-visual-studio/41.png)

## <a name="view-exceptions-and-failed-requests"></a>Zobrazit výjimky a neúspěšné požadavky
Sestavy výjimek se zobrazí v okně Hledání. (V některých starších typech aplikací v ASP.NET je potřeba [nastavit monitorování výjimek](app-insights-asp-net-exceptions.md), abyste viděli výjimky, které rozhraní zpracovává.)

Klikněte na výjimku a získejte trasování zásobníku. Pokud je kód aplikace otevřen v sadě Visual Studio, můžete kliknutím z trasování zásobníku přejít na příslušný řádek kódu.

![Trasování zásobníku výjimky](./media/app-insights-visual-studio/17.png)

## <a name="view-request-and-exception-summaries-in-the-code"></a>Zobrazit souhrny požadavků a výjimek v kódu
V řádku Code Lens nad jednotlivými obslužnými metodami uvidíte počet požadavků a výjimek zaznamenaných nástrojem Application Insights za posledních 24 hodin.

![Trasování zásobníku výjimky](./media/app-insights-visual-studio/21.png)

> [!NOTE] 
> Code Lens zobrazí data Application Insights, pouze pokud jste [nakonfigurovali aplikaci k odesílání telemetrie na portál Application Insights](app-insights-asp-net.md).
>

[Další informace o Application Insights v Code Lens](app-insights-visual-studio-codelens.md)

## <a name="trends"></a>Trendy
Trendy představují nástroj pro vizualizaci chování aplikace v čase. 

Vybírejte z **Trendů zkoumání telemetrie** z tlačítka panelu nástrojů Application Insights nebo okna hledání Application Insights. Zvolte jeden z pěti běžných dotazů, abyste mohli začít. Na základě typů telemetrie, časových rozsahů a dalších vlastností můžete analyzovat různé datové sady. 

Pokud chcete vyhledat anomálie v datech, vyberte jednu z možností anomálií v rozevíracím seznamu „Typ zobrazení“. Možnosti filtrování v dolní části okna usnadňují zdokonalování v konkrétních podmnožinách vaší telemetrie.

![Trendy](./media/app-insights-visual-studio/51.png)

[Další informace o trendech](app-insights-visual-studio-trends.md).

## <a name="local-monitoring"></a>Místní monitorování
(Z Visual Studio 2015 Update 2) Pokud jste nenakonfigurovali SDK k odesílání telemetrie na portál Application Insights (takže neexistuje žádný Instrumentační klíč v souboru ApplicationInsights.config) zobrazí okno diagnostiky telemetrii z nejnovější relace ladění. 

Toto je žádoucí, pokud jste již publikovali předchozí verzi aplikace. Nechcete, aby se telemetrie z vaší relace ladění promíchala s telemetrií na portálu služby Application Insights z publikované aplikace.

Je také užitečné, pokud máte některou [vlastní telemetrii](app-insights-api-custom-events-metrics.md), kterou chcete ladit před odesláním telemetrie na portál.

* *Napřed jsem službu Application Insights plně nakonfiguroval/a tak, aby posílala telemetrické údaje na portál. Ale teď chci telemetrické údaje zobrazovat jen v sadě Visual Studio.*
  
  * V okně hledání nastavení je možnost vyhledávání místní diagnostiky i v případě, že vaše aplikace odesílá telemetrii na portál.
  * Odesílání telemetrie na portál zastavíte okomentováním řádku `<instrumentationkey>...` ze souboru ApplicationInsights.config. Jakmile budete připraveni k opětovnému odeslání telemetrie na portál, komentář zrušte.


## <a name="next-steps"></a>Další postup
|  |  |
| --- | --- |
| **[Přidání dalších dat](app-insights-asp-net-more.md)**<br/>Sledování využití, dostupnosti, závislostí, výjimek. Integrujte trasování z rozhraní protokolování. Zapisuje vlastní telemetrii. |![Visual Studio](./media/app-insights-visual-studio/64.png) |
| **[Práce s portálem Application Insights](app-insights-dashboards.md)**<br/>Zobrazit řídicí panely, výkonné nástroje pro diagnostiku a analýzy, výstrahy, aktivní mapa závislostí vaší aplikace a exportovaný telemetrická data. |![Visual Studio](./media/app-insights-visual-studio/62.png) |

