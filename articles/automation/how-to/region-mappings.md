---
title: Podporované oblasti pro propojený pracovní prostor Log Analytics
description: Tento článek popisuje podporovaná mapování oblastí mezi účtem služby Automation a pracovním prostorem Log Analytics.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 24ff69e76736ffa93cecb795be563f172c422355
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744752"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>Podporované oblasti pro propojený pracovní prostor Log Analytics

V Azure Automation můžete pro své virtuální počítače povolit Update Management, Change Tracking a inventář a Start/Stop VMs during off-hours funkce. Propojení Log Analyticsho pracovního prostoru a účtu Automation v rámci předplatného ale podporuje jenom některé oblasti. Mapování oblastí platí pouze pro účet Automation a pracovní prostor Log Analytics. Pracovní prostor Log Analytics a účet Automation musí být ve stejném předplatném, ale můžou být v různých skupinách prostředků nasazených do stejné oblasti. Další informace najdete v tématu [Log Analytics pracovní prostor a účet Automation](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Podporovaná mapování

V následující tabulce jsou uvedena podporovaná mapování:

|**Oblast pracovního prostoru Log Analytics**|**Oblast Azure Automation**|
|---|---|
|**USA**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Kanada**||
|CanadaCentral|CanadaCentral|
|**Asie a Tichomoří**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**Evropa**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> mapování EastUS pro Log Analytics pracovní prostory na účty Automation není přesné mapování oblastí na oblast, ale je to správné mapování.

<sup>2</sup> v důsledku omezení kapacity není oblast dostupná, když vytváříte nové prostředky. To zahrnuje účty Automation a pracovní prostory Log Analytics. Dříve existující propojené prostředky v oblasti by však měly fungovat i nadále.

## <a name="unlink-a-workspace"></a>Zrušit propojení pracovního prostoru

Pokud se rozhodnete, že už nechcete integrovat svůj účet Automation s pracovním prostorem Log Analytics, můžete zrušit propojení svého účtu přímo s Azure Portal. Než budete pokračovat, musíte nejdřív [odebrat](move-account.md#remove-features) Update Management, Change Tracking a inventář a Start/Stop VMS during off-hours, pokud je používáte. Pokud je neodeberete, nemůžete dokončit operaci odpojování. 

Po odebrání funkcí můžete pomocí následujících kroků zrušit propojení svého účtu Automation.

> [!NOTE]
> Některé funkce, včetně dřívějších verzí řešení Azure SQL monitoring, mohly vytvořit assety automatizace, které je potřeba před odpojením pracovního prostoru odebrat.

1. V Azure Portal otevřete svůj účet Automation. Na stránce účet Automation vyberte v části **související prostředky**možnost **propojený pracovní prostor** .

2. Na stránce zrušit propojení pracovního prostoru vyberte zrušit **propojení pracovního prostoru**. Zobrazí se výzva, abyste ověřili, jestli chcete pokračovat.

3. I když Azure Automation odpojíte účet z pracovního prostoru Log Analytics, můžete sledovat průběh v nabídce **oznámení** .

4. Pokud jste použili Update Management, můžete taky chtít odebrat následující položky, které už nepotřebujete:

    * Plány aktualizací: každý má název, který odpovídá nasazení aktualizace, kterou jste vytvořili.
    * Skupiny hybridních pracovních procesů vytvořené pro funkci: každý má název podobný `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8` .

5. Pokud jste použili Start/Stop VMs during off-hours, můžete volitelně odebrat následující položky, které už nepotřebujete:

    * Spuštění a zastavení plánů Runbook VM
    * Spuštění a zastavení runbooků virtuálních počítačů
    * Proměnné

Alternativně můžete zrušit propojení pracovního prostoru s účtem Automation v pracovním prostoru.

1. V pracovním prostoru vyberte **účet Automation** v části **související prostředky**. 
2. Na stránce účet Automation vyberte zrušit **propojení účtu**.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o Update Management v článku [Update Management přehled](../automation-update-management.md).
* Přečtěte si o Change Tracking a inventáři v [přehledu Change Tracking a inventáře](../change-tracking.md).
* Přečtěte si o Start/Stop VMs during off-hours v článku [Start/Stop VMS during off-hours přehled](../automation-solution-vm-management.md).
