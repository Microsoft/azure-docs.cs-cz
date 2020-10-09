---
title: Porozumění zákazníkům v Azure Application Insights | Microsoft Docs
description: Kurz použití Azure Application Insights k pochopení, jakým způsobem zákazníci používají vaši aplikaci.
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.custom: mvc
ms.openlocfilehash: ce9bf6fe6d130cf8511db2b2351c0de01b753d81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318398"
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Použití Azure Application Insights k pochopení, jakým způsobem zákazníci používají vaši aplikaci

Azure Application Insights shromažďuje informace o využití a pomáhá pochopit, jakým způsobem uživatelé pracují s vaší aplikací.  Tento kurz vás provede různými prostředky, které jsou k dispozici pro analýzu těchto informací.  V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Analýza podrobností o uživatelích, kteří přistupují k vaší aplikaci
> * Použití informací o relacích k analýze, jakým způsobem zákazníci používají vaši aplikaci
> * Definování trychtýřů umožňujících porovnat požadované aktivity uživatelů s jejich skutečnou aktivitou 
> * Vytvoření sešitu pro konsolidaci vizualizací a dotazů v jednom dokumentu
> * Seskupení podobných uživatelů za účelem jejich společné analýzy
> * Zjištění, kteří uživatelé se vracejí k vaší aplikaci
> * Prozkoumání způsobu, jakým uživatelé procházejí vaši aplikaci


## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/downloads/) s následujícími úlohami:
    - Vývoj pro ASP.NET a web
    - Vývoj pro Azure
- Stáhnout a nainstalovat [Visual Studio Snapshot Debugger](https://aka.ms/snapshotdebugger).
- Nasadit do Azure aplikaci .NET a [povolit sadu Application Insights SDK](../app/asp-net.md). 
- [Odesílat z aplikace telemetrii](../app/usage-overview.md#send-telemetry-from-your-app) umožňující přidání vlastních zobrazení událostí a stránek.
- Odesílat [kontext uživatele](../app/usage-send-user-context.md) umožňující sledovat aktivity uživatele v průběhu času a naplno využívat funkce využití.

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.
Přihlaste se k Azure Portal v [https://portal.azure.com](https://portal.azure.com) .

## <a name="get-information-about-your-users"></a>Získání informací o uživatelích
Panel **Uživatelé** umožňuje různými způsoby porozumět důležitým podrobnostem o vašich uživatelích. Pomocí tohoto panelu můžete získat informace, jako například odkud se uživatelé připojují, podrobnosti o jejich klientech a oblasti aplikace, ke kterým přistupují. 

1. Vyberte **Application Insights** a pak vyberte své předplatné.
2. V nabídce vyberte **Uživatelé**.
3. Ve výchozím zobrazení se zobrazí počet jedinečných uživatelů, kteří se k vaší aplikaci připojili za posledních 24 hodin.  Tyto informace můžete filtrovat změnou časového intervalu a nastavením různých dalších kritérií.

    ![Tvůrce dotazů](media/tutorial-users/QueryBuilder.png)

6. Klikněte na rozevírací nabídku **V období** a změňte časový interval na 7dnů.  Tím se zvýší množství zahrnutých dat na různých grafech na panelu.

    ![Změna časového rozsahu](media/tutorial-users/TimeRange.png)

4. Klikněte na rozevírací nabídku **Rozdělit podle** a přidejte do grafu rozpis podle jednotlivých vlastností uživatele.  Vyberte **Země nebo oblast**.  Graf obsahuje stejná data, ale umožňuje zobrazit rozpis počtu uživatelů pro každou zemi nebo oblast.

    ![Graf Země nebo oblast](media/tutorial-users/CountryorRegion.png)

5. Umístěte kurzor do různých pruhů v grafu a Všimněte si, že počet pro každou zemi nebo oblast odráží pouze časové okno reprezentované tímto pruhem.
6. Podívejte se na sloupec **Přehledy** na pravé straně, který provádí analýzu dat o vašich uživatelích.  Najdete zde informace, jako je počet jedinečných relací v daném časovém období a záznamy se společnými vlastnostmi, které tvoří významnou část dat o uživatelích. 

    ![Sloupec Přehledy](media/tutorial-users/insights.png)


## <a name="analyze-user-sessions"></a>Analýza uživatelských relací
Panel **Relace** je podobný panelu **Uživatelé**.  Zatímco panel **Uživatelé** pomáhá porozumět podrobnostem o uživatelích, kteří přistupují k vaší aplikací, panel **Relace** pomáhá porozumět způsobu, jakým tito uživatelé vaši aplikaci používali.  

1. V nabídce vyberte **Relace**.
2. Podívejte se na graf a všimněte si, že máte k dispozici stejné možnosti filtrování a rozdělení dat jako na panelu **Uživatelé**.

    ![Tvůrce dotazů pro relace](media/tutorial-users/SessionsBuilder.png)

3. V podokně **Vzorek těchto relací** na pravé straně je seznam relací zahrnujících velký počet událostí.  Jedná se o zajímavé relace k analýze.

    ![Vzorek těchto relací](media/tutorial-users/SessionsSample.png)

4. Kliknutím na některou z relací zobrazte její **časovou osu relace**, na které se zobrazí všechny akce v rámci relace.  To vám může pomoct identifikovat informace, jako jsou relace s velkým počtem výjimek.

    ![Časová osa relace](media/tutorial-users/SessionsTimeline.png)

## <a name="group-together-similar-users"></a>Seskupení podobných uživatelů
**Kohorta** je sada uživatelů seskupených podle podobných vlastností.  Kohorty můžete použít k filtrování dat na dalších panelech, což vám umožní analyzovat konkrétní skupiny uživatelů.  Můžete například chtít analyzovat pouze uživatele, kteří dokončili nákup.

1.  V nabídce vyberte **Kohorty**.
2.  Kliknutím na **Nová** vytvořte novou kohortu.
3.  Vyberte rozevírací nabídku **Kteří použili** a vyberte akci.  Do sestavy se zahrnou pouze uživatelé, kteří v daném časovém intervalu provedli tuto akci.

    ![Kohorta uživatelů, kteří provedli zadané akce](media/tutorial-users/CohortsDropdown.png)

4.  V nabídce vyberte **Uživatelé**.
5.  V rozevírací nabídce **Zobrazit** vyberte kohortu, kterou jste právě vytvořili.  Data grafu se omezí na tyto uživatele.

    ![Kohorty v nástroji Uživatelé](media/tutorial-users/UsersCohort.png)


## <a name="compare-desired-activity-to-reality"></a>Porovnání požadované aktivity se skutečností
Zatímco předchozí panely se zaměřovaly na to, co prováděli uživatelé vaší aplikace, panel **Trychtýře** se zaměřuje na to, co chcete, aby uživatelé prováděli.  Trychtýř představuje posloupnost kroků ve vaší aplikaci a procento uživatelů, kteří procházejí mezi jednotlivými kroky.  Můžete například vytvořit trychtýř, který měří procento uživatelů, kteří se připojí k vaší aplikaci a vyhledají produkt.  Následně můžete zobrazit procento uživatelů, kteří si produkt přidají do nákupního košíku, a procento uživatelů, kteří dokončí nákup.

1. V nabídce vyberte **Trychtýře** a pak klikněte na **Nový**. 

    ![Snímek obrazovky ukazující, jak vytvořit nový trychtýř](media/tutorial-users/funnelsnew.png)

2. Zadejte **Název trychtýře**.
3. Vytvořte trychtýř s alespoň dvěma kroky a pro každý krok vyberte akci.  Seznam akcí se sestavuje na základě dat o využití shromážděných službou Application Insights.

    ![Snímek obrazovky ukazující, jak vytvořit kroky v novém trychtýři](media/tutorial-users/funnelsedit.png)

4. Kliknutím na **Uložit** trychtýř uložte a pak zobrazte jeho výsledky.  V okně napravo od trychtýře se zobrazí nejčastější události před první aktivitou a po poslední aktivitě, které vám pomůžou porozumět tendencím uživatelů kolem konkrétní posloupnosti.

    ![Snímek obrazovky znázorňující výsledky události nově vytvořeného trychtýře.](media/tutorial-users/funnelsright.png)


## <a name="learn-which-customers-return"></a>Zjištění, kteří zákazníci se vracejí
**Uchování** pomáhá porozumět tomu, kteří uživatelé se vracejí k vaší aplikaci.  

1. V nabídce vyberte **Uchování**.
2. Analyzované informace ve výchozím nastavení zahrnují uživatele, kteří provedli jakoukoli akci a pak se vrátili, aby provedli jakoukoli akci.  Tento filtr můžete změnit například tak, aby zahrnoval pouze uživatele, kteří se vrátili po dokončení nákupu.

    ![Snímek obrazovky ukazující, jak nastavit filtr uchovávání informací](media/tutorial-users/retentionquery.png)

3. Vracející se uživatelé, kteří splňují kritéria, se zobrazí v grafické a tabulkové podobě pro různé doby.  Typickým vzorem je postupné klesání počtu vracejících se uživatelů v průběhu času.  Náhlý pokles mezi časovými obdobími může vzbuzovat obavy. 

    ![Snímek obrazovky zobrazující graf pro uživatele, kteří se shodují s nastavenými kritérii pro filtr uchovávání dat.](media/tutorial-users/retentiongraph.png)

## <a name="analyze-user-navigation"></a>Analýza procházení uživatelů
**Tok uživatelů** vizualizuje, jak uživatelé procházejí mezi jednotlivými stránkami a funkcemi vaší aplikace.  To vám pomůže najít odpověď na otázky, jako například kam uživatelé obvykle přecházejí z konkrétní stránky, jak obvykle opouštějí vaši aplikaci a jestli existují nějaké akce, které se pravidelně opakují.

1.  V nabídce vyberte **Toky uživatelů**.
2.  Kliknutím na **Nový** vytvořte nový tok uživatelů a pak klikněte na **Upravit**, abyste mohli upravit jeho podrobnosti.
3.  Rozšiřte **Časový rozsah** na 7 dnů a vyberte počáteční událost.  Tento tok bude sledovat uživatelské relace začínající touto událostí.

    ![Snímek obrazovky ukazující, jak vytvořit nový tok uživatele.](media/tutorial-users/flowsedit.png)

4.  Zobrazí se tok uživatelů s různými cestami uživatelů a počty jejich relací.  Modré čáry označují akci, kterou uživatel provedl po aktuální akci.  Červená čára označuje konec uživatelské relace.

    ![Snímek obrazovky znázorňující zobrazení uživatelských cest a počtů relací pro tok uživatele.](media/tutorial-users/flows.png)

5.  Pokud chcete z toku odebrat událost, klikněte na **x** v rohu akce a pak klikněte na **Vytvořit graf**.  Graf se překreslí a všechny instance dané události se odeberou.  Klikněte na **Upravit** a všimněte si, že událost je teď přidaná mezi **Vyloučené události**.

    ![Snímek obrazovky zobrazující seznam vyloučených událostí pro tok uživatele.](media/tutorial-users/flowsexclude.png)

## <a name="consolidate-usage-data"></a>Konsolidace dat o využití
**Sešity** kombinují v interaktivních dokumentech vizualizace dat, analytické dotazy a text.  Pomocí sešitů můžete seskupovat běžné informace o využití, konsolidovat informace o konkrétním incidentu nebo informovat váš tým o využití vaší aplikace.

1.  V nabídce vyberte **Sešity**.
2.  Kliknutím na **Nový** vytvořte nový sešit.
3.  K dispozici již máte dotaz, který zahrne veškerá data o využití za poslední den a zobrazí je v pruhovém grafu.  Můžete použít tento dotaz, ručně ho upravit nebo kliknout na **Ukázkové dotazy** a vybrat si z dalších užitečných dotazů.

    ![Snímek obrazovky zobrazující seznam ukázkových dotazů, které můžete použít.](media/tutorial-users/samplequeries.png)

4.  Klikněte na **Úpravy hotovy**.
5.  V horním podokně klikněte na **Upravit** a upravte text v horní části sešitu.  Tento text je formátovaný s využitím markdownu.

    ![Snímek obrazovky znázorňující úpravu textu v horní části sešitu](media/tutorial-users/markdown.png)

6.  Kliknutím na **Přidat uživatele** přidejte graf s informacemi o uživatelích.  Pokud chcete, upravte podrobnosti grafu, a pak ho uložte kliknutím na **Úpravy hotovy**.


## <a name="next-steps"></a>Další kroky
Teď, když jste zjistili, jak analyzovat uživatele, přejděte k dalšímu kurzu, ve kterém se dozvíte, jak vytvářet vlastní řídicí panely, které tyto informace kombinují s dalšími užitečnými daty o vaší aplikaci.

> [!div class="nextstepaction"]
> [Vytváření vlastních řídicích panelů](./tutorial-app-dashboards.md)

