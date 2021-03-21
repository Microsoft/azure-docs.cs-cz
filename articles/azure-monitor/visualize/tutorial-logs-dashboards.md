---
title: Vytváření a sdílení řídicích panelů s daty Azure Log Analytics | Microsoft Docs
description: V tomto kurzu se seznámíte s tím, jak Log Analytics řídicí panely mohou vizualizovat všechny vaše uložené dotazy v protokolu, což vám umožní zobrazit vaše prostředí jediným objektivem.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 05/28/2020
ms.custom: mvc
ms.openlocfilehash: 84c710e44fbbccdefd5bf811477e1fa4c3989114
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043452"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Vytváření a sdílená řídicích panelů s daty Log Analytics

Řídicí panely Log Analytics mohou vizualizovat všechny uložené dotazy protokolu, což vám umožní najít, sladit a sdílet provozní data IT v organizaci.  Tento kurz se zabývá vytvořením dotazu protokolu, který se použije k podpoře sdíleného řídicího panelu, ke kterému se bude přihlašovat tým podpory IT.  Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření sdíleného řídicího panelu na webu Azure Portal
> * Vizualizace dotazu protokolu výkonu 
> * Přidání dotazu protokolu na sdílený řídicí panel 
> * Přizpůsobení dlaždice na sdíleném řídicím panelu

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač [připojený k pracovnímu prostoru služby Log Analytics](../vm/quick-collect-azurevm.md).  
 
## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Vytvoření sdíleného řídicího panelu
Výběrem **řídicího panelu** otevřete výchozí [řídicí panel](../../azure-portal/azure-portal-dashboards.md). Řídicí panel bude vypadat jinak než v příkladu níže.

![Řídicí panel webu Azure Portal](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Tady můžete kombinovat provozní data všech prostředků Azure, která jsou nejdůležitější pro IT, včetně telemetrie z Azure Log Analytics.  Než se podíváme na vizualizaci dotazu protokolu, nejdřív vytvoříme řídicí panel a nasdílíme ho.  Pak se můžeme zaměřit na náš ukázkový dotaz protokolu výkonu, který se vykreslí jako spojnicový graf a přidá se na řídicí panel.  

> [!NOTE]
> V řídicích panelech Azure se pomocí dotazů protokolu podporují tyto typy grafů:
> - areachart
> - columnchart
> - piechart (vykreslí se na řídicím panelu jako prstenec)
> - scatterchart
> - timechart

Řídicí panel vytvoříte výběrem tlačítka **Nový řídicí panel** vedle názvu aktuálního řídicího panelu.

![Vytvoření nového řídicího panelu na webu Azure Portal](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Tato akce vytvoří nový, prázdný, privátní řídicí panel a přepne vás do režimu přizpůsobení, ve kterém můžete řídicí panel pojmenovat a přidat na něj dlaždice nebo změnit jejich uspořádání. Upravte název řídicího panelu a pro tento kurz zadejte *ukázkový řídicí panel* a pak vyberte **hotové přizpůsobení**.<br><br> ![Uložení přizpůsobeného řídicího panelu Azure](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Když vytvoříte řídicí panel, ve výchozím nastavení je privátní. To znamená, že se zobrazí pouze vám. Pokud ho chcete zpřístupnit ostatním, použijte tlačítko **Sdílet**, které se zobrazí vedle dalších příkazů řídicího panelu.

![Sdílení nového řídicího panelu na webu Azure Portal](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

Zobrazí se výzva k výběru předplatného a skupiny prostředků, do kterých se má řídicí panel publikovat. Pro usnadnění práce vás prostředí pro publikování na portálu navede k umístění řídicích panelů do skupiny prostředků **dashboards** (řídicí panely).  Ověřte vybrané předplatné a pak klikněte na **Publikovat**.  Přístup k informacím zobrazeným na řídicím panelu se řídí pomocí [řízení přístupu na základě role Azure (RBAC)](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-query"></a>Vizualizace dotazu protokolu
[Log Analytics](../logs/log-analytics-tutorial.md) je vyhrazený portál, který se používá pro práci s dotazy protokolů a jejich výsledky. Mezi tyto funkce patří možnost upravovat dotazy na několik řádků, selektivní spouštění kódu, funkce IntelliSense závislá na kontextu a inteligentní analýzy. V tomto kurzu použijete Log Analytics k vytvoření zobrazení výkonu v grafické podobě, uložíte ho pro budoucí dotaz a připnete ho na sdílený řídicí panel, který jste vytvořili dříve.

Otevřete Log Analytics výběrem **protokolů** v nabídce Azure monitor. Začíná novým prázdným dotazem.

![Domovská stránka](media/tutorial-logs-dashboards/homepage.png)

Zadejte následující dotaz, který vrátí záznamy o využití procesoru pro počítače se systémem Windows i Linux, seskupené podle počítačů a TimeGenerated a zobrazí se ve vizuálním grafu. Kliknutím na tlačítko **Spustit** spusťte dotaz a zobrazíte výsledný graf.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Uložte dotaz výběrem tlačítka **Uložit** v horní části stránky.

![Uložit dotaz](media/tutorial-logs-dashboards/save-query.png)

V ovládacím panelu **Uložit dotaz** zadejte název, například *virtuální počítače Azure – využití procesoru* , a kategorii, jako jsou *řídicí panely* , a pak klikněte na **Uložit**.  Tímto způsobem můžete vytvořit knihovnu běžných dotazů, které můžete použít a upravit.  Nakonec připnout na sdílený řídicí panel, který jste vytvořili dříve, tak, že vyberete tlačítko **Připnout na řídicí panel** v pravém horním rohu stránky a pak vyberete název řídicího panelu.

Když teď máte dotaz připnutý na řídicím panelu, všimněte si, že je pod ním uvedený obecný název a komentář.

![Ukázka řídicího panelu Azure](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Měli bychom změnit název na něco smysluplného, co snadno pochopí uživatelé, kterým se zobrazí.  Kliknutím na tlačítko Upravit upravíte název a podnadpis dlaždice a kliknete na **aktualizovat**.  Zobrazí se banner s výzvou k publikování nebo zahození změn.  Klikněte na **Uložit kopii**.  

![Dokončená konfigurace ukázkového řídicího panelu](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak vytvořit řídicí panel v Azure Portal a přidat do něj dotaz protokolu.  Přejděte k dalšímu kurzu, kde se seznámíte s různými odpověďmi, které můžete implementovat, na základě výsledků dotazu protokolu.  

> [!div class="nextstepaction"]
> [Reakce na události s využitím upozornění Log Analytics](../alerts/tutorial-response.md)