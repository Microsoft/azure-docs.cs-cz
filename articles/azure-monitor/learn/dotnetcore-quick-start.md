---
title: Základní ASP.NET – přehledy aplikací Azure Monitor
description: Obsahuje pokyny k rychlému nastavení ASP.NET Core Web App pro monitorování pomocí Azure Monitor Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: ef46b86186d1f5e26360de891b3a090ab0ece66b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78894819"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Zahájení monitorování webové aplikace ASP.NET Core

Služba Azure Application Insights umožňuje monitorovat webové aplikace z hlediska dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vaší aplikaci a nečekat na to, až je nahlásí uživatelé. 

Tento rychlý start vás provede přidáním sady Application Insights SDK do existující webové aplikace ASP.NET Core. Další informace o konfiguraci přehledů aplikací bez pokladny sady Visual Studio v tomto [článku](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core).

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

- [Nainstalujte Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s následujícími úlohami:
  - Vývoj pro ASP.NET a web
  - Vývoj pro Azure
- [Nainstalovat sadu .NET Core 2.0 SDK](https://dotnet.microsoft.com/download)
- Budete potřebovat předplatné Azure a webovou aplikaci v .NET Core.

Pokud nemáte ASP.NET webové aplikace Core, můžete pomocí našeho podrobného průvodce [vytvořit aplikaci ASP.NET Core a přidat přehledy aplikací.](../../azure-monitor/app/asp-net-core.md)

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Povolení Application Insights

Application Insights může shromažďovat telemetrická data ze všech aplikací připojených k internetu bez ohledu na to, jestli jsou spuštěné místně nebo v cloudu. Pokud chcete tato data začít zobrazovat, použijte následující kroky.

1. Vyberte **Vytvořit nástroje** > **Developer tools** > pro vývojáře prostředků Application**Insights**.

   > [!NOTE]
   >Pokud je to poprvé, co vytváříte prostředek Application Insights, můžete se dozvědět více pomocí dokumentu Vytvořit dokument [o prostředku Resource Resource.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)

    Zobrazí se konfigurační pole. K vyplnění vstupních polí použijte následující tabulku.

   | Nastavení        |  Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | Globálně jedinečná hodnota | Název identifikující aplikaci, kterou monitorujete |
   | **Skupina prostředků**     | myResourceGroup      | Název nové skupiny prostředků pro hostování dat App Insights. Můžete vytvořit novou skupinu prostředků nebo použít existující. |
   | **Umístění** | USA – východ | Vyberte umístění ve vaší blízkosti nebo v blízkosti místa, kde se vaše aplikace hostuje. |

2. Klikněte na **Vytvořit**.



## <a name="configure-app-insights-sdk"></a>Konfigurace sady App Insights SDK

1. Otevřete svůj **projekt** webové aplikace ASP.NET Core v sadě Visual Studio, klikněte pravým tlačítkem na název aplikace v **Průzkumníku řešení** a vyberte **Přidat** > **Telemetrie Application Insights**.

    ![Přidání Telemetrie Application Insights](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Klikněte na tlačítko **Začínáme**

3. Vyberte svůj účet a předplatné > Vyberte **existující prostředek,** který jste vytvořili na portálu Azure > **registr kliknutí**.

4. Vyberte **Project** > **Manage NuGet Packages** > **Package source: nuget.org** > **Update** Application Insights SDK balíčky na nejnovější stabilní verzi.

5. Vyberte **možnost Začít ladění** > **bez ladění** (Ctrl+F5) pro spuštění aplikace.

    ![Nabídka Přehled služby Application Insights](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Trvá 3–5 minut, než se na portálu začnou zobrazovat data. Pokud používáte testovací aplikaci s nízkým provozem, nezapomínejte, že většina metrik se zachycuje, jenom když jsou nějaké aktivní požadavky nebo operace.

## <a name="start-monitoring-in-the-azure-portal"></a>Zahájení monitorování na webu Azure Portal

1. Znovu otevřete stránku **Přehled** aplikací na webu Azure portal výběrem **plochy** a v posledních prostředcích vyberte prostředek, který jste vytvořili dříve, a zobrazte podrobnosti o aktuálně spuštěné aplikaci.

   ![Nabídka Přehled služby Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Po kliknutí na **Mapa aplikace** se zobrazí rozložení vztahů závislosti mezi komponentami vaší aplikace. U každé komponenty se zobrazují klíčové ukazatele výkonu, jako je zatížení, výkon, selhání a upozornění.

   ![Mapa aplikace](./media/dotnetcore-quick-start/5appmap.png)

3. Klikněte na ikonu ![ **App Analytics** Ikona aplikace Mapa ikony](./media/dotnetcore-quick-start/006.png) **Zobrazení v Analytics**. Otevře se **Application Insights – Analytics** s bohatým dotazovacím jazykem pro analýzu všech dat shromážděných službou Application Insights. V tomto případě jsme za vás vytvořili dotaz, který vykreslí počet požadavků ve formě grafu. Můžete psát své vlastní dotazy pro analýzu dalších dat.

   ![Graf analýzy uživatelských požadavků za časové období](./media/dotnetcore-quick-start/6analytics.png)

4. Vraťte se na stránku **Přehled** a prohlédněte si řídicí panely klíčových ukazatelů výkonu.  Tento řídicí panel poskytuje statistické údaje o stavu vaší aplikace, včetně počtu příchozích požadavků, doby jejich trvání a všech chyb, ke kterým došlo. 

   ![Graf s časovou osou přehledu stavu](./media/dotnetcore-quick-start/7kpidashboards.png)

5. Na levé straně klikněte na **metriky**. Pomocí průzkumníka metrik prozkoumejte stav a využití vašeho prostředku. Můžete kliknout na **Přidat nový graf** a vytvořit další vlastní zobrazení nebo vybrat **Upravit** a upravit existující typy grafů, jejich výšku, paletu barev, seskupení a metriky. Můžete například vytvořit graf, který zobrazuje průměrnou dobu načítání stránky prohlížeče výběrem možnosti "Doba načítání stránky prohlížeče" z rozbalovacího seznamu metrik a "Průmak" z agregace. Další informace o Průzkumníku metrik Azure najdete na stránce [Začínáme s Průzkumníkem metrik Azure](../../azure-monitor/platform/metrics-getting-started.md).

     ![Karta Metriky: Graf průměrné doby načítání stránky prohlížeče](./media/dotnetcore-quick-start/8metrics.png)

## <a name="video"></a>Video

- Externí podrobné video o [konfiguraci přehledů aplikací pomocí rozhraní .NET Core a Visual Studia](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) od začátku.
- Externí podrobné video o [konfiguraci přehledů aplikací pomocí kódu .NET Core a Visual Studio](https://youtu.be/ygGt84GDync) code od začátku.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Po dokončení testování můžete odstranit skupinu prostředků a všechny související prostředky. Postupujte podle následujících kroků.

> [!NOTE]
> Pokud jste použili existující skupinu prostředků, níže uvedené pokyny nebudou fungovat a budete muset odstranit jednotlivé prostředky Application Insights. Mějte na paměti, že kdykoli odstraníte skupinu prostředků, všechny podzásobené prostředky, které jsou členy této skupiny, budou odstraněny.

1. Na webu Azure Portal v nabídce vlevo klikněte na **Skupiny prostředků** a pak na **myResourceGroup**.
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte **myResourceGroup** a potom klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyhledání a diagnostika běhových výjimek](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
