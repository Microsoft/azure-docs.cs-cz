---
title: Vytváření a sdílení řídicích panelů s daty Azure Log Analytics | Microsoft Docs
description: Tento kurz vám pomůže porozumět způsobu, jakým řídicí panely Log Analytics vizualizují všechna vaše uložená prohledávání protokolů a poskytují jednotný přehled vašeho prostředí.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: magoedte
ms.custom: mvc
ms.component: ''
ms.openlocfilehash: 8ab6107279fe546205605b24da53073f49c7a204
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014444"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Vytváření a sdílená řídicích panelů s daty Log Analytics

Řídicí panely Log Analytics dokáží vizualizovat všechna vaše uložená prohledávání protokolů a poskytují možnost prohledávat, korelovat a sdílet provozní data IT v rámci organizace.  Tento kurz se zabývá vytvořením prohledávání protokolu, které se použije jako základ sdíleného řídicího panelu, ke kterému bude mít přístup váš tým podpory pro provoz IT.  Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření sdíleného řídicího panelu na webu Azure Portal
> * Vizualizace prohledávání protokolu výkonu 
> * Přidání prohledávání protokolu na sdílený řídicí panel 
> * Přizpůsobení dlaždice na sdíleném řídicím panelu

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač [připojený k pracovnímu prostoru Log Analytics](log-analytics-quick-collect-azurevm.md).  
 
## <a name="log-in-to-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Vytvoření sdíleného řídicího panelu

Po přihlášení k webu Microsoft Azure Portal jako první uvidíte [řídicí panel](../azure-portal/azure-portal-dashboards.md).<br> ![Řídicí panel Azure](media/log-analytics-tutorial-dashboards/log-analytics-portal-dashboard.png)

Tady můžete kombinovat provozní data všech prostředků Azure, která jsou nejdůležitější pro IT, včetně telemetrie z Azure Log Analytics.  Než se pustíme do vizualizace prohledávání protokolu, vytvoříme nejprve řídicí panel a nastavíme jeho sdílení.  To nám umožní dát řídicí panel stranou, dokud na něj nepřidáme příklad prohledávání protokolu výkonu, které se vykreslí jako spojnicový graf.  

Řídicí panel vytvoříte výběrem tlačítka **Nový řídicí panel** vedle názvu aktuálního řídicího panelu.<br> ![Vytvoření nového řídicího panelu na webu Azure Portal](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-01.png)

Tato akce vytvoří nový, prázdný, privátní řídicí panel a přepne vás do režimu přizpůsobení, ve kterém můžete řídicí panel pojmenovat a přidat na něj dlaždice nebo změnit jejich uspořádání. Upravte název řídicího panelu, pro účely tohoto rychlého startu zadejte *Ukázkový řídicí panel*, a pak vyberte **Přizpůsobení dokončeno**.<br><br> ![Uložení přizpůsobeného řídicího panelu Azure](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-02.png)

Když vytvoříte řídicí panel, ve výchozím nastavení je privátní. To znamená, že se zobrazí pouze vám. Pokud ho chcete zpřístupnit ostatním, použijte tlačítko **Sdílet**, které se zobrazí vedle dalších příkazů řídicího panelu.<br> ![Sdílení nového řídicího panelu na webu Azure Portal](media/log-analytics-tutorial-dashboards/log-analytics-share-dashboard.png) 

Zobrazí se výzva k výběru předplatného a skupiny prostředků, do kterých se má řídicí panel publikovat. Pro usnadnění práce vás prostředí pro publikování na portálu navede k umístění řídicích panelů do skupiny prostředků **dashboards** (řídicí panely).  Ověřte vybrané předplatné a pak klikněte na **Publikovat**.  Přístup k informacím zobrazeným na řídicím panelu se řídí pomocí [řízení přístupu na základě prostředku Azure](../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-search"></a>Vizualizace prohledávání protokolu

Na webu Azure Portal můžete vytvářet základní dotazy na jeden řádek z portálu pro prohledávání protokolů. Portál pro prohledávání protokolů můžete použít, aniž byste otevírali externí portál, a můžete pomocí něj provádět řadu funkcí s prohledáváními protokolů, včetně vytváření pravidel upozornění, vytváření skupin počítačů a exportování výsledků dotazu. 

[Portál pro Log Analytics](../log-analytics/query-language/get-started-analytics-portal.md) je vyhrazený portál poskytující pokročilé funkce, které na portálu pro prohledávání protokolů nejsou k dispozici. Mezi tyto funkce patří možnost upravovat dotazy na několik řádků, selektivní spouštění kódu, funkce IntelliSense závislá na kontextu a inteligentní analýzy. Na portálu pro pokročilou analýzu vytvoříte zobrazení výkonu v grafické podobě, uložíte ho pro budoucí hledání a připnete ho na sdílený řídicí panel vytvořený dříve.   

Portál pro pokročilou analýzu otevřete přes odkaz na portálu pro prohledávání protokolů.<br> ![Otevření portálu pro pokročilou analýzu](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-01.png)

Na portálu pro pokročilou analýzu zadejte následující dotaz, který vrátí pouze záznamy o využití procesoru pro počítače s Windows i Linuxem seskupené podle hodnot Computer a TimeGenerated a zobrazí je ve vizuálním grafu:

```
Perf | where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1m), Computer | render timechart
```

Uložte dotaz výběrem tlačítka **Uložit dotaz** v pravém horním rohu.<br> ![Uložení dotazu na portálu pro pokročilou analýzu](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-02.png)<br><br> Na ovládacím panelu **Uložit dotaz** zadejte název, například *Virtuální počítače Azure – využití procesoru*, a klikněte na **Uložit**.  Tímto způsobem můžete vytvořit knihovnu běžných dotazů pro hledání nebo je upravovat, aniž byste je museli celé přepisovat.  Nakonec tento graf připněte na sdílený řídicí panel vytvořený dříve, a to výběrem tlačítka **Připnout graf na řídicí panel Azure** v pravém středním rohu stránky.  

Když teď máte dotaz připnutý na řídicím panelu, všimněte si, že je pod ním uvedený obecný název a komentář.<br> ![Ukázka řídicího panelu Azure](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-01.png)<br><br>  Měli bychom změnit název na něco smysluplného, co snadno pochopí uživatelé, kterým se zobrazí.  Klikněte pravým tlačítkem na dlaždici a vyberte **Upravit název**.  Jakmile budete hotovi s úpravou názvu a podnadpisu dlaždice, klikněte na **Aktualizovat**.  Zobrazí se banner s výzvou k publikování nebo zahození změn.  Klikněte na **Publikovat změny** a pak zavřete ovládací panel **Upravit název**.  

![Dokončená konfigurace ukázkového řídicího panelu](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak vytvořit řídicí panel na webu Azure Portal a přidat na něj prohledávání protokolu.  V dalším kurzu se seznámíte s různými reakcemi, které můžete implementovat na základě výsledků prohledávání protokolu.  

> [!div class="nextstepaction"]
> [Reakce na události s využitím upozornění Log Analytics](log-analytics-tutorial-response.md)
