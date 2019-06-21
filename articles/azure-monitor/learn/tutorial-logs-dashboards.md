---
title: Vytváření a sdílení řídicích panelů s daty Azure Log Analytics | Microsoft Docs
description: Tento kurz vám pomůže porozumět jak řídicí panely Log Analytics dokáží vizualizovat všechna vaše dotazy uložený protokol získáte jednotný přehled vašeho prostředí.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 93cda8680bc665055d449e86c24d6565f6fc525f
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296414"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Vytváření a sdílená řídicích panelů s daty Log Analytics

Log Analytics řídicích panelů můžete vizualizovat všechna vaše dotazy uložený protokol poskytují možnost prohledávat, korelovat a sdílet provozní data IT v organizaci.  Tento kurz se zabývá vytvořením dotazu protokolu, který se použije pro podporu sdíleného řídicího panelu, které budou mít přístup váš tým podpory pro provoz IT.  Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření sdíleného řídicího panelu na webu Azure Portal
> * Vizualizujte dotaz protokolu výkonu 
> * Přidat dotaz protokolu na sdílený řídicí panel 
> * Přizpůsobení dlaždice na sdíleném řídicím panelu

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač [připojený k pracovnímu prostoru služby Log Analytics](quick-collect-azurevm.md).  
 
## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Vytvoření sdíleného řídicího panelu
Vyberte **řídicí panel** otevřít výchozí [řídicí panel](../../azure-portal/azure-portal-dashboards.md). Řídicí panel bude vypadat jinak než v příkladu níže.

![Řídicí panel Azure](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Tady můžete kombinovat provozní data všech prostředků Azure, která jsou nejdůležitější pro IT, včetně telemetrie z Azure Log Analytics.  Předtím, než se pustíme do vizualizace protokolu dotazu, Pojďme nejdříve vytvořit řídicí panel a sdílet je.  Pak můžeme zaměřit na náš příklad výkonu protokolu dotaz, který se vykreslí jako spojnicový graf a přidat na řídicí panel.  

Řídicí panel vytvoříte výběrem tlačítka **Nový řídicí panel** vedle názvu aktuálního řídicího panelu.

![Vytvoření nového řídicího panelu na webu Azure portal](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Tato akce vytvoří nový, prázdný, privátní řídicí panel a přepne vás do režimu přizpůsobení, ve kterém můžete řídicí panel pojmenovat a přidat na něj dlaždice nebo změnit jejich uspořádání. Upravte název řídicího panelu a určete *ukázkový řídicí panel* pro tento kurz a pak vyberte **přizpůsobení dokončeno**.<br><br> ![Uložení přizpůsobeného řídicího panelu Azure](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Když vytvoříte řídicí panel, ve výchozím nastavení je privátní. To znamená, že se zobrazí pouze vám. Pokud ho chcete zpřístupnit ostatním, použijte tlačítko **Sdílet**, které se zobrazí vedle dalších příkazů řídicího panelu.

![Sdílení nového řídicího panelu na webu Azure portal](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

Zobrazí se výzva k výběru předplatného a skupiny prostředků, do kterých se má řídicí panel publikovat. Pro usnadnění práce vás prostředí pro publikování na portálu navede k umístění řídicích panelů do skupiny prostředků **dashboards** (řídicí panely).  Ověřte vybrané předplatné a pak klikněte na **Publikovat**.  Přístup k informacím zobrazeným na řídicím panelu se řídí pomocí [řízení přístupu na základě prostředku Azure](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-query"></a>Vizualizace protokolů dotazu
[Log Analytics](../log-query/get-started-portal.md) je vyhrazený portál používané k práci s dotazy log a jejich výsledky. Mezi tyto funkce patří možnost upravovat dotazy na několik řádků, selektivní spouštění kódu, funkce IntelliSense závislá na kontextu a inteligentní analýzy. V tomto kurzu použijete Log Analytics pro vytvoření zobrazení výkonu v grafické podobě, uložte jej pro budoucí dotazu a připněte sdílený řídicí panel vytvořený dříve.

Výběrem otevřete Log Analytics **protokoly** v nabídce Azure Monitor. Spustí se nový prázdný dotaz.

![Domovská stránka](media/tutorial-logs-dashboards/homepage.png)

Zadejte následující dotaz, který vrací procesoru záznamy o využití pro počítače s Windows i Linuxem, seskupené podle hodnot Computer a TimeGenerated a zobrazí ve vizuálním grafu. Klikněte na tlačítko **spustit** ke spuštění dotazu a zobrazení Výsledný graf.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Uložte dotaz výběrem **Uložit** tlačítko v horní části stránky.

![Uložit dotaz](media/tutorial-logs-dashboards/save-query.png)

V **uložit dotaz** ovládací panely, zadejte název, jako *virtuální počítače Azure – využití procesoru* a kategorie, jako *řídicí panely* a potom klikněte na tlačítko **uložit** .  Tímto způsobem můžete vytvořit knihovnu běžných dotazů, které můžete použít a upravit.  Nakonec to připněte na sdílený řídicí panel vytvořený dříve tak, že vyberete **Pin** tlačítko v pravém horním rohu stránky a pak vyberete název řídicího panelu.

Když teď máte dotaz připnutý na řídicím panelu, všimněte si, že je pod ním uvedený obecný název a komentář.

![Ukázka řídicího panelu Azure](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Měli bychom změnit název na něco smysluplného, co snadno pochopí uživatelé, kterým se zobrazí.  Klikněte na tlačítko Upravit přizpůsobení názvu a podnadpisu dlaždice, a potom klikněte na **aktualizace**.  Zobrazí se banner s výzvou k publikování nebo zahození změn.  Klikněte na tlačítko **uložit kopii**.  

![Dokončená konfigurace ukázkového řídicího panelu](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak vytvořit řídicí panel na webu Azure Portal a přidat dotaz protokolu k němu.  Pokračujte k dalšímu kurzu, kde se seznámíte s různými reakcemi, které můžete implementovat podle výsledků dotazu protokolu.  

> [!div class="nextstepaction"]
> [Reakce na události s využitím upozornění Log Analytics](tutorial-response.md)
