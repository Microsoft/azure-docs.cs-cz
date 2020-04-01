---
title: Vytváření a sdílení řídicích panelů s daty Azure Log Analytics | Microsoft Docs
description: Tento kurz vám pomůže pochopit, jak řídicí panely Log Analytics můžete vizualizovat všechny uložené dotazy protokolu, což vám jeden objektiv pro zobrazení vašeho prostředí.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.custom: mvc
ms.openlocfilehash: 76ba79561df4a75004369d24c4c6af82de9b1cfc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77661528"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Vytváření a sdílená řídicích panelů s daty Log Analytics

Řídicí panely Log Analytics mohou vizualizovat všechny uložené dotazy protokolu, což vám umožní najít, korelovat a sdílet provozní data IT v organizaci.  Tento kurz se zabývá vytvořením dotazu protokolu, který bude použit pro podporu sdíleného řídicího panelu, ke kterému bude mít přístup tým podpory operací IT.  Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření sdíleného řídicího panelu na webu Azure Portal
> * Vizualizace dotazu protokolu výkonu 
> * Přidání dotazu protokolu na sdílený řídicí panel 
> * Přizpůsobení dlaždice na sdíleném řídicím panelu

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač [připojený k pracovnímu prostoru služby Log Analytics](quick-collect-azurevm.md).  
 
## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese . 

## <a name="create-a-shared-dashboard"></a>Vytvoření sdíleného řídicího panelu
Výběrem **možnosti Řídicí panel** otevřete výchozí [řídicí panel](../../azure-portal/azure-portal-dashboards.md). Řídicí panel bude vypadat jinak než v níže uvedeném příkladu.

![Řídicí panel webu Azure Portal](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Tady můžete kombinovat provozní data všech prostředků Azure, která jsou nejdůležitější pro IT, včetně telemetrie z Azure Log Analytics.  Než vstoupíme do vizualizace dotazu protokolu, nejprve vytvoříme řídicí panel a sdílíme ho.  Pak se můžeme zaměřit na náš ukázkový dotaz protokolu výkonu, který se vykreslí jako spojnicový graf, a přidat ho na řídicí panel.  

Řídicí panel vytvoříte výběrem tlačítka **Nový řídicí panel** vedle názvu aktuálního řídicího panelu.

![Vytvoření nového řídicího panelu na webu Azure Portal](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Tato akce vytvoří nový, prázdný, privátní řídicí panel a přepne vás do režimu přizpůsobení, ve kterém můžete řídicí panel pojmenovat a přidat na něj dlaždice nebo změnit jejich uspořádání. Upravte název řídicího panelu a zadejte *ukázkový řídicí panel* pro tento kurz a pak vyberte **Hotovo přizpůsobení**.<br><br> ![Uložení přizpůsobeného řídicího panelu Azure](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Když vytvoříte řídicí panel, ve výchozím nastavení je privátní. To znamená, že se zobrazí pouze vám. Pokud ho chcete zpřístupnit ostatním, použijte tlačítko **Sdílet**, které se zobrazí vedle dalších příkazů řídicího panelu.

![Sdílení nového řídicího panelu na webu Azure Portal](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

Zobrazí se výzva k výběru předplatného a skupiny prostředků, do kterých se má řídicí panel publikovat. Pro usnadnění práce vás prostředí pro publikování na portálu navede k umístění řídicích panelů do skupiny prostředků **dashboards** (řídicí panely).  Ověřte vybrané předplatné a pak klikněte na **Publikovat**.  Přístup k informacím zobrazeným na řídicím panelu se řídí pomocí [řízení přístupu na základě prostředku Azure](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-query"></a>Vizualizace dotazu protokolu
[Log Analytics](../log-query/get-started-portal.md) je vyhrazený portál používaný pro práci s dotazy protokolu a jejich výsledky. Mezi tyto funkce patří možnost upravovat dotazy na několik řádků, selektivní spouštění kódu, funkce IntelliSense závislá na kontextu a inteligentní analýzy. V tomto kurzu použijete analýzu protokolů k vytvoření zobrazení výkonu v grafické podobě, uložíte ho pro budoucí dotaz a připnete ho na dříve vytvořený sdílený řídicí panel.

Otevřete Analýzu protokolů výběrem **protokolů** v nabídce Azure Monitor. Začíná novým prázdným dotazem.

![Domovská stránka](media/tutorial-logs-dashboards/homepage.png)

Zadejte následující dotaz pro vrácení záznamů využití procesoru pro počítače se systémem Windows i Linux seskupené podle počítače a TimeGenerated a zobrazené ve vizuálním grafu. Klepnutím na tlačítko **Spustit** spusťte dotaz a zobrazte výsledný graf.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Uložte dotaz výběrem tlačítka **Uložit** v horní části stránky.

![Uložit dotaz](media/tutorial-logs-dashboards/save-query.png)

V ovládacím **panelu Uložit dotaz** zadejte název, jako jsou *virtuální počítače Azure – využití procesoru* a kategorie, jako jsou *řídicí panely,* a klikněte na **Uložit**.  Tímto způsobem můžete vytvořit knihovnu běžných dotazů, které můžete použít a upravit.  Nakonec to připněte na sdílený řídicí panel vytvořený dříve tak, že vyberete tlačítko **Připnout k řídicímu panelu** v pravém horním rohu stránky a pak vyberete název řídicího panelu.

Když teď máte dotaz připnutý na řídicím panelu, všimněte si, že je pod ním uvedený obecný název a komentář.

![Ukázka řídicího panelu Azure](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Měli bychom změnit název na něco smysluplného, co snadno pochopí uživatelé, kterým se zobrazí.  Klepnutím na tlačítko upravit přizpůsobíte název a titulky dlaždice a potom klepněte na tlačítko **Aktualizovat**.  Zobrazí se banner s výzvou k publikování nebo zahození změn.  Klepněte na **tlačítko Uložit kopii**.  

![Dokončená konfigurace ukázkového řídicího panelu](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili, jak vytvořit řídicí panel na webu Azure Portal a přidat do něj dotaz protokolu.  Přejdete k dalšímu kurzu a dozvíte se různé odpovědi, které můžete implementovat na základě výsledků dotazu protokolu.  

> [!div class="nextstepaction"]
> [Reakce na události s využitím upozornění Log Analytics](tutorial-response.md)
