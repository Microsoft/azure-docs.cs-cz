---
title: Odeslání protokolu aktivit Azure do Log Analytics pracovního prostoru pomocí Azure Portal
description: Použijte Azure Portal k vytvoření pracovního prostoru Log Analytics a nastavení diagnostiky pro odeslání protokolu aktivit do protokolů Azure Monitor.
ms.subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 47cf0a1f1d40b9e8358d6bdb4a3ea6a501b67da2
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91439214"
---
# <a name="send-azure-activity-log-to-log-analytics-workspace-using-azure-portal"></a>Odeslání protokolu aktivit Azure do Log Analytics pracovního prostoru pomocí Azure Portal
Protokol aktivit je protokol platformy v Azure, který poskytuje přehled o událostech na úrovni předplatného. To zahrnuje informace, jako je například změna prostředku nebo spuštění virtuálního počítače. Protokol aktivit můžete zobrazit v Azure Portal nebo načíst položky pomocí PowerShellu a rozhraní příkazového řádku. V tomto rychlém startu se dozvíte, jak pomocí Azure Portal vytvořit pracovní prostor Log Analytics a nastavení diagnostiky k odeslání protokolu aktivit do Azure Monitor protokolů, kde je můžete analyzovat pomocí [dotazů protokolu](../log-query/log-query-overview.md) a povolit další funkce, jako jsou například výstrahy a [sešity](../platform/workbooks-overview.md) [protokolu](../platform/alerts-log-query.md) . 

## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 



## <a name="create-a-log-analytics-workspace"></a>Vytvoření pracovního prostoru Log Analytics
V Azure Portal vyhledejte a potom vyberte **pracovní prostory Log Analytics**. 

![Snímek obrazovky Azure Portal se ve vyhledávacím poli zobrazuje "pracovní prostory Log Analytics" a Log Analytics v pracovních prostorech zvýrazněných v části služby.](media/quick-create-workspace/azure-portal-01.png)
  
Klikněte na **Přidat**a zadejte hodnoty pro **skupinu prostředků**, **název**pracovního prostoru a **umístění**. Název pracovního prostoru musí být v rámci všech předplatných Azure jedinečný.

![Vytvoření pracovního prostoru](media/quick-collect-activity-log/create-workspace.png)

Kliknutím na tlačítko **zkontrolovat + vytvořit** zkontrolujte nastavení a pak **vytvořte** pracovní prostor. Tím se vybere výchozí cenová úroveň s průběžnými **platbami** , které neúčtují žádné změny, dokud nezačnete shromažďovat dostatečné množství dat. Za shromáždění protokolu aktivit se neúčtují žádné poplatky.


## <a name="create-diagnostic-setting"></a>Vytvoření nastavení diagnostiky
V Azure Portal vyhledejte a pak vyberte **monitor**. 

![Snímek obrazovky Azure Portal se slovem "monitor" ve vyhledávacím poli a monitorování zvýrazněným v části služby.](media/quick-collect-activity-log/azure-portal-monitor.png)

Vyberte **Protokol aktivit**. Měli byste vidět nedávné události pro aktuální předplatné. Kliknutím na **nastavení diagnostiky** zobrazíte nastavení diagnostiky pro odběr.

![Protokol aktivit](media/quick-collect-activity-log/activity-log.png)

Pokud chcete vytvořit nové nastavení, klikněte na **Přidat nastavení diagnostiky** . 

![Vytvoření nastavení diagnostiky](media/quick-collect-activity-log/create-diagnostic-setting.png)

Do pracovního prostoru zadejte název, jako je například *Odeslat protokol aktivity*. Vyberte každou z kategorií. Vyberte **odeslat Log Analytics** jako jediný cíl a potom zadejte pracovní prostor, který jste vytvořili. Kliknutím na **Uložit** vytvořte nastavení diagnostiky a potom stránku zavřete.

![Nové nastavení diagnostiky](media/quick-collect-activity-log/new-diagnostic-setting.png)

## <a name="generate-log-data"></a>Generovat data protokolu
Do pracovního prostoru Log Analytics se odešlou jenom nové položky protokolu aktivit, takže se v předplatném provede několik akcí, které se budou protokolovat jako spuštění nebo zastavení virtuálního počítače nebo vytvoření nebo úprava jiného prostředku. Možná budete muset několik minut počkat, než se nastavení diagnostiky vytvoří a data, která se mají zpočátku zapsat do pracovního prostoru. Po této prodlevě se všechny události zapsané do protokolu aktivit odešlou do pracovního prostoru během několika sekund.

## <a name="retrieve-data-with-a-log-query"></a>Načtení dat pomocí dotazu protokolu

V nabídce **Azure monitor** vyberte **protokoly** . Zavřete stránku **příklady dotazů** . Pokud obor není nastavený na pracovní prostor, který jste vytvořili, klikněte na **Vybrat obor** a vyhledejte ho.

![Rozsah Log Analytics](media/quick-collect-activity-log/log-analytics-scope.png)

V okně dotazu zadejte `AzureActivity` a klikněte na **Spustit**. Jedná se o jednoduchý dotaz, který vrátí všechny záznamy v tabulce *AzureActivity* , která obsahuje všechny záznamy odeslané z protokolu aktivit.

![Jednoduchý dotaz](media/quick-collect-activity-log/query-01.png)

Rozbalte jeden ze záznamů, abyste zobrazili jeho podrobné vlastnosti.

![Rozbalit vlastnosti](media/quick-collect-activity-log/expand-properties.png)

Vyzkoušejte složitější dotaz, například to, `AzureActivity | summarize count() by CategoryValue` který poskytuje počet událostí shrnutých podle kategorie.

![Složitý dotaz](media/quick-collect-activity-log/query-02.png)


## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste nakonfigurovali protokol aktivit, který se má odeslat do Log Analytics pracovního prostoru. Teď můžete nakonfigurovat další data, která se mají shromáždit, do pracovního prostoru, kde je můžete analyzovat pomocí [dotazů protokolu](../log-query/log-query-overview.md) v Azure monitor a využívat funkce, jako jsou výstrahy a [sešity](../platform/workbooks-overview.md) [protokolu](../platform/alerts-log-query.md) . Měli byste další shromáždit [protokoly prostředků](../platform/resource-logs.md) z vašich prostředků Azure, které vám poskytnou přehled o operacích, které byly provedeny v rámci jednotlivých prostředků v protokolu aktivit.


> [!div class="nextstepaction"]
> [Shromažďování a analýza protokolů prostředků pomocí Azure Monitor](tutorial-resource-logs.md)
