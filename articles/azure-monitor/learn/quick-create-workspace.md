---
title: Vytvoření pracovního prostoru Log Analytics na portálu Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit pracovní prostor Log Analytics umožňuje správu řešení a shromažďování dat z vašich cloudových a místních prostředích na webu Azure Portal.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 3c2e9d5634916c3713b7e3380c0496611d8f60a6
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656275"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Vytvoření pracovního prostoru Log Analytics na portálu Azure portal
Pomocí nabídky **pracovní prostory Log Analytics** můžete vytvořit pracovní prostor Log Analytics pomocí Azure Portal. Log Analytics pracovní prostor je jedinečné prostředí pro Azure Monitor data protokolu. Každý pracovní prostor má své vlastní úložiště a konfiguraci dat a zdroje dat a řešení jsou nakonfigurovány tak, aby ukládaly data do konkrétního pracovního prostoru. Pokud máte v úmyslu shromažďovat data z následujících zdrojů, potřebujete Log Analytics pracovní prostor:

* Prostředky ve vašem předplatném Azure
* Místní služba System Center Operations Manager monitorovat počítače
* Kolekce zařízení z Configuration Manager 
* Diagnostika nebo protokolování dat ze služby Azure storage

U jiných zdrojů, jako jsou virtuální počítače Azure a Windows nebo virtuální počítače s Linuxem ve vašem prostředí naleznete v následujících tématech:

*  [Shromažďování dat z virtuálních počítačů Azure](../learn/quick-collect-azurevm.md) 
*  [Shromažďování dat z hybridního počítače se systémem Linux](../learn/quick-collect-linux-computer.md)
*  [Shromažďování dat z hybridního počítače s Windows](quick-collect-windows-computer.md)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Vytvořit pracovní prostor
1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics pracovní prostory**.

    ![Azure Portal](media/quick-create-workspace/azure-portal-01.png)
  
2. Klikněte na **Přidat**a pak vyberte volby pro následující položky:

   * Zadejte název nového **pracovního prostoru služby Log Analytics**, například *DefaultLAWorkspace*. Tento název musí být globálně jedinečný v rámci všech předplatných Azure Monitor.
   * Vyberte **předplatné**, které má být cílem propojení, výběrem z rozevíracího seznamu, pokud výchozí vybrané předplatné není vhodné.
   * V případě **skupiny prostředků**vyberte možnost použít existující skupinu prostředků, která už je nastavená, nebo vytvořte novou.  
   * Vyberte dostupné **umístění**.  Další informace najdete v tématu které [oblasti Log Analytics jsou k dispozici v](https://azure.microsoft.com/regions/services/) článku a vyhledejte Azure monitor z **hledání pole produkt** .  
   * Pokud vytváříte pracovní prostor v novém předplatném vytvořeném po 2. dubnu 2018, automaticky se použije cenový plán *podle počtu GB* a možnost vybrat cenovou úroveň nebude dostupná.  Pokud vytváříte pracovní prostor pro existující předplatné vytvořené před 2. dubnem nebo pro předplatné, které se vázalo na existující registraci smlouvy Enterprise (EA), vyberte upřednostňovanou cenovou úroveň.  Další informace o jednotlivých vrstvách najdete v článku [informace o cenách Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Okno pro vytvoření Log Analytics prostředku](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Po zadání požadovaných informací v podokně **Pracovní prostor služby Log Analytics** klikněte na **OK**.  

Během ověřování informací a vytváření pracovního prostoru můžete průběh zpracování sledovat prostřednictvím položky nabídky **Oznámení**. 

## <a name="next-steps"></a>Další kroky
Teď, když máte k dispozici pracovní prostor, konfigurace shromažďování dat monitorování telemetrických dat a spustit prohledávání protokolů pro analýzu těchto dat přidat řešení správy, které poskytují další data a analytické přehledy. 

* Pokud chcete povolit shromažďování dat z prostředků Azure pomocí Azure Diagnostics nebo úložiště Azure, přečtěte si téma [shromáždění protokolů služby Azure a metrik pro použití v Log Analytics](../platform/collect-azure-metrics-logs.md).  
* [Přidejte System Center Operations Manager jako zdroj dat](../platform/om-agents.md) , abyste mohli shromažďovat data z agentů, kteří hlásí skupinu pro správu Operations Manager, a uložit je do pracovního prostoru Log Analytics. 
* Připojte [Configuration Manager](../platform/collect-sccm.md) k importu počítačů, které jsou členy kolekcí v hierarchii.  
* Projděte si dostupná [řešení pro monitorování](../insights/solutions.md) a postup přidání nebo odebrání řešení z pracovního prostoru.
