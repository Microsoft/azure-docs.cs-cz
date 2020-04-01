---
title: Vytvoření vlastních řídicích panelů ve službě Azure Application Insights | Microsoft Docs
description: Kurz vytváření vlastních řídicích panelů klíčového ukazatele výkonu pomocí Azure Application Insights
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 07/3/2019
ms.custom: mvc
ms.openlocfilehash: caf8b1899cad95ade6297e78e8f2cf35939ef189
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77661630"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Vytváření vlastních řídicích panelů klíčového ukazatele výkonu pomocí Azure Application Insights

Můžete vytvořit více řídicích panelů na portálu Azure Portal, aby každý obsahoval dlaždice s vizualizací dat z více prostředků Azure v různých skupinách prostředků a předplatných.  Můžete připnout různé grafy a zobrazení z Azure Application Insights a vytvářet vlastní řídicí panely, které vám poskytnou úplný přehled o stavu a výkonu vaší aplikace. Tento kurz vás provede procesem vytvoření vlastního řídicího panelu, který bude obsahovat více typů dat a vizualizací z Azure Application Insights.  Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření vlastního řídicího panelu v Azure
> * Přidání dlaždice z Galerie dlaždic
> * Přidání standardní metriky v Application Insights na řídicí panel
> * Přidání grafu vlastní metriky v Application Insights na řídicí panel
> * Přidání výsledků dotazu protokolů (Analytics) na řídicí panel



## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Nasadit do Azure aplikaci .NET a [povolit sadu Application Insights SDK](../../azure-monitor/app/asp-net.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="create-a-new-dashboard"></a>Vytvoření nového řídicího panelu
Jeden řídicí panel může obsahovat prostředky z více aplikací, skupin prostředků a předplatných.  Tento kurz začněte vytvořením nového řídicího panelu pro vaši aplikaci.  

1. V podokně řídicího panelu vyberte **Nový řídicí panel**.

   ![Nový řídicí panel](media/tutorial-app-dashboards/1newdashboard.png)

1. Zadejte název pro řídicí panel.
1. Podívejte se do **Galerie dlaždic**, kde najdete celou řadu dlaždic, které můžete přidat do řídicího panelu.  Kromě přidávání dlaždic z galerie můžete grafy a další zobrazení připnout přímo z Application Insights na řídicí panel.
1. Vyhledejte dlaždici **Markdown** a přetáhněte ji na řídicí panel.  Tato dlaždice umožňuje přidat text formátovaný v markdownu, což je ideální pro přidání popisného textu na řídicí panel.
1. Přidejte text do vlastností dlaždice a změňte její velikost na plátně řídicího panelu.
    
    ![Úprava dlaždice Markdown](media/tutorial-app-dashboards/2dashboard-text.png)

1. Kliknutím na **Dokončení přizpůsobení** v horní části obrazovky ukončete režim přizpůsobení dlaždic.

## <a name="add-health-overview"></a>Přidání přehledu o stavu
Řídicí panel se statickým textem není příliš zajímavý, takže teď přidejte dlaždici z Application Insights, která zobrazí informace o vaší aplikaci.  Dlaždice můžete z Application Insights přidat z Galerie dlaždic nebo je můžete připnout přímo z obrazovek Application Insights.  Díky tomu můžete konfigurovat grafy a zobrazení, která už znáte, ještě než je připnete na řídicí panel.  Přidejte nejdříve standardní přehled o stavu pro vaši aplikaci.  Není nutné nic konfigurovat a v řídicím panelu je možné provádět minimální přizpůsobení.


1. Vyberte zdroj **Application Insights** na domovské obrazovce.
2. V podokně **Přehled** klikněte ![na](media/tutorial-app-dashboards/pushpin.png) ikonu pinu a přidejte dlaždici na poslední řídicí panel, který jste si prohlíželi.  
 
3. Vpravém horním rohu se zobrazí oznámení, že vaše dlaždice byla připnutá k řídicímu panelu. Kliknutím **na Připnuté na řídicí panel** v oznámení se vrátíte na řídicí panel nebo použijte podokno řídicího panelu.
4. Tato dlaždice je nyní přidána na řídicí panel. Vyberte **Upravit,** chcete-li změnit umístění dlaždice. Klikněte na něj a přetáhněte jej do polohy a potom klepněte na **tlačítko Hotovo přizpůsobení**. Na řídicím panelu teď máte dlaždici s užitečnými informacemi.

    ![Řídicí panel s časovou osou přehledu](media/tutorial-app-dashboards/4dashboard-edit.png)

## <a name="add-custom-metric-chart"></a>Přidání grafu vlastní metriky
Panel **Metrika** umožňuje vykreslit v grafu metriku shromážděnou pomocí Application Insights v čase s volitelnými filtry a seskupením.  Podobně jako všechno ostatní ve službě Application Insights můžete tento graf přidat na řídicí panel.  Nejdřív je ale nutné ho trochu přizpůsobit.

1. Vyberte zdroj **Application Insights** na domovské obrazovce.
1. Vyberte **Metriky**.  
2. Už je vytvořený prázdný graf a vám se zobrazí výzva k přidání metriky.  Přidejte do grafu metriku a volitelně přidejte filtr a seskupení.  Následující příklad ukazuje počet žádostí na server seskupených podle úspěchu.  Díky tomu máte průběžné zobrazení úspěšných a neúspěšných žádostí.

    ![Přidání metriky](media/tutorial-app-dashboards/metrics.png)

4. Vpravo vyberte **Připnout na řídicí panel.** Tento postup přidá zobrazení na poslední řídicí panel, se kterým jste pracovali.

3.  Vpravém horním rohu se zobrazí oznámení, že vaše dlaždice byla připnutá k řídicímu panelu. Kliknutím **na Připnuté na řídicí panel** v oznámení se vrátíte na řídicí panel nebo použijte okno řídicího panelu.

4. Tato dlaždice je nyní přidána na řídicí panel. Vyberte **Upravit,** chcete-li změnit umístění dlaždice. Klikněte na něj a přetáhněte jej do polohy a potom klepněte na **tlačítko Hotovo přizpůsobení**.

## <a name="add-logs-analytics-query"></a>Dotaz přidat protokoly (Analytics)
Azure Application Insights Logs (Analytics) poskytuje bohatý dotazovací jazyk, který umožňuje analyzovat všechna data shromážděná application insights. Stejně jako grafy a další zobrazení můžete přidat výstup dotazu protokolů na řídicí panel.

Vzhledem k tomu, že protokoly Azure Applications Insights (Analytics) je samostatná služba, musíte sdílet řídicí panel, aby zahrnoval a zahrnoval dotaz protokolů. Když sdílíte řídicí panel Azure, publikujete ho jako prostředek Azure, který ho může zpřístupnit ostatním uživatelům a prostředkům.  

1. V horní části obrazovky řídicího panelu klikněte na **Sdílet**.

    ![Publikování řídicího panelu](media/tutorial-app-dashboards/8dashboard-share.png)

2. Zachovejte **Název řídicího panelu** a vyberte **Název předplatného**, abyste mohli řídicí panel sdílet.  Klikněte na **Publikovat**.  Řídicí panel je teď dostupný dalším službám a předplatným.  Volitelně můžete definovat konkrétní uživatele, kteří mají mít přístup k řídicímu panelu.
1. Vyberte zdroj **Application Insights** na domovské obrazovce.
2. Kliknutím na **Protokoly (Analytics)** vlevo pod položkou sledování otevřete portál Protokoly (Analytics).
3. Zadejte následující dotaz, který vrátí 10 nejžádanějších stránek a počet žádostí o ně:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Klepnutím na tlačítko **Spustit** ověřte výsledky dotazu.
5. Klikněte na ikonu špendlíku. ![Ikona špendlíku](media/tutorial-app-dashboards/pushpin.png) a vyberte název řídicího panelu. Důvod, proč tato možnost umožňuje vybrat řídicí panel na rozdíl od předchozích kroků, kde byl použit poslední řídicí panel je proto, že protokoly (Analytics) konzole je samostatná služba a je třeba vybrat ze všech dostupných sdílených řídicích panelů.

5. Než se vrátíte na řídicí panel, přidejte další dotaz, ale tentokrát jej vykreslete jako graf, abyste viděli různé způsoby vizualizace dotazu protokolů na řídicím panelu. Začněte s následujícím dotazem, který shrnuje 10 operací s největším množstvím výjimek.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Vyberte **Graf** a poté změňte typ vizualizace výstupu na **Prstencový**.

    ![Graf protokolů (Analytics)](media/tutorial-app-dashboards/11querychart.png)

6. Klikněte na ikonu špendlíku. ![Ikona špendlíku](media/tutorial-app-dashboards/pushpin.png) vpravo nahoře připnete graf na řídicí panel a tentokrát vyberte odkaz, který chcete vrátit na řídicí panel.
4. Výsledky dotazů jsou teď přidané do řídicího panelu ve formátu, který jste vybrali.  Klepněte na položku a přetáhněte každou z nich do polohy a potom klepněte na **tlačítko Hotovo přizpůsobení**.
5. Výběr ikony tužky ![Ikona tužky](media/tutorial-app-dashboards/pencil.png) na každém titulu, aby jim popisný název.

5. Vyberte **Sdílet,** chcete-li znovu publikovat změny na řídicím panelu, který teď obsahuje různé grafy a vizualizace z Application Insights.


## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili vytvářet vlastní řídicí panely, podívejte se na zbytek dokumentace Application Insights včetně případové studie.

> [!div class="nextstepaction"]
> [Hloubková diagnostika](../../azure-monitor/app/devops.md)
