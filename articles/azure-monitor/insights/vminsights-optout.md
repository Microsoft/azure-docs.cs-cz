---
title: Vypnout monitorování pro virtuální počítače (preview) ve službě Azure Monitor | Dokumentace Microsoftu
description: Tento článek popisuje, jak zastavit monitorování virtuálních počítačů ve službě Azure Monitor pro virtuální počítače.
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
ms.openlocfilehash: eb667486a6e3279cb78fefe02723f14d9f7c9b4f
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155685"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>Vypnout monitorování na vašich virtuálních počítačů ve službě Azure Monitor pro virtuální počítače (preview)

Když povolíte monitorování virtuálních počítačů (VM), můžete později vypnout monitorování pro virtuální počítače ve službě Azure Monitor. Tento článek ukazuje, jak vypnout monitorování pro jednu nebo více virtuálních počítačů.  

Azure Monitor pro virtuální počítače v současné době nepodporuje selektivní zákaz monitorování virtuálního počítače. Váš pracovní prostor Log Analytics může podporovat monitorování Azure pro virtuální počítače a dalších řešení. To může také shromažďovat dalšími daty monitorování. Pokud váš pracovní prostor Log Analytics poskytuje tyto služby, musíte pochopit vliv a zakážete sledování před spuštěním metody.

Azure Monitor pro virtuální počítače se spoléhá na následující součásti pro zajištění jeho prostředí:

* Pracovnímu prostoru Log Analytics, která ukládá data monitorování z virtuálních počítačů a dalších zdrojů.
* Shromažďování čítačů výkonu, které jsou nakonfigurované v pracovním prostoru. Kolekci se aktualizuje konfiguraci monitorování na všechny virtuální počítače připojené k pracovnímu prostoru.
* `InfrastructureInsights` a `ServiceMap`, které jsou řešení nakonfigurovat v pracovním prostoru monitorování. Tato řešení aktualizovat konfiguraci monitorování na všechny virtuální počítače připojené k pracovnímu prostoru.
* `MicrosoftMonitoringAgent` a `DependencyAgent`, které jsou rozšíření virtuálních počítačů Azure. Tato rozšíření shromažďují a zasílají data do pracovního prostoru.

Při přípravě na vypnout monitorování na vašich virtuálních počítačů, mějte tyto aspekty:

* Pokud vyhodnotit pomocí jednoho virtuálního počítače a používají Zkontrolujte předem vybrané výchozí pracovní prostor Log Analytics, můžete zakázat monitorování odinstalací agenta závislostí z virtuálního počítače a agenta Log Analytics se odpojuje z tohoto pracovního prostoru. Tento přístup je vhodný, pokud máte v úmyslu použít virtuální počítač pro jiné účely a rozhodnout později připojit s jiným pracovním prostorem.
* Pokud jste vybrali dříve existující pracovní prostor Log Analytics, který podporuje další řešení monitorování a shromažďování dat z jiných zdrojů, můžete z pracovního prostoru odebrat komponenty řešení bez přerušení nebo by to ovlivnilo pracovního prostoru.  

>[!NOTE]
> Po odebrání součástí řešení z pracovního prostoru, může nadále zobrazovat stav z vašich virtuálních počítačů Azure; Konkrétně budete zobrazit výkon a mapování dat, když přejdete na některém zobrazení na portálu. Data se nakonec zastaví povolí, **výkonu** a **mapy** zobrazení. Ale **stavu** zobrazení nadále zobrazovat stav pro virtuální počítače. **Vyzkoušet** možnost je k dispozici z vybraného virtuálního počítače Azure, takže můžete znovu povolit monitorování v budoucnu.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Úplné odebrání monitorování Azure pro virtuální počítače

Pokud stále potřebujete pracovní prostor Log Analytics, použijte následující postup úplně odebrat monitorování Azure pro virtuální počítače. Odebere `InfrastructureInsights` a `ServiceMap` řešení z pracovního prostoru.  

>[!NOTE]
>Pokud jste použili Service Map, řešení pro monitorování předtím, než jste povolili službu Azure Monitor pro virtuální počítače a stále spoléhat na to, neodebírejte toto řešení podle popisu v posledním kroku následující postup.  
>

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na webu Azure Portal vyberte **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Podle zadávaného seznam filtrech návrhy na základě vašich zadání. Vyberte **Log Analytics**.
3. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor jste zvolili, když jste povolili monitorování Azure pro virtuální počítače.
4. Na levé straně vyberte **řešení**.  
5. V seznamu řešení, vyberte **InfrastructureInsights (název pracovního prostoru)** . Na **přehled** stránku pro řešení, vyberte **odstranit**. Po zobrazení výzvy k potvrzení, vyberte **Ano**.  
6. V seznamu řešení, vyberte **ServiceMap (název pracovního prostoru)** . Na **přehled** stránku pro řešení, vyberte **odstranit**. Po zobrazení výzvy k potvrzení, vyberte **Ano**.  

Před povolením monitorování Azure pro virtuální počítače, pokud jste to neudělali [shromažďování čítačů výkonu](vminsights-enable-overview.md#performance-counters-enabled) pro virtuální počítače Windows nebo Linuxem v pracovním prostoru [zakázat pravidla](../platform/data-sources-performance-counters.md#configuring-performance-counters) pro Windows a Linux.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Zakázat monitorování a zachovat pracovní prostor  

Pokud váš pracovní prostor Log Analytics je stále potřebuje k podpoře monitorování z jiných zdrojů, následujícím postupem vypnout monitorování na virtuálním počítači, který jste použili k vyhodnocení monitorování Azure pro virtuální počítače. Pro virtuální počítače Azure, odeberete agenta závislostí rozšíření virtuálního počítače a rozšíření virtuálního počítače agenta Log Analytics pro Windows nebo Linux přímo z virtuálního počítače. 

>[!NOTE]
>Neodebírat agenta Log Analytics, pokud: 
>
> * Azure Automation spravuje virtuální počítač k orchestraci procesy nebo ke správě konfigurace nebo aktualizace. 
> * Azure Security Center slouží ke správě virtuálních počítačů pro zabezpečení a detekce hrozeb. 
>
> Pokud odeberete agenta Log Analytics, vám zabrání tyto služby a řešení aktivně spravuje váš virtuální počítač. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
2. Na webu Azure Portal, vyberte **virtuálních počítačů**. 
3. V seznamu vyberte virtuální počítač. 
4. Na levé straně vyberte **rozšíření**. Na **rozšíření** stránce **DependencyAgent**.
5. Na stránce Vlastnosti rozšíření, vyberte **odinstalovat**.
6. Na **rozšíření** stránce **MicrosoftMonitoringAgent**. Na stránce Vlastnosti rozšíření, vyberte **odinstalovat**.  
