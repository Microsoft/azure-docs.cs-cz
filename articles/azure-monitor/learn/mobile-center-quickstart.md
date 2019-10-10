---
title: Rychlý Start s Azure Application Insights | Microsoft Docs
description: Poskytuje pokyny pro rychlé nastavení mobilní aplikace pro monitorování pomocí Application Insights a App Center
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.service: application-insights
ms.reviewer: daviste
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 2c198ce8af17912d3174269b6cced96b5c1ac8bd
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243236"
---
# <a name="start-analyzing-your-mobile-app-with-app-center-and-application-insights"></a>Začněte s analýzou mobilní aplikace pomocí App Center a Application Insights

Tento rychlý Start vás provede připojením instance App Center vaší aplikace k Application Insights. Pomocí Application Insights můžete dotazovat, segmentovat, filtrovat a analyzovat vaši telemetrii pomocí výkonnějších nástrojů, než jsou dostupné ze služby [analýzy](https://docs.microsoft.com/mobile-center/analytics/) App Center.

## <a name="prerequisites"></a>Požadované součásti

K dokončení tohoto rychlého startu potřebujete:

- Předplatné Azure.
- Nativní aplikace pro iOS, Android, Xamarin, Universal Windows nebo reaguje.
 
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="sign-up-with-app-center"></a>Zaregistrujte se App Center
Začněte tím, že vytvoříte účet a [Zaregistrujte se App Center](https://appcenter.ms/signup?utm_source=ApplicationInsights&utm_medium=Azure&utm_campaign=docs).

## <a name="onboard-to-app-center"></a>Připojit k App Center

Než budete moct použít Application Insights v mobilní aplikaci, musíte aplikaci připojit k [App Center](https://docs.microsoft.com/mobile-center/). Application Insights nepřijímá telemetrii přímo z mobilní aplikace. Místo toho vaše aplikace odesílá vlastní telemetrii událostí do App Center. Pak App Center průběžně exportuje kopie těchto vlastních událostí do Application Insights při přijetí událostí. (To se nevztahuje na [sadu SDK Application Insights js](https://github.com/Microsoft/ApplicationInsights-JS) ani na [nereagují nativní modul plug-in](https://github.com/Microsoft/ApplicationInsights-JS/tree/master/vNext/extensions/applicationinsights-react-native) , kde se telemetrie odesílá přímo do Application Insights.)

Pokud chcete svou aplikaci připojit, postupujte podle pokynů pro rychlý start App Center pro každou platformu, kterou vaše aplikace podporuje. Vytvořte samostatné instance App Center pro každou platformu:

* [iOS](https://docs.microsoft.com/mobile-center/sdk/getting-started/ios).
* [Android](https://docs.microsoft.com/mobile-center/sdk/getting-started/android).
* [Xamarin](https://docs.microsoft.com/mobile-center/sdk/getting-started/xamarin).
* [Univerzální pro Windows](https://docs.microsoft.com/mobile-center/sdk/getting-started/uwp).
* [Reagují na nativní](https://docs.microsoft.com/mobile-center/sdk/getting-started/react-native).

## <a name="track-events-in-your-app"></a>Sledování událostí v aplikaci

Po připojení aplikace k App Center je potřeba ji upravit pro odeslání vlastní telemetrie událostí pomocí sady App Center SDK. Vlastní události jsou jediným typem App Center telemetrie, která se exportuje do Application Insights.

Pokud chcete odesílat vlastní události z aplikací pro iOS, použijte metody `trackEvent` nebo `trackEvent:withProperties` v sadě SDK pro App Center. [Přečtěte si další informace o sledování událostí z aplikací pro iOS.](https://docs.microsoft.com/mobile-center/sdk/analytics/ios)

```Swift
MSAnalytics.trackEvent("Video clicked")
```

Pokud chcete odesílat vlastní události z aplikací pro Android, použijte metodu `trackEvent` v sadě App Center SDK. [Přečtěte si další informace o sledování událostí z aplikací pro Android.](https://docs.microsoft.com/mobile-center/sdk/analytics/android)

```Java
Analytics.trackEvent("Video clicked")
```

K odesílání vlastních událostí z jiných aplikačních platforem použijte metody `trackEvent` ve svých App Center sadách SDK.

Pokud chcete mít jistotu, že se přijímají vaše vlastní události, v části **Analýza** v tématu App Center přejít na kartu **události** . Zobrazení událostí od odeslání z vaší aplikace může trvat několik minut.

## <a name="create-an-application-insights-resource"></a>Vytvoření prostředku Application Insights

Jakmile vaše aplikace posílá vlastní události a tyto události přijímá App Center, musíte v Azure Portal vytvořit prostředek typu App Center Application Insights:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. Vyberte **vytvořit prostředek** > **vývojářské nástroje** > **Application Insights**.

    > [!NOTE]
    > Pokud Application Insights prostředek vytvoříte poprvé, můžete si o tom přečíst další informace v dokumentu [vytvoření prostředku Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

    Zobrazí se konfigurační pole. Vstupní pole vyplníte pomocí následující tabulky.

    | Nastavení        |  Hodnota           | Description  |
   | ------------- |:-------------|:-----|
   | **Název**      | Některá globálně jedinečná hodnota, například myApp-iOS | Název, který identifikuje monitorovanou aplikaci |
     | **Skupina prostředků**     | Nová skupina prostředků nebo existující v nabídce | Skupina prostředků, ve které se má vytvořit nový prostředek Application Insights |
   | **Umístění** | Umístění z nabídky | Vyberte umístění poblíž vaší aplikace nebo poblíž místa, kde je vaše aplikace hostovaná. |

3. Klikněte na **Vytvořit**.

Pokud vaše aplikace podporuje víc platforem (iOS, Android atd.), je nejlepší vytvořit samostatné prostředky Application Insights, jednu pro každou platformu.

## <a name="export-to-application-insights"></a>Exportovat do Application Insights

V novém prostředku Application Insights na stránce **Přehled** . Zkopírujte klíč instrumentace z prostředku.

V instanci [App Center](https://appcenter.ms/) pro vaši aplikaci:

1. Na stránce **Nastavení** klikněte na **exportovat**.
2. Zvolte **Nový export**, vyberte **Application Insights**a pak klikněte na **přizpůsobit**.
3. Do boxu vložte Application Insights klíč instrumentace.
4. Souhlas s rostoucím využitím předplatného Azure, které obsahuje váš Application Insights prostředek. Každý prostředek Application Insights je zdarma pro prvních 1 GB dat přijatých za měsíc. [Přečtěte si další informace o cenách Application Insights.](https://azure.microsoft.com/pricing/details/application-insights/)

Nezapomeňte tento postup opakovat pro každou platformu, kterou vaše aplikace podporuje.

Po nastavení [exportu](https://docs.microsoft.com/mobile-center/analytics/export) se každá vlastní událost přijatá pomocí App Center zkopíruje do Application Insights. Může trvat několik minut, než se dostanou události Application Insights, takže pokud se neobjeví okamžitě, před dalším diagnostikou počkejte trochu.

Chcete-li při prvním připojení poskytnout více dat, bude do Application Insights automaticky exportována poslední 48 hodin vlastních událostí v App Center.

## <a name="start-monitoring-your-app"></a>Zahájení monitorování aplikace

Application Insights se může dotazovat, segmentovat, filtrovat a analyzovat vlastní telemetrii událostí z vašich aplikací, a to za rámec analytických nástrojů, které poskytuje App Center.

1. **Dotazování vlastní telemetrie událostí** Na stránce **přehled** Application Insights vyberte **protokoly (analýza)** .

   Otevře se portál Application Insights protokoly (Analytics) přidružený k vašemu prostředku Application Insights. Portál protokolů (Analytics) vám umožňuje přímo dotazovat data pomocí dotazovacího jazyka Log Analytics, takže se můžete zeptat na libovolně složité otázky týkající se vaší aplikace a jejích uživatelů.
   
   Otevřete na portálu protokoly (Analytics) novou kartu a vložte ji do následujícího dotazu. Vrátí počet různých uživatelů, kteří z vaší aplikace poslali jednotlivé vlastní události za posledních 24 hodin, seřazené podle těchto různých počtů.

   ```AIQL
   customEvents
   | where timestamp >= ago(24h)
   | summarize dcount(user_Id) by name 
   | order by dcount_user_Id desc 
   ```

   ![Portál pro protokoly (Analytics)](./media/mobile-center-quickstart/analytics-portal-001.png)

   1. Vyberte dotaz Kliknutím kamkoli na dotaz v textovém editoru.
   2. Pak klikněte na **Přejít** a spusťte dotaz. 

   Přečtěte si další informace o [Application Insights Analytics](../../azure-monitor/app/analytics.md) a [dotazovacím jazyku Log Analytics](https://aka.ms/LogAnalyticsLanguageReference).


2. **Segmentace a filtrování vlastní telemetrie událostí.** Na stránce **přehled** Application Insights v obsahu vyberte **Uživatelé** .

   ![Ikona nástroje Uživatelé](./media/mobile-center-quickstart/users-icon-001.png)

   Nástroj Uživatelé zobrazuje, kolik uživatelů vaší aplikace kliklo na určitá tlačítka, navštívilo určité obrazovky nebo provedlo jakoukoli jinou akci, kterou sledujete jako událost s App Center SDK. Pokud jste hledali způsob, jak segmentovat a filtrovat události App Center, je nástroj pro uživatele skvělou volbou.

   ![Nástroj pro uživatele](./media/mobile-center-quickstart/users-001.png) 

   Například segmentujte využití podle geografického výběru výběrem **země nebo oblast** v rozevírací nabídce **rozdělit podle** .

3. **Analýza převodů, uchovávání a navigačních vzorů ve vaší aplikaci.** Na stránce **přehled** Application Insights vyberte v obsahu položku **toky uživatelů** .

   ![Nástroj Toky uživatelů](./media/mobile-center-quickstart/user-flows-001.png)

   Nástroj Toky uživatelů vizualizuje, které události uživatelé odesílají po určité počáteční události. Je užitečné získat celkový přehled o tom, jak uživatelé procházejí vaši aplikaci. Může také odhalit místa, kde mnoho uživatelů z vaší aplikace provádí změny, nebo opakovat stejné akce.

   Kromě Toky uživatelů má Application Insights k zodpovězení konkrétních otázek několik dalších nástrojů pro analýzu chování uživatelů:

   * **Trychtýře** pro analýzu a monitorování sazeb převodu.
   * **Uchovávání informací** o tom, jak dobře vaše aplikace zachovává uživatele v průběhu času.
   * **Sešity** pro kombinování vizualizací a textu do sestavy ke sdílení.
   * **Kohorty** pro pojmenování a ukládání konkrétních skupin uživatelů nebo událostí, aby na ně bylo možné snadno odkazovat z jiných analytických nástrojů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nechcete Application Insights s App Center nadále používat, vypněte export v App Center a odstraňte Application Insights prostředek. Tím zabráníte dalšímu navýšení Application Insights pro tento prostředek.

Vypnutí exportu v App Center:

1. V App Center přejít na **Nastavení** a vyberte **exportovat**.
2. Klikněte na Application Insights export, který chcete odstranit, a pak klikněte na **Odstranit export** v dolní části a potvrďte.

Postup odstranění prostředku Application Insights:

1. V nabídce na levé straně Azure Portal klikněte na **skupiny prostředků** a pak vyberte skupinu prostředků, ve které se vytvořil prostředek Application Insights.
2. Otevřete prostředek Application Insights, který chcete odstranit. Pak v horní nabídce prostředku klikněte na **Odstranit** a potvrďte. Tato akce trvale odstraní kopii dat, která byla exportována do Application Insights.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Pochopení způsobu, jakým zákazníci používají vaši aplikaci](../../azure-monitor/app/usage-overview.md)
