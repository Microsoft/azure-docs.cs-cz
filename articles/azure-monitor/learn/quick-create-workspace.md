---
title: Vytvoření pracovního prostoru Analýzy protokolů na webu Azure Portal | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit pracovní prostor Log Analytics, který umožní řešení pro správu a shromažďování dat z cloudových a místních prostředí na webu Azure Portal.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 3c2e9d5634916c3713b7e3380c0496611d8f60a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656275"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Vytvoření pracovního prostoru Analýzy protokolů na webu Azure Portal
Pomocí nabídky **pracovníprostory Log Analytics** můžete vytvořit pracovní prostor Analýzy protokolů pomocí portálu Azure. Pracovní prostor Log Analytics je jedinečné prostředí pro data protokolu Azure Monitor. Každý pracovní prostor má své vlastní úložiště dat a konfiguraci a zdroje dat a řešení jsou nakonfigurovány tak, aby ukládali data v určitém pracovním prostoru. Pracovní prostor Log Analytics vyžadujete, pokud máte v úmyslu shromažďovat data z následujících zdrojů:

* Prostředky Azure ve vašem předplatném
* Místní počítače monitorované správcem operací system center
* Kolekce zařízení z nástroje Configuration Manager 
* Diagnostika nebo data protokolu z úložiště Azure

Další zdroje, jako jsou virtuální počítače Azure a virtuální počítače s Windows nebo Linux ve vašem prostředí, najdete v následujících tématech:

*  [Shromažďování dat z virtuálních počítačů Azure](../learn/quick-collect-azurevm.md) 
*  [Shromažďování dat z hybridního počítače s Linuxem](../learn/quick-collect-linux-computer.md)
*  [Shromažďování dat z hybridního počítače se systémem Windows](quick-collect-windows-computer.md)

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese . 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **pracovní prostory Analýzy protokolů**.

    ![portál Azure](media/quick-create-workspace/azure-portal-01.png)
  
2. Klikněte na **Přidat**a vyberte volby pro následující položky:

   * Zadejte název nového **pracovního prostoru služby Log Analytics**, například *DefaultLAWorkspace*. Tento název musí být globálně jedinečný ve všech předplatných Azure Monitoru.
   * Vyberte **předplatné**, které má být cílem propojení, výběrem z rozevíracího seznamu, pokud výchozí vybrané předplatné není vhodné.
   * V **části Skupina prostředků**zvolte, zda chcete použít existující skupinu prostředků, která již byla nastavena, nebo vytvořte novou.  
   * Vyberte dostupné **umístění**.  Další informace najdete v [tématech, ve kterých oblastech je Služba Log Analytics dostupná,](https://azure.microsoft.com/regions/services/) a vyhledejte Azure Monitor z pole **Hledat produkt.**  
   * Pokud vytváříte pracovní prostor v novém předplatném vytvořeném po 2. dubnu 2018, automaticky se použije cenový plán *podle počtu GB* a možnost vybrat cenovou úroveň nebude dostupná.  Pokud vytváříte pracovní prostor pro existující předplatné vytvořené před 2.  Další informace o konkrétních úrovních najdete v [tématu Podrobnosti o cenách analýzy protokolů](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Vytvořit okno prostředků Analýzy protokolů](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Po zadání požadovaných informací v podokně **pracovního prostoru Analýzy protokolů** klepněte na tlačítko **OK**.  

Během ověřování informací a vytváření pracovního prostoru můžete průběh zpracování sledovat prostřednictvím položky nabídky **Oznámení**. 

## <a name="next-steps"></a>Další kroky
Teď, když máte k dispozici pracovní prostor, můžete nakonfigurovat kolekci monitorování telemetrie, spustit vyhledávání protokolů k analýze těchto dat a přidat řešení pro správu poskytovat další data a analytické přehledy. 

* Pokud chcete povolit shromažďování dat z prostředků Azure pomocí Azure Diagnostics nebo Azure storage, přečtěte si najdete [v tématu Shromažďování protokolů služeb Azure a metrik pro použití v Log Analytics](../platform/collect-azure-metrics-logs.md).  
* [Přidejte nástroj Operations Manager system center jako zdroj dat](../platform/om-agents.md) pro shromažďování dat od agentů, kteří hlásí vaši skupinu pro správu nástroje Operations Manager, a uložte je do pracovního prostoru Analýzy protokolů. 
* Připojte [nástroj Configuration Manager](../platform/collect-sccm.md) k importu počítačů, které jsou členy kolekcí v hierarchii.  
* Projděte si [dostupná řešení monitorování](../insights/solutions.md) a postup přidání nebo odebrání řešení z pracovního prostoru.
