---
title: Vytvoření pracovního prostoru Log Analytics na portálu Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit pracovní prostor Log Analytics umožňuje správu řešení a shromažďování dat z vašich cloudových a místních prostředích na webu Azure Portal.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: magoedte
ms.openlocfilehash: f5bc5edaccf07f4840a2db329fb5c3a0c51b7a6d
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55999432"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Vytvoření pracovního prostoru Log Analytics na portálu Azure portal
Použití **pracovních prostorů Log Analytics** nabídky pro vytvoření pracovního prostoru Log Analytics pomocí webu Azure portal. Pracovní prostor Log Analytics je jedinečný prostředí pro data protokolů Azure Monitor. Každý pracovní prostor má své vlastní úložiště dat a konfigurace a konfigurace zdroje dat a řešení pro ukládání dat v konkrétním pracovním prostoru. Budete potřebovat pracovní prostor Log Analytics, pokud máte v úmyslu na shromažďování dat z těchto zdrojů:

* Prostředky ve vašem předplatném Azure
* Místní služba System Center Operations Manager monitorovat počítače
* Kolekce zařízení ze System Center Configuration Manager 
* Diagnostika nebo protokolování dat ze služby Azure storage

U jiných zdrojů, jako jsou virtuální počítače Azure a Windows nebo virtuální počítače s Linuxem ve vašem prostředí naleznete v následujících tématech:

*  [Shromažďování dat z virtuálních počítačů Azure](../learn/quick-collect-azurevm.md) 
*  [Shromažďování dat z počítače s Linuxem hybridní](../learn/quick-collect-linux-computer.md)
*  [Shromažďování dat z počítače Windows hybridní](quick-collect-windows-computer.md)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **pracovních prostorů Log Analytics**.

    ![portál Azure](media/quick-create-workspace/azure-portal-01.png)
  
2. Klikněte na tlačítko **přidat**a podle potřeby změňte hodnoty následujících položek:

  * Zadejte název nového **pracovního prostoru Log Analytics**, například *DefaultLAWorkspace*. 
  * Vyberte **předplatné**, které má být cílem propojení, výběrem z rozevíracího seznamu, pokud výchozí vybrané předplatné není vhodné.
  * Pro **skupiny prostředků**, zvolte možnost používat existující prostředek již skupinu nastavení nebo vytvořte novou.  
  * Vyberte dostupný **umístění**.  Další informace najdete v tématu který [Log Analytics je dostupná v oblastech](https://azure.microsoft.com/regions/services/).
  * Pokud vytváříte pracovní prostor v novém předplatném vytvořeném po 2. dubnu 2018, automaticky se použije cenový plán *podle počtu GB* a možnost vybrat cenovou úroveň nebude dostupná.  Pokud vytváříte pracovní prostor pro existující předplatné vytvořené před 2. dubnem nebo pro předplatné, které se vázalo na existující registraci smlouvy Enterprise (EA), vyberte upřednostňovanou cenovou úroveň.  Další informace o jednotlivých úrovních najdete v tématu [podrobnosti o cenách služby Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Po zadání požadovaných informací v podokně **Pracovní prostor Log Analytics** klikněte na **OK**.  

Během ověřování informací a vytváření pracovního prostoru můžete průběh zpracování sledovat prostřednictvím položky nabídky **Oznámení**. 

## <a name="next-steps"></a>Další postup
Teď, když máte k dispozici pracovní prostor, konfigurace shromažďování dat monitorování telemetrických dat a spustit prohledávání protokolů pro analýzu těchto dat přidat řešení správy, které poskytují další data a analytické přehledy. 

* Pokud chcete povolit shromažďování dat z prostředků Azure pomocí diagnostiky Azure nebo do úložiště Azure, najdete v článku [metriky pro použití v Log Analytics a Azure shromažďovat protokoly služby](../platform/collect-azure-metrics-logs.md).  
* [Přidat System Center Operations Manager jako zdroj dat](../platform/om-agents.md) shromažďovat data z agenty posílající sestavy skupině pro správu nástroje Operations Manager a uloží je v pracovním prostoru Log Analytics. 
* Připojit [nástroje Configuration Manager](../platform/collect-sccm.md) importovat počítače, které jsou členy kolekce v hierarchii.  
* Zkontrolujte [řešení monitorování](../insights/solutions.md) k dispozici a jak přidat nebo odebrat některé řešení z pracovního prostoru.
