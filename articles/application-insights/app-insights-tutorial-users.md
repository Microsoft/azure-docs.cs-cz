---
title: Pochopení vašim zákazníkům ve službě Azure Application Insights | Microsoft Docs
description: Kurz k používání služby Azure Application Insights pochopit, jak zákazníci používají aplikaci.
keywords: ''
services: application-insights
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: db61c300ad82270e59d315fa3372d9e4390c7a21
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2017
ms.locfileid: "24099017"
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Slouží k pochopení, jak zákazníci používají aplikaci Azure Application Insights

Azure Application Insights shromažďuje informace o využití, které vám pomohou pochopit, jak uživatelům interakci s vaší aplikací.  Tento kurz vás provede různými prostředky, které jsou k dispozici k analýze tyto informace.  Se dozvíte, jak:

> [!div class="checklist"]
> * Analyzovat podrobnosti o uživatele, kteří používají aplikace
> * Pomocí informací o relaci k analýze jak zákazníci používat vaši aplikaci
> * Definování nálevky, které umožňují porovnat vaše aktivity požadované uživatelů k jejich skutečné aktivity 
> * Vytvořte sešit konsolidovat vizualizace a dotazy do jednoho dokumentu
> * Seskupování podobných uživatelů k jejich analýze společně
> * Zjistěte, kteří uživatelé jsou vrácení do vaší aplikace
> * Zkontrolujte, jak uživatelé přecházejí mezi vaší aplikace


## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu je potřeba:

- Nainstalovat [Visual Studio 2017](https://www.visualstudio.com/downloads/) s následujícími sadami funkcí:
    - Vývoj pro ASP.NET a web
    - Vývoj pro Azure
- Stáhněte a nainstalujte [Visual Studio Debugger snímku](http://aka.ms/snapshotdebugger).
- Nasazení aplikace .NET do Azure a [povolit Application Insights SDK](app-insights-asp-net.md). 
- [Odeslání telemetrie z vaší aplikace](app-insights-usage-overview.md#send-telemetry-from-your-app) pro přidání vlastních událostí nebo stránky zobrazení
- Odeslat [uživatelský kontext](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context) ke sledování, co uživatel provede v čase a plně využívat funkce využití.

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.
Přihlaste se k portálu Azure v [https://portal.azure.com](https://portal.azure.com).

## <a name="get-information-about-your-users"></a>Získání informací o vaši uživatelé
**Uživatelé** panel umožňuje pochopit důležité podrobnosti o vaši uživatelé v mnoha různými způsoby. Tento panel můžete použít k pochopení informace jako kde vaši uživatelé se připojují z podrobnosti svého klienta a oblasti vaší aplikace, které máte přístup. 

1. Vyberte **Application Insights** a potom vyberte své předplatné.
2. Vyberte **uživatelé** v nabídce.
3. Výchozí zobrazení se zobrazuje počet jedinečných uživatelů, kterým se připojili k vaší aplikaci za posledních 24 hodin.  Můžete změnit časové okno a nastavit různé další kritéria filtru pro tyto informace.

    ![Tvůrce dotazů](media\app-insights-tutorial-users\QueryBuilder.png)

6. Klikněte **během** rozevíracího seznamu a změnit časové okno na 7 dní.  Tím se zvyšuje dat obsažených v různých grafů v panelu.

    ![Změnit časové rozmezí](media\app-insights-tutorial-users\TimeRange.png)

4. Klikněte **rozdělit** rozevírací přidat rozpis podle vlastnosti uživatele do grafu.  Vyberte **zemi nebo oblast**.  Graf obsahuje stejná data, ale můžete zobrazit rozpis počet uživatelů, pro každou zemi.

    ![Země nebo oblasti grafu](media\app-insights-tutorial-users\CountryorRegion.png)

5. Umístěte kurzor na různé řádky v tabulce a Všimněte si, že počet pro každou zemi odráží pouze časový interval určený parametrem tento panel.
6. Podívejte se na **Insights** sloupec vpravo, která provádět analýzy na uživatelská data.  To poskytuje informace, jako je počet jedinečných relací v průběhu období a záznamy s společných vlastností, které tvoří významné dat uživatele 

    ![Statistika sloupce](media\app-insights-tutorial-users\insights.png)


## <a name="analyze-user-sessions"></a>Analýza uživatelských relací
**Relací** panel je podobná **uživatelé** panelu.  Kde **uživatelé** vám pomůže pochopit podrobnosti o uživatele, kteří používají aplikaci **relací** vám pomůže pochopit, jak uživatelům používat aplikace.  

1. Vyberte **relací** v nabídce.
2. Podívejte se na graf a poznamenejte si, že máte stejné možnosti k filtrování a rozdělení dat jako v **uživatelé** panelu.

    ![Tvůrce dotazů relací](media\app-insights-tutorial-users\SessionsBuilder.png)

3. **Ukázka tyto relace** podokně na pravé straně zobrazí relací, které zahrnují velké množství událostí.  Jedná se o zajímavých relace k analýze.

    ![Ukázka tyto relace](media\app-insights-tutorial-users\SessionsSample.png)

4. Klikněte na jednu relaci zobrazíte jeho **časová osa relace**, který zobrazuje každou akci v relací.  Můžete určit informace, jako je relace s velkým počtem výjimky.

    ![Časová osa relací](media\app-insights-tutorial-users\SessionsTimeline.png)

## <a name="group-together-similar-users"></a>Seskupit podobné uživatelů
A **kohorty** je sada uživatelů groupd na podobnou charakteristikou.  Můžete vytvořit kohorty pro filtrování dat v jiných panelů umožňuje analyzovat konkrétní skupiny uživatelů.  Například můžete chtít analyzovat pouze uživatelé, kteří dokončení nákupu.

1.  Vyberte **kohorty** v nabídce.
2.  Klikněte na tlačítko **nový** k vytvoření nové kohorty.
3.  Vyberte **kdo používá** rozevírací seznam a vyberte akci.  Pouze uživatelé, kteří provést tuto akci v rámci časový interval sestavy budou zahrnuty.

    ![Kohorty, který provedl zadané akce](media\app-insights-tutorial-users\CohortsDropdown.png)

4.  Vyberte **uživatelé** v nabídce.
5.  V **zobrazit** rozevíracího seznamu, vyberte kohorty jste právě vytvořili.  Data grafu je omezená na uživatele.

    ![Kohorty v nástroji pro uživatele](media\app-insights-tutorial-users\UsersCohort.png)


## <a name="compare-desired-activity-to-reality"></a>Porovnání požadované aktivity skutečnosti
Při předchozí panelů se zaměřuje na co se uživatelům vaší aplikace, **nálevky** soustředit na co chcete uživatelům.  Ikona představuje sadu kroků ve vaší aplikaci a procento uživatelů, kteří se pohybují mezi kroky.  Můžete například vytvořit trychtýřového grafu, které měří podíl uživatelů, kteří se připojují k vaší aplikace, kteří vyhledávání produktu.  Můžete se podívat, procento uživatelů, kteří přidat produktu do nákupního košíku a procento uživatelů, kteří dokončete nákup.

1. Vyberte **nálevky** v nabídce a pak klikněte na tlačítko **nový**. 

    ![](media\app-insights-tutorial-users\funnelsnew.png)

2. Zadejte **Trychtýřový název**.
3. Vytvořte trychtýřového grafu s alespoň dva kroky výběrem akce pro každý krok.  Seznam akcí, vychází z dat využití získané nástrojem Application Insights.

    ![](media\app-insights-tutorial-users\funnelsedit.png)

4. Klikněte na tlačítko **Uložit** uložte trychtýřového grafu a zobrazte jeho výsledky.  Napravo od trychtýřového grafu zobrazí nejčastější události před první aktivitu a za poslední aktivity, které vám pomohou pochopit uživatele tendence kolem konkrétní pořadí.

    ![](media\app-insights-tutorial-users\funnelsright.png)


## <a name="learn-which-customers-return"></a>Další informace, které zákazníci vrátit
**Uchování** vám pomůže pochopit, kteří uživatelé jsou vracející se zpět do vaší aplikace.  

1. Vyberte **uchování** v nabídce.
2. Ve výchozím nastavení zahrnuje analyzovaných informace uživatelů, kteří provádět žádnou akci a poté vrácen provádět veškeré akce.  Tento filtr můžete změnit na jakékoli zahrnout, například jenom uživatelé, kteří vrácené po dokončení nákupu.

    ![](media\app-insights-tutorial-users\retentionquery.png)

3. Vracejících uživatelů, které odpovídají kritériím se zobrazují v grafické a tabulky tvar různých dobách trvání.  Typický vzor je pro postupné pokles při vracení uživatele v průběhu času.  Náhlé vyřaďte z jednoho časového období na další může vyvolat o problém. 

    ![](media\app-insights-tutorial-users\retentiongraph.png)

## <a name="analyze-user-navigation"></a>Analýza navigační uživatele
A **uživatelskému toku** vizualizuje, jak uživatelé přecházejí mezi stránkami a funkce vaší aplikace.  To lze zodpovědět otázky, například kde uživatelé obvykle přesunout z konkrétní stránky, jak obvykle ukončete aplikaci a pokud jsou všechny akce, které jsou pravidelně opakovat.

1.  Vyberte **uživatele toky** v nabídce.
2.  Klikněte na tlačítko **nový** vytvořit nový tok uživatele a potom klikněte na **upravit** upravit její podrobnosti.
3.  Zvýšit **časový rozsah** na 7 dní a pak vyberte počáteční událost.  Tok bude sledovat uživatelské relace, které začínají tuto událost.

    ![](media\app-insights-tutorial-users\flowsedit.png)

4.  Zobrazí se uživatelskému toku a uvidíte cesty jiný uživatel a počty jejich relace.  Modré čáry označují akci, která uživatel provedl po aktuální akce.  Red řádek označuje konec relace uživatele.

    ![](media\app-insights-tutorial-users\flows.png)

5.  Postup odebrání události z toku, klikněte na tlačítko **x** v horním rohu akci a pak klikněte na tlačítko **vytvoření grafu**.  Graf bude překreslen pomocí všechny instance této události odebrán.  Klikněte na tlačítko **upravit** zobrazíte, že událost je nyní přidána do **vyloučené události**.

    ![](media\app-insights-tutorial-users\flowsexclude.png)

## <a name="consolidate-usage-data"></a>Konsolidovat data o využití
**Sešity** zkombinovat dokumenty interaktivní vizualizaci dat, analytické dotazy a text.  Sešity můžete seskupit běžných informací o využití, konsolidovat informace z konkrétní incidentu nebo zpětně hlásit si s týmem využití vaší aplikace.

1.  Vyberte **sešity** v nabídce.
2.  Klikněte na tlačítko **nový** k vytvoření nového sešitu.
3.  Dotaz je již k dispozici, která zahrnuje všechna data využití za posledních den zobrazí jako pruhový graf.  Můžete použít tento dotaz, ručně upravit nebo klikněte na tlačítko **ukázkové dotazy** vybrat z jiné užitečné dotazy.

    ![](media\app-insights-tutorial-users\samplequeries.png)

4.  Klikněte na tlačítko **dokončení úprav**.
5.  Klikněte na tlačítko **upravit** v horním podokně můžete upravit text v horní části sešitu.  To je naformátován pomocí markdownu.

    ![](media\app-insights-tutorial-users\markdown.png)

6.  Klikněte na tlačítko **přidat uživatele** přidat graf s informace o uživateli.  V případě, že chcete a pak klikněte na tlačítko Upravit podrobnosti grafu **dokončení úprav** ji uložit.


## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili jak analyzovat vaši uživatelé, přechodu na v dalším kurzu se dozvíte, jak vytvořit vlastní řídicí panely, které tyto informace zkombinovat s další užitečné data o vaší aplikaci.

> [!div class="nextstepaction"]
> [Vytvořit vlastní řídicí panely](app-insights-tutorial-dashboards.md)
