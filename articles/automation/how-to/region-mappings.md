---
title: Mapování pracovního prostoru Azure Automation a Log Analytics
description: Tento článek popisuje mapování povolená mezi účtem služby Automation a pracovním prostorem Log Analytics pro podporu řešení.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 965d6b206bb64e90fe59798ce0c37ccf029117f5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849509"
---
# <a name="workspace-mappings"></a>Mapování pracovních prostorů

Při povolování řešení jako Update Management, Change Tracking a inventáře nebo řešení Start/Stop VMs during off-hours jsou podporovány pouze některé oblasti pro propojení Log Analyticsho pracovního prostoru a účtu Automation. Toto mapování platí pouze pro účet Automation a pracovní prostor Log Analytics. Prostředky, které se hlásí do vašeho účtu Automation nebo do pracovního prostoru Log Analytics, můžou být v jiných oblastech.

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

<sup>1</sup> mapování EastUS pro Log Analytics pracovní prostory do účtů Automation není přesné mapování oblastí na oblast, ale je to správné mapování.

<sup>2</sup> z důvodu omezení kapacity není oblast při vytváření nových prostředků dostupná. To zahrnuje účty Automation a pracovní prostory Log Analytics. Stávající propojené prostředky v oblasti ale budou fungovat i nadále.

## <a name="unlink-workspace"></a>Zrušit propojení pracovního prostoru

Pokud se rozhodnete, že už nechcete integrovat svůj účet Automation s pracovním prostorem Log Analytics, můžete zrušit propojení svého účtu přímo s Azure Portal. Než budete pokračovat, musíte nejprve odebrat Update Management, Change Tracking a inventář nebo Start/Stop VMs during off-hours řešení, pokud je používáte. Pokud je neodeberete, proces nebude moci pokračovat. Projděte si článek pro konkrétní řešení, které jste naimportovali, abyste pochopili kroky potřebné k jeho odebrání.

Po odebrání těchto řešení můžete provést následující kroky a zrušit propojení svého účtu Automation.

> [!NOTE]
> Některá řešení, včetně dřívějších verzí řešení Azure SQL monitoring, mohla vytvořit automatizační prostředky a možná je budete muset před odpojením pracovního prostoru odebrat.

1. V Azure Portal otevřete svůj účet Automation a na stránce účet Automation vyberte **propojený pracovní prostor** v části **související prostředky** na levé straně.

2. Na stránce zrušit propojení pracovního prostoru klikněte na **Zrušit propojení pracovního prostoru**. Zobrazí se výzva s dotazem, zda chcete pokračovat.

3. I když se Azure Automation pokusí odpojit účet Log Analytics pracovním prostoru, můžete sledovat průběh v nabídce **oznámení** .

Pokud jste použili řešení Update Management, možná budete chtít po odebrání řešení odebrat následující položky, které už nepotřebujete.

* Aktualizace plánů – každá bude mít názvy, které odpovídají vytvořeným nasazením aktualizací.

* Skupiny hybridních pracovních procesů vytvořené pro řešení – každá bude pojmenována podobně jako `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`).

Pokud jste použili řešení Start/Stop VMs during off-hours, možná budete chtít po odebrání řešení odebrat následující položky, které už nepotřebujete.

* Spuštění a zastavení plánů Runbook VM
* Spuštění a zastavení runbooků virtuálních počítačů
* Proměnné

Alternativně můžete také zrušit propojení pracovního prostoru s účtem Automation z pracovního prostoru Log Analytics. V pracovním prostoru vyberte **účet Automation** v části **související prostředky**. Na stránce účet Automation vyberte zrušit **propojení účtu**.

## <a name="next-steps"></a>Další kroky

Naučte se, jak začlenit následující řešení:

Update Management a Change Tracking a inventář:

* Z [virtuálního počítače](../automation-onboard-solutions-from-vm.md)
* Z [účtu Automation](../automation-onboard-solutions-from-automation-account.md)
* Při [procházení více počítačů](../automation-onboard-solutions-from-browse.md)
* Z [Runbooku](../automation-onboard-solutions.md)

Spuštění/zastavení virtuálních počítačů mimo špičku

* [Nasazení Start/Stop VMs during off-hours](../automation-solution-vm-management.md)
