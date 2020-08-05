---
title: Vytvoření vlastních řídicích panelů ve službě Azure Application Insights | Microsoft Docs
description: Kurz vytváření vlastních řídicích panelů klíčového ukazatele výkonu pomocí Azure Application Insights
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 07/3/2019
ms.custom: mvc
ms.openlocfilehash: 706ae5a6c93468da9a65293c1bb4eefb136b938d
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553270"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Vytváření vlastních řídicích panelů klíčového ukazatele výkonu pomocí Azure Application Insights

Můžete vytvořit více řídicích panelů na portálu Azure Portal, aby každý obsahoval dlaždice s vizualizací dat z více prostředků Azure v různých skupinách prostředků a předplatných.  Můžete připnout různé grafy a zobrazení z Azure Application Insights a vytvářet vlastní řídicí panely, které vám poskytnou úplný přehled o stavu a výkonu vaší aplikace. Tento kurz vás provede procesem vytvoření vlastního řídicího panelu, který bude obsahovat více typů dat a vizualizací z Azure Application Insights.

 Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření vlastního řídicího panelu v Azure
> * Přidání dlaždice z Galerie dlaždic
> * Přidání standardní metriky v Application Insights na řídicí panel
> * Přidání grafu vlastní metriky v Application Insights na řídicí panel
> * Přidání výsledků dotazu log (Analytics) na řídicí panel

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu potřebujete:

- Nasadit do Azure aplikaci .NET a [povolit sadu Application Insights SDK](../app/asp-net.md).

> [!NOTE]
> Požadovaná oprávnění pro práci s řídicími panely jsou popsaná v článku o [porozumění řízení přístupu pro řídicí panely](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboard-share-access#understanding-access-control-for-dashboards).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Vytvoření nového řídicího panelu
Jeden řídicí panel může obsahovat prostředky z více aplikací, skupin prostředků a předplatných.  Tento kurz začněte vytvořením nového řídicího panelu pro vaši aplikaci.  

1. V podokně řídicí panel vyberte **nový řídicí panel**.

   ![Nový řídicí panel](media/tutorial-app-dashboards/1newdashboard.png)

1. Zadejte název pro řídicí panel.
1. Podívejte se do **Galerie dlaždic**, kde najdete celou řadu dlaždic, které můžete přidat do řídicího panelu.  Kromě přidávání dlaždic z galerie můžete připnout grafy a další zobrazení přímo z Application Insights na řídicí panel.
1. Vyhledejte dlaždici **Markdown** a přetáhněte ji na řídicí panel.  Tato dlaždice umožňuje přidat text formátovaný v Markdownu, který je ideální pro přidání popisného textu do řídicího panelu.
1. Přidejte text do vlastností dlaždice a změňte její velikost na plátně řídicího panelu.
    
    ![Úprava dlaždice Markdown](media/tutorial-app-dashboards/2dashboard-text.png)

1. V horní části obrazovky klikněte na **Hotovo přizpůsobení** a ukončete režim přizpůsobení dlaždice.

## <a name="add-health-overview"></a>Přidání přehledu o stavu
Řídicí panel se statickým textem není velice zajímavý, takže teď přidáte dlaždici z Application Insights zobrazíte informace o vaší aplikaci.  Dlaždice můžete z Application Insights přidat z Galerie dlaždic nebo je můžete připnout přímo z obrazovek Application Insights.  Díky tomu můžete konfigurovat grafy a zobrazení, která už znáte, ještě než je připnete na řídicí panel.  Přidejte nejdříve standardní přehled o stavu pro vaši aplikaci.  Není nutné nic konfigurovat a v řídicím panelu je možné provádět minimální přizpůsobení.


1. Vyberte prostředek **Application Insights** na domovské obrazovce.
2. V podokně **Přehled** klikněte na ![ ikonu Připnout ikonu připnutí ](media/tutorial-app-dashboards/pushpin.png) a přidejte dlaždici na poslední prohlížený řídicí panel.  
 
3. V pravém horním rohu se zobrazí oznámení o tom, že se dlaždice připnula na váš řídicí panel. Klikněte na tlačítko **připnuté na řídicí panel** v oznámení a vraťte se na řídicí panel nebo použijte podokno řídicí panel.
4. Tato dlaždice se teď přidá na řídicí panel. Chcete-li změnit umístění dlaždice, vyberte možnost **Upravit** . Klikněte na něj a přetáhněte ho na místo a pak klikněte na **hotové přizpůsobení**. Na řídicím panelu teď máte dlaždici s užitečnými informacemi.

    ![Řídicí panel s časovou osou přehledu](media/tutorial-app-dashboards/4dashboard-edit.png)

## <a name="add-custom-metric-chart"></a>Přidání grafu vlastní metriky
Panel **Metrika** umožňuje vykreslit v grafu metriku shromážděnou pomocí Application Insights v čase s volitelnými filtry a seskupením.  Podobně jako všechno ostatní ve službě Application Insights můžete tento graf přidat na řídicí panel.  Nejdřív je ale nutné ho trochu přizpůsobit.

1. Vyberte prostředek **Application Insights** na domovské obrazovce.
1. Vyberte **Metriky**.  
2. Už je vytvořený prázdný graf a vám se zobrazí výzva k přidání metriky.  Přidejte do grafu metriku a volitelně přidejte filtr a seskupení.  Následující příklad ukazuje počet žádostí na server seskupených podle úspěchu.  Díky tomu máte průběžné zobrazení úspěšných a neúspěšných žádostí.

    ![Přidání metriky](media/tutorial-app-dashboards/metrics.png)

4. Na pravé straně vyberte **Připnout na řídicí panel** . Tento postup přidá zobrazení na poslední řídicí panel, se kterým jste pracovali.

3.  V pravém horním rohu se zobrazí oznámení o tom, že se dlaždice připnula na váš řídicí panel. Kliknutím na **připnuté na řídicí panel** v oznámení se vraťte na řídicí panel nebo použijte okno řídicí panel.

4. Tato dlaždice se teď přidá na řídicí panel. Chcete-li změnit umístění dlaždice, vyberte možnost **Upravit** . Klikněte na něj a přetáhněte ho na místo a pak klikněte na **hotové přizpůsobení**.

## <a name="add-logs-analytics-query"></a>Přidat dotaz na protokoly (Analytics)
Protokoly Azure Application Insights (analýza) poskytují bohatý dotazovací jazyk, který umožňuje analyzovat všechna shromážděná data Application Insights. Stejně jako grafy a další zobrazení můžete přidat výstup dotazu protokolů do řídicího panelu.

Vzhledem k tomu, že Azure Application Insights log (Analytics) je samostatná služba, je nutné sdílet řídicí panel, aby obsahoval dotaz na protokoly. Když sdílíte řídicí panel Azure, publikujete ho jako prostředek Azure, který ho může zpřístupnit ostatním uživatelům a prostředkům.  

1. V horní části obrazovky řídicího panelu klikněte na **Sdílet**.

    ![Publikování řídicího panelu](media/tutorial-app-dashboards/8dashboard-share.png)

2. Zachovejte **Název řídicího panelu** a vyberte **Název předplatného**, abyste mohli řídicí panel sdílet.  Klikněte na **Publikovat**.  Řídicí panel je teď dostupný dalším službám a předplatným.  Volitelně můžete definovat konkrétní uživatele, kteří mají mít přístup k řídicímu panelu.
1. Vyberte prostředek **Application Insights** na domovské obrazovce.
2. Kliknutím na **protokoly (Analytics)** na levé straně v části sledování otevřete portál protokolů (Analytics).
3. Zadejte následující dotaz, který vrátí 10 nejžádanějších stránek a počet žádostí o ně:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Kliknutím na tlačítko **Spustit** ověříte výsledky dotazu.
5. Klikněte na ikonu připnutí ![Ikona špendlíku](media/tutorial-app-dashboards/pushpin.png) a vyberte název řídicího panelu. Důvod, proč tato možnost vybírá řídicí panel, na rozdíl od předchozích kroků, kde byl použit poslední řídicí panel, protože konzola protokolů (Analytics) je samostatná služba a je nutné vybrat ze všech dostupných sdílených řídicích panelů.

5. Než se vrátíte na řídicí panel, přidejte další dotaz, ale tentokrát ho vykreslete jako graf, abyste viděli různé způsoby, jak vizualizovat dotazy protokolů na řídicím panelu. Začněte s následujícím dotazem, který shrnuje 10 operací s největším množstvím výjimek.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Vyberte **Graf** a poté změňte typ vizualizace výstupu na **Prstencový**.

    ![Graf protokolů (Analytics)](media/tutorial-app-dashboards/11querychart.png)

6. Klikněte na ikonu připnutí ![Ikona špendlíku](media/tutorial-app-dashboards/pushpin.png) vpravo nahoře připněte graf na řídicí panel a tentokrát vyberte odkaz, který se má vrátit na řídicí panel.
4. Výsledky dotazů jsou teď přidané do řídicího panelu ve formátu, který jste vybrali.  Klikněte na jednotlivé pozice a přetáhněte je na všechny a pak klikněte na **hotové přizpůsobení**.
5. Vyberte ikonu tužky. ![Ikona tužky](media/tutorial-app-dashboards/pencil.png) u každého titulu dejte jim popisný název.

5. Vyberte **sdílet** a znovu publikujte změny na řídicím panelu, který teď obsahuje řadu grafů a vizualizací z Application Insights.


## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili vytvářet vlastní řídicí panely, podívejte se na zbytek dokumentace Application Insights včetně případové studie.

> [!div class="nextstepaction"]
> [Hloubková diagnostika](../app/devops.md)

