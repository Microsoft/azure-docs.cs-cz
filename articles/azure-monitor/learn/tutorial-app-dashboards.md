---
title: Vytvoření vlastních řídicích panelů ve službě Azure Application Insights | Microsoft Docs
description: Kurz vytváření vlastních řídicích panelů klíčového ukazatele výkonu pomocí Azure Application Insights
keywords: ''
services: application-insights
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: af4e9d0ec90c203a83acf142dd9c81494fe5bc47
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54081043"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Vytváření vlastních řídicích panelů klíčového ukazatele výkonu pomocí Azure Application Insights

Můžete vytvořit více řídicích panelů na portálu Azure Portal, aby každý obsahoval dlaždice s vizualizací dat z více prostředků Azure v různých skupinách prostředků a předplatných.  Můžete připnout různé grafy a zobrazení z Azure Application Insights a vytvářet vlastní řídicí panely, které vám poskytnou úplný přehled o stavu a výkonu vaší aplikace.  Tento kurz vás provede procesem vytvoření vlastního řídicího panelu, který bude obsahovat více typů dat a vizualizací z Azure Application Insights.  Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření vlastního řídicího panelu v Azure
> * Přidání dlaždice z Galerie dlaždic
> * Přidání standardní metriky v Application Insights na řídicí panel 
> * Přidání grafu vlastní metriky v Application Insights na řídicí panel
> * Přidání výsledků dotazu Analytics na řídicí panel 



## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Nasadit aplikaci .NET do Azure a [povolit sadu Application Insights SDK](../../azure-monitor/app/asp-net.md). 

## <a name="log-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Vytvoření nového řídicího panelu
Jeden řídicí panel může obsahovat prostředky z více aplikací, skupin prostředků a předplatných.  Tento kurz začněte vytvořením nového řídicího panelu pro vaši aplikaci.  

2.  Na hlavní obrazovce portálu vyberte **Nový řídicí panel**.

    ![Nový řídicí panel](media/tutorial-app-dashboards/new-dashboard.png)

3. Zadejte název pro řídicí panel.
4. Podívejte se do **Galerie dlaždic**, kde najdete celou řadu dlaždic, které můžete přidat do řídicího panelu.  Kromě přidávání dlaždic z galerie můžete připínat grafy a jiná zobrazení přímo z Application Insights na řídicí panel.
5. Vyhledejte dlaždici **Markdown** a přetáhněte ji na řídicí panel.  Tato dlaždice vám umožní přidat text ve formátu Markdown, který je ideální pro přidávání popisného textu do řídicího panelu.
6. Přidejte text do vlastností dlaždice a změňte její velikost na plátně řídicího panelu.
    
    ![Úprava dlaždice Markdown](media/tutorial-app-dashboards/edit-markdown.png)

6. Klikněte na **Přizpůsobení dokončeno** v horní části obrazovky, aby se ukončil režim přizpůsobení dlaždice, a potom na **Publikovat změny**, aby se uložily provedené změny.

    ![Řídicí panel s dlaždicí Markdown](media/tutorial-app-dashboards/dashboard-01.png)


## <a name="add-health-overview"></a>Přidání přehledu o stavu
Řídicí panel jenom se statickým textem není moc zajímavý, takže teď přidáte dlaždici z Application Insights, která zobrazí informace o vaší aplikaci.  Dlaždice můžete z Application Insights přidat z Galerie dlaždic nebo je můžete připnout přímo z obrazovek Application Insights.  Díky tomu můžete konfigurovat grafy a zobrazení, která už znáte, ještě než je připnete na řídicí panel.  Přidejte nejdříve standardní přehled o stavu pro vaši aplikaci.  Není nutné nic konfigurovat a v řídicím panelu je možné provádět minimální přizpůsobení.


1. Vyberte **Application Insights** v nabídce Azure a pak vyberte svoji aplikaci.
2. V **Časové ose přehledu** vyberte kontextovou nabídku a klikněte na **Připnout na řídicí panel**.  Tento postup přidá dlaždici na poslední řídicí panel, který byl zobrazený.  

    ![Připnutí časové osy přehledu](media/tutorial-app-dashboards/pin-overview-timeline.png)
 
3. V horní části obrazovky klikněte na **Zobrazit řídicí panel**, abyste se vrátili na řídicí panel.
4. Časová osa přehledu je přidaná na řídicí panel.  Klikněte na ni a přetáhněte ji na místo a potom klikněte na **Přizpůsobení dokončeno** a **Publikovat změny**.  Na řídicím panelu teď máte dlaždici s užitečnými informacemi.

    ![Řídicí panel s časovou osou přehledu](media/tutorial-app-dashboards/dashboard-02.png)



## <a name="add-custom-metric-chart"></a>Přidání grafu vlastní metriky
Panel **Metrika** umožňuje vykreslit v grafu metriku shromážděnou pomocí Application Insights v čase s volitelnými filtry a seskupením.  Podobně jako všechno ostatní ve službě Application Insights můžete tento graf přidat na řídicí panel.  Nejdřív je ale nutné ho trochu přizpůsobit.

1. Vyberte **Application Insights** v nabídce Azure a pak vyberte svoji aplikaci.
1. Vyberte **Metriky**.  
2. Už je vytvořený prázdný graf a vám se zobrazí výzva k přidání metriky.  Přidejte do grafu metriku a volitelně přidejte filtr a seskupení.  Následující příklad ukazuje počet žádostí na server seskupených podle úspěchu.  Díky tomu máte průběžné zobrazení úspěšných a neúspěšných žádostí.

    ![Přidání metriky](media/tutorial-app-dashboards/metrics-chart.png)

4. Vyberte kontextovou nabídku pro graf a vyberte **Připnout na řídicí panel**.  Tento postup přidá zobrazení na poslední řídicí panel, se kterým jste pracovali.

    ![Připnutí grafu metriky](media/tutorial-app-dashboards/pin-metrics-chart.png)

3. V horní části obrazovky klikněte na **Zobrazit řídicí panel**, abyste se vrátili na řídicí panel.

4. Na řídicím panelu teď máte graf metrik časové osy. Klikněte na něj a přetáhněte ho na místo a potom klikněte na **Přizpůsobení dokončeno** a **Publikovat změny**. 

    ![Řídicí panel s metrikami](media/tutorial-app-dashboards/dashboard-03.png)


## <a name="metrics-explorer"></a>Průzkumník metrik
**Průzkumník metrik** je podobný Metrikám, ovšem umožňuje výrazně větší míru přizpůsobení po přidání na řídicí panel.  Co použijete k vykreslení metrik v grafu, závisí na vašich osobních preferencích a požadavcích.

1. Vyberte **Application Insights** v nabídce Azure a pak vyberte svoji aplikaci.
1. Vyberte **Průzkumník metrik**. 
2. Klikněte na graf, abyste ho mohli upravovat, a vyberte jednu nebo více metrik a případně podrobnou konfiguraci.  V příkladu je spojnicový graf, který sleduje průměrnou dobu odezvy stránky.
3. Kliknutím na ikonu připínáčku v pravém horním rohu přidejte graf na řídicí panel a potom ho přetáhněte myší na místo.

    ![Průzkumník metrik](media/tutorial-app-dashboards/metrics-explorer.png)

4. Dlaždice Průzkumník metrik umožňuje větší míru přizpůsobení, jakmile je přidaná na řídicí panel.  Klikněte pravým tlačítkem myši na dlaždici a vyberte **Upravit název**, pokud chcete přidat vlastní název.  Pokračujte a provádějte další úpravy, jestli chcete.

    ![Řídicí panel s průzkumníkem metrik](media/tutorial-app-dashboards/dashboard-04a.png)

5. Na řídicím panelu teď máte přidaný graf Průzkumníka metrik.

    ![Řídicí panel s průzkumníkem metrik](media/tutorial-app-dashboards/dashboard-04.png)

## <a name="add-analytics-query"></a>Přidání dotazu Analytics
Azure Application Insights Analytics poskytuje bohatý dotazovací jazyk umožňující analýzu všech dat shromážděných službou Application Insights.  Stejně jako grafy a jiná zobrazení můžete výstup dotazu Analytics přidat na řídicí panel.   

Vzhledem k tomu, že Azure Application Insights Analytics je samostatná služba, musíte řídicí panel sdílet, aby zahrnoval dotaz Analytics.  Když nasdílíte řídicí panel Azure, publikujete ho jako prostředek Azure, který ho může zpřístupnit pro ostatní uživatele a prostředky.  

1. V horní části obrazovky řídicího panelu klikněte na **Sdílet**.

    ![Publikování řídicího panelu](media/tutorial-app-dashboards/publish-dashboard.png)

2. Zachovejte **Název řídicího panelu** a vyberte **Název předplatného**, abyste mohli řídicí panel sdílet.  Klikněte na **Publikovat**.  Řídicí panel je teď dostupný dalším službám a předplatným.  Volitelně můžete definovat konkrétní uživatele, kteří mají mít přístup k řídicímu panelu.
1. Vyberte **Application Insights** v nabídce Azure a pak vyberte svoji aplikaci.
2. Klikněte na **Analytics** v horní části obrazovky a otevřete portál Analytics.

    ![Spuštění Analytics](media/tutorial-app-dashboards/start-analytics.png)

3. Zadejte následující dotaz, který vrátí 10 nejžádanějších stránek a počet žádostí o ně:

    ```
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10 
    ```

4. Klikněte na **Přejít**, aby se ověřily výsledky dotazu.
5. Klikněte na ikonu připínáčku a vyberte název řídicího panelu.  Důvodu, proč s touto možností musíte vybrat řídicí panel na rozdíl od předchozího postupu, kde byl použit poslední řídicí panel, je, že konzola Analytics je samostatná služba a potřebuje si vybrat ze všech dostupných sdílených řídicích panelů.

    ![Připnutí dotazu Analytics](media/tutorial-app-dashboards/analytics-pin.png)

5. Než přejdete zpět na řídicí panel, přidejte další dotaz, ale tentokrát ho vykreslete jako graf tak, abyste viděli různé způsoby vizualizace dotazu Analytics v řídicím panelu.  Začněte s následujícím dotazem, který shrnuje 10 operací s největším množstvím výjimek.

    ```
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10 
    ```

6. Vyberte **Graf** a poté změňte typ vizualizace výstupu na **Prstencový**.

    ![Graf Analytics](media/tutorial-app-dashboards/analytics-chart.png)

6. Kliknutím na ikonu připínáčku připněte graf na řídicí panel a tentokrát vyberte odkaz k návratu do řídicího panelu.
4. Výsledky dotazů jsou teď přidané do řídicího panelu ve formátu, který jste vybrali.  Klikněte na každý z nich a přetáhněte ho na místo a potom klikněte na **Úpravy hotovy**.
5. Klikněte pravým tlačítkem myši na každou dlaždici a vyberte **Upravit název**, pokud jim chcete dát popisný název.

    ![Řídicí panel s Analytics](media/tutorial-app-dashboards/dashboard-05.png)

5. Klikněte na **Publikovat změny**, aby se změny potvrdily do řídicího panelu, který teď zahrnuje celou řadu grafů a vizualizací z Application Insights.


## <a name="next-steps"></a>Další postup
Teď, když jste se naučili vytvářet vlastní řídicí panely, podívejte se na zbytek dokumentace Application Insights včetně případové studie.

> [!div class="nextstepaction"]
> [Hloubková diagnostika](../../application-insights/app-insights-devops.md)
