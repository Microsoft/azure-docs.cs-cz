---
title: Jak zakázat monitorování prostřednictvím služby Azure Monitor pro virtuální počítače (preview) | Dokumentace Microsoftu
description: Tento článek popisuje, jak můžete ukončit monitorování virtuálních počítačů pomocí Azure monitoru pro virtuální počítače.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: magoedte
ms.openlocfilehash: c6a8322998b391b9021292a3a7213ded56b67feb
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263352"
---
# <a name="how-to-disable-monitoring-of-your-virtual-machines-with-azure-monitor-for-vms-preview"></a>Zakázání monitorování virtuálních počítačů pomocí Azure monitoru pro virtuální počítače (preview)

Pokud po povolení monitorování virtuálních počítačů se rozhodnete, že už nechcete sledujte je pomocí Azure monitoru pro virtuální počítače, můžete zakázat monitorování. Tento článek ukazuje, jak to provést pro jeden nebo více virtuálních počítačů.  

Azure Monitor pro virtuální počítače v současné době nepodporuje selektivnímu zakázání monitorování vašich virtuálních počítačů. Pokud váš pracovní prostor Log Analytics je konfigurován pro podporu tohoto řešení a dalších řešení a také shromažďovat další data monitorování, je důležité porozumět dopadu a metod popsaných níže, než budete pokračovat.

Azure Monitor pro virtuální počítače se spoléhá na následující součásti pro zajištění jeho prostředí:

* Pracovní prostor Log Analytics, která ukládá data monitorování se shromažďují z virtuálních počítačů a dalších zdrojů.
* Shromažďování čítačů výkonu, které jsou nakonfigurované v pracovním prostoru, která aktualizuje konfiguraci monitorování pro všechny virtuální počítače připojené k pracovnímu prostoru.
* Dvě řešení monitorování, které jsou nakonfigurované v pracovním prostoru - **InfrastructureInsights** a **ServiceMap**, které aktualizace konfigurace monitorování pro všechny virtuální počítače připojené k pracovnímu prostoru.
* Dvě rozšíření virtuálního počítače Azure **MicrosoftMonitoringAgent** a **DependencyAgent**, která shromažďují a zasílají data do pracovního prostoru.

Při přípravě zakázat monitorování virtuálních počítačů pomocí Azure monitoru pro virtuální počítače, zvažte následující:

* Pokud hodnotíte s jeden virtuální počítač a přijali předem vybraná výchozího pracovního prostoru Log Analytics, můžete zakázat monitorování odinstalací agenta závislostí z virtuálního počítače a agenta Log Analytics se odpojuje z tohoto pracovního prostoru. Tento přístup je vhodné, pokud určené pro instalaci na použití virtuálního počítače pro jiné účely a rozhodnout později připojit s jiným pracovním prostorem.
* Pokud používáte pracovní prostor Log Analytics pro podporu jiných řešení pro monitorování a shromažďování dat z jiných zdrojů, můžete odebrat monitorování Azure pro virtuální počítače součástí řešení z pracovního prostoru, aniž by došlo k přerušení nebo dopad do pracovního prostoru.  

>[!NOTE]
> Po odebrání součástí řešení z pracovního prostoru, může nadále zobrazovat stav z vašich virtuálních počítačů Azure; Konkrétně mapy data o výkonu a přejdete na některém zobrazení na portálu. Data se nakonec zastaví povolí, v zobrazení výkon a mapy za nějakou dobu; Zobrazení stavu budou ale nadále zobrazovat stav pro virtuální počítače. **Vyzkoušet** možnost je k dispozici z vybraného virtuálního počítače Azure můžete znovu povolit monitorování v budoucnu.  

## <a name="complete-removal-of-azure-monitor-for-vms"></a>Dokončení odstranění služby Azure Monitor pro virtuální počítače

Následující kroky popisují, jak úplně odebrat monitorování Azure pro virtuální počítače, pokud pořád budete potřebovat pracovní prostor Log Analytics. Chcete odebrat **InfrastructureInsights** a **ServiceMap** řešení z pracovního prostoru.  

>[!NOTE]
>Pokud jste používali Service Map monitorovacího řešení před povolením monitorování Azure pro virtuální počítače a stále spoléhat na to, neodebírejte toto řešení, jak je popsáno v kroku 6 níže.  
>

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte Log Analytics. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
3. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor jste si zvolili při registraci pro virtuální počítače Azure Monitor.
4. V levém podokně vyberte **řešení**.  
5. V seznamu řešení, vyberte **InfrastructureInsights (název pracovního prostoru)** a pak na **přehled** stránce řešení, klikněte na tlačítko **odstranit**.  Po zobrazení výzvy k potvrzení klikněte na tlačítko **Ano**.  
6. Seznam řešení, vyberte **ServiceMap (název pracovního prostoru)** a pak na **přehled** stránce řešení, klikněte na tlačítko **odstranit**.  Po zobrazení výzvy k potvrzení klikněte na tlačítko **Ano**.  

Pokud před zprovoznění Azure Monitor pro virtuální počítače, které nebyly [shromažďování čítačů výkonu povolena](vminsights-onboard.md?toc=/azure/azure-monitor/toc.json#performance-counters-enabled) pro Windows nebo linuxem virtuálních počítačů ve vašem pracovním prostoru, budete muset zakázat tato pravidla pomocí následujících kroků popsaných [tady](../../azure-monitor/platform/data-sources-performance-counters.md?toc=/azure/azure-monitor/toc.json#configuring-performance-counters) pro Windows a Linux.

## <a name="disable-monitoring-for-an-azure-vm-and-retain-workspace"></a>Vypnout monitorování pro virtuální počítač Azure a uchování pracovního prostoru  

Následující kroky popisují, jak vypnout monitorování pro virtuální počítač, který byl povolen pro vyhodnocení monitorování Azure pro virtuální počítače, ale pracovní prostor Log Analytics je stále vyžadují k podpoře monitorování z jiných zdrojů. Pokud je virtuální počítač Azure, budete k odebrání agenta závislostí rozšíření virtuálního počítače a agenta Log Analytics rozšíření virtuálního počítače pro Windows/Linux přímo z virtuálního počítače. 

>[!NOTE]
>Virtuální počítač je spravován Azure Automation k orchestraci procesy, spravovat konfiguraci, nebo spravovat aktualizace nebo spravovaná pomocí služby Azure Security Center k zabezpečení správy a detekci hrozeb, by se neměly odebírat agenta Log Analytics. V opačném případě můžete zabrání tyto služby a řešení aktivně spravuje váš virtuální počítač. 

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 
2. Na webu Azure Portal, vyberte **virtuálních počítačů**. 
3. V seznamu vyberte virtuální počítač. 
4. V levém podokně vyberte **rozšíření** a na **rozšíření** stránce **DependencyAgent**.
5. Na stránce Vlastnosti rozšíření, klikněte na tlačítko **odinstalovat**.
6. Na **rozšíření** stránce **MicrosoftMonitoringAgent** a klikněte na stránce Vlastnosti rozšíření, **odinstalovat**.  
