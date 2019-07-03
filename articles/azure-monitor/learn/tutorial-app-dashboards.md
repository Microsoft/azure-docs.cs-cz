---
title: Vytvoření vlastních řídicích panelů ve službě Azure Application Insights | Microsoft Docs
description: Kurz vytváření vlastních řídicích panelů klíčového ukazatele výkonu pomocí Azure Application Insights
keywords: ''
services: application-insights
author: lgayhardt
ms.author: lagayhar
ms.date: 07/3/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: fa91933c2243cd1a2737f93f796314330b6cf5a0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541449"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Vytváření vlastních řídicích panelů klíčového ukazatele výkonu pomocí Azure Application Insights

Můžete vytvořit více řídicích panelů na portálu Azure Portal, aby každý obsahoval dlaždice s vizualizací dat z více prostředků Azure v různých skupinách prostředků a předplatných.  Můžete připnout různé grafy a zobrazení z Azure Application Insights a vytvářet vlastní řídicí panely, které vám poskytnou úplný přehled o stavu a výkonu vaší aplikace. Tento kurz vás provede procesem vytvoření vlastního řídicího panelu, který bude obsahovat více typů dat a vizualizací z Azure Application Insights.  Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření vlastního řídicího panelu v Azure
> * Přidání dlaždice z Galerie dlaždic
> * Přidání standardní metriky v Application Insights na řídicí panel
> * Přidání grafu vlastní metriky v Application Insights na řídicí panel
> * Přidat na řídicí panel výsledků dotazu protokoly (analýza)



## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Nasadit aplikaci .NET do Azure a [povolit sadu Application Insights SDK](../../azure-monitor/app/asp-net.md).

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Vytvoření nového řídicího panelu
Jeden řídicí panel může obsahovat prostředky z více aplikací, skupin prostředků a předplatných.  Tento kurz začněte vytvořením nového řídicího panelu pro vaši aplikaci.  

1. V podokně řídicí panel, vyberte **nový řídicí panel**.

   ![Nový řídicí panel](media/tutorial-app-dashboards/1newdashboard.png)

1. Zadejte název pro řídicí panel.
1. Podívejte se do **Galerie dlaždic**, kde najdete celou řadu dlaždic, které můžete přidat do řídicího panelu.  Kromě přidávání dlaždic z galerie, můžete připínat grafy a jiná zobrazení přímo z Application Insights na řídicí panel.
1. Vyhledejte dlaždici **Markdown** a přetáhněte ji na řídicí panel.  Tato dlaždice vám umožňuje přidat text ve formátu markdown, která je ideální pro přidávání popisného textu do řídicího panelu.
1. Přidejte text do vlastností dlaždice a změňte její velikost na plátně řídicího panelu.
    
    ![Úprava dlaždice Markdown](media/tutorial-app-dashboards/2dashboard-text.png)

1. Klikněte na tlačítko **přizpůsobení dokončeno** v horní části obrazovky, chcete-li ukončit režim přizpůsobení dlaždice.

## <a name="add-health-overview"></a>Přidání přehledu o stavu
Řídicí panel se statickým textem není moc zajímavý, takže teď přidáte dlaždici z Application Insights a zobrazit informace o vaší aplikaci.  Dlaždice můžete z Application Insights přidat z Galerie dlaždic nebo je můžete připnout přímo z obrazovek Application Insights.  Díky tomu můžete konfigurovat grafy a zobrazení, která už znáte, ještě než je připnete na řídicí panel.  Přidejte nejdříve standardní přehled o stavu pro vaši aplikaci.  Není nutné nic konfigurovat a v řídicím panelu je možné provádět minimální přizpůsobení.


1. Vyberte vaše **Application Insights** prostředků na domovské obrazovce.
2. V **přehled** podokně klikněte na ikonu připínáčku ![ikonu připínáčku](media/tutorial-app-dashboards/pushpin.png) a přidejte tak dlaždici na poslední řídicí panel, který byl zobrazený.  
 
3. V pravém horním rohu se zobrazí oznámení, že byla dlaždice připnout na řídicí panel. Klikněte na tlačítko **Připnuto na řídicí panel** v oznámení vrátit na řídicí panel nebo pomocí podokna řídicího panelu.
4. Tuto dlaždici je teď přidaná na řídicí panel. Vyberte **upravit** Chcete-li změnit umístění dlaždice. Klikněte na tlačítko a přetáhněte it na místo a pak klikněte na tlačítko **přizpůsobení dokončeno**. Na řídicím panelu teď máte dlaždici s užitečnými informacemi.

    ![Řídicí panel s časovou osou přehledu](media/tutorial-app-dashboards/4dashboard-edit.png)

## <a name="add-custom-metric-chart"></a>Přidání grafu vlastní metriky
Panel **Metrika** umožňuje vykreslit v grafu metriku shromážděnou pomocí Application Insights v čase s volitelnými filtry a seskupením.  Podobně jako všechno ostatní ve službě Application Insights můžete tento graf přidat na řídicí panel.  Nejdřív je ale nutné ho trochu přizpůsobit.

1. Vyberte vaše **Application Insights** prostředků na domovské obrazovce.
1. Vyberte **Metriky**.  
2. Už je vytvořený prázdný graf a vám se zobrazí výzva k přidání metriky.  Přidejte do grafu metriku a volitelně přidejte filtr a seskupení.  Následující příklad ukazuje počet žádostí na server seskupených podle úspěchu.  Díky tomu máte průběžné zobrazení úspěšných a neúspěšných žádostí.

    ![Přidání metriky](media/tutorial-app-dashboards/metrics.png)

4. Vyberte **připnout na řídicí panel** na pravé straně. Tento postup přidá zobrazení na poslední řídicí panel, se kterým jste pracovali.

3.  V pravém horním rohu se zobrazí oznámení, že byla dlaždice připnout na řídicí panel. Klikněte na tlačítko **Připnuto na řídicí panel** v oznámení vrátit na řídicí panel nebo použít okno řídicí panel.

4. Tuto dlaždici je teď přidaná na řídicí panel. Vyberte **upravit** Chcete-li změnit umístění dlaždice. Klikněte na tlačítko a přetáhněte it na místo a pak klikněte na tlačítko **přizpůsobení dokončeno**.

## <a name="add-logs-analytics-query"></a>Přidat dotaz protokoly (analýza)
Azure Application Insights protokoly (Analytics) poskytuje Bohatý dotazovací jazyk umožňující analýzu všech dat shromážděných službou Application Insights. Stejně jako grafy a jiná zobrazení můžete přidat výstup dotazu protokoly na řídicí panel.

Protože Azure aplikace Insights protokoly (Analytics) je samostatná služba, budete muset sdílet tento řídicí panel, aby zahrnoval dotaz protokoly. Když sdílíte řídicí panel Azure, publikujete ho jako prostředek Azure, která ho může zpřístupnit pro ostatní uživatele a prostředky.  

1. V horní části obrazovky řídicího panelu klikněte na **Sdílet**.

    ![Publikování řídicího panelu](media/tutorial-app-dashboards/8dashboard-share.png)

2. Zachovejte **Název řídicího panelu** a vyberte **Název předplatného**, abyste mohli řídicí panel sdílet.  Klikněte na tlačítko **publikovat**.  Řídicí panel je teď dostupný dalším službám a předplatným.  Volitelně můžete definovat konkrétní uživatele, kteří mají mít přístup k řídicímu panelu.
1. Vyberte vaše **Application Insights** prostředků na domovské obrazovce.
2. Klikněte na tlačítko **protokoly (Analytics)** na levé straně v části monitorování se otevře portál protokoly (Analytics).
3. Zadejte následující dotaz, který vrátí 10 nejžádanějších stránek a počet žádostí o ně:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Klikněte na tlačítko **spustit** na ověřování výsledků dotazu.
5. Kliknutím na ikonu připínáčku ![Ikona Připnutí](media/tutorial-app-dashboards/pushpin.png) a vyberte název řídicího panelu. Z důvodu, že tuto možnost můžete vybrat nějaký řídicí panel na rozdíl od předchozích kroků, kdy byl použit poslední řídicí panel má totiž konzole protokoly (Analytics) je samostatná služba a potřebuje si vybrat ze všech dostupných sdílených řídicích panelů.

5. Než přejdete zpět na řídicí panel, přidejte další dotaz, ale tentokrát ho vykreslete jako graf tak, že se zobrazí různé způsoby vizualizace dotazu protokoly na řídicím panelu. Začněte s následujícím dotazem, který shrnuje 10 operací s největším množstvím výjimek.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Vyberte **Graf** a poté změňte typ vizualizace výstupu na **Prstencový**.

    ![Protokoly (Analytics) grafu](media/tutorial-app-dashboards/11querychart.png)

6. Kliknutím na ikonu připínáčku ![Ikona Připnutí](media/tutorial-app-dashboards/pushpin.png) kód PIN vpravo nahoře graf na řídicí panel a tentokrát vyberte odkaz k návratu do řídicího panelu.
4. Výsledky dotazů jsou teď přidané do řídicího panelu ve formátu, který jste vybrali.  Klikněte a přetáhněte každou na místo a pak na **přizpůsobení dokončeno**.
5. Vyberte ikonu tužky ![Ikona tužky](media/tutorial-app-dashboards/pencil.png) v nadpisu každé jim dát popisný název.

5. Vyberte **sdílené složky** znovu publikovat provedené změny do řídicího panelu, který teď zahrnuje celou řadu grafů a vizualizací z Application Insights.


## <a name="next-steps"></a>Další postup
Teď, když jste se naučili vytvářet vlastní řídicí panely, podívejte se na zbytek dokumentace Application Insights včetně případové studie.

> [!div class="nextstepaction"]
> [Hloubková diagnostika](../../azure-monitor/app/devops.md)
