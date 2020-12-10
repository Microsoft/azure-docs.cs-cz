---
title: Vytvoření vlastních řídicích panelů ve službě Azure Application Insights | Microsoft Docs
description: Kurz vytváření vlastních řídicích panelů klíčového ukazatele výkonu pomocí Azure Application Insights
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/30/2020
ms.custom: mvc, contperf-fy21q1
ms.openlocfilehash: 701480300101b019830c57d9aa000534fa63bb6a
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028525"
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

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Nasadit do Azure aplikaci .NET a [povolit sadu Application Insights SDK](../app/asp-net.md).

> [!NOTE]
> Požadovaná oprávnění pro práci s řídicími panely jsou popsaná v článku o [porozumění řízení přístupu pro řídicí panely](../../azure-portal/azure-portal-dashboard-share-access.md#understanding-access-control-for-dashboards).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Vytvoření nového řídicího panelu

> [!WARNING]
> Pokud přesunete prostředek Application Insights do jiné skupiny prostředků nebo předplatného, budete muset ručně aktualizovat řídicí panel tak, že odeberete staré dlaždice a připnete nové dlaždice ze stejného Application Insights prostředku na nové místo.

Jeden řídicí panel může obsahovat prostředky z více aplikací, skupin prostředků a předplatných.  Tento kurz začněte vytvořením nového řídicího panelu pro vaši aplikaci.  

1. V rozevírací nabídce na levé straně Azure Portal vyberte možnost **řídicí panel**.

    ![Rozevírací seznam nabídky portálu Azure Portal](media/tutorial-app-dashboards/dashboard-from-menu.png)

2. V podokně řídicí panel vyberte **nový řídicí** panel a pak **prázdný řídicí panel**.

   ![Nový řídicí panel](media/tutorial-app-dashboards/new-dashboard.png)

3. Zadejte název pro řídicí panel.
4. Podívejte se do **Galerie dlaždic**, kde najdete celou řadu dlaždic, které můžete přidat do řídicího panelu.  Kromě přidávání dlaždic z galerie můžete připnout grafy a další zobrazení přímo z Application Insights na řídicí panel.
5. Vyhledejte dlaždici **Markdown** a přetáhněte ji na řídicí panel.  Tato dlaždice umožňuje přidat text formátovaný v Markdownu, který je ideální pro přidání popisného textu do řídicího panelu. Další informace najdete v tématu [použití dlaždice Markdownu na řídicích panelech Azure k zobrazení vlastního obsahu](../../azure-portal/azure-portal-markdown-tile.md).
6. Přidejte text do vlastností dlaždice a změňte její velikost na plátně řídicího panelu.

    [![Úprava dlaždice Markdown](media/tutorial-app-dashboards/markdown.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

7. Vyberte **Hotovo přizpůsobení** v horní části obrazovky a ukončete režim přizpůsobení dlaždice.

## <a name="add-health-overview"></a>Přidání přehledu o stavu

Řídicí panel se statickým textem není velice zajímavý, takže teď přidáte dlaždici z Application Insights zobrazíte informace o vaší aplikaci. Dlaždice můžete z Application Insights přidat z Galerie dlaždic nebo je můžete připnout přímo z obrazovek Application Insights. Díky tomu můžete konfigurovat grafy a zobrazení, která už znáte, ještě než je připnete na řídicí panel.  Přidejte nejdříve standardní přehled o stavu pro vaši aplikaci.  Není nutné nic konfigurovat a v řídicím panelu je možné provádět minimální přizpůsobení.


1. Vyberte prostředek **Application Insights** na domovské obrazovce.
2. V podokně **Přehled** vyberte ![ ikonu připnutí ikony připnutí ](media/tutorial-app-dashboards/pushpin.png) a přidejte tak dlaždici na řídicí panel.
3. Na kartě připnout na řídicí panel vyberte, na který řídicí panel se má dlaždice přidat, nebo vytvořte novou.
 
3. V pravém horním rohu se zobrazí oznámení o tom, že se dlaždice připnula na váš řídicí panel.  Vyberte **připnuté na řídicí panel** v oznámení a vraťte se na řídicí panel nebo použijte podokno řídicí panel.
4. Tato dlaždice se teď přidá na řídicí panel. Chcete-li změnit umístění dlaždice, vyberte možnost **Upravit** . Vyberte ho a přetáhněte ho na místo a pak vyberte **hotové přizpůsobení**. Na řídicím panelu teď máte dlaždici s užitečnými informacemi.

    [![Řídicí panel v režimu úprav](media/tutorial-app-dashboards/dashboard-edit-mode.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

## <a name="add-custom-metric-chart"></a>Přidání grafu vlastní metriky

Panel **Metrika** umožňuje vykreslit v grafu metriku shromážděnou pomocí Application Insights v čase s volitelnými filtry a seskupením.  Podobně jako všechno ostatní ve službě Application Insights můžete tento graf přidat na řídicí panel.  Nejdřív je ale nutné ho trochu přizpůsobit.

1. Vyberte prostředek **Application Insights** na domovské obrazovce.
1. Vyberte **Metriky**.  
2. Už je vytvořený prázdný graf a vám se zobrazí výzva k přidání metriky.  Přidejte do grafu metriku a volitelně přidejte filtr a seskupení.  Následující příklad ukazuje počet žádostí na server seskupených podle úspěchu.  Díky tomu máte průběžné zobrazení úspěšných a neúspěšných žádostí.

    [![Přidání metriky](media/tutorial-app-dashboards/metrics.png)](media/tutorial-app-dashboards/metrics.png#lightbox)

4. Na pravé straně vyberte **Připnout na řídicí panel** .

3.  V pravém horním rohu se zobrazí oznámení o tom, že se dlaždice připnula na váš řídicí panel. Vyberte **připnuté na řídicí panel** v oznámení a vraťte se na řídicí panel nebo použijte kartu Řídicí panel.

4. Tato dlaždice se teď přidá na řídicí panel. Chcete-li změnit umístění dlaždice, vyberte možnost **Upravit** . Vyberte a přetáhněte dlaždici na pozici a pak vyberte **hotové přizpůsobení**.

## <a name="add-logs-query"></a>Přidat dotaz na protokoly

Protokoly Azure Application Insights poskytují bohatý dotazovací jazyk, který umožňuje analyzovat všechna shromážděná data Application Insights. Stejně jako grafy a další zobrazení můžete přidat výstup dotazu protokolů do řídicího panelu.

1. Vyberte prostředek **Application Insights** na domovské obrazovce.
2. Vyberte **protokoly** vlevo v části monitorování a otevřete kartu protokoly.
3. Zadejte následující dotaz, který vrátí 10 nejžádanějších stránek a počet žádostí o ně:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Vyberte možnost **Spustit** a ověřte výsledky dotazu.
5. Výběrem ikony připnutí ![Ikona špendlíku](media/tutorial-app-dashboards/pushpin.png) a vyberte název řídicího panelu.

5. Než se vrátíte na řídicí panel, přidejte další dotaz, ale vykreslete ho jako graf, abyste viděli různé způsoby vizualizace dotazů protokolů na řídicím panelu. Začněte s následujícím dotazem, který shrnuje 10 operací s největším množstvím výjimek.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Vyberte **Graf** a poté změňte typ vizualizace výstupu na **Prstencový**.

    [![Prstencový graf s výše uvedeným dotazem](media/tutorial-app-dashboards/logs-doughnut.png)](media/tutorial-app-dashboards/logs-doughnut.png#lightbox)

6. Výběrem ikony připnutí ![Ikona špendlíku](media/tutorial-app-dashboards/pushpin.png) v pravém horním rohu připněte graf na řídicí panel a pak se vraťte na řídicí panel.
7. Výsledky dotazů jsou teď přidané do řídicího panelu ve formátu, který jste vybrali. Vyberte a přetáhněte jednotlivé jednotlivé pozice a potom vyberte **hotové přizpůsobení**.
8. Vyberte ikonu tužky. ![Ikona tužky](media/tutorial-app-dashboards/pencil.png) u každého titulu dejte jim popisný název.

## <a name="share-dashboard"></a>Sdílet řídicí panel

1. V horní části řídicího panelu vyberte **sdílet** , aby se změny publikovaly.
2. Volitelně můžete definovat konkrétní uživatele, kteří mají mít přístup k řídicímu panelu. Další informace najdete v tématu [sdílení řídicích panelů Azure pomocí řízení přístupu na základě role v Azure](../../azure-portal/azure-portal-dashboard-share-access.md).
3. Vyberte **Publikovat**.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili vytvářet vlastní řídicí panely, podívejte se na zbytek dokumentace Application Insights včetně případové studie.

> [!div class="nextstepaction"]
> [Hloubková diagnostika](../app/devops.md)
