---
title: Vytvoření pracovního prostoru Log Analytics na portálu Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit pracovní prostor Log Analytics umožňuje správu řešení a shromažďování dat z vašich cloudových a místních prostředích na webu Azure Portal.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: bc8162a157cee10deeb8bf3f1e62a53fbdd30d0e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513435"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Vytvoření pracovního prostoru Log Analytics na portálu Azure portal
Pomocí nabídky **pracovní prostory Log Analytics** můžete vytvořit pracovní prostor Log Analytics pomocí Azure Portal. Log Analytics pracovní prostor je jedinečné prostředí pro Azure Monitor data protokolu. Každý pracovní prostor má své vlastní úložiště a konfiguraci dat a zdroje dat a řešení jsou nakonfigurovány tak, aby ukládaly data do konkrétního pracovního prostoru. Pokud máte v úmyslu shromažďovat data z následujících zdrojů, potřebujete Log Analytics pracovní prostor:

* Prostředky ve vašem předplatném Azure
* Místní služba System Center Operations Manager monitorovat počítače
* Kolekce zařízení z Configuration Manager 
* Diagnostika nebo protokolování dat ze služby Azure storage

U jiných zdrojů, jako jsou virtuální počítače Azure a Windows nebo virtuální počítače s Linuxem ve vašem prostředí naleznete v následujících tématech:

*  [Shromažďování dat z virtuálních počítačů Azure](../learn/quick-collect-azurevm.md) 
*  [Shromažďování dat z počítače s Linuxem hybridní](../learn/quick-collect-linux-computer.md)
*  [Shromažďování dat z počítače Windows hybridní](quick-collect-windows-computer.md)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure-portal"></a>Přihlásit se na Azure Portal
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics pracovní prostory**.

    ![Portál Azure](media/quick-create-workspace/azure-portal-01.png)
  
2. Klikněte na **Přidat**a pak vyberte volby pro následující položky:

   * Zadejte název nového **pracovního prostoru služby Log Analytics**, například *DefaultLAWorkspace*. Tento název musí být globálně jedinečný v rámci všech předplatných Azure Monitor.
   * Vyberte **předplatné**, které má být cílem propojení, výběrem z rozevíracího seznamu, pokud výchozí vybrané předplatné není vhodné.
   * Pro **skupiny prostředků**, zvolte možnost používat existující prostředek již skupinu nastavení nebo vytvořte novou.  
   * Vyberte dostupný **umístění**.  Další informace najdete v tématu které [oblasti Log Analytics jsou k dispozici v](https://azure.microsoft.com/regions/services/) článku a vyhledejte Azure monitor z **hledání pole produkt** .  
   * Pokud vytváříte pracovní prostor v novém předplatném vytvořeném po 2. dubnu 2018, automaticky se použije cenový plán *podle počtu GB* a možnost vybrat cenovou úroveň nebude dostupná.  Pokud vytváříte pracovní prostor pro existující předplatné vytvořené před 2. dubnem nebo pro předplatné, které se vázalo na existující registraci smlouvy Enterprise (EA), vyberte upřednostňovanou cenovou úroveň.  Další informace o jednotlivých úrovních najdete v tématu [podrobnosti o cenách služby Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Okno pro vytvoření Log Analytics prostředku](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Po zadání požadovaných informací v podokně **Pracovní prostor služby Log Analytics** klikněte na **OK**.  

Během ověřování informací a vytváření pracovního prostoru můžete průběh zpracování sledovat prostřednictvím položky nabídky **Oznámení**. 

## <a name="next-steps"></a>Další kroky
Teď, když máte k dispozici pracovní prostor, konfigurace shromažďování dat monitorování telemetrických dat a spustit prohledávání protokolů pro analýzu těchto dat přidat řešení správy, které poskytují další data a analytické přehledy. 

* Pokud chcete povolit shromažďování dat z prostředků Azure pomocí diagnostiky Azure nebo do úložiště Azure, najdete v článku [metriky pro použití v Log Analytics a Azure shromažďovat protokoly služby](../platform/collect-azure-metrics-logs.md).  
* [Přidat System Center Operations Manager jako zdroj dat](../platform/om-agents.md) shromažďovat data z agenty posílající sestavy skupině pro správu nástroje Operations Manager a uloží je v pracovním prostoru Log Analytics. 
* Připojit [nástroje Configuration Manager](../platform/collect-sccm.md) importovat počítače, které jsou členy kolekce v hierarchii.  
* Projděte si dostupná [řešení pro monitorování](../insights/solutions.md) a postup přidání nebo odebrání řešení z pracovního prostoru.
