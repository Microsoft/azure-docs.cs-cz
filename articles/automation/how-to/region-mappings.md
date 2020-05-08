---
title: Mapování pracovního prostoru Azure Automation a Log Analytics
description: Tento článek popisuje mapování povolená mezi účtem služby Automation a pracovním prostorem Log Analytics.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6b00b25dc4eaea5bc8a3f5fbd42389aff501f14a
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901014"
---
# <a name="workspace-mappings"></a>Mapování pracovních prostorů

V Azure Automation můžete povolit následující řešení: "Update Management", "Change Tracking a inventář" a "Spustit/zastavit virtuální počítače v době mimo špičku". V takovém případě však mějte na paměti, že k propojení Log Analyticsho pracovního prostoru a účtu Automation v rámci předplatného jsou podporovány pouze určité oblasti. Toto mapování platí pouze pro účet Automation a pracovní prostor Log Analytics. Pracovní prostor Log Analytics a účet Automation musí být ve stejném předplatném, ale můžou být v různých skupinách prostředků nasazených do stejné oblasti.

Další informace najdete v tématu [Log Analytics pracovní prostor a účet Automation](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

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

Pokud se rozhodnete, že už nechcete integrovat svůj účet Automation s pracovním prostorem Log Analytics, můžete zrušit propojení svého účtu přímo s Azure Portal. Než budete pokračovat, musíte nejprve odebrat "Update Management," Change Tracking a inventarizaci "a" Spustit/zastavit virtuální počítače v době mimo špičku ", pokud je používáte. Pokud je neodeberete, nemůžete dokončit operaci odpojování. Projděte si článek pro každé řešení, které chcete povolit, abyste pochopili kroky potřebné k jeho odebrání.

Po odebrání těchto kroků můžete zrušit propojení svého účtu Automation.

> [!NOTE]
> Některá řešení, včetně dřívějších verzí řešení Azure SQL monitoring, mohla vytvářet prostředky pro automatizaci a může být potřeba je odebrat před odpojením pracovního prostoru.

1. V Azure Portal otevřete svůj účet Automation. Na stránce **účet Automation** vyberte v části **související prostředky**možnost **propojený pracovní prostor** .

2. Na stránce zrušit **propojení pracovního prostoru** vyberte zrušit **propojení pracovního prostoru**. Zobrazí se výzva, abyste ověřili, jestli chcete pokračovat.

3. I když se Azure Automation pokusí odpojit účet Log Analytics pracovním prostoru, můžete sledovat průběh v nabídce **oznámení** .

4. Pokud jste použili "Update Management", možná budete chtít po odebrání odebrat následující položky, které už nepotřebujete.

    * Plány aktualizací: každý má název, který odpovídá nasazení aktualizace, kterou jste vytvořili.
    * Skupiny hybridních pracovních procesů vytvořené pro řešení: každý má název podobný řetězci `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`.

5. Pokud jste v době mimo špičku použili možnost Spustit/zastavit virtuální počítače, můžete volitelně odebrat následující položky, které po jejím odebrání nepotřebujete.

    * Spuštění a zastavení plánů Runbook VM
    * Spuštění a zastavení runbooků virtuálních počítačů
    * Proměnné

Alternativně můžete zrušit propojení pracovního prostoru s účtem Automation v pracovním prostoru.

1. V pracovním prostoru vyberte **účet Automation** v části **související prostředky**. 
2. Na stránce **účet Automation** vyberte zrušit **propojení účtu**.

## <a name="next-steps"></a>Další kroky

* Naučte se, jak začít používat "Update Management" a "Change Tracking a inventář":

    * Z [virtuálního počítače](../automation-onboard-solutions-from-vm.md).
    * Z [účtu Automation](../automation-onboard-solutions-from-automation-account.md).
    * Při [procházení více počítačů](../automation-onboard-solutions-from-browse.md).
    * Z [Runbooku](../automation-onboard-solutions.md).

* Naučte se, jak začít používat "spuštění/zastavení virtuálních počítačů v době mimo špičku":

    * [Spuštění/zastavení virtuálních počítačů v době mimo špičku](../automation-solution-vm-management.md)
