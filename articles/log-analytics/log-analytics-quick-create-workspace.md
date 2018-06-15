---
title: Vytvořit pracovní prostor v Azure Log Analytics | Microsoft Docs
description: Naučte se vytvořit pracovní prostor analýzy protokolů k povolení správy řešení a shromažďování dat z vašich cloudových a místních prostředích.
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: magoedte
ms.openlocfilehash: e05d620a457edf427c946366a9caca471ac8531e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "30833128"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Vytvořit pracovní prostor analýzy protokolů na portálu Azure
Na portálu Azure, které můžete nastavit pracovní prostor analýzy protokolů, které je jedinečné prostředí analýzy protokolů s vlastní úložiště dat, zdroje dat a řešení.  Podle pokynů popsaných v tomto článku jsou požadovány, pokud máte v úmyslu na shromažďování dat z následujících zdrojů:

* Prostředky v rámci vašeho předplatného Azure
* Místní počítačů monitorovaných nástrojem System Center Operations Manager
* Kolekce zařízení z System Center Configuration Manager 
* Diagnostika nebo protokolování dat z úložiště Azure

Další zdroje, jako jsou virtuální počítače Azure a systému Windows nebo Linux počítače ve vašem prostředí najdete v následujících tématech:

*  [Shromažďování dat o virtuálních počítačích Azure](log-analytics-quick-collect-azurevm.md) 
*  [Shromažďování dat o počítače se systémem Linux](log-analytics-quick-collect-linux-computer.md)
*  [Shromažďování dat o počítače se systémem Windows](log-analytics-quick-collect-windows-computer.md)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.<br><br> ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Klikněte na **Vytvořit** a podle potřeby změňte hodnoty následujících položek:

  * Zadejte název nového **pracovního prostoru OMS**, například *DefaultLAWorkspace*. 
  * Vyberte **předplatné**, které má být cílem propojení, výběrem z rozevíracího seznamu, pokud výchozí vybrané předplatné není vhodné.
  * Pro **skupiny prostředků**, využít existující prostředek skupiny již instalační program nebo vytvořte novou.  
  * Vyberte dostupný **umístění**.  Další informace najdete na stránce uvádějící [oblasti, ve kterých je dostupná služba Log Analytics](https://azure.microsoft.com/regions/services/).
  * Při vytváření pracovního prostoru v nové předplatné vytvořený po 2. dubna 2018, budou automaticky používat *za GB* cenách plán a možnost zvolit cenovou úroveň, nebudete mít k dispozici.  Pokud vytváříte pracovní prostor pro stávající předplatné vytvořené před duben 2, nebo k odběru, který je vázaný na existující EA zápisu, vyberte upřednostňovaný cenovou úroveň.  Další informace o jednotlivých úrovních najdete v [podrobnostech o cenách Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-02.png)<br>  

3. Po zadání požadovaných informací v podokně **Pracovní prostor OMS** klikněte na **OK**.  

Během ověřování informací a vytváření pracovního prostoru můžete průběh zpracování sledovat prostřednictvím položky nabídky **Oznámení**. 

## <a name="next-steps"></a>Další postup
Teď, když máte k dispozici pracovního prostoru, můžete nakonfigurovat kolekce monitorování telemetrie, spusťte hledání protokolu k analýze dat a přidat řešení pro správu k poskytování dalších dat a analytické Statistika. 

* Chcete-li povolit shromažďování dat z prostředků Azure s Azure Diagnostics nebo úložiště Azure, přečtěte si téma [protokoly služby Azure shromažďovat a metriky pro použití v analýzy protokolů](log-analytics-azure-storage.md).  
* [Přidat System Center Operations Manager jako zdroj dat](log-analytics-om-agents.md) pro shromažďování dat od agentů reporting skupině pro správu nástroje Operations Manager a uložte ho do pracovního prostoru úložiště analýzy protokolů. 
* Připojit [nástroje Configuration Manager](log-analytics-sccm.md) import počítače, které jsou členy kolekce v hierarchii.  
* Zkontrolujte [řešení pro správu](/log-analytics-add-solutions.md) k dispozici a jak přidat nebo odebrat řešení z pracovního prostoru.

