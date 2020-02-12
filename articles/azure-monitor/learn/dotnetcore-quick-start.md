---
title: Rychlý Start ASP.NET Core – Azure Monitor Application Insights
description: Poskytuje pokyny pro rychlé nastavení ASP.NET Core webové aplikace pro monitorování pomocí Azure Monitor Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: e05926d7a76f47cc3c95088fa2d8f1a6e385bbbc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75399017"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Zahájení monitorování webové aplikace ASP.NET Core

Služba Azure Application Insights umožňuje monitorovat webové aplikace z hlediska dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vaší aplikaci a nečekat na to, až je nahlásí uživatelé. 

Tento rychlý Start vás provede přidáním sady Application Insights SDK do existující webové aplikace ASP.NET Core. Další informace o konfiguraci Application Insights bez použití sady Visual Studio v tomto [článku](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

- [Nainstalujte Visual Studio 2019](https://www.visualstudio.com/downloads/) s následujícími úlohami:
  - Vývoj pro ASP.NET a web
  - Vývoj pro Azure
- [Nainstalovat sadu .NET Core 2.0 SDK](https://www.microsoft.com/net/core)
- Budete potřebovat předplatné Azure a webovou aplikaci v .NET Core.

Pokud nemáte webovou aplikaci ASP.NET Core, můžete pomocí našeho podrobného průvodce [vytvořit aplikaci ASP.NET Core a přidat Application Insights.](../../azure-monitor/app/asp-net-core.md)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na web [Azure Portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Povolení Application Insights

Application Insights může shromažďovat telemetrická data ze všech aplikací připojených k internetu bez ohledu na to, jestli jsou spuštěné místně nebo v cloudu. Pokud chcete tato data začít zobrazovat, použijte následující kroky.

1. Vyberte **Vytvořit prostředek** > **Vývojářské nástroje** > **Application Insights**.

   > [!NOTE]
   >Pokud Application Insights prostředek vytvoříte poprvé, můžete si o tom přečíst další informace v dokumentu [vytvoření prostředku Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

    Zobrazí se konfigurační pole. K vyplnění vstupních polí použijte následující tabulku.

   | Nastavení        |  Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | Globálně jedinečná hodnota | Název identifikující aplikaci, kterou monitorujete |
   | **Skupina prostředků**     | myResourceGroup      | Název nové skupiny prostředků pro hostování dat App Insights Můžete vytvořit novou skupinu prostředků nebo použít existující. |
   | **Umístění** | USA – východ | Vyberte umístění ve vaší blízkosti nebo v blízkosti místa, kde se vaše aplikace hostuje. |

2. Klikněte na **Vytvořit**.



## <a name="configure-app-insights-sdk"></a>Konfigurace sady App Insights SDK

1. Otevřete svůj **projekt** webové aplikace ASP.NET Core v sadě Visual Studio, klikněte pravým tlačítkem na název aplikace v **Průzkumníku řešení** a vyberte **Přidat** > **Telemetrie Application Insights**.

    ![Přidání Telemetrie Application Insights](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Klikněte na **tlačítko Začínáme** .

3. Vyberte svůj účet a předplatné > vyberte **existující prostředek** , který jste vytvořili v Azure Portal > klikněte na **zaregistrovat**.

4. Vyberte **projekt** > **spravovat balíčky NuGet** > **zdroj balíčku: nuget.org** > **aktualizujte** balíčky Application Insights SDK na nejnovější stabilní verzi.

5. Vyberte **Ladění** > **Spustit bez ladění** (Ctrl + F5) a spusťte vaši aplikaci.

    ![Nabídka Přehled služby Application Insights](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Trvá 3–5 minut, než se na portálu začnou zobrazovat data. Pokud používáte testovací aplikaci s nízkým provozem, nezapomínejte, že většina metrik se zachycuje, jenom když jsou nějaké aktivní požadavky nebo operace.

## <a name="start-monitoring-in-the-azure-portal"></a>Zahájení monitorování na webu Azure Portal

1. Znovu otevřete stránku **přehledu** Application Insights v Azure Portal výběrem možnosti **Domů** a v části Nedávné prostředky vyberte prostředek, který jste vytvořili dříve, a zobrazte podrobnosti o aktuálně spuštěné aplikaci.

   ![Nabídka Přehled služby Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Po kliknutí na **Mapa aplikace** se zobrazí rozložení vztahů závislosti mezi komponentami vaší aplikace. U každé komponenty se zobrazují klíčové ukazatele výkonu, jako je zatížení, výkon, selhání a upozornění.

   ![Mapa aplikace](./media/dotnetcore-quick-start/5appmap.png)

3. Klikněte na ikonu **Analýza aplikace** ![ikona mapa aplikace](./media/dotnetcore-quick-start/006.png) **zobrazení v části analýza**. Otevře se **Application Insights – Analytics** s bohatým dotazovacím jazykem pro analýzu všech dat shromážděných službou Application Insights. V tomto případě jsme za vás vytvořili dotaz, který vykreslí počet požadavků ve formě grafu. Můžete psát své vlastní dotazy pro analýzu dalších dat.

   ![Graf analýzy uživatelských požadavků za časové období](./media/dotnetcore-quick-start/6analytics.png)

4. Vraťte se na stránku **Přehled** a Projděte si řídicí panely klíčových ukazatelů výkonu.  Tento řídicí panel poskytuje statistické údaje o stavu vaší aplikace, včetně počtu příchozích požadavků, doby jejich trvání a všech chyb, ke kterým došlo. 

   ![Graf s časovou osou přehledu stavu](./media/dotnetcore-quick-start/7kpidashboards.png)

5. Na levé straně klikněte na **metriky**. Pomocí Průzkumníka metrik můžete prozkoumat stav a využití vašeho prostředku. Můžete kliknout na **Přidat nový graf** a vytvořit další vlastní zobrazení nebo vybrat **Upravit** a upravit existující typy grafů, jejich výšku, paletu barev, seskupení a metriky. Můžete například vytvořit graf, který zobrazuje průměrnou dobu načítání stránek prohlížečem vynásobením možnosti doba načítání stránky prohlížeče z rozevírací nabídky metriky a "průměr" z agregace. Další informace o Azure Průzkumník metrik najdete [v článku Začínáme s azure Průzkumník metrik](../../azure-monitor/platform/metrics-getting-started.md).

     ![Karta metriky: průměrný graf času načítání stránek prohlížeče](./media/dotnetcore-quick-start/8metrics.png)

## <a name="video"></a>Video

- Externí video týkající [se konfigurace Application Insights s využitím zcela nového rozhraní .NET Core a sady Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) .
- Externí video týkající [se konfigurace Application Insights s .NET Core a Visual Studio Code](https://youtu.be/ygGt84GDync) od začátku.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Až budete s testováním hotovi, můžete odstranit skupinu prostředků a všechny související prostředky. Provedete to podle následujících kroků.

> [!NOTE]
> Pokud jste použili existující skupinu prostředků, následující pokyny nebudou fungovat a bude potřeba jenom odstranit jednotlivé prostředky Application Insights. Mějte na paměti, že kdykoli odstraníte skupinu prostředků, odstraní se všechny základního prostředky, které jsou členy této skupiny.

1. Na webu Azure Portal v nabídce vlevo klikněte na **Skupiny prostředků** a pak na **myResourceGroup**.
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte **myResourceGroup** a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyhledání a diagnostika běhových výjimek](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
