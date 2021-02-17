---
title: Monitorování mobilních aplikací pomocí Azure Monitor Application Insights
description: Poskytuje pokyny pro rychlé nastavení mobilní aplikace pro monitorování pomocí Azure Monitor Application Insights a App Center
ms.subservice: application-insights
ms.topic: quickstart
author: lgayhardt
ms.author: lagayhar
ms.date: 06/26/2019
ms.reviewer: daviste
ms.custom: mvc
ms.openlocfilehash: 0c53569ea50d91bbd703197d1a4c3546e10e04ec
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100628585"
---
# <a name="start-analyzing-your-mobile-app-with-app-center-and-application-insights"></a>Začínáme s analýzou mobilní aplikace pomocí App Center a Application Insights

Tento rychlý start vás provede propojením instance App Center vaší aplikace s Application Insights. S Application Insights můžete dotazovat, segmentovat, filtrovat a analyzovat telemetrická data pomocí výkonnějších nástrojů, které jsou dostupné ve službě [Analytics](/mobile-center/analytics/) v App Center.

## <a name="prerequisites"></a>Požadavky

Co budete potřebovat k dokončení tohoto rychlého startu:

- Předplatné Azure.
- Aplikace pro iOS nebo Android, aplikace Xamarin, univerzální aplikace pro Windows nebo aplikace React Native.
 
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-up-with-app-center"></a>Zaregistrujte se App Center
Začněte tím, že vytvoříte účet a [Zaregistrujte se App Center](https://appcenter.ms/signup?utm_source=ApplicationInsights&utm_medium=Azure&utm_campaign=docs).

## <a name="onboard-to-app-center"></a>Připojení k App Center

Než budete ve své mobilní aplikaci moct použít Application Insights, musíte svou aplikaci připojit k [App Center](/mobile-center/). Application Insights nepřijímá telemetrii z mobilní aplikace přímo. Místo toho aplikace odesílá vlastní telemetrii událostí do App Center. App Center pak s přijímáním událostí průběžně exportuje kopie těchto vlastních událostí do Application Insights. (To se nevztahuje na [sadu SDK Application Insights js](https://github.com/Microsoft/ApplicationInsights-JS) ani na [nereagují nativní modul plug-in](https://github.com/Microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-react-native) , kde se telemetrie odesílá přímo do Application Insights.)

Pokud chcete připojit svou aplikaci, postupujte podle rychlého startu pro App Center, a to pro všechny platformy, které vaše aplikace podporuje. Vytvoření samostatných instancí App Center pro jednotlivé platformy:

* [iOS](/mobile-center/sdk/getting-started/ios).
* [Android](/mobile-center/sdk/getting-started/android).
* [Xamarin](/mobile-center/sdk/getting-started/xamarin).
* [Univerzální pro Windows](/mobile-center/sdk/getting-started/uwp).
* [Reagují na nativní](/mobile-center/sdk/getting-started/react-native).

## <a name="track-events-in-your-app"></a>Sledování událostí v aplikaci

Po připojení vaší aplikace k App Center je potřeba ji upravit pro odesílání vlastní telemetrie událostí pomocí sady App Center SDK. Vlastní události jsou jediným typem telemetrie App Center, která se exportuje do Application Insights.

K odesílání vlastních události z aplikací pro iOS použijte metodu `trackEvent` nebo `trackEvent:withProperties` v sadě App Center SDK. [Další informace o sledování událostí z aplikací pro iOS.](/mobile-center/sdk/analytics/ios)

```Swift
MSAnalytics.trackEvent("Video clicked")
```

K odesílání vlastních události z aplikací pro Android použijte metodu `trackEvent` v sadě App Center SDK. [Další informace o sledování událostí z aplikací pro Android.](/mobile-center/sdk/analytics/android)

```Java
Analytics.trackEvent("Video clicked")
```

K odesílání vlastních událostí z jiných platforem aplikací použijte metody `trackEvent` v příslušných sadách App Center SDK.

Pokud chcete ověřit přijímání vlastních událostí, přejděte v App Center v části **Analytics** na kartu **Události**. Zobrazení událostí může od odeslání z aplikace několik minut trvat.

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Jakmile vaše aplikace odesílá vlastní události a App Center tyto události přijímá, je potřeba na webu Azure Portal vytvořit prostředek Application Insights typu App Center:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Vyberte **vytvořit prostředek pro**  >  **vývojáře**  >  **Application Insights**.

    > [!NOTE]
    > Pokud Application Insights prostředek vytvoříte poprvé, můžete si o tom přečíst další informace v dokumentu [vytvoření prostředku Application Insights](../app/create-new-resource.md) .

    Zobrazí se konfigurační pole. K vyplnění vstupních polí použijte následující tabulku.

    | Nastavení        |  Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | Nějaká globálně jedinečná hodnota, například myApp-iOS | Název identifikující aplikaci, kterou monitorujete |
     | **Skupina prostředků**     | Nová skupina prostředků nebo existující skupina prostředků z nabídky | Skupina prostředků, ve které se má vytvořit nový prostředek Application Insights |
   | **Umístění** | Umístění z nabídky | Vyberte umístění ve vaší blízkosti nebo v blízkosti místa, kde se vaše aplikace hostuje. |

3. Klikněte na **Vytvořit**.

Pokud vaše aplikace podporuje více platforem (iOS, Android atd.), je vhodné vytvořit pro každou platformu samostatný prostředek Application Insights.

## <a name="export-to-application-insights"></a>Export do Application Insights

V novém prostředku Application Insights na stránce **Přehled** . Zkopírujte klíč instrumentace z prostředku.

V instanci [App Center](https://appcenter.ms/) pro vaši aplikaci:

1. Na stránce **Nastavení** klikněte na **Export**.
2. Zvolte **Nový export**, vyberte **Application Insights** a pak klikněte na **Přizpůsobit**.
3. Vložte do pole váš instrumentační klíč Application Insights.
4. Vyjádřete souhlas s navýšením využití předplatného Azure, které obsahuje váš prostředek Application Insights. Pro každý prostředek Application Insights je první 1 GB přijatých dat za měsíc zdarma. [Další informace o cenách Application Insights.](https://azure.microsoft.com/pricing/details/application-insights/)

Nezapomeňte tento postup zopakovat pro každou platformu, kterou vaše aplikace podporuje.

Po nastavení [exportu](/mobile-center/analytics/export) se každá vlastní událost přijatá v App Center zkopíruje do Application Insights. Přijetí událostí v Application Insights může několik minut trvat, takže pokud se hned nezobrazí, před další diagnostikou chvíli počkejte.

Abyste při prvním připojení měli k dispozici více dat, do Application Insights se automaticky vyexportují události v App Center za posledních 48 hodin.

## <a name="start-monitoring-your-app"></a>Zahájení monitorování aplikace

Nad rámec analytických nástrojů, které poskytuje App Center, může Application Insights dotazovat, segmentovat, filtrovat a analyzovat vlastní telemetrii událostí z vašich aplikací.

1. **Dotazování vlastní telemetrie událostí.** Na stránce **přehled** Application Insights vyberte **protokoly (analýza)**.

   Otevře se portál Application Insights protokoly (Analytics) přidružený k vašemu prostředku Application Insights. Portál protokolů (Analytics) vám umožňuje přímo dotazovat data pomocí dotazovacího jazyka Log Analytics, takže se můžete zeptat na libovolně složité otázky týkající se vaší aplikace a jejích uživatelů.
   
   Otevřete na portálu protokoly (Analytics) novou kartu a vložte ji do následujícího dotazu. Dotaz vrátí počet jedinečných uživatelů, kteří z vaší aplikace odeslali jednotlivé vlastní události za posledních 24 hodin, a výpis seřadí podle tohoto počtu jedinečných uživatelů.

   ```AIQL
   customEvents
   | where timestamp >= ago(24h)
   | summarize dcount(user_Id) by name 
   | order by dcount_user_Id desc 
   ```

   ![Portál pro protokoly (Analytics)](./media/mobile-center-quickstart/analytics-portal-001.png)

   1. Vyberte dotaz kliknutím na libovolnou část dotazu v textovém editoru.
   2. Pak dotaz spusťte kliknutím na **Přejít**. 

   Další informace o [Application Insights Analytics](../logs/log-query-overview.md) a [dotazovacím jazyku Log Analytics](/azure/data-explorer/kusto/query/).


2. **Segmentace a filtrování vlastní telemetrie událostí.** Na stránce **Přehled** Application Insights zvolte v obsahu **Uživatelé**.

   ![Ikona nástroje Uživatelé](./media/mobile-center-quickstart/users-icon-001.png)

   Nástroj Uživatelé ukazuje, kolik uživatelů vaší aplikace kliklo na určitá tlačítka, navštívilo určité obrazovky nebo provedlo jakoukoli jinou akci, kterou sledujete jako událost pomocí sady App Center SDK. Pokud jste hledali způsob, jak segmentovat a filtrovat události App Center, nástroj Uživatelé je skvělou volbou.

   ![Nástroj Uživatelé](./media/mobile-center-quickstart/users-001.png) 

   Například můžete segmentovat využití podle zeměpisné oblasti tím, že v rozevírací nabídce **Rozdělit podle** zvolíte **Země nebo oblast**.

3. **Analýza vzorů konverze, udržení a navigace ve vaší aplikaci.** Na stránce **Přehled** Application Insights zvolte v obsahu **Toky uživatelů**.

   ![Nástroj Toky uživatelů](./media/mobile-center-quickstart/user-flows-001.png)

   Nástroj Toky uživatelů vizualizuje, jaké události uživatelé odesílají po nějaké počáteční události. To je užitečné k získání celkového přehledu o způsobu, jakým uživatelé procházejí vaši aplikaci. Nástroj může také odhalit některá místa, kde mnoho uživatelů z vaší aplikace odchází nebo kde opakované provádějí stejné akce.

   Kromě nástroje Toky uživatelů obsahuje Application Insights několik dalších nástrojů pro analýzu chování uživatelů, které řeší konkrétní otázky:

   * **Trychtýře** pro analýzy a monitorování konverzního poměru.
   * **Udržení** pro analýzy toho, jak dobře vaše aplikace udržuje uživatele v průběhu času.
   * **Sešity** pro kombinaci vizualizací a textu do sestavy s možností sdílení.
   * **Kohorty** pro pojmenování a ukládání konkrétních skupin uživatelů nebo událostí, aby se na ně dalo snadno odkazovat v jiných analytických nástrojích.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nechcete pokračovat v používání Application Insights s App Center, vypněte export v App Center a odstraňte prostředek Application Insights. Tím se vyhnete dalšímu účtování Application Insights za tento prostředek.

Vypnutí exportu v App Center:

1. V App Center přejděte do **Nastavení** a zvolte **Export**.
2. Klikněte na export do Application Insights, který chcete odstranit, pak klikněte na **Odstranit export** v dolní části a potvrďte volbu.

Odstranění prostředku Application Insights:

1. V nabídce vlevo na webu Azure Portal klikněte na **Skupiny prostředků** a vyberte skupinu prostředků, ve které se vytvořil váš prostředek Application Insights.
2. Otevřete prostředek Application Insights, který chcete odstranit. Pak v horní nabídce prostředku klikněte na **Odstranit** a potvrďte volbu. Tím se trvale odstraní kopie dat exportovaných do Application Insights.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Porozumění způsobu, jakým zákazníci používají vaši aplikaci](../app/usage-overview.md)