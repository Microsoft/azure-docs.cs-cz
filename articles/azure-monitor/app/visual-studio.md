---
title: Ladění v aplikaci Visual Studio s využitím Azure Application Insights
description: Analýza výkonu a diagnostika webové aplikace během ladění a v produkčním prostředí.
ms.topic: conceptual
ms.date: 03/17/2017
ms.custom: vs-azure
ms.openlocfilehash: 2507dbf7bb8294c949f434d5fa96ccc0af9a7eb3
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563534"
---
# <a name="debug-your-applications-with-azure-application-insights-in-visual-studio"></a>Ladění aplikací pomocí Azure Application Insights v aplikaci Visual Studio
V sadě Visual Studio (2015 a novější) můžete analyzovat výkon a diagnostikovat problémy ve vaší webové aplikaci v ASP.NET během ladění i v produkčním prostředí pomocí telemetrie z [Azure Application Insights](./app-insights-overview.md).

Pokud jste webovou aplikaci v ASP.NET vytvořili pomocí sady Visual Studio 2017 nebo novější, sada Application Insights SDK už v ní je. V opačném případě, pokud jste to ještě neudělali, [přidejte Application Insights do své aplikace](./asp-net.md).

Pokud chcete monitorovat aplikaci za provozu v produkčním prostředí, telemetrii Application Insights normálně zobrazíte na webu [Azure Portal](https://portal.azure.com), kde můžete nastavit upozornění a použít výkonné monitorovací nástroje. Pro účely ladění ale můžete vyhledávat a analyzovat telemetrii také v sadě Visual Studio. Můžete použít Visual Studio k analýze telemetrie jak z produkčního webu, tak z ladění, které běží na vašem vývojovém počítači. V druhém případě můžete spuštěné ladění analyzovat, i když jste ještě nenakonfigurovali sadu SDK k odesílání telemetrie na web Azure Portal. 

## <a name="debug-your-project"></a><a name="run"></a> Ladění projektu
Spusťte webovou aplikaci v režimu místního ladění pomocí klávesy F5. Otevřete různé stránky k vygenerování nějaké telemetrie.

V aplikaci Visual Studio se zobrazí počet událostí, které byly zaprotokolovány modulem Application Insights ve vašem projektu.

![V sadě Visual Studio se zobrazí tlačítko Application Insights během ladění.](./media/visual-studio/appinsights-09eventcount.png)

Kliknutím na toto tlačítko můžete vyhledávat telemetrii. 

## <a name="application-insights-search"></a>Hledání Application Insights
V okně Hledání Application Insights se zobrazí události, které byly zaprotokolovány. (Pokud jste se k Azure přihlásili při nastavování Application Insights, můžete hledat stejné události v Azure Portal.)

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
Sestavy výjimek se zobrazí v okně Hledání. (V některých starších typech aplikací v ASP.NET je potřeba [nastavit monitorování výjimek](./asp-net-exceptions.md), abyste viděli výjimky, které rozhraní zpracovává.)

Klikněte na výjimku a získejte trasování zásobníku. Pokud je kód aplikace otevřen v sadě Visual Studio, můžete kliknutím z trasování zásobníku přejít na příslušný řádek kódu.

![Snímek obrazovky ukazuje objekt o objektu v trasování zásobníku.](./media/visual-studio/17.png)

## <a name="view-request-and-exception-summaries-in-the-code"></a>Zobrazení souhrnů požadavků a výjimek v kódu
V řádku Code Lens nad jednotlivými metodami obslužné rutiny vidíte počet požadavků a výjimek protokolovaných Application Insights za posledních 24 h.

![Snímek obrazovky ukazuje výjimku v rámci kontextového dialogu.](./media/visual-studio/21.png)

> [!NOTE] 
> Code Lens zobrazí data Application Insights, pouze pokud jste [nakonfigurovali aplikaci k odesílání telemetrie na portál Application Insights](./asp-net.md).
>

[Další informace o Application Insights v Code Lens](./visual-studio-codelens.md)

## <a name="local-monitoring"></a>Místní monitorování
(Ze sady Visual Studio 2015 Update 2) Pokud jste nenakonfigurovali sadu SDK tak, aby odesílala telemetrii na portál Application Insights (takže v ApplicationInsights.config neexistuje žádný klíč instrumentace), zobrazí se v okně diagnostiky telemetrie z poslední ladicí relace. 

Toto je žádoucí, pokud jste již publikovali předchozí verzi aplikace. Nechcete, aby se telemetrie z vaší relace ladění promíchala s telemetrií na portálu služby Application Insights z publikované aplikace.

Je také užitečné, pokud máte některou [vlastní telemetrii](./api-custom-events-metrics.md), kterou chcete ladit před odesláním telemetrie na portál.

* *Nejdřív jsem nakonfigurovali Application Insights k odeslání telemetrie na portál. Ale teď chci zobrazit telemetrii jenom v aplikaci Visual Studio.*
  
  * V okně hledání nastavení je možnost vyhledávání místní diagnostiky i v případě, že vaše aplikace odesílá telemetrii na portál.
  * Pokud chcete zastavit vystavení telemetrie na portálu, odkomentujte řádek `<instrumentationkey>...` z ApplicationInsights.config. Až budete připraveni na portál znovu odeslat telemetrii, odkomentujte ho.


## <a name="next-steps"></a>Další kroky

 * **[Práce s portálem Application Insights](./overview-dashboard.md)**. Zobrazit řídicí panely, výkonné nástroje pro diagnostiku a analýzy, výstrahy, živou mapu závislostí vaší aplikace a exportovaná data telemetrie. 

